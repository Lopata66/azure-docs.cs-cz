---
title: Ukázky dotazů pro začátečníky
description: Pomocí Azure Resource graphu můžete spouštět některé úvodní dotazy, včetně počítání prostředků, řazení prostředků nebo konkrétní značky.
ms.date: 04/27/2020
ms.topic: sample
ms.openlocfilehash: 4e098f9b1767b16969a7ccc9bed3930fa86c493a
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84147983"
---
# <a name="starter-resource-graph-query-samples"></a>Ukázky dotazů na graf počátečních prostředků

Prvním krokem k porozumění dotazům s Azure Resource Graph jsou základní znalosti [dotazovacího jazyka](../concepts/query-language.md). Pokud jste ještě neznali [KQL (Kusto Query Language)](/azure/kusto/query/index), doporučujeme si projít si [kurz pro KQL](/azure/kusto/query/tutorial) , který vám pomůže pochopit, jak vytvářet požadavky na prostředky, které hledáte.

Projdeme následující úvodní dotazy:

> [!div class="checklist"]
> - [Počet prostředků Azure](#count-resources)
> - [Počet prostředků trezoru klíčů](#count-keyvaults)
> - [Vypsat prostředky seřazené podle názvu](#list-resources)
> - [Zobrazit všechny virtuální počítače seřazené podle názvu v sestupném pořadí](#show-vms)
> - [Zobrazit prvních pět virtuálních počítačů podle názvu a jejich typu operačního systému](#show-sorted)
> - [Počet virtuálních počítačů podle typu operačního systému](#count-os)
> - [Zobrazit prostředky, které obsahují úložiště](#show-storage)
> - [Seznam všech veřejných IP adres](#list-publicip)
> - [Počet prostředků, které mají IP adresy nakonfigurované pomocí předplatného](#count-resources-by-ip)
> - [Vypíše prostředky s určitou hodnotou značky.](#list-tag)
> - [Vypíše všechny účty úložiště s určitou hodnotou značky.](#list-specific-tag)
> - [Zobrazit aliasy pro prostředek virtuálního počítače](#show-aliases)
> - [Zobrazit odlišné hodnoty pro konkrétní alias](#distinct-alias-values)
> - [Zobrazit nepřidružené skupiny zabezpečení sítě](#unassociated-nsgs)
> - [Získání souhrnu úspory nákladů z Azure Advisor](#advisor-savings)

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free), ještě než začnete.

## <a name="language-support"></a>Podpora jazyků

Azure PowerShell (prostřednictvím modulu) a Azure CLI (prostřednictvím rozšíření) podporují Azure Resource Graph. Před spuštěním jakéhokoli z následujících dotazů zkontrolujte, že je vaše prostředí připravené. Zobrazte [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) a [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) pro pokyny k instalaci a ověření vašeho výběru prostředí.

## <a name="count-azure-resources"></a><a name="count-resources"></a>Počet prostředků Azure

Tento dotaz vrátí počet prostředků Azure, které existují v předplatných, ke kterým máte přístup. Tento dotaz je také vhodný k ověření, že vaše vybrané prostředí má nainstalované a funkční odpovídající komponenty služby Azure Resource Graph.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults"></a>Počet prostředků trezoru klíčů

Tento dotaz `count` místo toho používá `summarize` k výpočtu počtu vrácených záznamů. V počtu jsou zahrnuté jenom trezory klíčů.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources"></a>Výpis prostředků seřazených podle názvu

Tento dotaz vrátí jakýkoli typ prostředku, ale pouze vlastnosti **name** (Název), **type** (Typ) a **location** (Umístění). Pomocí klauzule `order by` seřadí vlastnosti podle vlastnosti **name** (Název) ve vzestupném pořadí (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"></a>Zobrazení všech virtuálních počítačů, které jsou seřazené podle názvu v sestupném pořadí

Když chceme vypsat pouze virtuální počítače (typ `Microsoft.Compute/virtualMachines`), můžeme ve výsledcích porovnat shodu vlastnosti **type** (Typ). Podobně jako v předchozím dotazu musí být změny `desc``order by` být řazeny sestupně. `=~` ve shodě typu říká Azure Resource Graphu aby nerozlišoval malá a velká písmena.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"></a>Zobrazení prvních pěti virtuálních počítačů podle názvu a jejich typu operačního systému

Tento dotaz bude používat `top` pouze k načtení pěti odpovídajících záznamů, které jsou řazeny podle názvu. Typ prostředku Azure je `Microsoft.Compute/virtualMachines`. `project` říká Azure Resource Graph, které vlastnosti použít.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os"></a>Počet virtuálních počítačů podle typu operačního systému

Vycházíme z předchozího dotazu a stále omezujeme prostředky Azure na typ `Microsoft.Compute/virtualMachines`, ale už neomezujeme počet vrácených záznamů.
Místo toho jsme použili `summarize` a `count()` k definování, jak seskupit a agregovat hodnoty podle vlastností, což je v tomto příkladu `properties.storageProfile.osDisk.osType`. Příklad toho, jak tento řetězec vypadá v úplném objektu, najdete v části [zjišťování prostředků – objevování virtuálních počítačů](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

Jiný způsob, jak napsat stejný dotaz je vlastnost `extend` a dočasné pojmenování pro použití v rámci dotazu, v tomto případě **os**. **OS** je potom využit `summarize` a `count()` stejně jako v předchozím příkladu.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

> [!NOTE]
> Uvědomte si, že zatímco `=~` umožňuje nerozlišovat malá a velká písmena, použití vlastností (jako například **properties.storageProfile.osDisk.osType**) v dotazu vyžaduje rozlišování malých a velkých písmen. Pokud je vlastnost nesprávného případu, je vrácena hodnota null nebo nesprávná hodnota a seskupení nebo sumarizace by byly nesprávné.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage"></a>Zobrazení prostředků, které obsahují úložiště

Místo explicitního definování typu k porovnání, tento dotaz z příkladu najde jakékoli prostředky Azure, které `contains` slovo **úložiště**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip"></a>Seznam všech veřejných IP adres

Podobně jako v předchozím dotazu se vyhledají všechny záznamy, jejichž typ obsahuje slovo **publicIPAddresses**.
Tento dotaz rozbalí tento model tak, aby obsahoval jenom výsledky, ve kterých **Vlastnosti. IPAddress** 
 `isnotempty` vrátí jenom **Vlastnosti. IPAddress**, a `limit` výsledky podle horní části.
100. V závislosti na zvoleném prostředí možná budete muset odebrat uvozovky.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"></a>Počet prostředků, které mají IP adresy nakonfigurované podle předplatného

Pomocí předchozího ilustračního dotazu a přidáním `summarize` a `count()`, získáme seznam podle předplatného prostředků s nakonfigurovanými IP adresami.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag"></a>Seznam prostředků s konkrétní hodnotou značky

Rozsah výsledků můžeme omezit podle vlastností jiných než typ prostředku Azure, jako je například značka. V tomto příkladu vyfiltrujeme prostředky Azure s názvem značky **Environment** (Prostředí) s hodnotou **Internal** (Interní).

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

Pokud chcete vrátit také značky prostředku a jejich hodnoty, přidejte ke klíčovému slovu `project` vlastnost **tags** (Značky).

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"></a>Seznam všech účtů úložiště s konkrétní hodnotou značky

Zkombinujte funkci filtrování z předchozího příkladu a vyfiltrujte typ prostředku Azure podle vlastnosti **type** (Typ). Tento dotaz naše hledání omezuje také na konkrétní typy prostředků Azure s konkrétním názvem a hodnotou značky.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

> [!NOTE]
> Tento příklad používá `==` pro shodu místo podmínky `=~`. `==` je shoda rozlišující velikost písmen.

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"></a>Zobrazit aliasy pro prostředek virtuálního počítače

[Azure Policy aliasy](../../policy/concepts/definition-structure.md#aliases) používají Azure Policy ke správě dodržování předpisů prostředků. Graf prostředků Azure může vracet _aliasy_ typu prostředku. Tyto hodnoty jsou užitečné při porovnávání aktuální hodnoty aliasů při vytváření vlastní definice zásad. Pole _aliasy_ není ve výchozím nastavení k dispozici ve výsledcích dotazu. Slouží `project aliases` k explicitnímu přidání do výsledků.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"></a>Zobrazit odlišné hodnoty pro konkrétní alias

Zobrazení hodnoty aliasů u jednoho prostředku je užitečné, ale nezobrazuje skutečnou hodnotu použití Azure Resource graphu k dotazování napříč předplatnými. Tento příklad vyhledá všechny hodnoty konkrétního aliasu a vrátí jedinečné hodnoty.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs"></a>Zobrazit nepřidružené skupiny zabezpečení sítě

Tento dotaz vrátí skupiny zabezpečení sítě (skupin zabezpečení sítě), které nejsou přidružené k síťovému rozhraní nebo podsíti.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings"></a>Získání souhrnu úspory nákladů z Azure Advisor

Tento dotaz shrnuje úspory nákladů na jednotlivé [Azure Advisor](../../../advisor/advisor-overview.md) doporučení.

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Ikona Průzkumníka grafu prostředků](../media/resource-graph-small.png) Vyzkoušejte tento dotaz v Průzkumníku Azure Resource graphu:

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%20%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%20%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%20%7C%20extend%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%20%7C%20summarize%20dcount%28resources%29%2C%20bin%28sum%28savings%29%2C%200.01%29%20by%20solution%2C%20currency%20%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%20%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.com](../../media/new-window.png)
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%20%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%20%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%20%7C%20extend%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%20%7C%20summarize%20dcount%28resources%29%2C%20bin%28sum%28savings%29%2C%200.01%29%20by%20solution%2C%20currency%20%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%20%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.us</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.us](../../media/new-window.png)
- Portál Azure Čína: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%20%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%20%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%20%7C%20extend%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%20%7C%20summarize%20dcount%28resources%29%2C%20bin%28sum%28savings%29%2C%200.01%29%20by%20solution%2C%20currency%20%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%20%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.cn</a> ![ ikona otevřít odkaz v novém okně Portal.Azure.cn](../../media/new-window.png)

---

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazovacím jazyce](../concepts/query-language.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](../concepts/explore-resources.md).
- Podívejte se na ukázky [pokročilých dotazů](advanced.md).