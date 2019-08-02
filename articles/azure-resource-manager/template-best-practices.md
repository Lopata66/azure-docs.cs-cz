---
title: Osvědčené postupy pro šablony Azure Resource Manager
description: Popisuje doporučené přístupy k vytváření Azure Resource Manager šablon. Nabízí návrhy, aby se předešlo běžným problémům při používání šablon.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: cdec216187050a449f23f72474e0265acce14c5f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67867394"
---
# <a name="azure-resource-manager-template-best-practices"></a>Doporučené postupy pro šablonu Azure Resource Manager

Tento článek obsahuje doporučení k vytvoření šablony Správce prostředků. Tato doporučení vám pomůžou vyhnout se běžným problémům při použití šablony k nasazení řešení.

Doporučení, jak řídit vaše předplatná Azure, najdete v [tématu uživatelské rozhraní Azure Enterprise: Zásady správného řízení](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)předplatného.

Doporučení k vytváření šablon, které fungují ve všech cloudových prostředích Azure, najdete v tématu [vývoj šablon Azure Resource Manager pro konzistenci cloudu](templates-cloud-consistency.md).

## <a name="template-limits"></a>Omezení šablon

Omezte velikost šablony na 4 MB a každý soubor parametrů na 64 KB. Limit velikosti 4 MB se vztahuje na konečný stav šablony po rozbalení pomocí iterativních definic prostředků a hodnot proměnných a parametrů. 

Máte také omezení:

* parametry 256
* proměnné 256
* 800 prostředků (včetně počtu kopií)
* 64 výstupní hodnoty
* 24 576 znaků ve výrazu šablony

