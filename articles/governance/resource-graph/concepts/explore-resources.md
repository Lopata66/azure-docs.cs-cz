---
title: Prozkoumat prostředky Azure.
description: Zjistěte, jak pomocí dotazovacího jazyka prostředků grafu můžete zkoumat vaše prostředky a zjistit, jak jsou připojené.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0b4a75558f5e82b707ae5d012acef4d2c5c4b7a0
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62732977"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Zkoumání prostředků Azure pomocí služby Resource Graph

Azure Graph prostředků poskytuje schopnost zkoumat a zjišťovat prostředky Azure, rychle a ve velkém měřítku. Navržené pro rychlou odezvu, je skvělý způsob, jak získat informace o vašem prostředí a také o vlastnosti, které tvoří vašich prostředků Azure.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>Prozkoumejte virtual machines

Běžné prostředků v Azure je virtuální počítač. Jako typ prostředku virtuální počítače mají mnoho vlastností, které je možné zadávat dotazy. Každou vlastnost nabízí možnost pro filtrování nebo vyhledání přesně prostředek, který hledáte.

### <a name="virtual-machine-discovery"></a>Zjišťování virtuálních počítačů

Začněme s použitím jednoduchého dotazu k získání jednoho virtuálního počítače z našeho prostředí a podívejte se na vlastnosti vrátila.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Prostředí Azure PowerShell `Search-AzGraph` rutina vrátí **PSCustomObject** ve výchozím nastavení. Výstup vypadají stejně jako co bude vráceno z rozhraní příkazového řádku Azure, aby `ConvertTo-Json` rutina se používá. Výchozí hodnota pro **hloubky** je _2_. Nastavení na _100_ měli převést všechny vrácené úrovně.

Výsledky JSON jsou strukturovaná podobně jako v následujícím příkladu:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Vlastnosti Řekněte nám, další informace o prostředku virtuálního počítače, včetně SKU, operační systém, disky, značky, a skupinu prostředků a předplatného je členem.

### <a name="virtual-machines-by-location"></a>Virtuální počítače podle umístění

Pořízení, co jsme se dozvěděli o prostředků virtuálních počítačů, použijeme **umístění** vlastnosti count všech virtuálních počítačů podle umístění. Pokud chcete aktualizovat dotaz, vytvoříme odeberete limit a shrnout počet hodnot umístění.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Výsledky JSON jsou strukturovaná podobně jako v následujícím příkladu:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Teď vidíme kolik virtuálních počítačů v každé oblasti Azure.

### <a name="virtual-machines-by-sku"></a>Skladová jednotka potřebuje virtuální počítače

Po návratu do původní vlastnosti virtuálního počítače, zkuste najít všechny virtuální počítače, které mají velikost SKU **Standard_B2s**. Vrátí vyhledávání v kódu JSON, vidíme, že je uložený v **properties.hardwareprofile.vmsize**. Aktualizujeme dotaz, který najde všechny virtuální počítače, které odpovídají této velikosti a vrátit pouze název virtuálního počítače a oblast.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuální počítače připojené na premium managed disks

Pokud jsme chtěli přimět podrobnosti premium managed disks, které jsou připojené k těmto **Standard_B2s** virtuální počítače, můžeme rozšířit dotazu a poskytli nám ID prostředku tyto spravované disky.

```kusto
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Dalším způsobem, jak získat skladovou Položku by byly pomocí **aliasy** vlastnost **Microsoft.Compute/virtualMachines/sku.name**. Zobrazit [zobrazit aliasy](../samples/starter.md#show-aliases) a [zobrazovat odlišné alias hodnoty](../samples/starter.md#distinct-alias-values) příklady.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Výsledkem je seznam ID disků.

### <a name="managed-disk-discovery"></a>Zjišťování spravovaného disku

První záznam z předchozího dotazu, se podíváme vlastnosti, které existují na spravovaný disk, který se připojuje k první virtuální počítač. Aktualizované dotaz používá ID disku a změní typ.

Příklad výstupu z předchozího dotazu, například:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Před spuštěním dotazu, jak jsme věděli **typ** by měla nyní být **Microsoft.Compute/disks**?
Když se podíváte na úplné ID, zobrazí se vám **/providers/Microsoft.Compute/disks/** jako součást řetězce. Tento fragment řetězce poskytuje nápovědu, jaký typ pro hledání. Je to alternativní metoda může být odeberete limit podle typu a místo toho pouze hledat podle ID pole. Je jedinečný Identifikátor, by byla vrácena jenom jednomu záznamu a **typ** vlastnost na něm poskytuje tuto podrobností.

> [!NOTE]
> Pro tento příklad fungoval je potřeba nahradit pole ID výsledku ze svého vlastního prostředí.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Výsledky JSON jsou strukturovaná podobně jako v následujícím příkladu:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Prozkoumejte virtual machines k vyhledání veřejné IP adresy

Tato sada rozhraní příkazového řádku Azure dotazů, nejprve vyhledá a ukládá všechna síťová rozhraní (NIC) prostředkům připojeným k virtuálním počítačům. Poté používá k nalezení každý prostředek IP adresy, která je veřejná IP adresa seznam síťových adaptérů a tyto hodnoty. Navíc poskytuje seznam veřejných IP adres.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

Použití `nics.txt` soubor v dalším dotazu se získat podrobnosti zdroje související síťové rozhraní ve kterých je veřejné IP adresy připojit k síťové kartě

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Poslední, použijte seznam veřejných IP adres prostředků uložený v `ips.txt` od jejich získat skutečný veřejnou IP adresu a zobrazit.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>Další postup

- Další informace o [dotazovacím jazyku](query-language.md)
- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md)
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md)