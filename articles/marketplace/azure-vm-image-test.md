---
title: Otestování image virtuálního počítače Azure pro Azure Marketplace
description: Naučte se testovat a odesílat nabídku virtuálních počítačů Azure v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 10/15/2020
ms.openlocfilehash: a9698981b1a658664bfc14886628bbfd0a4a64d2
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976924"
---
# <a name="test-a-virtual-machine-image"></a>Otestování image virtuálního počítače

Tento článek popisuje, jak otestovat a odeslat image virtuálního počítače (VM) na komerčním tržišti, aby splňovala nejnovější Azure Marketplace požadavky na publikování.

Před odesláním nabídky virtuálního počítače dokončete tyto kroky:

- Nasazení virtuálního počítače Azure pomocí generalizované image; Přečtěte si téma [Vytvoření virtuálního počítače pomocí schválené základny](azure-vm-create-using-approved-base.md) nebo [Vytvoření virtuálního počítače pomocí vlastní image](azure-vm-create-using-own-image.md).
- Spusťte ověřování.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Nasazení virtuálního počítače Azure pomocí generalizované image

Tato část popisuje, jak nasadit zobecněnou image virtuálního pevného disku (VHD) pro vytvoření nového prostředku virtuálního počítače Azure. Pro tento proces budeme používat dodanou šablonu Azure Resource Manager a skript Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Příprava šablony Azure Resource Manager

Tato část popisuje, jak vytvořit a nasadit image virtuálního počítače (VM) zadaného uživatelem. To můžete provést tak, že zadáte image virtuálního pevného disku operačního systému a datového disku z virtuálního pevného disku nasazeného v Azure. Tyto kroky nasadí virtuální počítač pomocí zobecněného virtuálního pevného disku.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Nahrajte na účet Azure Storage svůj zobecněný virtuální pevný disk s operačním systémem a virtuální pevné disky s daty.
3. Na domovské stránce vyberte **vytvořit prostředek**, vyhledejte "nasazení šablony" a vyberte **vytvořit**.
4. **V editoru vyberte vytvořit vlastní šablonu**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Zobrazuje výběr šablony.":::

