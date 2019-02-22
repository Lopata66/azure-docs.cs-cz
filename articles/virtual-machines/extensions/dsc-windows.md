---
title: Desired State Configuration obslužná rutina rozšíření Azure | Dokumentace Microsoftu
description: Nahrát a použít konfigurace DSC Powershellu na Virtuálním počítači Azure pomocí rozšíření DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 26b083069380d7bf107cd3be54cb2e4786789e11
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593859"
---
# <a name="powershell-dsc-extension"></a>Rozšíření PowerShell DSC

## <a name="overview"></a>Přehled

Rozšíření PowerShell DSC pro Windows je publikována a podporované společností Microsoft. Rozšíření nahraje a aplikuje konfiguraci DSC Powershellu na Virtuálním počítači Azure. Rozšíření DSC volá do prostředí PowerShell DSC pro vydává přijaté konfigurace DSC na virtuálním počítači. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření virtuálního počítače DSC pro Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC podporuje následující operační systém

Windows Server. 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, klient Windows 7/8.1/10

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSC pro Windows vyžaduje, aby cílového virtuálního počítače moci komunikovat s Azure a umístění konfiguračního balíčku (souboru .zip), pokud je uložen v umístění mimo Azure. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro nastavení část rozšíření DSC v šabloně Azure Resource Manageru. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| vydavatele | Microsoft.Powershell.DSC | řetězec |
| type | DSC | řetězec |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Nastavení hodnoty vlastností

| Název | Typ dat | Popis
| ---- | ---- | ---- |
| settings.wmfVersion | řetězec | Určuje verzi Windows Management Framework, který musí být nainstalován na váš virtuální počítač. Nastavení této vlastnosti 'nejnovější' nainstaluje nejaktuálnější verzi WMF. Pouze aktuální možné hodnoty této vlastnosti jsou "4.0", '5.0' a 'nejnovější'. Tyto možné hodnoty jsou v souladu s aktualizací. Výchozí hodnota je 'nejnovější'. |
| settings.configuration.url | řetězec | Určuje adresu URL umístění, ze kterého chcete stáhnout konfigurační soubor zip DSC. Pokud zadaná adresa URL vyžaduje SAS token pro přístup, musíte nastavit vlastnost protectedSettings.configurationUrlSasToken hodnotu váš token SAS. Tato vlastnost je vyžadována, pokud jsou definovány settings.configuration.script a/nebo settings.configuration.function.
| settings.configuration.script | řetězec | Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce stažený z adresy URL určený vlastností configuration.url souboru zip. Tato vlastnost je vyžadována, pokud jsou definovány settings.configuration.url a/nebo settings.configuration.script.
| settings.configuration.function | řetězec | Určuje název konfigurace DSC. Konfigurace s názvem musí být součástí skriptu určené configuration.script. Tato vlastnost je vyžadována, pokud jsou definovány settings.configuration.url a/nebo settings.configuration.function.
| settings.configurationArguments | Kolekce | Definuje všechny parametry, které chcete předat do vaší konfigurace DSC. Tato vlastnost se šifrovat nebude.
| settings.configurationData.url | řetězec | Určuje adresu URL z nichž lze stáhnout soubor konfiguračních dat (.pds1) použít jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje SAS token pro přístup, musíte nastavit vlastnost protectedSettings.configurationDataUrlSasToken hodnotu váš token SAS.
| settings.privacy.dataEnabled | řetězec | Povolí nebo zakáže shromažďování telemetrie. Pouze možné hodnoty této vlastnosti jsou "Zapnout", "Zakázat", ", nebo $null. Opuštění tato vlastnost prázdná nebo mít hodnotu null bude povolit telemetrii
| settings.advancedOptions.forcePullAndApply | Bool | Povolí rozšíření DSC k aktualizaci a o přijetí změn při aktualizaci režimu vydává konfigurace DSC.
| settings.advancedOptions.downloadMappings | Kolekce | Definuje alternativní umístění pro stažení závislosti, jako jsou WMF a .NET

### <a name="protected-settings-property-values"></a>Chráněné hodnoty nastavení vlastností

| Název | Typ dat | Popis
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | řetězec | Definuje všechny parametry, které chcete předat do vaší konfigurace DSC. Tato vlastnost bude šifrovat. |
| protectedSettings.configurationUrlSasToken | řetězec | Určuje token SAS k adrese URL definované configuration.url přístup. Tato vlastnost bude šifrovat. |
| protectedSettings.configurationDataUrlSasToken | řetězec | Určuje token SAS k adrese URL definované configurationData.url přístup. Tato vlastnost bude šifrovat. |


## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru.
Šablony jsou ideální při nasazování jedné nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení.
Ukázka šablony Resource Manageru, která zahrnuje rozšíření DSC pro Windows můžete najít na [Galerie Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Balíček rozšíření dojde ke stažení a nasazení do tohoto umístění ve virtuálním počítači Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Soubor stavu rozšíření obsahuje dílčí stav a úspěšných/neúspěšných stavové kódy spolu s podrobné informace o chybě a popis pro každé rozšíření spustit.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Rozšíření výstupní protokoly jsou zaznamenány na následující adresář:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich význam

| Kód chyby | Význam | Je to možné akce |
| :---: | --- | --- |
| 1000 | Obecná chyba | Zprávy pro tuto chybu je poskytována určité výjimky v protokolech rozšíření |
| 52 | Chyba instalace rozšíření | Zprávy pro tuto chybu pochází od konkrétní výjimky |
| 1002 | Chyba instalace WMF | Chyba při instalaci WMF. |
| 1004 | Neplatný Zip balíčku | Neplatný archivu zip Chyba při rozbalování souboru zip |
| 1100 | Chyba argumentu | Označuje potíže s vstup uživatele. Zpráva pro chybu pochází od konkrétní výjimky|


### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
