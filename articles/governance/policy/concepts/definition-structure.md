---
title: Struktura definic Azure Policy
description: Popisuje, jak je používat prostředku definice zásady Azure Policy stanovit vytváření názvů pro prostředky ve vaší organizaci zadáním popisu vašeho nového, když je tato zásada vynucená a jaký vliv má provést.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1d2925f36001ac37a3d94eb192c52fc3f25a95d5
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785698"
---
# <a name="azure-policy-definition-structure"></a>Struktura definic Azure Policy

Definice zásad prostředků používá Azure Policy umožňuje vytvořit vytváření názvů pro prostředky ve vaší organizaci zadáním popisu vašeho nového, když je tato zásada vynucená a jaký vliv má provést. Definuje konvence, můžete řídit náklady a snadněji spravovat vaše prostředky. Například můžete určit, zda jsou povoleny pouze určité typy virtuálních počítačů. Nebo můžete vyžadovat, aby všechny prostředky měly konkrétní značku. Všechny podřízené prostředky dědí zásady. Takže pokud zásady se použijí pro skupinu prostředků, se vztahuje na všechny prostředky v příslušné skupině prostředků.

Schéma používané službou Azure Policy najdete tady: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Vytvoření definice zásady pomocí JSON. Definice zásady obsahuje elementy pro:

- režim
- parameters
- Zobrazovaný název
- description
- pravidlo zásad
  - logické hodnocení
  - Efekt

Například následující kód JSON ukazuje zásadu, která omezí, ve které jsou nasazené prostředky:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Všechny ukázky zásady Azure jsou v [ukázky zásad](../samples/index.md).

## <a name="mode"></a>Mode

**Režimu** Určuje, jaké typy prostředků, se vyhodnotí zásady. Jsou podporované režimy:

- `all`: vyhodnocení skupiny prostředků a všechny typy prostředků
- `indexed`: jenom vyhodnotit typy prostředků, které podporují značky a umístění

Doporučujeme vám, že jste nastavili **režimu** k `all` ve většině případů. Všechny definice zásad, které jsou vytvořené pomocí portálu `all` režimu. Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure, můžete zadat **režimu** parametr ručně. Pokud neobsahuje definici zásady **režimu** hodnota je výchozí hodnota je `all` v prostředí Azure PowerShell a o `null` v rozhraní příkazového řádku Azure, což je totéž jako `indexed`, pro zpětné kompatibility.

