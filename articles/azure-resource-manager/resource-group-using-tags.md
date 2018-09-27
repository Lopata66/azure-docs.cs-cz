---
title: Značky prostředků Azure pro logické uspořádání | Dokumentace Microsoftu
description: Ukazuje způsob použití značek k uspořádání prostředků Azure pro fakturaci a správu.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: tomfitz
ms.openlocfilehash: 82a48c902b72b5aca546c45032a13e831533f9ee
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393464"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Používání značek k uspořádání prostředků Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Použití značek k prostředkům, musí mít uživatel přístup pro zápis do tohoto typu prostředku. Chcete-li použít značky ke všem typům prostředků, použijte [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) role. Použít značky pro prostředek pouze jeden typ, použijte roli přispěvatele pro tento prostředek. Například pokud chcete použít značky na virtuální počítače, použijte [Přispěvatel virtuálních počítačů](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

Příklady v tomto článku vyžadují prostředí Azure PowerShell verze 6.0 nebo novější. Pokud nemáte verzi 6.0 nebo novější, [aktualizovat verzi](/powershell/azure/install-azurerm-ps).

Pokud chcete zobrazit existující značky pro *skupinu prostředků*, použijte:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Výstup tohoto skriptu bude v následujícím formátu:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Pokud chcete zobrazit existující značky pro *prostředek s konkrétním ID prostředku*, použijte:

```powershell
(Get-AzureRmResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Nebo, pokud chcete zobrazit existující značky pro *prostředek se zadaným názvem a skupinou prostředků*, použijte:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Pokud chcete získat *skupiny prostředků s konkrétní značkou*, použijte:

```powershell
(Get-AzureRmResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Pokud chcete získat *prostředky s konkrétní značkou*, použijte:

```powershell
(Get-AzureRmResource -Tag @{ Dept="Finance"}).Name
```

Chcete-li získat *prostředky, které mají název konkrétní značku*, použijte:

```powershell
(Get-AzureRmResource -TagName Dept).Name
```

Pokaždé, když použijete značky na prostředek nebo skupinu prostředků, přepíšete pro daný prostředek nebo skupinu prostředků existující značky. Proto je nutné použít jiný přístup na základě toho, jestli prostředek nebo skupina prostředků má existující značky.

Pokud chcete přidat značky ke *skupině prostředků bez existujících značek*, použijte:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Pokud chcete přidat značky ke *skupině prostředků s existujícími značkami*, načtěte existující značky, přidejte novou značku a znovu tyto značky použijte:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Pokud chcete přidat značky k *prostředku bez existujících značek*, použijte:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Pokud chcete přidat značky k *prostředku s existujícími značkami*, použijte:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved") 
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *nezachovat existující značky u prostředků*, použijte tento skript:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *zachovat existující značky u prostředků, které nejsou duplikáty*, použijte tento skript:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Pokud chcete odebrat všechny značky, předejte prázdnou zatřiďovací tabulku:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit existující značky pro *skupinu prostředků*, použijte:

```azurecli
az group show -n examplegroup --query tags
```

Výstup tohoto skriptu bude v následujícím formátu:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Nebo, pokud chcete zobrazit existující značky pro *prostředek, který má zadaný název, typ a prostředek skupiny*, použijte:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Když ve smyčce přes kolekce prostředků, můžete chtít zobrazit ID prostředků podle prostředku. Kompletní příklad je uveden dále v tomto článku. Pokud chcete zobrazit existující značky pro *prostředek s konkrétním ID prostředku*, použijte:

```azurecli
az resource show --id <resource-id> --query tags
```

Chcete-li získat skupiny prostředků, které s konkrétní značkou, použijte `az group list`:

```azurecli
az group list --tag Dept=IT
```

Chcete-li získat všechny potřebné prostředky, které mají určitou značkou a hodnotou, použijte `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Pokaždé, když použijete značky na prostředek nebo skupinu prostředků, přepíšete pro daný prostředek nebo skupinu prostředků existující značky. Proto je nutné použít jiný přístup na základě toho, jestli prostředek nebo skupina prostředků má existující značky.

Pokud chcete přidat značky ke *skupině prostředků bez existujících značek*, použijte:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Pokud chcete přidat značky k *prostředku bez existujících značek*, použijte:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Chcete-li přidat prostředek, který již má značky, načtěte existující značky, přeformátujte tuto hodnotu a znovu použít stávající i nové značky: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Pokud chcete použít všechny značky ze skupiny prostředků na prostředky a *nezachovat existující značky u prostředků*, použijte tento skript:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Chcete-li použít všechny značky ze skupiny prostředků na prostředky a *zachovat existující značky u prostředků*, pomocí následujícího skriptu:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Šablony

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Webu Azure portal a PowerShell používají [REST API Resource Manageru](https://docs.microsoft.com/rest/api/resources/) na pozadí. Pokud je potřeba integrovat označování do jiného prostředí, můžete získat značky pomocí **získat** na ID prostředku a aktualizaci sady s použitím značky **oprava** volání.

## <a name="tags-and-billing"></a>Značky a fakturace

Značky můžete použít k seskupení fakturačních údajů. Například pokud se spuštění několika virtuálních počítačů pro jiné organizace, použijte nákladové středisko značek k použití skupiny. Značky lze použít také ke kategorizaci náklady podle prostředí modulu runtime, jako je například fakturovaného využití pro virtuální počítače spuštěné v provozním prostředí.

Můžete načíst informace o značkách prostřednictvím [využití prostředků Azure a RateCard API](../billing/billing-usage-rate-card-overview.md) nebo použití souboru hodnot oddělených čárkami (CSV). Stáhněte si soubor využití z [portál účtů Azure](https://account.windowsazure.com/) nebo [portál EA](https://ea.azure.com). Další informace o programový přístup k fakturačním údajům najdete v tématu [získání přehledů o spotřebě prostředků Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Operace rozhraní REST API najdete v části [Azure Billing Reference k REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Když si stáhnete použití sdíleného svazku clusteru pro služby, které podporují značky s využitím fakturace, značky se zobrazí v **značky** sloupce. Další informace najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure](../billing/billing-understand-your-bill.md).

![Zobrazit značky ve fakturaci](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Další postup

* Pomocí vlastních zásad, můžete použít omezení a pravidla týkající se vašeho předplatného. Zásady, které definujete může být nutné, aby všechny prostředky hodnotu pro konkrétní značku. Další informace najdete v tématu [co je Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Úvod do při nasazování prostředků pomocí Azure Powershellu najdete v tématu [pomocí Azure Powershellu s Azure Resource Managerem](powershell-azure-resource-manager.md).
* Úvod do při nasazování prostředků pomocí Azure CLI najdete v tématu [pomocí Azure CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru](xplat-cli-azure-resource-manager.md).
* Úvodní informace o použití portálu najdete v tématu [pomocí webu Azure portal ke správě vašich prostředků Azure](resource-group-portal.md).  
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).
