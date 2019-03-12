---
title: Zabezpečený pody zásadami sítě ve službě Azure Kubernetes Service (AKS)
description: Informace o zabezpečení provozu přenášeného do a z podů s použitím zásad sítě Kubernetes ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 81b45a25c8040916b835ab333c5ce80ab6c1a788
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772309"
---
# <a name="secure-traffic-between-pods-by-using-network-policies-in-azure-kubernetes-service"></a>Zabezpečení přenosu mezi pody pomocí zásady sítě ve službě Azure Kubernetes

Když spustíte moderních aplikací založených na mikroslužbách v Kubernetes, často chcete řídit, které součásti mohou komunikovat mezi sebou. Jak můžete tok provozu mezi pody v clusteru služby Azure Kubernetes Service (AKS) bude použito principu nejnižších možných oprávnění. Řekněme, že pravděpodobně chcete blokovat provoz přímo na back endové aplikace. *Zásady sítě* funkce v Kubernetes umožňuje definovat pravidla pro příchozí a odchozí přenos dat mezi pody v clusteru.

Calico, opensourcové sítě a řešení zabezpečení sítě Ruska Tigera, nabízí modul zásad sítě, které můžete implementovat pravidla zásad sítě Kubernetes. Tento článek popisuje, jak nainstalovat modul zásad sítě Calico a vytvářet zásady sítě Kubernetes pro řízení toku přenosů mezi pody ve službě AKS.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce může změnit před obecné dostupnosti (GA).

## <a name="before-you-begin"></a>Před zahájením

Musí mít Azure CLI verze 2.0.56 nebo později nainstalována a nakonfigurována. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

