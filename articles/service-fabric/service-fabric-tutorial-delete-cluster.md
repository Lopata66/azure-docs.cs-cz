---
title: Odstranění clusteru Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak odstranit cluster Service Fabric hostovaný v Azure a všechny jeho prostředky. Můžete odstranit skupinu prostředků obsahující cluster nebo selektivně odstranit jednotlivé prostředky.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 660b8cc8ec142c1c0710480020f103215359987f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385067"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Kurz: Odebrat Cluster Service Fabric se systémem v Azure

Tento kurz je 5. část série a ukazuje, jak odstranit Cluster Service Fabric běžící v Azure. Pokud chcete cluster Service Fabric úplně odstranit, musíte odstranit také prostředky, které cluster používá. Máte dvě možnosti: buď odstranit skupinu prostředků, ve které se cluster nachází (čímž odstraníte tento prostředek clusteru a všechny další prostředky v této skupině), nebo jednotlivě odstranit daný prostředek clusteru a jeho přidružené prostředky (ale ne jiné prostředky v této skupině).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * Odstranění clusteru


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure//install-Az-ps) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Odstranění skupiny prostředků obsahující cluster Service Fabric
Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, se kterým chcete cluster odebrat.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](https://portal.azure.com). Odstraňte skupinu prostředků a všechny prostředky clusteru pomocí rutiny [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) nebo příkazu [AZ Group Delete](/cli/azure/group?view=azure-cli-latest) .

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Selektivní odstranění prostředku clusteru a souvisejících prostředků
Pokud vaše skupina prostředků obsahuje jen prostředky přidružené ke clusteru Service Fabric, který chcete odstranit, pak je jednodušší odstranit celou skupinu. Pokud chcete prostředky ve skupině prostředků odstranit selektivně a zachovat prostředky, které ke clusteru přidružené nejsou, postupujte podle následujících kroků.

Vytvořte seznam prostředků ve skupině prostředků:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

U každého prostředku, který chcete odstranit, spusťte následující skript:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Pokud chcete prostředek clusteru odstranit, spusťte následující skript:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

Teď když jste kurz dokončili, zkuste toto:
* Zjistěte, jak zkontrolovat a spravovat cluster Service Fabric pomocí [Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).
* Přečtěte si, jak [opravit operační systém Windows](service-fabric-patch-orchestration-application.md) na uzlech clusteru.
* Naučte se agregovat a shromažďovat události pro [clustery Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [instalační Log Analytics](service-fabric-diagnostics-oms-setup.md) , abyste mohli monitorovat události clusteru.