`indexed` má být použit při vytváření zásad, která bude vynucovat značky nebo umístění. Tato akce není povinná, ale zabrání prostředky, které nepodporují značky a umístěním objeví jako nedodržující předpisy ve výsledcích dodržování předpisů. Jedinou výjimkou je **skupiny prostředků**. Zásady, které se pokoušíte vynutit místa nebo značky na skupinu prostředků, nastavte **režimu** k `all` a konkrétně cíl `Microsoft.Resources/subscriptions/resourceGroup` typu. Příklad najdete v tématu [vynutit značky skupiny prostředků](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Parametry

Parametry pomáhají zjednodušit správu zásad snížením počtu definic zásad. Představte si, že parametrů, jako je pole ve formuláři – `name`, `address`, `city`, `state`. Tyto parametry vždy stejné, ale změnit jejich hodnoty podle jednotlivých vyplňování formuláře.
Parametry fungovat stejným způsobem jako při vytváření zásad. Včetně parametrů v definici zásad, můžete využít této zásadě pro různé scénáře pomocí různých hodnot.

> [!NOTE]
> Definice parametry pro zásadu nebo definice iniciativy můžete nakonfigurovat jenom během počátečního vytváření zásady nebo iniciativa. Parametry definici není možné později změnit.
> To zabrání existující přiřazení zásady nebo iniciativa nepřímo prováděné neplatný.

Můžete třeba definovat zásady pro vlastnost prostředku možné omezit místa, kde můžete nasadit prostředky. Při vytváření zásady, v takovém případě by deklarovat následující parametry:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Typ parametru může být řetězec nebo pole. Vlastnost metadat se používá pro nástroje, jako je na webu Azure portal k zobrazení informací uživatelsky přívětivé.

V rámci vlastnost metadat, můžete použít **strongType** poskytnout vícenásobný výběr seznam možností na webu Azure portal. Povolené hodnoty pro **strongType** aktuálně zahrnují:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

V pravidlu zásad můžete odkazovat na parametry u následujících `parameters` syntaxe funkce hodnotu nasazení:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Umístění definice

Při vytváření definice iniciativy nebo zásady, je důležité, že zadáte umístění definice.

Umístění definice určuje obor, ke které je možné přiřadit definici iniciativy nebo zásady. Umístění lze zadat jako skupinu pro správu nebo předplatného.

> [!NOTE]
> Pokud budete chtít platí tato definice zásady pro více předplatných, musí být umístění, která obsahuje odběry, které se přiřazení iniciativy nebo zásady skupiny pro správu.

## <a name="display-name-and-description"></a>Zobrazovaný název a popis

Můžete použít **displayName** a **popis** identifikovat definice zásad a zadání při použití kontextu.

## <a name="policy-rule"></a>Pravidlo zásad

Pravidlo zásad se skládá z **Pokud** a **pak** bloky. V **Pokud** bloku, definujete jednu nebo více podmínek, které určují, kdy je tato zásada vynucená. Tyto podmínky přesně definovat scénář pro zásady můžete použít logické operátory.

V **pak** bloku, definujete efekt, který se stane, když **Pokud** jsou splněny podmínky.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Logické operátory

Podporované logické operátory jsou:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**Není** syntaxe obrátí výsledek podmínky. **AllOf** syntaxe (podobně jako logický **a** operace) vyžaduje všechny podmínky na hodnotu true. **AnyOf** syntaxe (podobně jako logický **nebo** operace) vyžaduje jednu nebo více podmínek na hodnotu true.

Logické operátory lze vnořit. Následující příklad ukazuje **není** operace, která je vnořená v rámci **allOf** operace.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Podmínky

Podmínka vyhodnotí, zda **pole** splňuje určitá kritéria. Jsou podporované podmínky:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Při použití **jako** a **notLike** podmínky, můžete zadat zástupný znak `*` v hodnotě.
Hodnota nesmí obsahovat více než jeden zástupný znak `*`.

Při použití **odpovídat** a **notMatch** podmínky, poskytují `#` představující číslici, `?` pro písmeno a jakýkoli jiný znak k reprezentaci tohoto znaku skutečný. Příklady najdete v tématu [povolit více vzory názvů](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Fields (Pole)

Podmínky jsou vytvářena pomocí polí. Představuje pole vlastnosti datové části požadavku prostředku, který se používá k popisu stavu prostředku.

Podporovány jsou následující pole:

- `name`
- `fullName`
  - Vrátí úplný název prostředku. Úplný název prostředku je název prostředku předcházený žádné nadřazené názvy prostředků (například "myServer/databáze").
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Kde **\<tagName\>** je název značky ověřit podmínku.
  - Příklad: `tags.CostCenter` kde **nákladové středisko** je název značky.
- `tags[<tagName>]`
  - Tato syntaxe závorky podporuje názvy značek, které obsahují období.
  - Kde **\<tagName\>** je název značky ověřit podmínku.
  - Příklad: `tags[Acct.CostCenter]` kde **Acct.CostCenter** je název značky.
- Vlastnost aliasy – seznam najdete v tématu [aliasy](#aliases).

### <a name="effect"></a>Efekt

Zásady podporuje následující typy dopadu:

- **Odepřít**: vygeneruje událost do protokolu auditu a požadavek selže
- **Auditovat**: vygeneruje událost upozornění v protokolu auditu, ale ne nesplní žádost
- **Připojit**: Přidá definovanou sadu polí k této žádosti
- **AuditIfNotExists**: povolí auditování Pokud prostředek neexistuje.
- **DeployIfNotExists**: nasadí prostředek, pokud ještě neexistuje.

Pro **připojit**, je nutné zadat následující údaje:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Hodnota může být řetězec nebo objekt formátu JSON.

S **AuditIfNotExists** a **DeployIfNotExists** můžete vyhodnotit existenci souvisejících prostředků a použít pravidla a odpovídající efekt, pokud daný prostředek neexistuje. Můžete třeba vyžadovat, že sledovací proces sítě se nasazuje pro všechny virtuální sítě. Příklad audit, když rozšíření virtuálního počítače není nasazený, naleznete v tématu [auditovat, jestli rozšíření neexistuje](../samples/audit-ext-not-exist.md).

Kompletní informace o jednotlivých vliv pořadí vyhodnocení, vlastností a příkladů, najdete v části [účinky zásad Principy](effects.md).

### <a name="policy-functions"></a>Funkce zásad

Podmnožinu [funkce šablon Resource Manageru](../../../azure-resource-manager/resource-group-template-functions.md) jsou k dispozici pro použití v rámci pravidla zásad. Jsou aktuálně podporované funkce:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [předplatné](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Kromě toho `field` funkce je k dispozici pro pravidla zásad. Tato funkce je primárně určen pro použití s **AuditIfNotExists** a **DeployIfNotExists** polí odkaz na prostředek, který je právě vyhodnocována. Například můžete zobrazit na [DeployIfNotExists příklad](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Příklady zásad – funkce

Používá tento příklad pravidla zásad `resourceGroup` prostředků funkce získáte **název** vlastnost, společně s `concat` pole a objektu funkce pro sestavení `like` podmínku, která vynucuje název prostředku pomocí názvu skupiny prostředků.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

Používá tento příklad pravidla zásad `resourceGroup` prostředků funkce získáte **značky** hodnota vlastnosti pole **nákladové středisko** označit skupiny prostředků a připojte ho k **nákladové středisko**  značku na nový prostředek.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Aliasy

Vlastnost aliasy používáte pro přístup k určité vlastnosti pro typ prostředku. Aliasy umožňují omezit, jaké hodnoty nebo podmínky jsou povolené pro vlastnost prostředku. Každý alias namapuje cesty v různých verzích rozhraní API pro typ daný prostředek. Modul zásad během hodnocení zásad, získá vlastnost cesty pro tuto verzi rozhraní API.

Seznam aliasů se pořád rozrůstá. Pokud chcete zjistit, jaké aliasy jsou aktuálně podporovány službou Azure Policy, použijte jednu z následujících metod:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
    'Authorization'='Bearer ' + $token.AccessToken
  }

  # Create a splatting variable for Invoke-RestMethod
  $invokeRest = @{
    Uri = 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases'
    Method = 'Get'
    ContentType = 'application/json'
    Headers = $authHeader
  }

  # Invoke the REST API
  $response = Invoke-RestMethod @invokeRest

  # Create an List to hold discovered aliases
  $aliases = [System.Collections.Generic.List[pscustomobject]]::new()

  foreach ($ns in $response.value)
  {
      foreach ($rT in $ns.resourceTypes)
      {
          if ($rT.aliases)
          {
              foreach ($obj in $rT.aliases)
              {
                  $alias = [PSCustomObject]@{
                      Namespace    = $ns.namespace
                      resourceType = $rT.resourceType
                      alias        = $obj.name
                  }
                  $aliases.Add($alias)
              }
          }
      }
  }

  # Output the list and sort it by Namespace, resourceType and alias. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- Rozhraní REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Principy alias [*]

Některé aliasy, které jsou k dispozici máte verzi, která se zobrazí jako 'normal' název, který má **[\*]** k němu připojená. Příklad:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

V prvním příkladu se používá k vyhodnocení celého pole, ve kterém **[\*]** alias vyhodnocuje každý prvek pole.

Podívejme se na pravidlo zásad jako příklad. Tato zásada **Odepřít** účet úložiště, který má ipRules nakonfigurovaný a pokud **žádný** ipRules mít hodnotu "127.0.0.1".

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

**IpRules** pole je následující příklad:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Zde je, jak se zpracovávají v tomto příkladu:

- `networkAcls.ipRules` – Zkontrolujte, že pole je jiná než null. Je vyhodnocen jako true, takže vyhodnocování pokračuje.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` -Zkontroluje každý _hodnotu_ vlastnost **ipRules** pole.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Jako pole, které se budou zpracovávat každý prvek.

    - "127.0.0.1"! = "127.0.0.1" vyhodnotí jako false.
    - "127.0.0.1"! = "192.168.1.1" vyhodnotí jako true.
    - Alespoň jeden _hodnotu_ vlastnost **ipRules** pole vyhodnotí jako false, takže vyhodnocování se zastaví.

Jako podmínka vyhodnocena jako false **Odepřít** efekt neaktivuje.

## <a name="initiatives"></a>Iniciativy

Iniciativy umožňují seskupit několik definic zásad souvisejících ke zjednodušení přiřazení a správy, protože pracujete s skupinu jako jednu položku. Můžete například seskupit všech souvisejících označení definic zásad v jednoho initiative. Místo toho každé zásady zvlášť, můžete použít iniciativy.

Následující příklad ukazuje, jak vytvořit iniciativu pro zpracování dvěma značkami: `costCenter` a `productName`. Využívá dvě předdefinované zásady použít výchozí hodnotu značky.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md)
- Kontrola [Principy účinky zásad](effects.md)
- Pochopit postup [programové vytváření zásad](../how-to/programmatically-create.md)
- Zjistěte, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md)
- Objevte jak [opravit nekompatibilní prostředky](../how-to/remediate-resources.md)
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../../management-groups/overview.md)