K vytvoření clusteru AKS, můžete použít zásady sítě, nejprve povolte příznak funkce v rámci předplatného. K registraci *EnableNetworkPolicy* příznak funkce, použijte [az funkce register] [ az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Trvá několik minut, než se stav zobrazíte *registrované*. Můžete zkontrolovat stav registrace pomocí [seznam funkcí az] [ az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register] [ az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Přehled služby Síťové zásady

Všechny podů v clusteru AKS mohla odesílat a přijímat provoz bez omezení, ve výchozím nastavení. Pro zlepšení zabezpečení, můžete definovat pravidla, která řídí tok provozu. Back endové aplikace jsou často dostupná jenom v případě do požadované front-endové služby, třeba. Nebo databáze součásti jsou pouze přístupné aplikačních vrstev, které k nim připojit.

Zásady sítě jsou prostředky Kubernetesu, které vám umožňují řídit tok přenosů mezi pody. Můžete povolit nebo odepřít přenosy podle nastavení, jako je přiřazené popisky, obor názvů nebo provoz portu. Zásady sítě, definovaná podle manifesty YAML. Tyto zásady mohou být součástí širší manifestu, který vytvoří také nasazení nebo služby.

Zobrazit zásady sítě v akci, Pojďme vytvořit a potom rozbalte na zásadu, která definuje toku provozu:

* Odepřete veškerý provoz směrem k pod.
* Povolení provozu na základě popisků pod.
* Povolení provozu na základě v oboru názvů.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Vytvoření clusteru AKS a povolit zásady sítě

Zásady sítě jde Povolit jenom při vytvoření clusteru. Nelze povolit zásady sítě v existujícím clusteru AKS. 

Zásady sítě pomocí AKS cluster, je nutné použít [modul plug-in Azure CNI] [ azure-cni] a definovat vlastní virtuální sítě a podsítě. Podrobné informace o tom, jak naplánovat rozsahy požadované podsítě, naleznete v tématu [konfiguraci rozšířeného sítě][use-advanced-networking].

Následující ukázkový skript:

* Vytvoří virtuální síť a podsíť.
* Vytvoří instanční objekt pro použití služby Azure Active Directory (Azure AD) s clusterem AKS.
* Přiřadí *Přispěvatel* oprávnění pro AKS clusteru instanční objekt služby ve virtuální síti.
* Vytvoří AKS cluster v definované virtuální sítě a povolí zásady sítě.

Zadejte vlastní zabezpečené *SP_PASSWORD*. Můžete nahradit *RESOURCE_GROUP_NAME* a *název_clusteru* proměnné:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.6 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

Vytvoření clusteru bude trvat několik minut. Když bude cluster připravený, nakonfigurujte `kubectl` pro připojení k vašemu clusteru Kubernetes pomocí [az aks get-credentials] [ az-aks-get-credentials] příkazu. Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odepřít veškerý příchozí provoz do pod

Předtím, než můžete definovat pravidla pro konkrétní síťové přenosy dat povolit, nejprve vytvořte zásady sítě chcete odepřít veškerý provoz. Tato zásada vám výchozí bod pro začít na seznam povolených jenom požadované přenosy. Je také jasně vidět, zahodí přenos, při použití zásad sítě.

Ukázková aplikace prostředí a pravidla přenosů, nejdřív vytvoříme obor názvů s názvem *vývoj* ke spuštění příkladu pody:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Vytvořte pod back-end příklad, na kterém běží server NGINX. Pod tento back-end umožňuje simulovat ukázkovou back endové webové aplikaci. Vytvoření tohoto podu v *vývoj* obor názvů a otevřete port *80* k poskytování webového provozu. Popisek pod s *app = webové aplikace, role = back-endu* tak, aby nám můžete cílit zásady sítě v další části:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Vytvoření další pod a připojení Terminálové relaci testování, že jste úspěšně dosáhnout výchozí webovou stránku serveru NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` potvrďte, že dostanete výchozí webovou stránku serveru NGINX:

```console
wget -qO- http://backend
```

Následující ukázkový výstup ukazuje, že vrátí výchozí webovou stránku serveru NGINX:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončit připojené relaci Terminálové služby. Test pod je automaticky odstraní.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Vytvoření a použití zásady sítě

Teď, když zkontrolujete, že používáte základní webová stránka serveru NGINX v podu ukázkové back-end, vytvořte zásady sítě chcete odepřít veškerý provoz. Vytvořte soubor s názvem `backend-policy.yaml` a vložte následující YAML manifestu. Používá tento manifest *podSelector* připojit zásady podů, které mají *app:webapp, role: back-endu* popisek jako ukázka podu NGINX. V části se nedefinují žádná pravidla *příchozího přenosu dat*, takže je odepřen veškerý příchozí provoz do pod:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Použít zásady sítě pomocí [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testování zásady sítě


Podívejme se, pokud webová stránka serveru NGINX v podu back-end můžete použít znovu. Vytvořte pod jiného testu a připojte relaci Terminálové:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` se, pokud je přístup k výchozí webovou stránku serveru NGINX. Tentokrát, nastavte hodnotu časového limitu na *2* sekund. Zásady sítě teď blokuje veškerý příchozí provoz, takže stránku nelze načíst, jak je znázorněno v následujícím příkladu:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Ukončit připojené relaci Terminálové služby. Test pod je automaticky odstraní.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Povolí příchozí provoz na základě na popisek pod

V předchozí části naplánovaný pod back-end serveru NGINX a zásady sítě byl vytvořen za účelem odepřít veškerý provoz. Pojďme vytvořit front-endu pod a aktualizovat zásady sítě umožňující provoz z front-endu pody.

Aktualizovat zásady sítě umožňující provoz z podů s popisky *app:webapp, role: front-endu* a libovolný obor názvů. Upravit předchozí *back-endu policy.yaml* a přidejte *matchLabels* pravidla příchozího přenosu dat tak, aby manifestu bude vypadat jako v následujícím příkladu:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Použije tyto zásady sítě *namespaceSelector* a *podSelector* – element pro příchozí pravidlo. Je důležité pro pravidla příchozího přenosu dat bude syntaxe YAML sčítání. V tomto příkladu musí odpovídat oba prvky pro příchozí pravidlo použít. Kubernetes verzí starších než *1.12* nemusí správně interpretovat tyto prvky a omezit síťový provoz podle očekávání. Další informace o tomto chování najdete v tématu [chování z do a z selektory][policy-rules].

Použít zásady sítě aktualizované pomocí [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Naplánovat, která je označena jako pod *app = webové aplikace, role = front-endu* a připojte relaci Terminálové:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` se, pokud je přístup k výchozí webovou stránku serveru NGINX:

```console
wget -qO- http://backend
```

Vzhledem k tomu, že pravidlo příchozího přenosu dat umožňuje provozu s využitím podů, které mají popisky *aplikace: webové aplikace, role: front-endu*, je povolený provoz z front-endu pod. Následující příklad výstupu ukazuje na výchozí webovou stránku serveru NGINX vrátila:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončit připojené relaci Terminálové služby. Pokud chcete pod automaticky odstraní.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testování pod bez odpovídající popisku

Zásady sítě povoluje provoz z podů s popiskem *aplikace: webové aplikace, role: front-endu*, ale měl odepřít veškerý ostatní provoz. Můžeme otestovat, jestli jiného pod bez tyto popisky můžou přistupovat k pod back-end serveru NGINX. Vytvořte pod jiného testu a připojte relaci Terminálové:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` se, pokud je přístup k výchozí webovou stránku serveru NGINX. Zásady sítě blokuje příchozí provoz, takže stránku nelze načíst, jak je znázorněno v následujícím příkladu:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Ukončit připojené relaci Terminálové služby. Test pod je automaticky odstraní.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Povolení provozu pouze z v rámci definovaný obor názvů

V předchozím příkladu jste vytvořili zásady sítě, který byl odepřen veškerý provoz a potom aktualizovat zásady tak, aby přijímaly provoz z podů s určitým popiskem. Další běžné potřeby je omezení provozu směřujícího do pouze v daném oboru názvů. V předchozích příkladech byly pro provoz v *vývoj* obor názvů, vytvářet zásady sítě, které brání provoz z jiného oboru názvů, jako například *produkční*, dosáhly tyto pody.

Nejprve vytvořte nový obor názvů pro simulaci výrobní obor názvů:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Plánování testů pod v *produkční* obor názvů, který je označen jako *app = webové aplikace, role = front-endu*. Připojení Terminálové relaci:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` potvrďte, že dostanete výchozí webovou stránku serveru NGINX:

```console
wget -qO- http://backend.development
```

Vzhledem k tomu, že popisky pod shodují, co je aktuálně povoleno v zásadách sítě, provoz je povolený. Zásady sítě nevypadá na obory názvů, pouze pod popisky. Následující příklad výstupu ukazuje na výchozí webovou stránku serveru NGINX vrátila:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončit připojené relaci Terminálové služby. Test pod je automaticky odstraní.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizovat zásady sítě

Umožňuje aktualizovat pravidlo příchozího přenosu dat *namespaceSelector* části povoluje pouze provoz v rámci *vývoj* oboru názvů. Upravit *back-endu policy.yaml* soubor manifestu, jak je znázorněno v následujícím příkladu:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

V příkladech složitější, můžete definovat více pravidel příchozího přenosu dat, jako je třeba *namespaceSelector* a pak *podSelector*.

Použít zásady sítě aktualizované pomocí [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Otestovat aktualizované síťové zásady

Naplánovat jinou pod v *produkční* obor názvů a připojte relaci Terminálové:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` zobrazíte, že zásady sítě nyní zakazuje provoz:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Ukončit test podu:

```console
exit
```

S přenosy byl odepřen z *produkční* obor názvů, plánu testů pod zpátky *vývoj* obor názvů a připojte relaci Terminálové:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí pomocí `wget` zobrazíte, že zásady sítě povoluje provoz:

```console
wget -qO- http://backend
```

Provoz je povolený, protože pod je naplánována v oboru názvů, odpovídá co je povolené v zásadách sítě. Následující ukázkový výstup ukazuje na výchozí webovou stránku serveru NGINX vrátila:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončit připojené relaci Terminálové služby. Test pod je automaticky odstraní.

```console
exit
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jsme vytvořili dva obory názvů a použije zásady sítě. K uvolnění těchto prostředků, použijte [kubectl odstranit] [ kubectl-delete] příkaz a zadejte názvy prostředků:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Další postup

Další informace o síťové prostředky, najdete v článku [sítě koncepty pro aplikace ve službě Azure Kubernetes Service (AKS)][concepts-network].

Další informace o zásadách najdete v tématu [zásady sítě, Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
