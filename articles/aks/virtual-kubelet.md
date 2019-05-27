---
title: Spuštění Virtual Kubelet v clusteru služby Azure Kubernetes Service (AKS)
description: Naučte se používat Virtual Kubelet Azure Kubernetes Service (AKS) ke spuštění kontejnerů Linuxu a Windows v Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/14/2018
ms.author: iainfou
ms.openlocfilehash: f7a0269ff22987648d134cb7f4fba8e28e29fd8b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956287"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Virtual Kubelet pomocí služby Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) poskytuje hostované prostředí pro spouštění kontejnerů v Azure. Při použití ACI, není nutné ke správě základní infrastruktury pro výpočetní prostředky, tento management za vás postará Azure. Při spouštění kontejnerů v ACI, se účtují za sekundu pro každý spuštěný kontejner.

Při použití zprostředkovatele Virtual Kubelet pro Azure Container Instances, kontejnerů Linuxu a Windows můžete naplánovat na instanci kontejneru, jako by byl uzlu standardní Kubernetes. Tato konfigurace umožňuje využít možnosti Kubernetes a správu hodnotu a náklady na benefit služeb container instances.

> [!NOTE]
> AKS nyní obsahuje integrovanou podporu pro plánování kontejnery ve službě ACI, volá *virtuální uzly*. Tyto virtuální uzly aktuálně podporují instance kontejnerů Linuxu. Pokud je potřeba naplánovat instance kontejneru Windows, můžete pokračovat v používání Virtual Kubelet. V opačném případě používali virtuální uzly místo ruční Virtual Kubelet pokyny, které jste si poznamenali v tomto článku. Můžete začít s virtuálními uzly pomocí [rozhraní příkazového řádku Azure] [ virtual-nodes-cli] nebo [webu Azure portal][virtual-nodes-portal].
>
> Virtual Kubelet je experimentální opensourcový projekt a by měla sloužit jako takové. Abyste mohli přispívat, soubor problémů a přečtěte si další informace o virtual kubelet, najdete v článku [projektu z Githubu Virtual Kubelet][vk-github].

## <a name="before-you-begin"></a>Než začnete

Tento dokument předpokládá, že máte AKS cluster. Pokud potřebujete AKS cluster, přečtěte si [rychlý start Azure Kubernetes Service (AKS)][aks-quick-start].

