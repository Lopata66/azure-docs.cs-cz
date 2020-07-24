---
title: Upgrade imagí uzlu služby Azure Kubernetes Service (AKS)
description: Naučte se upgradovat image na uzlech clusteru AKS a fondech uzlů.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 13842512c7d983a70b32132b4a16be37ac0b2b54
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050714"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Preview – upgrady imagí uzlu služby Azure Kubernetes Service (AKS)

AKS podporuje upgrade imagí na uzlu, takže budete mít nejnovější aktualizace operačního systému a modulu runtime. AKS poskytuje za týden jednu novou image s nejnovějšími aktualizacemi, takže je vhodné pravidelně upgradovat image vašeho uzlu na nejnovější funkce, včetně aktualizací pro Linux nebo Windows. V tomto článku se dozvíte, jak upgradovat image uzlů clusteru AKS a jak aktualizovat image fondu uzlů bez upgradu verze Kubernetes.

Pokud vás zajímá informace o nejnovějších obrázcích poskytovaných službou AKS, přečtěte si [poznámky k verzi AKS](https://github.com/Azure/AKS/releases) , kde najdete další podrobnosti.

Informace o tom, jak upgradovat verzi Kubernetes pro váš cluster, najdete v tématu [upgrade clusteru AKS][upgrade-cluster].

## <a name="register-the-node-image-upgrade-preview-feature"></a>Registrace funkce aktualizace image pro upgrade uzlu

Chcete-li použít funkci upgradu uzlu image v období Preview, je nutné ji zaregistrovat.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

Dokončení registrace bude trvat několik minut. Pro ověření, zda je funkce zaregistrována, použijte následující příkaz:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

Během období Preview budete potřebovat rozšíření CLI *AKS-Preview* pro použití upgradu bitové kopie uzlu. Použijte příkaz [AZ Extension Add][az-extension-add] a potom vyhledejte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci `Microsoft.ContainerService` poskytovatele prostředků pomocí příkazu [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Upgradovat všechny uzly ve všech fondech uzlů

Upgrade bitové kopie uzlu se provádí pomocí nástroje `az aks upgrade` . Chcete-li upgradovat image uzlu, použijte následující příkaz:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Během upgradu ověřte stav imagí uzlů pomocí následujícího `kubectl` příkazu, abyste získali popisky a vyfiltroval informace o aktuálním obrázku uzlu:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po dokončení upgradu použijte `az aks show` k získání aktualizovaného podrobností o fondu uzlů. Ve vlastnosti se zobrazí aktuální obrázek uzlu `nodeImageVersion` .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Upgrade konkrétního fondu uzlů

Upgrade image ve fondu uzlů je podobný jako upgrade image v clusteru.

Pokud chcete aktualizovat image operačního systému fondu uzlů bez provedení upgradu clusteru Kubernetes, použijte `--node-image-only` možnost v následujícím příkladu:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Během upgradu ověřte stav imagí uzlů pomocí následujícího `kubectl` příkazu, abyste získali popisky a vyfiltroval informace o aktuálním obrázku uzlu:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Po dokončení upgradu použijte `az aks nodepool show` k získání aktualizovaného podrobností o fondu uzlů. Ve vlastnosti se zobrazí aktuální obrázek uzlu `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Upgrade imagí uzlů pomocí přepětí uzlů

Chcete-li urychlit proces upgradu image uzlu, můžete upgradovat image uzlů pomocí přizpůsobitelné hodnoty přepětí uzlu. Ve výchozím nastavení používá AKS ke konfiguraci upgradů jeden další uzel.

Pokud byste chtěli zvýšit rychlost upgradů, použijte `--max-surge` hodnotu ke konfiguraci počtu uzlů, které se mají použít pro upgrady, aby byly dokončeny rychleji. Další informace o kompromisech různých `--max-surge` nastavení najdete v tématu [přizpůsobení upgradu uzlu přepětí][max-surge].

Následující příkaz nastaví maximální hodnotu nárůstu pro provádění upgradu bitové kopie uzlu:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Během upgradu ověřte stav imagí uzlů pomocí následujícího `kubectl` příkazu, abyste získali popisky a vyfiltroval informace o aktuálním obrázku uzlu:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Použijte `az aks nodepool show` k získání aktualizovaných podrobností o fondu uzlů. Ve vlastnosti se zobrazí aktuální obrázek uzlu `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Další kroky

- Informace o nejnovějších imagích uzlů najdete v [poznámkách k vydání verze AKS](https://github.com/Azure/AKS/releases) .
- Přečtěte si, jak upgradovat verzi Kubernetes pomocí [upgradu clusteru AKS][upgrade-cluster].
- [Použití aktualizací zabezpečení a jádra pro uzly Linux ve službě Azure Kubernetes Service (AKS)][security-update]
- Přečtěte si další informace o více fondech uzlů a o tom, jak upgradovat fondy uzlů pomocí [vytváření a správy fondů více uzlů][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
