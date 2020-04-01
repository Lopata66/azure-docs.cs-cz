---
title: Kurz Kubernetes v Azure – Upgrade clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) se dozvíte, jak upgradovat existující cluster AKS na nejnovější dostupnou verzi Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 4d9ef061904fb1a0fff25506eedb82158971bed5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77622030"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Kurz: Upgrade Kubernetes ve službě Azure Kubernetes Service (AKS)

V rámci životního cyklu aplikace a clusteru možná budete chtít provést upgrade na nejnovější dostupnou verzi Kubernetes a využívat nové funkce. Cluster Azure Kubernetes Service (AKS) je možné upgradovat pomocí Azure CLI.

V tomto kurzu, který je sedmou částí sedmidílné série, se upgraduje cluster Kubernetes. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech byla aplikace zabalena do bitové kopie kontejneru. Tato bitová kopie byla odeslána do registru kontejnerů Azure a vy jste vytvořili cluster AKS. Aplikace byla poté nasazena do clusteru AKS. Pokud jste tyto kroky neprovedli a chcete je sledovat, začněte s [kurzem 1 – Vytvořte i images kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spouštěli Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Získání dostupných verzí clusteru

Před upgradem clusteru pomocí příkazu [az aks get-upgrades][] zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

V následujícím příkladu je aktuální verze *1.14.8*a dostupné verze jsou zobrazeny ve sloupci *Upgrady.*

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.14.8           1.14.8             1.15.5, 1.15.7
```

## <a name="upgrade-a-cluster"></a>Upgrade clusteru

Chcete-li minimalizovat narušení spuštěných aplikací, uzly AKS jsou pečlivě uzavřeny a vybity. V tomto procesu jsou prováděny následující kroky:

1. Plánovač Kubernetes zabraňuje další pody jsou naplánovány na uzlu, který má být upgradován.
1. Spuštěné pody v uzlu jsou naplánovány na jiných uzlech v clusteru.
1. Vytvoří se uzel, který spouští nejnovější součásti Kubernetes.
1. Když je nový uzel připraven a připojen ke clusteru, plánovač Kubernetes začne spouštět pody na něm.
1. Starý uzel je odstraněn a další uzel v clusteru zahájí proces cordon u vyprazdňování.

Cluster AKS můžete upgradovat pomocí příkazu [az aks upgrade][]. Následující příklad upgraduje cluster na Kubernetes verze *1.14.6*.

> [!NOTE]
> Najednou můžete upgradovat pouze jednu dílčí verzi. Můžete například upgradovat z *1.14.x* na *1.15.x*, ale nelze je upgradovat přímo z *1.14.x* na *1.16.x.* Chcete-li upgradovat z *1.14.x* na *1.16.x*, nejprve upgradujte z *1.14.x* na *1.15.x*a proveďte další upgrade z *1.15.x* na *1.16.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.15.5
```

Následující kondenzovaný příklad výstupu ukazuje *kubernetesVersion* nyní zprávy *1.15.5*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.15.5",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Ověření upgradu

Následujícím způsobem ověřte úspěšné provedení upgradu pomocí příkazu [az aks show][]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující ukázkový výstup ukazuje, že cluster AKS spouští *KubernetesVersion 1.15.5*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.15.5               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Odstranění clusteru

Vzhledem k tomu, že tento kurz je poslední částí řady, můžete odstranit cluster AKS. Jelikož uzly prostředí Kubernetes běží na virtuálních počítačích v Azure, účtují se za ně poplatky, i když cluster nevyužíváte. Pomocí příkazu [az group delete][az-group-delete] odeberete skupinu prostředků, službu kontejneru a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste upgradovali Kubernetes v clusteru AKS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

Další informace o službě AKS najdete na následujícím odkazu.

> [!div class="nextstepaction"]
> [Přehled služby AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
