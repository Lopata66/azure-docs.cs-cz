---
title: Ukázkový – Audit transparentní šifrování dat pro službu SQL Database
description: Tato definice zásady ukázka Audituje SQL database nemá transparentní šifrování dat povolené.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 04/27/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: e8ee800ff9f286f901a84a039e3c433442ae11b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60923284"
---
# <a name="sample---audit-sql-database-encryption"></a>Ukázkový – auditovat šifrování databáze SQL

Tyto integrované zásady JSON auditují, jestli databáze SQL nemá povolené transparentní šifrování dat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Ukázková šablona

```json
{
   "properties": {
      "displayName": "Audit transparent data encryption status",
      "description": "Audit transparent data encryption status for SQL databases",
      "mode": "Indexed",
      "parameters": {
         "effect": {
            "type": "string",
            "defaultValue": "AuditIfNotExists",
            "allowedValues": [
               "AuditIfNotExists",
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
                  "equals": "Microsoft.Sql/servers/databases"
               },
               {
                  "field": "name",
                  "notEquals": "master"
               }
            ]
         },
         "then": {
            "effect": "[parameters('effect')]",
            "details": {
               "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
               "name": "current",
               "existenceCondition": {
                  "allOf": [
                     {
                        "field": "Microsoft.Sql/transparentDataEncryption.status",
                        "equals": "enabled"
                     }
                  ]
               }
            }
         }
      }
   }
}
```

K nasazení této šablony můžete použít [Azure Portal](#deploy-with-the-portal) s [PowerShellem](#deploy-with-powershell) nebo s [Azure CLI](#deploy-with-azure-cli). K získání integrovaných zásad použijte ID `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Nasazení s využitím portálu

Při přiřazování zásad vyberte z dostupných integrovaných definic **Auditování stavu transparentního šifrování dat**.

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Vyčištění nasazení PowerShellu

Pokud chcete odebrat přiřazení zásad, spusťte následující příkaz.

```powershell
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Vyčištění nasazení Azure CLI

Pokud chcete odebrat přiřazení zásad, spusťte následující příkaz.

```cli
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Další postup

- Další ukázky najdete v [ukázkách pro Azure Policy](index.md).