Musíte také Azure CLI verze **2.0.33** nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Chcete-li nainstalovat Virtual Kubelet [Helm](https://docs.helm.sh/using_helm/#installing-helm) je také nutný.

### <a name="register-container-instances-feature-provider"></a>Registrace poskytovatele funkce Container Instances

Pokud jste dříve nepoužili služby Azure Container Instance (ACI), zaregistrujte poskytovatele služby s vaším předplatným. Stav registrace poskytovatele ACI pomocí příkazu [az provider list] [az provider list], můžete zjistit, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* poskytovatele hlásit jako *registrované*, jak je znázorněno v následujícím příkladu výstupu:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Pokud poskytovatel zobrazí jako *NotRegistered*, zaregistrujte poskytovatele pomocí [az registrovat poskytovatele] [az provider register], jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Pro clustery s podporou RBAC

Pokud váš cluster AKS je povoleno RBAC, musíte vytvořit účet služby a role vazby pro použití s Tiller. Další informace najdete v tématu [řízení přístupu na základě rolí Helm][helm-rbac]. Pokud chcete vytvořit účet služby a role vazby, vytvořte soubor s názvem *rbac virtuální kubelet.yaml* a vložte následující definice:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Použít účet služby a vazbu s [použití kubectl] [ kubectl-apply] a určete vaše *rbac virtuální kubelet.yaml* souboru, jak je znázorněno v následujícím příkladu:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Nakonfigurujte Helm použít účet tiller service:

```console
helm init --service-account tiller
```

Teď můžete pokračovat instalací Virtual Kubelet do clusteru AKS.

## <a name="installation"></a>Instalace

Použití [az aks install-connector] [ aks-install-connector] příkaz k instalaci Virtual Kubelet. Tento příklad nasadí konektor Linux i Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Tyto argumenty jsou k dispozici pro `aks install-connector` příkazu.

| Argument: | Popis | Požaduje se |
|---|---|:---:|
| `--connector-name` | Název konektoru ACI.| Ano |
| `--name` `-n` | Název spravovaného clusteru. | Ano |
| `--resource-group` `-g` | Název skupiny prostředků. | Ano |
| `--os-type` | Typ operačního systému instance kontejneru. Povolené hodnoty: Both, Linux, Windows. Výchozí: Linux | Ne |
| `--aci-resource-group` | Skupina prostředků, ve kterém chcete vytvořit skupiny kontejnerů ACI. | Ne |
| `--location` `-l` | Umístění pro vytvoření skupiny kontejnerů ACI. | Ne |
| `--service-principal` | Instanční objekt služby používat k ověřování do rozhraní API Azure. | Ne |
| `--client-secret` | Tajný klíč přidružený k objektu služby. | Ne |
| `--chart-url` | Adresa URL grafu helmu, který nainstaluje konektoru ACI. | Ne |
| `--image-tag` | Značka obrázku virtual kubelet Image kontejneru. | Ne |

## <a name="validate-virtual-kubelet"></a>Ověření Virtual Kubelet

Chcete-li ověřit, jestli je nainstalovaná Virtual Kubelet, vrátí seznam uzlů Kubernetes pomocí [kubectl get uzly] [ kubectl-get] příkazu.

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Spuštění kontejneru Linuxu

Vytvořte soubor s názvem `virtual-kubelet-linux.yaml` a zkopírujte do následující kód YAML. Všimněte si, že [nodeSelector] [ node-selector] a [toleration] [ toleration] byly použity k naplánování kontejneru na uzlu.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Spusťte aplikaci [kubectl vytvořit] [ kubectl-create] příkazu.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Použití [kubectl get pods] [ kubectl-get] příkazů `-o wide` argument do výstupního seznam podů naplánované uzlu. Všimněte si, že `aci-helloworld` pod byla naplánována na `virtual-kubelet-virtual-kubelet-linux` uzlu.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Spuštění kontejneru Windows

Vytvořte soubor s názvem `virtual-kubelet-windows.yaml` a zkopírujte do následující kód YAML. Všimněte si, že [nodeSelector] [ node-selector] a [toleration] [ toleration] byly použity k naplánování kontejneru na uzlu.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Spusťte aplikaci [kubectl vytvořit] [ kubectl-create] příkazu.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Použití [kubectl get pods] [ kubectl-get] příkazů `-o wide` argument do výstupního seznam podů naplánované uzlu. Všimněte si, že `nanoserver-iis` pod byla naplánována na `virtual-kubelet-virtual-kubelet-win` uzlu.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Odebrat Virtual Kubelet

Použití [az aks remove-connector] [ aks-remove-connector] příkazu odeberte Virtual Kubelet. Nahraďte hodnoty argumentů název konektoru, clusteru AKS a skupinu prostředků clusteru AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

> [!NOTE]
> Pokud dojde k chybám odebrání oba typy konektorů operačního systému, nebo chcete odebrat jenom konektor Windows nebo Linux OS, můžete ručně zadat typ operačního systému. Přidat `--os-type` parametr na předchozí `az aks remove-connector` příkaz a zadejte `Windows` nebo `Linux`.

## <a name="next-steps"></a>Další postup

Možné problémy s Virtual Kubelet, najdete v článku [známé adaptivní a alternativní řešení][vk-troubleshooting]. K hlášení problémů s Virtual Kubelet [otevřete problém na Githubu][vk-issues].

Další informace o Virtual Kubelet na [projektu z Githubu Virtual Kubelet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
