---
title: Zabezpečení v luskech pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)
description: Naučte se zabezpečit provoz, který se zachází do lusků, pomocí zásad sítě Kubernetes ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 1339fe66a4925104d459c0491caccdd7db5998a7
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114466"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)

Když v Kubernetes spustíte moderní aplikace založené na mikroslužbách, často chcete určit, které součásti můžou vzájemně komunikovat. Zásada minimálního oprávnění by měla být použita na to, jak přenos dat může procházet mezi lusky v clusteru služby Azure Kubernetes (AKS). Řekněme, že pravděpodobně chcete zablokovat provoz přímo do back-endové aplikace. Funkce *zásady sítě* v Kubernetes vám umožňuje definovat pravidla pro příchozí a odchozí provoz mezi lusky v clusteru.

V tomto článku se dozvíte, jak nainstalovat modul zásad sítě a vytvořit zásady sítě Kubernetes pro řízení toku provozu mezi lusky v AKS. Zásady sítě by se měly používat jenom pro uzly se systémem Linux a lusky v AKS.

## <a name="before-you-begin"></a>Před zahájením

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Verzi `az --version` zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

> [!TIP]
> Pokud jste ve verzi Preview použili funkci síťové zásady, doporučujeme [vytvořit nový cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Pokud chcete pokračovat v používání stávajících testovacích clusterů, které používaly zásady sítě během verze Preview, upgradujte cluster na nové verze Kubernetes pro nejnovější verzi GA a pak nasaďte následující manifest YAML, abyste opravili selhání serveru metrik a Kubernetes ovládacího. Tato oprava se vyžaduje jenom u clusterů, které používaly modul zásad sítě Calico.
>
> Z hlediska zabezpečení je nejvhodnější [zkontrolovat obsah tohoto MANIFESTU YAML][calico-aks-cleanup] a pochopit, co se nasazuje do clusteru AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Přehled zásad sítě

Všechny lusky v clusteru AKS můžou ve výchozím nastavení odesílat a přijímat přenosy bez omezení. Pro zvýšení zabezpečení můžete definovat pravidla, která řídí tok přenosů. Back-endové aplikace se často zveřejňují jenom pro požadované front-endové služby, například. Nebo součásti databáze jsou přístupné pouze pro aplikační vrstvy, které k nim připojovat.

Zásada sítě je specifikace Kubernetes, která definuje zásady přístupu pro komunikaci mezi lusky. Pomocí zásad sítě můžete definovat uspořádanou sadu pravidel pro odesílání a příjem provozu a použít je pro kolekci lusků, která odpovídá jednomu nebo více selektorům popisku.

Tato pravidla zásad sítě se definují jako YAML manifesty. Zásady sítě je možné zahrnout do širšího manifestu, který také vytváří nasazení nebo službu.

### <a name="network-policy-options-in-aks"></a>Možnosti zásad sítě v AKS

Azure poskytuje dva způsoby, jak implementovat zásady sítě. Při vytváření clusteru AKS zvolíte možnost Zásady sítě. Po vytvoření clusteru se možnost Zásady nedá změnit:

* Vlastní implementace Azure označovaná jako *zásady sítě Azure*.
* *Calico zásady sítě*, což je open source řešení zabezpečení sítě a sítě založené na [Tigera][tigera].

Obě implementace používají Linux *softwaru iptables* k vykonání zadaných zásad. Zásady jsou přeloženy do sad povolených a nepovolených párů IP adres. Tyto páry se pak naprogramují jako pravidla filtru IPTable.

Zásada sítě funguje jenom s možností Azure CNI (rozšířené). Implementace se liší pro tyto dvě možnosti:

* *Zásady sítě Azure* – Azure CNI nastaví most v hostiteli virtuálního počítače pro síť v rámci uzlů. Pravidla filtrování se používají, pokud pakety procházejí přes most.
* *Zásady sítě Calico* – Azure CNI nastavuje místní trasy jádra pro provoz uvnitř uzlu. Zásady jsou aplikovány na síťové rozhraní pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Rozdíly mezi zásadami Azure a Calico a jejich funkcemi

| Funkce                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Podporované platformy                      | Linux                      | Linux                       |
| Podporované síťové možnosti             | Azure CNI                  | Azure CNI                   |
| Dodržování předpisů pomocí specifikace Kubernetes | Všechny podporované typy zásad |  Všechny podporované typy zásad |
| Další funkce                      | Žádné                       | Model rozšířených zásad skládající se z globálních síťových zásad, globální síťové sady a koncového bodu hostitele. Další informace o použití rozhraní `calicoctl` příkazového řádku ke správě těchto rozšířených funkcí naleznete v tématu [calicoctl User reference][calicoctl]. |
| Podpora                                  | Podporováno technickou podporou a technickým týmem pro Azure | Podpora komunity Calico. Další informace o další placené podpoře najdete v tématu [Možnosti podpory pro Project Calico][calico-support]. |
| Protokolování                                  | Pravidla přidaná/Odstraněná v softwaru iptables se protokolují na všech hostitelích pod */var/log/Azure-npm.log* . | Další informace najdete v tématu [protokoly komponent Calico][calico-logs] . |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Vytvoření clusteru AKS a povolení zásad sítě

Pokud chcete zobrazit zásady sítě v akci, vytvoříme a pak rozbalíme zásadu, která definuje tok přenosů:

* Odepřít veškerý provoz do pod.
* Povoluje provoz na základě popisků pod.
* Povolte provoz založený na oboru názvů.

Nejdřív vytvořte cluster AKS, který podporuje zásady sítě. Funkce zásady sítě se dá povolit, jenom když je cluster vytvořený. V existujícím clusteru AKS nemůžete povolit síťové zásady.

Pokud chcete používat zásady sítě s clusterem AKS, musíte použít [modul plug-in Azure CNI][azure-cni] a definovat vlastní virtuální síť a podsítě. Podrobnější informace o tom, jak naplánovat požadované rozsahy podsítí, najdete v tématu [Konfigurace pokročilých sítí][use-advanced-networking].

Následující vzorový skript:

* Vytvoří virtuální síť a podsíť.
* Vytvoří instanční objekt služby Azure Active Directory (Azure AD) pro použití s clusterem AKS.
* Přiřadí oprávnění *přispěvatele* pro objekt služby Cluster AKS ve virtuální síti.
* Vytvoří v definované virtuální síti cluster AKS a povolí zásady sítě.
    * Použije se možnost Zásady sítě *Azure* . Pokud chcete místo toho použít Calico jako zásadu sítě, použijte `--network-policy calico` parametr.

Poskytněte vlastní zabezpečení *sp_password*. Proměnné *RESOURCE_GROUP_NAME* a *CLUSTER_NAME* můžete nahradit:

```azurecli-interactive
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
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

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
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Vytvoření clusteru bude trvat několik minut. Až bude cluster připravený, nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odepřít veškerý příchozí provoz do pod

Před definováním pravidel pro povolení konkrétního síťového provozu vytvořte nejprve zásadu sítě, která zamítne veškerý provoz. Tato zásada nabízí výchozí bod, který začíná na seznam povolených přenosů. Můžete také jasně vidět, že při použití zásad sítě dojde k přerušení provozu.

V případě ukázkových prostředí aplikace a pravidel přenosů nejprve vytvoříme obor názvů s názvem *vývoj* pro spuštění příkladu lusků:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Vytvořte příklad back-endu pod, na kterém běží NGINX. Pomocí tohoto back-endu se dá simulovat Ukázková webová aplikace v back-endu. Vytvořte tuto položku pod oborem názvů pro *vývoj* a otevřete port *80* pro obsloužení webového provozu. Popište ho jako *App = WebApp, role = back-end* , abyste se mohli na něj zaměřit pomocí zásad sítě v následující části:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Vytvořte další pod a připojte relaci terminálu k otestování, jestli můžete úspěšně dosáhnout výchozí webové stránky NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k potvrzení, že máte přístup k výchozí webové stránce Nginx:

```console
wget -qO- http://backend
```

Následující vzorový výstup ukazuje, že se vrátila výchozí webová stránka NGINX:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Vytvoření a použití zásad sítě

Teď, když jste se potvrdili, že můžete použít základní webovou stránku NGINX na ukázkovém back-endu pod, vytvořit zásadu sítě, která zamítne veškerý provoz. Vytvořte soubor s názvem `backend-policy.yaml` a vložte následující manifest YAML. Tento manifest používá *podSelector* k připojení zásady k luskům, které mají *aplikaci: WebApp, role: back-end* , jako je ukázka Nginx pod. V rámci příchozího přenosunejsou definována žádná pravidla, takže veškerý příchozí provoz do uzlu pod je odepřen:

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

Použijte zásady sítě pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testování zásad sítě


Pojďme se podívat, jestli můžete použít webovou stránku NGINX v back-endu pod. Vytvořte další test pod a připojte relaci terminálu:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, zda máte přístup k výchozí webové stránce Nginx. Tentokrát nastavte hodnotu časového limitu na *2* sekund. Zásada sítě teď blokuje veškerý příchozí provoz, takže stránku nejde načíst, jak je znázorněno v následujícím příkladu:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Povolit příchozí provoz na základě popisku pod

V předchozí části se naplánoval back-end NGINX pod tím, že se vytvořila zásada sítě pro zamítnutí veškerého provozu. Pojďme vytvořit front-end pod a aktualizovat zásady sítě tak, aby umožňovaly provoz z front-endovéch lusků.

Aktualizujte zásady sítě tak, aby povolovaly provoz z lusků s aplikací Labels *: WebApp, role: front-end* a v jakémkoli oboru názvů. Upravte předchozí soubor *back-end-Policy. yaml* a přidejte *matchLabels* pravidla pro příchozí, aby váš manifest vypadal jako v následujícím příkladu:

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
> Tato zásada sítě používá *namespaceSelector* a element *podSelector* pro pravidlo příchozího přenosu dat. Syntaxe YAML je důležitá pro doplňková pravidla příchozího přenosu dat. V tomto příkladu musí oba elementy odpovídat pro použití pravidla příchozího přenosu dat. Verze Kubernetes starší než *1,12* nemusí tyto prvky správně interpretovat a omezit síťový provoz podle očekávání. Další informace o tomto chování najdete v tématu [chování a od][policy-rules]selektorů.

Použijte aktualizované zásady sítě pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Naplánujte pod označený jako *App = WebApp, role = front-end* a připojte relaci terminálu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, zda máte přístup k výchozí webové stránce Nginx:

```console
wget -qO- http://backend
```

Vzhledem k tomu, že pravidlo příchozího přenosu dat povoluje provoz s lusky, které mají aplikaci Labels *: WebApp, role: front*-end, je povolen provoz z front-endu pod. Následující příklad výstupu ukazuje výchozí webovou stránku NGINX, která se vrátila:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Pole pod je automaticky odstraněno.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Test pod bez odpovídajícího popisku

Zásady sítě umožňují provoz z lusků s označením *aplikace: WebApp, role: front-end*, ale měla by Odepřít všechny ostatní přenosy. Pojďme se podívat, jestli jiný pod ním bez těchto popisků může mít přístup k back-endové NGINX pod. Vytvořte další test pod a připojte relaci terminálu:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, zda máte přístup k výchozí webové stránce Nginx. Zásada sítě blokuje příchozí provoz, takže nelze načíst stránku, jak je znázorněno v následujícím příkladu:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Povolení provozu pouze v rámci definovaného oboru názvů

V předchozích příkladech jste vytvořili zásadu sítě, která odepřela veškerý provoz, a pak aktualizovali zásady tak, aby povolovala provoz z lusků s určitým popiskem. Další běžnou potřebou je omezit provoz jenom na v rámci daného oboru názvů. Pokud předchozí příklady byly pro provoz v oboru názvů pro *vývoj* , vytvořte zásady sítě, které brání provozu z jiného oboru názvů, jako je například *Výroba*, od dosažení lusků.

Nejdřív vytvořte nový obor názvů pro simulaci produkčního oboru názvů:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Naplánujte test pod v *produkčním* oboru názvů, který je označený jako *App = WebApp, role = front-endu*. Připojit relaci terminálu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k potvrzení, že máte přístup k výchozí webové stránce Nginx:

```console
wget -qO- http://backend.development
```

Vzhledem k tomu, že se jmenovky pod shodují s tím, co je aktuálně povoleno v zásadách sítě, je povolen provoz. Zásada sítě se nezobrazuje na oborech názvů, jenom na jmenovky pod. Následující příklad výstupu ukazuje výchozí webovou stránku NGINX, která se vrátila:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizace zásad sítě

Pojďme aktualizovat oddíl *namespaceSelector* pravidla příchozího přenosu dat, aby povoloval jenom přenosy z oboru názvů pro *vývoj* . Upravte soubor manifestu *back-end-Policy. yaml* , jak je znázorněno v následujícím příkladu:

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

V složitějších příkladech můžete definovat více pravidel příchozího přenosu dat, jako je *namespaceSelector* , a pak *podSelector*.

Použijte aktualizované zásady sítě pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Otestování aktualizovaných síťových zásad

Naplánujte další pod v produkčním oboru názvů a připojte relaci terminálu:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, že síťové zásady nyní zakazuje provoz:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Ukončete test pod:

```console
exit
```

Když se provoz zamítl z *produkčního* oboru názvů, naplánujte ho zpátky v oboru názvů pro *vývoj* a připojte relaci Terminálové služby:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na příkazovém řádku prostředí použijte `wget` k tomu, abyste viděli, že síťové zásady povolují přenosy:

```console
wget -qO- http://backend
```

Provoz je povolen, protože v oboru názvů je naplánováno, které odpovídá tomu, co je v zásadách sítě povoleno. Následující vzorový výstup ukazuje výchozí webovou stránku NGINX, která se vrátila:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jsme vytvořili dva obory názvů a použili jste zásady sítě. K vyčištění těchto prostředků použijte příkaz [kubectl Delete][kubectl-delete] a zadejte názvy prostředků:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Další kroky

Další informace o síťových prostředcích najdete v tématu [Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)][concepts-network].

Další informace o zásadách najdete v tématu [zásady sítě Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
