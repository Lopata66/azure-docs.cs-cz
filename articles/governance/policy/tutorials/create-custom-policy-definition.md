---
title: Vytvoření vlastní definice zásad
description: Vytvořte vlastní definici zásad pro Azure Policy pro zajištění vlastních obchodních pravidel.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 240d0fa388fbdfdd3d29d735aed708a096440740
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980358"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Kurz: Vytvoření vlastní definice zásady

Vlastní definice zásad umožňuje zákazníkům definovat vlastní pravidla pro používání Azure. Tato pravidla se často vysazují:

- Postupy zabezpečení
- Správa nákladů
- Pravidla specifická pro organizaci (například pojmenování nebo umístění)

Bez ohledu na to, jaký má obchodní ovladač pro vytváření vlastních zásad, jsou tyto kroky stejné jako při definování nových vlastních zásad.

Než vytvoříte vlastní zásadu, podívejte se do [ukázek zásad](../samples/index.md) a podívejte se, jestli už existuje zásada, která vyhovuje vašim potřebám.

Přístup k vytváření vlastních zásad se řídí těmito kroky:

> [!div class="checklist"]
> - Určení podnikových požadavků
> - Namapujte všechny požadavky na vlastnost prostředku Azure.
> - Namapujte vlastnost na alias.
> - Určení, který efekt použít
> - Vytvoření definice zásady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="identify-requirements"></a>Identifikace požadavků

Před vytvořením definice zásady je důležité pochopit záměr této zásady. V tomto kurzu použijeme běžný požadavek na podnikový zabezpečení jako cíl k ilustraci kroků, které jsou k diskrokování:

- U každého účtu úložiště musí být povolený protokol HTTPS.
- Každý účet úložiště musí být pro protokol HTTP zakázaný.

Vaše požadavky by měly jasně identifikovat jak "tak jak", tak do stavů prostředků "nebude být".

