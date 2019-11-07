---
title: Získání změn prostředků
description: Zjistěte, jak najít, kdy byl prostředek změněn, a získejte seznam vlastností, které se změnily.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 44e7bbde40dbd4b79a6ce3735ab5a1ac81421d3b
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622564"
---
# <a name="get-resource-changes"></a>Získání změn prostředků

Prostředky se mění v průběhu denního využití, z důvodu změny konfigurace nebo dokonce změny nasazení.
Změna může pocházet z individuálního nebo automatizovaného procesu. Většina změn je záměrné, ale někdy ne. V posledních 14 dnech historie změn vám Azure Resource Graph umožní:

- Najde, kdy se u vlastnosti Azure Resource Manager zjistily změny.
- Informace o jednotlivých změnách prostředků najdete v tématu podrobnosti o změně vlastnosti.
- Zobrazit úplné porovnání prostředku před a po zjištěné změně

Zjišťování změn a podrobnosti jsou užitečné v následujících ukázkových scénářích:

- Během správy incidentů získat informace o _potenciálně_ souvisejících změnách. Dotaz na události změny během konkrétního časového období a vyhodnocení podrobností o změně.
- Udržování databáze správy konfigurace označované jako CMDB, aktuální. Místo aktualizace všech prostředků a jejich úplných vlastností u naplánované četnosti Získejte jenom to, co se změnilo.
- Informace o tom, jaké další vlastnosti se mohly změnit, když prostředek změnil stav dodržování předpisů. Vyhodnocení těchto dalších vlastností může poskytnout přehled o dalších vlastnostech, které mohou být nutné ke správě pomocí definice Azure Policy.

Tento článek ukazuje, jak shromáždit tyto informace prostřednictvím sady SDK pro graf prostředků. Pokud chcete zobrazit tyto informace v Azure Portal, přečtěte si téma historie [změn](../../policy/how-to/determine-non-compliance.md#change-history-preview) v Azure Policy nebo [historie změn protokolu aktivit](../../../azure-monitor/platform/activity-log-view.md#azure-portal)Azure.
Podrobnosti o změnách vašich aplikací z infrastruktury do nasazení aplikace najdete v tématu [použití analýzy změn aplikace (Preview)](../../../azure-monitor/app/change-analysis.md) v Azure monitor.

> [!NOTE]
> Podrobnosti o změně v grafu prostředků jsou pro Správce prostředků vlastnosti. Informace o sledování změn v rámci virtuálního počítače Azure Automation najdete v tématu Konfigurace služby [Change Tracking](../../../automation/automation-change-tracking.md) nebo [Host Azure Policy na virtuálních](../../policy/concepts/guest-configuration.md)počítačích.

> [!IMPORTANT]
> Historie změn v grafu prostředků Azure je Public Preview.

## <a name="find-detected-change-events-and-view-change-details"></a>Najít zjištěné události změny a zobrazit podrobnosti o změně

Prvním krokem při zobrazení toho, co se změnilo u prostředku, je najít události změny týkající se tohoto prostředku v časovém intervalu. Každá událost změny také obsahuje podrobnosti o tom, co se u prostředku změnilo. Tento krok se provádí prostřednictvím koncového bodu **resourceChanges** REST.

Koncový bod **resourceChanges** akceptuje v textu požadavku následující parametry:

- **resourceId** \[požadované\]: prostředek Azure, ve kterém se mají hledat změny.
- **interval** \[požadovaných\]: vlastnost s _počátečním_ a _koncovým_ datem, kdy se má kontrolovat událost změny pomocí **Zulu časového pásma (Z)** .
- **fetchPropertyChanges** (volitelné): logická vlastnost, která nastavuje, zda objekt Response obsahuje změny vlastností.

Příklad textu žádosti:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

S výše uvedeným textem žádosti je REST API identifikátor URI pro **resourceChanges** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpověď vypadá podobně jako v tomto příkladu:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Každá zjištěná událost změny pro **ResourceID** má následující vlastnosti:

- **changeId** – tato hodnota je pro tento prostředek jedinečná. I když řetězec **changeId** může někdy obsahovat jiné vlastnosti, je zaručený pouze jedinečný.
- **beforeSnapshot** – obsahuje **snapshotId** a **časové razítko** snímku prostředku, který byl proveden před zjištěním změny.
- **afterSnapshot** – obsahuje **snapshotId** a **časové razítko** snímku prostředku, který byl proveden po zjištění změny.
- **ChangeType** – popisuje typ změny zjištěné u celého záznamu změny mezi **beforeSnapshot** a **afterSnapshot**. Hodnoty jsou: _vytvořit_, _aktualizovat_a _Odstranit_. Pole vlastností **propertyChanged** je zahrnuto pouze v případě, že je nastavena _aktualizace_ **ChangeType** .
- **propertyChanged** – toto pole vlastností podrobně popisuje všechny vlastnosti prostředku, které byly aktualizovány mezi **beforeSnapshot** a **afterSnapshot**:
  - **PropertyName** – název vlastnosti prostředku, která byla změněna.
  - **changeCategory** – popisuje, co změna provedla. Hodnoty jsou: _systém_ a _uživatel_.
  - **ChangeType** – popisuje typ změny zjištěné u jednotlivých vlastností prostředku.
    Hodnoty jsou: _INSERT_, _Update_, _Remove_.
  - **beforeValue** – hodnota vlastnosti prostředku v **beforeSnapshot**. Není zobrazeno, když je _vloženo_ **ChangeType** .
  - **afterValue** – hodnota vlastnosti prostředku v **afterSnapshot**. Se nezobrazí, pokud je _odebráno_ **ChangeType** .

## <a name="compare-resource-changes"></a>Porovnání změn prostředků

Pomocí **changeId** z koncového bodu **resourceChanges** se pak koncový bod **resourceChangeDetails** REST používá k získání před a po snímkům prostředku, který se změnil.

Koncový bod **resourceChangeDetails** vyžaduje v textu žádosti dva parametry:

- **ResourceID**: prostředek Azure pro porovnání změn v.
- **changeId**: jedinečná událost změny pro ID služby **ResourceID** shromážděná z **resourceChanges**.

Příklad textu žádosti:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

S výše uvedeným textem žádosti je REST API identifikátor URI pro **resourceChangeDetails** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Odpověď vypadá podobně jako v tomto příkladu:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
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
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** a **afterSnapshot** každý z nich poskytují čas pořízení snímku a vlastnosti v daném čase. Změna nastala v určitém bodě mezi těmito snímky. V předchozím příkladu vidíte, že vlastnost, která se změnila, byla **supportsHttpsTrafficOnly**.

Pro porovnání výsledků buď použijte vlastnost **changess** v **resourceChanges** nebo vyhodnoťte část **obsahu** každého snímku v **resourceChangeDetails** , abyste zjistili rozdíl. Pokud porovnáte snímky, **časové razítko** se vždycky zobrazí jako rozdíl, i když se očekává.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat prostředky](../concepts/explore-resources.md).