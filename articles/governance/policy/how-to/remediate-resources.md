---
title: Oprava prostředků, které nevyhovují předpisům
description: Tento návod vás provede nápravné prostředky, které jsou nekompatibilní zásady ve službě Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: d6753b319bc5bc4cbda18fe486695e5b0266acae
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169656"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Opravit nekompatibilní prostředky službou Azure Policy

Prostředky, které jsou pro nekompatibilní **deployIfNotExists** zásady můžou být přepnuté do vyhovujícího stavu prostřednictvím **nápravy**. Náprava dosahuje tím, že Azure Policy pro spuštění **deployIfNotExists** vliv na stávající prostředky přiřazené zásady. Tento článek popisuje kroky potřebné k pochopení a odstraňování problémů můžete využít Azure Policy provádět.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="how-remediation-security-works"></a>Jak funguje opravy zabezpečení

Spuštění šablony Azure Policy **deployIfNotExists** definice zásad, dělá to pomocí [se identita spravované](../../../active-directory/managed-identities-azure-resources/overview.md).
Služba Azure Policy vytvoří spravované identity pro každé přiřazení, ale musí mít podrobné informace o rolích udělit spravovaná identita. Pokud spravovaná identita chybí role, zobrazí se tato chyba během přiřazení zásady nebo iniciativa. Když na portálu Azure Policy automaticky udělí spravovanou identitu uvedené role po zahájení přiřazení.