I když jsme definovali očekávaný stav tohoto prostředku, ještě jsme nedefinovali, co chceme udělat s nekompatibilními prostředky. Azure Policy podporuje několik [efektů](../concepts/effects.md). Pro účely tohoto kurzu definujeme obchodní požadavek, který znemožní vytváření prostředků, pokud nevyhovují obchodním pravidlům. Pro splnění tohoto cíle použijeme účinek [zamítnout](../concepts/effects.md#deny) . Chceme také možnost pozastavit zásadu pro konkrétní přiřazení. V takovém případě použijeme efekt [disabled](../concepts/effects.md#disabled) a v definici [zásad se uplatní](../concepts/definition-structure.md#parameters) efekt.

## <a name="determine-resource-properties"></a>Určení vlastností prostředku

Na základě obchodních požadavků je prostředek Azure, který se bude auditovat pomocí Azure Policy účet úložiště. Nevím ale vlastnosti, které se mají použít v definici zásady. Azure Policy vyhodnocuje na základě reprezentace prostředku ve formátu JSON, takže musíme pochopit vlastnosti, které jsou k dispozici na tomto prostředku.

Existuje mnoho způsobů, jak určit vlastnosti prostředku Azure. Podíváme se na každou z těchto kurzů:

- Šablony Správce prostředků
  - Exportovat existující prostředek
  - Prostředí pro vytváření
  - Šablony pro rychlý Start (GitHub)
  - Referenční dokumentace k šablonám
- Průzkumník prostředků Azure

### <a name="resource-manager-templates"></a>Šablony Správce prostředků

Existuje několik způsobů, jak se podívat na [šablonu správce prostředků](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) , která obsahuje vlastnost, kterou chcete spravovat.

#### <a name="existing-resource-in-the-portal"></a>Existující prostředek na portálu

Nejjednodušší způsob, jak najít vlastnosti, je podívat se na existující prostředek stejného typu. Prostředky, které jsou už nakonfigurované s nastavením, které chcete vyhodnotit, taky poskytují hodnotu pro porovnání.
Podívejte se na stránku **Exportovat šablonu** (v části **nastavení**) v Azure Portal pro tento konkrétní prostředek.

![Stránka pro export šablony na stávajícím prostředku](../media/create-custom-policy-definition/export-template.png)

Když to uděláte, účet úložiště odhalí šablonu podobnou tomuto příkladu:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

V části **vlastnosti** je hodnota s názvem **supportsHttpsTrafficOnly** nastavená na **hodnotu false**. Tato vlastnost vypadá jako vlastnost, kterou hledáte. Také **typ** prostředku je **Microsoft. Storage/storageAccounts**. Typ nám umožňuje omezit zásady jenom na prostředky tohoto typu.

#### <a name="create-a-resource-in-the-portal"></a>Vytvoření prostředku na portálu

Dalším způsobem prostřednictvím portálu je prostředí pro vytváření prostředků. Při vytváření účtu úložiště prostřednictvím portálu je u možnosti na kartě **Upřesnit** možnost **migrace zabezpečení vyžadována**. Tato vlastnost má _zakázané_ a _povolené_ možnosti. Ikona informace obsahuje další text, který potvrzuje, že je pravděpodobná vlastnost, kterou chceme. Portál ale na této obrazovce nám neřekne název vlastnosti.

Na kartě **Revize + vytvořit** je odkaz na konci stránky a **stáhne šablonu pro automatizaci**. Když vyberete odkaz, otevře se šablona vytvářející prostředek, který jsme nakonfigurovali. V tomto případě se zobrazí dvě klíčové informace:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Tyto informace nám sdělí typ vlastnosti a také potvrdí, že **supportsHttpsTrafficOnly** je vlastnost, kterou hledáte.

#### <a name="quickstart-templates-on-github"></a>Šablony pro rychlý Start na GitHubu

[Šablony pro rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates) na GitHubu obsahují stovky správce prostředků šablon sestavených pro různé prostředky. Tyto šablony můžou být skvělým způsobem, jak najít vlastnost prostředku, kterou hledáte. Některé vlastnosti se můžou zdát, co hledáte, ale ovládají něco jiného.

#### <a name="resource-reference-docs"></a>Dokumentace k odkazům na prostředky

Pokud chcete ověřit vlastnost **supportsHttpsTrafficOnly** je správná, zkontrolujte odkaz na šablonu správce prostředků [prostředku účtu úložiště](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) ve zprostředkovateli úložiště.
Objekt Properties obsahuje seznam platných parametrů. Výběr odkazu [StorageAccountPropertiesCreateParameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) zobrazí tabulku přijatelných vlastností. k dispozici je **supportsHttpsTrafficOnly** a popis odpovídá tomu, co hledáte pro splnění obchodních požadavků.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure

Dalším způsobem, jak prozkoumat prostředky Azure, je prostřednictvím [Azure Resource Explorer](https://resources.azure.com) (Preview). Tento nástroj používá kontext předplatného, takže se na webu musíte ověřit pomocí vašich přihlašovacích údajů Azure. Po ověření můžete procházet podle zprostředkovatelů, předplatných, skupin prostředků a prostředků.

Vyhledejte prostředek účtu úložiště a podívejte se na vlastnosti. Tady se zobrazuje i vlastnost **supportsHttpsTrafficOnly** . Když vyberete kartu **dokumentace** , uvidíme, že popis vlastnosti se shoduje s tím, co jsme našli v referenční dokumentaci dříve.

## <a name="find-the-property-alias"></a>Najít alias vlastnosti

Identifikovali jsme vlastnost prostředku, ale musíme tuto vlastnost namapovat na [alias](../concepts/definition-structure.md#aliases).

Existuje několik způsobů, jak určit aliasy pro prostředek Azure. Podíváme se na každou z těchto kurzů:

- Azure CLI
- Azure PowerShell
- Graf prostředků Azure

### <a name="azure-cli"></a>Azure CLI

V Azure CLI se ke hledání aliasů prostředků používá skupina příkazů `az provider`. Vyfiltrujeme obor názvů **Microsoft. Storage** na základě detailů, které jsme o prostředku Azure dostali dřív.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Ve výsledcích se zobrazí alias podporovaný účty úložiště s názvem **supportsHttpsTrafficOnly**. Tento alias znamená, že můžeme napsat zásadu, abychom vynutili naše obchodní požadavky.

### <a name="azure-powershell"></a>Azure PowerShell

V Azure PowerShell se k hledání aliasů prostředků používá rutina `Get-AzPolicyAlias`. Vyfiltrujeme obor názvů **Microsoft. Storage** na základě detailů, které jsme o prostředku Azure dostali dřív.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Podobně jako Azure CLI zobrazuje výsledky aliasy podporované účty úložiště s názvem **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Graf prostředků Azure

[Azure Resource Graph](../../resource-graph/overview.md) je nová služba ve verzi Preview. Umožňuje další metodu hledání vlastností prostředků Azure. Tady je ukázkový dotaz pro prohlížení jednoho účtu úložiště s grafem prostředků:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Výsledky vypadají podobně jako v šablonách Správce prostředků a prostřednictvím Azure Resource Explorer. Výsledky grafu prostředků Azure ale můžou taky zahrnovat podrobnosti o [aliasu](../concepts/definition-structure.md#aliases) tím, že projedná _projekt_ s polem _aliasy_ :

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Tady je příklad výstupu z účtu úložiště pro aliasy:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Azure Resource Graph (Preview) je možné použít prostřednictvím [Cloud Shell](https://shell.azure.com)a díky tomu je rychlý a snadný způsob, jak prozkoumat vlastnosti vašich prostředků.

## <a name="determine-the-effect-to-use"></a>Určení efektu, který se má použít

Rozhodnutí o tom, co dělat s prostředky, které nedodržují předpisy, je skoro stejně důležité jako rozhodování, co vyhodnotit na prvním místě. Každá možná odpověď na prostředek, který nedodržuje předpisy, se nazývá [efekt](../concepts/effects.md).
Tento efekt řídí, jestli je prostředek, který nedodržuje předpisy, přihlášen, je blokovaný, má připojená data, nebo má přidružené nasazení pro uvedení prostředku zpátky do stavu kompatibility.

V našem příkladu je odepřený efekt, protože nepotřebujeme nekompatibilní prostředky vytvořené v našem prostředí Azure. Audit je vhodný první volbou pro účinek zásad, která určuje, jaký dopad zásad je před tím, než je nastavování zamítnuto. Jedním ze způsobů, jak změnit účinek na přiřazení, je parametrizovat efekt. Podrobnosti o tom, jak najdete v níže uvedených [parametrech](#parameters) .

## <a name="compose-the-definition"></a>Vytvoření definice

Teď máme podrobnosti o vlastnosti a alias pro to, co plánujeme spravovat. V dalším kroku vytvoříte samotné pravidlo zásad. Pokud ještě neznáte jazyk zásad, [strukturu definice](../concepts/definition-structure.md) referenčních zásad pro strukturu definice zásad. Tady je prázdná šablona toho, co definice zásad vypadá takto:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadata

První tři komponenty jsou metadata zásad. Tyto komponenty se dají snadno zadat, protože víme, pro které pravidlo vytváříme. [Režim](../concepts/definition-structure.md#mode) je primárně o značkách a umístění prostředků. Vzhledem k tomu, že nepotřebujeme omezit vyhodnocení na prostředky, které podporují značky, použijeme pro **režim**hodnotu _All_ .

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametry

Přestože jsme nepoužili parametr pro změnu hodnocení, chceme použít parametr a povolit změnu **efektu** pro řešení potíží. Definujeme parametr **effectType** a omezíme ho jenom na **Deny** a **disabled**. Tyto dvě možnosti odpovídají našim obchodním požadavkům. Blok dokončených parametrů vypadá jako v tomto příkladu:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Pravidlo zásad

Sestavování [pravidla zásad](../concepts/definition-structure.md#policy-rule) je posledním krokem při sestavování vlastní definice zásad. Identifikovali jsme dva příkazy, které se mají testovat:

- Tento **typ** účtu úložiště je **Microsoft. Storage/storageAccounts**
- To, že účet úložiště **supportsHttpsTrafficOnly** nemá **hodnotu true**

Protože potřebujeme, aby oba tyto příkazy byly pravdivé, použijeme [logický operátor](../concepts/definition-structure.md#logical-operators) **allOf** . Parametr **effectType** předáte k tomuto efektu namísto provedení statické deklarace. Naše dokončené pravidlo vypadá jako v tomto příkladu:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Definice dokončena

Tady je naše dokončená definice se všemi třemi částmi definovaných zásad:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

K vytvoření nové zásady se dá použít dokončená definice. Portál a každá sada SDK (Azure CLI, Azure PowerShell a REST API) přijímají definici různými způsoby, proto si Projděte příkazy pro každý, abyste ověřili správné využití. Pak ji přiřaďte pomocí parametrizovaného efektu k odpovídajícím prostředkům pro správu zabezpečení účtů úložiště.

## <a name="review"></a>Revize

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Identifikujte vaše podnikové požadavky.
> - Namapovaný každý požadavek na vlastnost prostředku Azure
> - Namapována vlastnost na alias.
> - Zjistili efekt, který se má použít
> - Složená definice zásad

## <a name="next-steps"></a>Další kroky

V dalším kroku vytvořte a přiřaďte zásadu pomocí vlastní definice zásad:

> [!div class="nextstepaction"]
> [Vytvoření a přiřazení definice zásady](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)