5. Do editoru vložte následující šablonu JSON a vyberte **Uložit**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
}
```

<br>

6. Zadejte hodnoty parametrů pro zobrazené stránky vlastností vlastního nasazení.

    | ResourceGroupName | Název existující skupiny prostředků Azure Obvykle používejte stejný RG jako Trezor klíčů. |
    | --- | --- |
    | TemplateFile | Úplná cesta k souboru VHDtoImage.jsv. |
    | userStorageAccountName | Název účtu úložiště |
    | dnsNameForPublicIP | Název DNS pro veřejnou IP adresu; musí být malými písmeny. |
    | subscriptionId | Identifikátor předplatného Azure. |
    | Umístění | Standardní geografické umístění skupiny prostředků v Azure. |
    | vmName | Název virtuálního počítače |
    | vhdUrl | Webová adresa virtuálního pevného disku. |
    | vmSize | Velikost instance virtuálního počítače |
    | publicIPAddressName | Název veřejné IP adresy. |
    | virtualNetworkName | Název virtuální sítě. |
    | nicName | Název karty síťového rozhraní pro virtuální síť. |
    | adminUserName | Uživatelské jméno účtu správce. |
    | adminPassword | Heslo správce. |


7. Po poskytnutí těchto hodnot vyberte **koupit**.

Azure spustí nasazení. Vytvoří nový virtuální počítač se zadaným nespravovaným virtuálním pevným diskem v zadané cestě k účtu úložiště. Průběh můžete sledovat v Azure Portal výběrem **Virtual Machines** na levé straně portálu. Po vytvoření virtuálního počítače se stav změní z počáteční na spuštěno.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Pro nasazení virtuálního počítače generace 2 použijte tuto šablonu:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Nasazení virtuálního počítače Azure pomocí PowerShellu

Zkopírujte a upravte následující skript, který poskytne hodnoty pro `$storageaccount` proměnné a `$vhdUrl` . Spusťte ho k vytvoření prostředku virtuálního počítače Azure ze stávajícího zobecněného virtuálního pevného disku.

```PowerShell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd
```

## <a name="run-validations"></a>Spustit ověřování

Existují dva způsoby, jak spustit ověřování na nasazené imagi.

### <a name="use-certification-test-tool-for-azure-certified"></a>Použití nástroje pro testování certifikace pro certifikaci v Azure

#### <a name="download-and-run-the-certification-test-tool"></a>Stažení a spuštění nástroje certifikace test

Nástroj certifikace pro certifikaci pro Azure Certified běží na místním počítači s Windows, ale testuje virtuální počítač se systémem Windows nebo Linux na platformě Azure. Potvrzuje, že vaše uživatelská image virtuálního počítače se dá používat s Microsoft Azure a že se splnily pokyny a požadavky týkající se přípravy vašeho virtuálního pevného disku.

1. Stáhněte a nainstalujte si nejnovější [Nástroj pro testování certifikace pro certifikaci Azure](https://www.microsoft.com/download/details.aspx?id=44299).
2. Otevřete nástroj certifikace a pak vyberte **Spustit nový test**.
3. Na obrazovce informace o testu zadejte **název testu** testovacího běhu.
4. Vyberte platformu pro váš virtuální počítač, buď **Windows Server** nebo **Linux**. Volba vaší platformy má vliv na zbývající možnosti.
5. Pokud váš virtuální počítač používá tuto databázovou službu, zaškrtněte políčko **test pro Azure SQL Database** .

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Připojení certifikačního nástroje k imagi virtuálního počítače

1. Vyberte režim ověřování SSH: ověřování hesla nebo ověření souboru klíče.
2. Pokud používáte ověřování na základě hesla, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno** a **heslo**. Můžete také změnit výchozí číslo portu SSH.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Zobrazuje výběr informací o testu virtuálního počítače.":::

3. Pokud používáte ověřování na základě klíčového souboru, zadejte hodnoty pro název DNS virtuálního počítače, uživatelské jméno a umístění privátního klíče. Můžete také zahrnout heslo nebo změnit výchozí číslo portu SSH.
4. Zadejte plně kvalifikovaný název DNS virtuálního počítače (například MyVMName.Cloudapp.net).
5. Zadejte **uživatelské jméno** a **heslo**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Zobrazuje výběr uživatelského jména a hesla virtuálního počítače.":::

6. Vyberte **Další**.

#### <a name="run-a-certification-test"></a>Spustit test certifikace

Po zadání hodnot parametrů pro vaši image virtuálního počítače v nástroji certifikace vyberte test připojení a vytvořte platné připojení k vašemu VIRTUÁLNÍmu počítači. Po ověření připojení vyberte **Další** a spusťte test. Po dokončení testu se zobrazí výsledky v tabulce. U každého testu se ve sloupci Stav zobrazuje (úspěch/chyba/upozornění). Pokud některý z testů selže, váš obrázek není certifikovaný. V takovém případě zkontrolujte zprávy o požadavcích a chybách, proveďte navrhované změny a spusťte test znovu.

Po dokončení automatizovaného testu zadejte další informace o imagi virtuálního počítače na dvou kartách obrazovky dotazníku, obecné vyhodnocení a přizpůsobení jádra a pak vyberte Další.

Poslední obrazovka vám umožní zadat další informace, například informace o přístupu SSH pro image virtuálního počítače se systémem Linux, a vysvětlení všech neúspěšných posouzení, pokud hledáte výjimky.

Nakonec vyberte možnost generovat sestavu pro stažení výsledků testů a souborů protokolu pro provedené testovací případy spolu s vašimi odpověďmi na dotazník. 
> [!Note]
> Někteří vydavatelé mají scénáře, kdy je potřeba, aby virtuální počítače byly uzamčené, protože mají na virtuálním počítači nainstalovaný software, jako jsou třeba brány firewall. V takovém případě si stáhněte [certifikovaný testovací nástroj](https://aka.ms/AzureCertificationTestTool) a odešlete zprávu na [podporu](https://aka.ms/marketplacepublishersupport)partnerského centra.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Použití prostředí PowerShell ke využívání rozhraní Self-Test API

### <a name="on-linux-os"></a>V operačním systému Linux

Volání rozhraní API v PowerShellu:

1. Použijte příkaz Invoke-WebRequest pro volání rozhraní API.
2. Metoda je post a typ obsahu je JSON, jak je znázorněno v následujícím příkladu kódu a snímku obrazovky.
3. Zadejte parametry těla ve formátu JSON.

Následující příklad ukazuje volání prostředí PowerShell do rozhraní API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Tady je příklad volání rozhraní API v PowerShellu:

[![Příklad obrazovky pro volání rozhraní API v prostředí PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Pomocí předchozího příkladu můžete načíst JSON a analyzovat ho a získat následující podrobnosti:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Tato ukázková obrazovka, která ukazuje `$res.Content` , zobrazuje podrobnosti o výsledcích testů ve formátu JSON:

[![Příklad obrazovky pro volání rozhraní API v prostředí PowerShell s podrobnostmi o výsledcích testu.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Tady je příklad výsledků testu JSON zobrazených v online prohlížeči JSON (například [Code Beautify](https://codebeautify.org/jsonviewer) nebo [Prohlížeč Json](https://jsonformatter.org/json-viewer)).

![Další výsledky testů v online prohlížeči JSON.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>V operačním systému Windows

Volání rozhraní API v PowerShellu:

1. Použijte příkaz Invoke-WebRequest pro volání rozhraní API.
2. Metoda je post a typ obsahu je JSON, jak je znázorněno v následujícím příkladu kódu a ukázce obrazovky.
3. Vytvořte parametry těla ve formátu JSON.

Tato ukázka kódu ukazuje volání prostředí PowerShell do rozhraní API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Tyto ukázkové obrazovky ukazují příklad volání rozhraní API v prostředí PowerShell:

**S klíčem SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Volání rozhraní API v PowerShellu s klíčem SSH.":::

**S heslem**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Volání rozhraní API v PowerShellu s heslem.":::

<br>Pomocí předchozího příkladu můžete načíst JSON a analyzovat ho a získat následující podrobnosti:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Tato obrazovka, která ukazuje `$res.Content` , zobrazuje podrobnosti o výsledcích testů ve formátu JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Podrobnosti o výsledcích testů ve formátu JSON.":::

<br>Tady je příklad výsledků testů zobrazených v online prohlížeči JSON (například [Code Beautify](https://codebeautify.org/jsonviewer) nebo [Prohlížeč Json](https://jsonformatter.org/json-viewer)).

![Výsledky testů v online prohlížeči JSON.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Použití funkce KUDRLINKOU ke využívání rozhraní Self-Test API v operačním systému Linux

V tomto příkladu bude k vytvoření volání POST API Azure Active Directory a Self-Host virtuálního počítače použit objekt kudrlinkou.

1. Vyžádejte si token Azure AD, který se ověří pro samoobslužné hostování virtuálního počítače.

   Ujistěte se, že se v požadavku na kudrlinkou nahradily správné hodnoty.

   ```
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```
   Tady je příklad odpovědi z požadavku:
   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Odeslat žádost o samočinný test virtuálního počítače

   Ujistěte se, že token a parametry nosiče jsou nahrazené správnými hodnotami.

   ```
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Příklad odpovědi z volání rozhraní API pro samočinný test VM
   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Další kroky

- [Získání identifikátoru URI sdíleného přístupového podpisu](azure-vm-get-sas-uri.md)