![Spravovaná identita - chybějící role](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Pokud se prostředek změnil **deployIfNotExists** je mimo rozsah přiřazení zásady nebo šabloně přistupuje k vlastnosti s prostředky mimo obor přiřazení zásady, spravovanou identitu tohoto přiřazení musí být [ručně udělen přístup](#manually-configure-the-managed-identity) nebo nápravy nasazení se nezdaří.

## <a name="configure-policy-definition"></a>Nakonfigurovat definici zásad

Prvním krokem je definování role, která **deployIfNotExists** potřebuje k úspěšnému nasazení obsahu zahrnuty šablony v definici zásad. V části **podrobnosti** vlastnost, přidejte **roleDefinitionIds** vlastnost. Tato vlastnost je pole řetězců, které odpovídají role ve vašem prostředí. Úplný příklad najdete v článku [deployIfNotExists příklad](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** používá identifikátor úplný zdroj a nepřijímá krátké **roleName** role. Chcete-li získat ID pro roli "Přispěvatel" ve vašem prostředí, použijte následující kód:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ručně nakonfigurovat spravované identity

Při vytváření přiřazení pomocí portálu Azure Policy vygeneruje spravovanou identitu i mu udělí role definované v **roleDefinitionIds**. Za těchto podmínek je třeba provést postup pro vytvoření spravované identity a přiřadit oprávnění ručně:

- Při používání sady SDK (jako je Azure PowerShell)
- Když se upraví prostředek mimo rozsah přiřazení pomocí šablony
- Pokud je prostředek mimo rozsah přiřazení čteného šablonou

> [!NOTE]
> Azure PowerShell a rozhraní .NET jsou pouze sady SDK, které aktuálně podporují tuto funkci.

### <a name="create-managed-identity-with-powershell"></a>Vytvořte spravovanou identitu pomocí Powershellu

Chcete-li vytvořit spravovanou identitu při přiřazování zásady, **umístění** musí být definován a **AssignIdentity** použít. Následující příklad získá definic předdefinovaných zásad **nasadit SQL DB transparentní šifrování dat**, nastaví cílová skupina prostředků a pak vytvoří přiřazení.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment` Proměnná nyní obsahuje ID objektu zabezpečení spravovanou identitu spolu se standardní hodnoty vrácená při vytváření přiřazení zásad. Je přístupný prostřednictvím `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Udělení definované role pomocí prostředí PowerShell

Nové spravovanou identitu, musíte dokončit replikace prostřednictvím Azure Active Directory lze udělit potřebná role. Po dokončení replikace následující příklad iteruje definici zásady v `$policyDef` pro **roleDefinitionIds** a používá [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) udělit nové identity spravované role.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Udělení definované role prostřednictvím portálu

Existují dva způsoby, jak udělit definované role pomocí portálu, pomocí spravované identity přiřazení **řízení přístupu (IAM)** nebo úpravou přiřazení zásady nebo iniciativa a kliknutím na **Uložit**.

Přidání role pro toto přiřazení spravovanou identitu, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**.

1. Vyhledejte přiřazení, která má spravovanou identitu a klikněte na název.

1. Najít **ID přiřazení** vlastnost na stránky pro úpravu. ID přiřazení bude vypadat:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Název spravované identity je poslední část přiřazení ID prostředku, který je `2802056bfc094dfb95d4d7a5` v tomto příkladu. Zkopírujte tuto část ID přiřazení prostředku.

1. Přejděte na prostředek nebo prostředky nadřazeného kontejneru (skupinu prostředků, předplatné, skupina pro správu), který se musí ručně přidat definice role.

1. Klikněte na tlačítko **řízení přístupu (IAM)** odkaz na stránce prostředků a klikněte na tlačítko **+ přidat přiřazení role** v horní části stránku řízení přístupu.

1. Vyberte vhodnou roli, která odpovídá **roleDefinitionIds** z definice zásady.
   Ponechte **přiřadit přístup k** nastavenou na výchozí hodnotu "Azure AD uživatele, skupiny nebo aplikace". V **vyberte** pole, vložte nebo napište část ID prostředku přiřazení dříve nachází. Po dokončení hledání, klikněte na objekt se stejným názvem vyberte ID a klikněte na **Uložit**.

## <a name="create-a-remediation-task"></a>Vytvořte úlohu nápravy

### <a name="create-a-remediation-task-through-portal"></a>Vytvořte úlohu nápravu prostřednictvím portálu

Při vyhodnocování, přiřazení zásad s **deployIfNotExists** efekt Určuje, zda existují nekompatibilní prostředky. Po nalezení nekompatibilní prostředky jsou podrobné informace jsou k dispozici na **nápravy** stránky. Společně se seznamem zásad, které mají nekompatibilní prostředky je možnosti k aktivaci **úloha opravy**. Tato možnost je, co vytvoří nasazení z **deployIfNotExists** šablony.

Chcete-li vytvořit **úloha opravy**, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Hledání zásad ve všech služeb](../media/remediate-resources/search-policy.png)

1. Vyberte **nápravy** na levé straně na stránku služby Azure Policy.

   ![Vyberte na stránce zásady nápravy](../media/remediate-resources/select-remediation.png)

1. Všechny **deployIfNotExists** přiřazení zásad s nekompatibilní prostředky jsou k dispozici na **zásady k nápravě** kartu a data tabulky. Klikněte na příslušnou zásadu s prostředky, které jsou nekompatibilní. **Nová úloha opravy** otevře se stránka.

   > [!NOTE]
   > Alternativní způsob, jak otevírat **úloha opravy** stránky je najít a klikněte na zásadu z **dodržování předpisů** stránce a potom klikněte na **vytvořit úlohu nápravy** tlačítko.

1. Na **nová úloha opravy** stránce, filtrovat prostředky pomocí řešení **oboru** tři tečky a vyberte podřízené prostředky z kde přiřazené zásady (včetně na jednotlivé prostředky objekty). Kromě toho pomocí **umístění** rozevíracího seznamu a dále filtrovat prostředky. Pouze prostředky uvedené v tabulce bude opraven.

   ![Napravit – vybrat prostředky, které chcete opravit](../media/remediate-resources/select-resources.png)

1. Začátek nápravy úlohy po prostředků se vyfiltrovaly kliknutím **napravit**. Otevře se stránka zásad dodržování předpisů pro **nápravy úlohy** kartu k zobrazení stavu průběh úlohy.

   ![Napravit – průběh úkolů nápravy](../media/remediate-resources/task-progress.png)

1. Klikněte na **úloha opravy** ze stránky zásad dodržování předpisů se získat podrobnosti o průběhu. Filtrování používá pro úlohy se zobrazí spolu s seznam prostředků je opravit.

1. Z **úloha opravy** stránky, klikněte pravým tlačítkem na prostředek můžete zobrazit buď nasazení úloh opravy nebo prostředek. Na konci řádku, klikněte na **událostí souvisejících s** zobrazíte podrobnosti, jako je chybová zpráva.

   ![Napravit - prostředků úkolu kontextové nabídky](../media/remediate-resources/resource-task-context-menu.png)

Prostředky nasazené prostřednictvím **úloha opravy** jsou přidány do **nasazené prostředky** karty na stránce zásady dodržování předpisů.

### <a name="create-a-remediation-task-through-azure-cli"></a>Vytvořte úlohu nápravu prostřednictvím rozhraní příkazového řádku Azure

Chcete-li vytvořit **úloha opravy** pomocí Azure CLI, použijte `az policy remediation` příkazy. Nahraďte `{subscriptionId}` svým ID předplatného a `{myAssignmentId}` s vaší **deployIfNotExists** ID přiřazení zásady.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další příkazy nápravy a příklady najdete v tématu [nápravy zásad az](/cli/azure/policy/remediation) příkazy.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Vytvořte úlohu nápravu prostřednictvím Azure Powershellu

Vytvoření **úloha opravy** pomocí Azure Powershellu, použijte `Start-AzPolicyRemediation` příkazy. Nahraďte `{subscriptionId}` svým ID předplatného a `{myAssignmentId}` s vaší **deployIfNotExists** ID přiřazení zásady.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Další rutiny nápravy a příklady najdete v tématu [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modulu.

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit postup [programové vytváření zásad](programmatically-create.md).
- Zjistěte, jak [získat data o dodržování předpisů](getting-compliance-data.md).
- Zkontrolujte, jaké skupiny pro správu je s [uspořádání prostředků se skupinami pro správu Azure](../../management-groups/overview.md).