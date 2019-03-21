---
title: Odstranění clusteru Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak odstranit cluster Service Fabric hostovaný v Azure a všechny jeho prostředky. Můžete odstranit skupinu prostředků obsahující cluster nebo selektivně odstranit jednotlivé prostředky.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 62372b4ce4032e019219b5be8c5a49ea135782c1
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223270"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Kurz: Odebrání clusteru Service Fabric běžící v Azure

V tomto kurzu je součástí pět série a ukazuje, jak odstranit cluster Service Fabric běžící v Azure. Pokud chcete cluster Service Fabric úplně odstranit, musíte odstranit také prostředky, které cluster používá. Máte dvě možnosti: buď odstranit skupinu prostředků, ve které se cluster nachází (čímž odstraníte tento prostředek clusteru a všechny další prostředky v této skupině), nebo jednotlivě odstranit daný prostředek clusteru a jeho přidružené prostředky (ale ne jiné prostředky v této skupině).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Odstranění skupiny prostředků se všemi jejími prostředky
> * Selektivní odstranění prostředků ze skupiny prostředků

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure pomocí šablony
> * [Monitorování clusteru](service-fabric-tutorial-monitor-cluster.md)
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * Odstranění clusteru

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte si [modul Azure PowerShell verze 4.1 nebo novější ](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) nebo [Azure CLI](/cli/azure/install-azure-cli).
* Vytvoření zabezpečeného [clusteru Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) v Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Odstranění skupiny prostředků obsahující cluster Service Fabric
Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, pomocí kterého chcete odebrat cluster.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](https://portal.azure.com). Odstraňte skupinu prostředků a všechny prostředky clusteru pomocí rutiny [Remove-AzureRMResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) nebo příkazu [az group delete](/cli/azure/group?view=azure-cli-latest).

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
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
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzureRmResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

U každého prostředku, který chcete odstranit, spusťte následující skript:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Pokud chcete prostředek clusteru odstranit, spusťte následující skript:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
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
* Zjistěte, jak [opravovat operační systém Windows](service-fabric-patch-orchestration-application.md) uzlů clusteru.
* Zjistěte, jak agregovat a shromažďování událostí [clustery Windows](service-fabric-diagnostics-event-aggregation-wad.md) a [nastavení Log Analytics](service-fabric-diagnostics-oms-setup.md) monitorovat události clusteru.