Můžete překročit několik omezení šablon pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazování prostředků Azure](resource-group-linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete zkombinovat několik hodnot do objektu. Další informace najdete v tématu [objekty jako parametry](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Resource group

Když nasadíte prostředky do skupiny prostředků, skupina prostředků uloží metadata o prostředcích. Metadata se ukládají do umístění skupiny prostředků.

Pokud je oblast skupiny prostředků dočasně nedostupná, nemůžete aktualizovat prostředky ve skupině prostředků, protože metadata nejsou k dispozici. Prostředky v jiných oblastech budou pořád fungovat podle očekávání, ale nemůžete je aktualizovat. Pokud chcete minimalizovat riziko, vyhledejte skupinu prostředků a prostředky ve stejné oblasti.

## <a name="parameters"></a>Parametry
Informace v této části mohou být užitečné při práci s [parametry](resource-group-authoring-templates.md#parameters).

### <a name="general-recommendations-for-parameters"></a>Obecná doporučení pro parametry

* Minimalizujte použití parametrů. Místo toho použijte proměnné nebo hodnoty literálů pro vlastnosti, které není nutné zadávat během nasazování.

* Pro názvy parametrů použijte ve stylu CamelCase Case.

* Použijte parametry pro nastavení, která se liší v závislosti na prostředí, jako je SKU, velikost nebo kapacita.

* Použijte parametry pro názvy prostředků, které chcete zadat pro snadnější identifikaci.

* Zadejte popis každého parametru v metadatech:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definujte výchozí hodnoty pro parametry, které nejsou citlivé. Zadáním výchozí hodnoty je snazší šablonu nasadit a uživatelé šablony uvidí příklad odpovídající hodnoty. Všechny výchozí hodnoty pro parametr musí být platné pro všechny uživatele ve výchozí konfiguraci nasazení. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Chcete-li zadat volitelný parametr, nepoužívejte jako výchozí hodnotu prázdný řetězec. Místo toho použijte literálovou hodnotu nebo výraz jazyka pro vytvoření hodnoty.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Nepoužívejte parametr pro verzi rozhraní API pro typ prostředku. Vlastnosti a hodnoty prostředků se mohou lišit podle čísla verze. Technologie IntelliSense v editoru kódu nemůže určit správné schéma, pokud je verze rozhraní API nastavena na parametr. Místo toho je třeba v šabloně pevně nakódovat verzi rozhraní API.

* Používejte `allowedValues` zřídka. Použijte ji pouze v případě, že je nutné zajistit, aby některé hodnoty nebyly zahrnuty do povolených možností. Pokud používáte moc široce, můžete zablokovat platná nasazení tím, že seznam nezůstane v aktuálním stavu. `allowedValues`

* Pokud název parametru ve vaší šabloně odpovídá parametru v příkazu PowerShell Deployment, Správce prostředků vyřeší tento konflikt názvů přidáním přípony **FromTemplate** do parametru Template. Například pokud zahrnete parametr s názvem **ResourceGroupName** v šabloně, je v konfliktu s parametrem **ResourceGroupName** v rutině [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Během nasazování budete vyzváni k zadání hodnoty pro **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Doporučení zabezpečení pro parametry

* Vždy používejte parametry pro uživatelská jména a hesla (nebo tajné klíče).

* Používá `securestring` se pro všechna hesla a tajné klíče. Pokud předáte citlivé údaje v objektu JSON, použijte `secureObject` typ. Parametry šablony se zabezpečeným řetězcem nebo typy zabezpečených objektů nelze přečíst po nasazení prostředků. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Nezadávejte výchozí hodnoty pro uživatelská jména, hesla ani žádná hodnota, která vyžaduje `secureString` typ.

* Neposkytněte výchozí hodnoty pro vlastnosti, které zvyšují prostor pro útoky v aplikaci.

### <a name="location-recommendations-for-parameters"></a>Doporučení pro umístění parametrů

* Pomocí parametru zadejte umístění pro prostředky a nastavte výchozí hodnotu na `resourceGroup().location`. Zadání parametru Location umožňuje uživatelům šablony určit umístění, ke kterému mají oprávnění k nasazení.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Nezadávejte `allowedValues` pro parametr Location. Zadaná umístění nemusí být k dispozici ve všech cloudech.

* Pro prostředky, které jsou pravděpodobně ve stejném umístění, použijte hodnotu parametru Location. Tento přístup minimalizuje počet vyzvání uživatelů k zadání informací o poloze.

* U prostředků, které nejsou k dispozici ve všech umístěních, použijte samostatný parametr nebo zadejte hodnotu umístění literálu.

## <a name="variables"></a>Proměnné

Následující informace můžou být užitečné při práci s proměnnými [](resource-group-authoring-templates.md#variables):

* Použijte proměnné pro hodnoty, které je třeba použít více než jednou v šabloně. Pokud se hodnota používá jenom jednou, pevně zakódovaná hodnota usnadňuje čtení šablony.

* Použijte proměnné pro hodnoty, které vytváříte ze složitých uspořádání funkcí šablon. Pokud se složitý výraz zobrazuje pouze v proměnných, šablona je snazší číst.

* Nepoužívejte proměnné pro `apiVersion` prostředek. Verze rozhraní API Určuje schéma prostředku. Často nemůžete změnit verzi, aniž byste museli měnit vlastnosti prostředku.

* V části **proměnné** v šabloně nemůžete použít [odkazovou](resource-group-template-functions-resource.md#reference) funkci. **Odkazovaná** funkce odvozuje svou hodnotu z běhového stavu prostředku. Proměnné jsou však při počáteční analýze šablony vyřešeny. Sestavte hodnoty, které vyžadují odkazovou funkci přímo v **části** Resources nebo Outputs šablony.

* Přidejte proměnné pro názvy prostředků, které musí být jedinečné.

* Pomocí [smyčky kopírování v proměnných](resource-group-create-multiple.md#variable-iteration) vytvořte opakovaný vzor objektů JSON.

* Odeberte nepoužité proměnné.

## <a name="resource-dependencies"></a>Závislosti prostředků

Při rozhodování, jaké [závislosti](resource-group-define-dependencies.md) se mají nastavit, postupujte podle následujících pokynů:

* Použijte **odkazovou** funkci a předejte název prostředku k nastavení implicitní závislosti mezi prostředky, které potřebují sdílet vlastnost. Nepřidejte explicitní `dependsOn` element, pokud jste již definovali implicitní závislost. Tento přístup snižuje riziko zbytečných závislostí.

* Nastavte podřízený prostředek jako závislý na svém nadřazeném prostředku.

* Prostředky s [prvkem podmínky](resource-group-authoring-templates.md#condition) nastavenou na hodnotu false jsou automaticky odebrány z pořadí závislostí. Nastavte závislosti, jako by byl prostředek vždy nasazen.

* Závislosti se dají uspořádat bez explicitního nastavování. Například váš virtuální počítač závisí na virtuálním síťovém rozhraní a rozhraní virtuální sítě závisí na virtuální síti a veřejných IP adresách. Proto se virtuální počítač nasadí po všech třech prostředcích, ale virtuální počítač se explicitně nenastaví jako závislý na všech třech prostředcích. Tento přístup objasňuje pořadí závislostí a usnadňuje změnu šablony později.

* Pokud je možné určit hodnotu před nasazením, zkuste prostředek nasadit bez závislosti. Pokud například hodnota konfigurace potřebuje název jiného prostředku, možná nebudete potřebovat závislost. Tyto pokyny nefungují vždycky, protože některé prostředky ověřují existenci druhého prostředku. Pokud se zobrazí chyba, přidejte závislost.

## <a name="resources"></a>Zdroje a prostředky

Následující informace můžou být užitečné při práci s [prostředky](resource-group-authoring-templates.md#resources):

* Aby mohli jiní přispěvatelé pochopit účel prostředku, zadejte **Komentáře** pro každý prostředek v šabloně:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Pokud v šabloně používáte *veřejný koncový bod* (například veřejný koncový bod služby Azure Blob Storage), neprovádějte *pevný kód* oboru názvů. K dynamickému načtení oboru názvů použijte **odkazovou** funkci. Tento přístup můžete použít k nasazení šablony do různých prostředí veřejného oboru názvů, aniž byste museli ručně měnit koncový bod v šabloně. Nastavte verzi rozhraní API na stejnou verzi, kterou používáte pro účet úložiště v šabloně:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Pokud je účet úložiště nasazený ve stejné šabloně, kterou vytváříte, nemusíte při odkazování na prostředek zadávat obor názvů poskytovatele. Následující příklad ukazuje zjednodušenou syntaxi:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Pokud máte v šabloně jiné hodnoty, které jsou nakonfigurovány pro použití veřejného oboru názvů, změňte tyto hodnoty tak, aby odrážely stejnou **odkazovou** funkci. Můžete například nastavit vlastnost **storageUri** profilu diagnostiky virtuálního počítače:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Můžete také vytvořit odkaz na existující účet úložiště, který je v jiné skupině prostředků:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Přiřaďte veřejné IP adresy virtuálnímu počítači pouze v případě, že je aplikace vyžaduje. Pokud se chcete připojit k virtuálnímu počítači (VM) pro ladění nebo pro účely správy nebo správy, použijte pravidla příchozího překladu adres (NAT), bránu virtuální sítě nebo JumpBox.
   
     Další informace o připojení k virtuálním počítačům najdete v těchto tématech:
   
   * [Spouštění virtuálních počítačů pro N-vrstvou architekturu v Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Nastavení přístupu WinRM pro virtuální počítače v Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Povolení externího přístupu k VIRTUÁLNÍmu počítači pomocí Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Povolení externího přístupu k VIRTUÁLNÍmu počítači pomocí PowerShellu](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Povolení externího přístupu k VIRTUÁLNÍmu počítači se systémem Linux pomocí Azure CLI](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Vlastnost **domainNameLabel** pro veřejné IP adresy musí být jedinečná. Hodnota **domainNameLabel** musí mít délku 3 až 63 znaků a musí následovat po pravidlech určených tímto regulárním výrazem: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Vzhledem k tomu, že funkce **uniqueString** generuje řetězec o délce 13 znaků, parametr **dnsPrefixString** je omezen na 50 znaků:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Když přidáte heslo do rozšíření vlastních skriptů, použijte vlastnost **commandToExecute** ve vlastnosti **protectedSettings** :
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Aby se zajistilo šifrování tajných klíčů při jejich předávání jako parametrů virtuálním počítačům a rozšířením, použijte vlastnost **protectedSettings** relevantních rozšíření.
   > 
   > 

## <a name="outputs"></a>Výstupy

Pokud k vytvoření veřejných IP adres použijete šablonu, zahrňte [část výstupy](resource-group-authoring-templates.md#outputs) , která vrátí podrobnosti o IP adrese a plně kvalifikovaném názvu domény (FQDN). Výstupní hodnoty můžete snadno získat podrobnosti o veřejné IP adresy a plně kvalifikované názvy domény po nasazení.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Další postup

* Informace o struktuře souboru šablony Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Doporučení k vytváření šablon, které fungují ve všech cloudových prostředích Azure, najdete v tématu [vývoj šablon Azure Resource Manager pro konzistenci cloudu](templates-cloud-consistency.md).
