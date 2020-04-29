---
title: Pochopení pořadí sekvence nasazení
description: Přečtěte si o výchozím pořadí, během kterého jsou v průběhu přiřazení podrobného plánu nasazeny artefakty podrobného plánu a jak přizpůsobit pořadí nasazení.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80677565"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Porozumění sekvenci nasazení v Azure modrotisky

Azure modrotisky používá **pořadí sekvencování** k určení pořadí vytváření prostředků při zpracování přiřazení definice podrobného plánu. Tento článek vysvětluje následující koncepty:

- Výchozí pořadí řazení, které se používá
- Jak přizpůsobit pořadí
- Jak se zpracovává vlastní objednávka

V příkladech JSON jsou proměnné, které je potřeba nahradit vlastními hodnotami:

- Proměnnou `{YourMG}` nahraďte názvem skupiny pro správu.

## <a name="default-sequencing-order"></a>Výchozí pořadí sekvencování

Pokud definice podrobného plánu neobsahuje žádnou direktivu, aby bylo možné nasazovat artefakty nebo tato direktiva je null, použije se následující pořadí:

- Artefakty **přiřazení role** na úrovni předplatného seřazené podle názvu artefaktu
- Artefakty **přiřazení zásad** na úrovni předplatného seřazené podle názvu artefaktu
- Artefakty **šablony Azure Resource Manager** na úrovni předplatného seřazené podle názvu artefaktu
- Artefakty **skupiny prostředků** (včetně podřízených artefaktů) seřazené podle názvu zástupného symbolu

V rámci každého artefaktu **skupiny prostředků** se pro artefakty, které se vytvářejí v rámci této skupiny prostředků, používá následující pořadí:

- Artefakty **přiřazení podřízené role** skupiny prostředků seřazené podle názvu artefaktu
- Artefakty přiřazení podřízených **zásad** skupiny prostředků seřazené podle názvu artefaktu
- Artefakty **šablony Azure Resource Manager** podřízené skupiny prostředků seřazené podle názvu artefaktu

> [!NOTE]
> Použití [artefaktů ()](../reference/blueprint-functions.md#artifacts) vytvoří implicitní závislost na artefaktu, na který je odkazováno.

## <a name="customizing-the-sequencing-order"></a>Přizpůsobení pořadí sekvencování

Při sestavování velkých podrobných definic podrobného plánu může být potřeba, aby se prostředky vytvořily v určitém pořadí. Nejběžnějším vzorem použití tohoto scénáře je, že definice podrobného plánu obsahuje několik šablon Azure Resource Manager. Azure Modrotiskys zpracovává tento model tím, že umožňuje definovat pořadí sekvencování.

Řazení je provedeno definováním `dependsOn` vlastnosti ve formátu JSON. Tato vlastnost podporuje definici podrobného plánu, pro skupiny prostředků a objekty artefaktů. `dependsOn`je pole řetězců názvů artefaktů, které musí být před vytvořením vytvořen konkrétní artefakt.

> [!NOTE]
> Při vytváření objektů podrobného plánu získá každý prostředek artefaktu svůj název z názvu souboru, pokud používáte [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact), nebo koncový bod adresy URL, pokud se používá [REST API](/rest/api/blueprints/artifacts/createorupdate).
> odkazy na _zdroj_ v artefaktech se musí shodovat s odkazy definovanými v definici podrobného plánu.

### <a name="example---ordered-resource-group"></a>Příklad – seřazená skupina prostředků

Tato příklad definice podrobného plánu má skupinu prostředků, která definovala vlastní pořadí sekvencování, deklarováním hodnoty `dependsOn`pro, společně se standardní skupinou prostředků. V tomto případě se artefakt s názvem **assignPolicyTags** zpracuje před **seřazenou** skupinou prostředků RG.
**Standard-RG** se zpracuje podle výchozího pořadí sekvencování.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Příklad – artefakt s vlastním pořadím

Tento příklad je artefaktem zásad, který závisí na šabloně Azure Resource Manager. Ve výchozím nastavení je artefakt zásad vytvořen před šablonou Azure Resource Manager. Toto řazení umožňuje artefaktu zásad počkat na vytvoření šablony Azure Resource Manager.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Příklad – artefakt šablony na úrovni předplatného v závislosti na skupině prostředků

Tento příklad je pro šablonu Správce prostředků nasazenou na úrovni předplatného tak, aby byla závislá na skupině prostředků. Ve výchozím pořadí se artefakty na úrovni předplatného vytvoří před všemi skupinami prostředků a podřízenými artefakty v těchto skupinách prostředků. Skupina prostředků je definovaná v definici podrobného plánu, jako je tato:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Artefakt šablony na úrovni předplatného v závislosti na skupině prostředků **čekání na uživatele** je definován takto:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Zpracování přizpůsobené sekvence

Během procesu vytváření se k vytvoření grafu závislostí artefaktů modrotisky používá topologické řazení. Tato kontrolu zajišťuje, aby se podporovaly jednotlivé úrovně závislostí mezi skupinami prostředků a artefakty.

Pokud je deklarována závislost artefaktu, která by nezměnila výchozí pořadí, nebude provedena žádná změna. Příkladem je skupina prostředků, která závisí na zásadě na úrovni předplatného. Dalším příkladem je přiřazení podřízené zásady skupiny prostředků ' standard-RG ', které závisí na přiřazení podřízené role skupiny prostředků ' standard-RG '. V obou případech `dependsOn` se nezměnila výchozí pořadí sekvencování a neudělaly se žádné změny.

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](lifecycle.md)
- Principy použití [statických a dynamických parametrů](parameters.md)
- Použití [zamykání prostředků podrobného plánu](resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)