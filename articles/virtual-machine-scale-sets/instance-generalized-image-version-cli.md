---
title: Vytvoření sady škálování z generalizované image
description: Vytvořte sadu škálování pomocí generalizované image v galerii sdílených imagí.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 59e29be2aade993c8aeae64b4aa4918b36a26b26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82797133"
---
# <a name="create-a-scale-set-from-a-generalized-image"></a>Vytvoření sady škálování z generalizované image

Vytvořte sadu škálování z generalizované verze Image uložené v [galerii sdílených imagí](shared-image-galleries.md) pomocí Azure CLI. Pokud chcete vytvořit sadu škálování pomocí specializované verze image, přečtěte si téma [vytvoření instance sady škálování z specializované image](instance-specialized-image-version-cli.md).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.4.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

V tomto příkladu nahraďte názvy prostředků podle potřeby. 

V galerii můžete zobrazit definice obrázků pomocí [AZ SIG image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) , abyste viděli název a ID definic.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Vytvořte sadu škálování pomocí [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Pomocí ID definice image pro `--image` můžete vytvořit instance sady škálování z nejnovější verze image, která je k dispozici. Můžete také vytvořit instance sady škálování z konkrétní verze, a to zadáním ID verze image pro `--image` . Uvědomte si, že použití konkrétní verze image znamená, že automatizace může selhat, pokud není dostupná konkrétní verze image, protože se odstranila nebo odebrala z oblasti. Pro vytvoření nového virtuálního počítače doporučujeme použít ID definice image, pokud není potřeba konkrétní verze image.

V tomto příkladu vytváříme instance z nejnovější verze *myImageDefinition* image.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

## <a name="next-steps"></a>Další kroky
[Azure image Builder (Preview)](../virtual-machines/linux/image-builder-overview.md) může přispět k automatizaci vytváření verzí image, můžete ji dokonce použít k aktualizaci a [Vytvoření nové verze image z existující verze image](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Pomocí šablon můžete také vytvořit prostředek Galerie sdílených imagí. K dispozici je několik šablon rychlého startu Azure: 

- [Vytvoření služby Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Vytvoření definici image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Vytvoření verze image v Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Další informace o galeriích sdílených imagí najdete v [přehledu](shared-image-galleries.md). Pokud narazíte na problémy, přečtěte si téma [řešení potíží s galeriemi sdílených imagí](troubleshooting-shared-images.md).