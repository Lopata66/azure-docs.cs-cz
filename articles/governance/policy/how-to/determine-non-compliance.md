---
title: Určení příčiny nedodržování předpisů
description: Pokud prostředek není kompatibilní, existuje mnoho možných důvodů. Přečtěte si, jak zjistit, co způsobilo nedodržení předpisů.
ms.date: 07/06/2020
ms.topic: how-to
ms.openlocfilehash: d548f5b9db141eb6aed5984c43f00543d0228f31
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970769"
---
# <a name="determine-causes-of-non-compliance"></a>Určení příčiny nedodržování předpisů

Pokud je u prostředku Azure zjištěno, že není kompatibilní s pravidlem zásad, je užitečné pochopit, na které straně pravidla prostředek není kompatibilní. Je také užitečné pochopit, jakou změnu změnil dříve kompatibilní prostředek, aby nesplňovala předpisy. Existují dva způsoby, jak najít tyto informace:

- [Podrobnosti o kompatibilitě](#compliance-details)
- [Historie změn (Preview)](#change-history)

## <a name="compliance-details"></a>Podrobnosti o kompatibilitě

Pokud prostředek nedodržuje předpisy, podrobnosti o kompatibilitě tohoto prostředku jsou k dispozici na stránce **dodržování zásad** . Podokno Podrobnosti o dodržování předpisů obsahuje následující informace:

- Podrobnosti o zdroji, jako je název, typ, umístění a ID prostředku
- Stav dodržování předpisů a časové razítko posledního vyhodnocení pro aktuální přiřazení zásad
- Seznam _důvodů_ neshody prostředků

> [!IMPORTANT]
> Vzhledem k podrobnostem dodržování předpisů pro prostředek, který _nedodržuje předpisy_ , se zobrazuje aktuální hodnota vlastností na daném prostředku, uživatel musí mít v **typu** prostředku operaci **čtení** . Například pokud je prostředek, _který nedodržuje předpisy_ , **Microsoft. COMPUTE/virtualMachines** , musí mít uživatel operaci **Microsoft. COMPUTE/virtualMachines/Read** . Pokud uživatel nemá potřebnou operaci, zobrazí se chyba přístupu.

Chcete-li zobrazit podrobnosti o kompatibilitě, postupujte takto:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na stránce **Přehled** nebo **dodržování předpisů** vyberte zásadu ve **stavu dodržování předpisů** , který _nedodržuje předpisy_.

1. Na kartě **Kompatibilita prostředků** na stránce **dodržování zásad** klikněte pravým tlačítkem myši nebo vyberte tři tečky prostředku ve **stavu dodržování předpisů** , který _nedodržuje předpisy_. Pak vyberte **Zobrazit podrobnosti o kompatibilitě**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Zobrazit podrobnosti kompatibility – možnost" border="false":::

1. V podokně **podrobností o dodržování předpisů** se zobrazují informace z posledního vyhodnocení prostředku na aktuální přiřazení zásad. V tomto příkladu se v poli **Microsoft. SQL/servery/verze** našla _12,0_ , zatímco definice zásady očekávala hodnotu _14,0_. Pokud prostředek z více důvodů není kompatibilní, každá z nich je uvedena v tomto podokně.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Podokno podrobností o dodržování předpisů a důvody pro nedodržování předpisů" border="false":::

   V případě definice zásady **auditIfNotExists** nebo **deployIfNotExists** obsahuje podrobnosti informace o vlastnosti **Details. Type** a všech volitelných vlastnostech. Seznam najdete v tématu [vlastnosti auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) a [vlastnosti deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Poslední vyhodnocený prostředek** je související prostředek z oddílu **podrobností** definice.

   Příklad částečné definice **deployIfNotExists** :

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Podokno podrobností o dodržování předpisů – * ifNotExists" border="false":::

> [!NOTE]
> Pokud chcete chránit data, když je hodnota vlastnosti _tajná_ , aktuální hodnota zobrazí hvězdičky.

Tyto podrobnosti vysvětlují, proč je prostředek aktuálně nekompatibilní, ale nezobrazují, kdy došlo ke změně prostředku, který způsobil, že se neshoduje s předpisy. Informace najdete v části o [historii změn (Preview)](#change-history) níže.

### <a name="compliance-reasons"></a>Důvody pro dodržování předpisů

V následující matrici je možné podle příslušné [podmínky](../concepts/definition-structure.md#conditions) v definici zásady mapovat tyto _důvody_ :

|Důvod | Podmínka |
|-|-|
|Aktuální hodnota musí obsahovat cílovou hodnotu jako klíč. |ContainsKey – nebo **ne** notContainsKey |
|Aktuální hodnota musí obsahovat cílovou hodnotu. |obsahuje nebo **není** notContains |
|Aktuální hodnota musí být stejná jako cílová hodnota. |je rovno nebo **není** notEquals |
|Aktuální hodnota musí být menší než cílová hodnota. |menší nebo **not** negreaterOrEqualsé |
|Aktuální hodnota musí být větší než nebo rovna cílové hodnotě. |greaterOrEquals nebo **méně** |
|Aktuální hodnota musí být větší než cílová hodnota. |větší nebo **not** nelessOrEquals |
|Aktuální hodnota musí být menší nebo rovna cílové hodnotě. |lessOrEquals nebo **ne** větší |
|Aktuální hodnota musí existovat. |neexistuje |
|Aktuální hodnota musí být v cílové hodnotě. |v nebo **ne** notIn |
|Aktuální hodnota musí být stejná jako cílová hodnota. |Like nebo **Not** notLike |
|V aktuální hodnotě se musí rozlišovat velká a malá písmena, která se shodují s cílovou hodnotou. |shoda nebo **not** nenotMatch |
|Aktuální hodnota musí rozlišovat velikost písmen, která neodpovídá cílové hodnotě. |matchInsensitively nebo **ne** notMatchInsensitively |
|Aktuální hodnota nesmí obsahovat cílovou hodnotu jako klíč. |notContainsKey nebo **ne** ContainsKey –|
|Aktuální hodnota nesmí obsahovat cílovou hodnotu. |notContains nebo **Not** Contains |
|Aktuální hodnota nesmí být rovna cílové hodnotě. |notEquals nebo **nerovná se** |
|Aktuální hodnota nesmí existovat. |**neexistuje**  |
|Aktuální hodnota nesmí být v cílové hodnotě. |notIn nebo **ne** v |
|Aktuální hodnota nesmí být stejná jako cílová hodnota. |notLike nebo **Not** like |
|Aktuální hodnota nesmí rozlišovat velká a malá písmena pro cílovou hodnotu. |notMatch nebo **neodpovídá** |
|Aktuální hodnota nesmí rozlišovat velká a malá písmena. |notMatchInsensitively nebo **ne** matchInsensitively |
|Žádné související prostředky neodpovídají podrobnostem o vlivu v definici zásady. |Prostředek typu, který je definován v **then. details. Type** a souvisí s prostředkem definovaným v části **if** pravidla zásad, neexistuje. |

## <a name="compliance-details-for-guest-configuration"></a>Podrobnosti o dodržování předpisů pro konfiguraci hosta

U zásad _auditIfNotExists_ v kategorii _Konfigurace hosta_ se ve virtuálním počítači může vyhodnotit víc nastavení a budete muset zobrazit podrobnosti o jednotlivých nastaveních. Pokud například provádíte audit pro seznam zásad hesel a jenom jeden z nich má _nevyhovující_stav, budete muset zjistit, které konkrétní zásady hesel nejsou kompatibilní a proč.

Je také možné, že nebudete mít přístup k virtuálnímu počítači přímo, ale potřebujete podávat zprávu o tom, proč virtuální počítač _nedodržuje předpisy_.

### <a name="azure-portal"></a>portál Azure

Začněte podle stejných kroků v části výše pro zobrazení podrobností o dodržování zásad.

V zobrazení podokna podrobností o dodržování předpisů klikněte na odkaz **naposledy vyhodnocený prostředek**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Zobrazit podrobnosti definice auditIfNotExists" border="false":::

Na stránce **přiřazení hosta** se zobrazí všechny dostupné podrobnosti o dodržování předpisů. Každý řádek v zobrazení představuje hodnocení, které bylo provedeno uvnitř počítače. Ve sloupci **důvod** se zobrazí fráze popisující, proč není přiřazení hostů _kompatibilní_. Pokud například provádíte audit zásad hesel, sloupec **důvod** by zobrazil text, včetně aktuální hodnoty pro každé nastavení.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Zobrazit podrobnosti o kompatibilitě" border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

Podrobnosti o kompatibilitě můžete zobrazit také z Azure PowerShell. Nejdřív se ujistěte, že máte nainstalovaný konfigurační modul hosta.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Aktuální stav všech přiřazení hostů pro virtuální počítač můžete zobrazit pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Chcete-li zobrazit pouze frázi _důvod_ , která popisuje, proč virtuální počítač _nedodržuje předpisy_, vraťte pouze podřízenou vlastnost důvod.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Můžete také vymezit historii dodržování předpisů pro přiřazení hostů v oboru pro daný počítač. Výstup z tohoto příkazu zahrnuje podrobnosti o každé sestavě pro virtuální počítač.

> [!NOTE]
> Výstup může vracet velký objem dat. Je doporučeno uložit výstup do proměnné.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Pro zjednodušení tohoto zobrazení použijte parametr **ShowChanged** . Výstup z tohoto příkazu obsahuje jenom sestavy, které následují změnu stavu dodržování předpisů.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"></a>Historie změn (Preview)

V rámci nové **verze Public Preview**jsou poslední 14 dní historie změn k dispozici pro všechny prostředky Azure, které podporují [odstranění režimu úplného režimu](../../../azure-resource-manager/templates/complete-mode-deletion.md). Historie změn poskytuje podrobnosti o tom, kdy byla zjištěna změna, a _vizuální rozdíl_ pro každou změnu. Detekce změn se aktivuje, když se přidají, odeberou nebo změní vlastnosti Azure Resource Manager.

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na stránce **Přehled** nebo **dodržování předpisů** vyberte zásadu v jakémkoli **stavu dodržování předpisů**.

1. Na kartě **Kompatibilita prostředků** na stránce **dodržování zásad** vyberte prostředek.

1. Na stránce **Kompatibilita prostředků** vyberte kartu **historie změn (Preview)** . Zobrazí se seznam zjištěných změn, pokud existují.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Karta historie změn Azure Policy na stránce dodržování předpisů prostředků" border="false":::

1. Vyberte jednu z zjištěných změn. _Rozdíly ve vizuálním_ zdroji se zobrazí na stránce **historie změn** .

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Změna vizuálního rozdílu ve Azure Policy historii změn na stránce Historie změn" border="false":::

_Vizuální rozdíl_ Aides při identifikaci změn prostředku. Zjištěné změny nemůžou souviset s aktuálním stavem dodržování předpisů daného prostředku.

Data historie změn poskytuje [Azure Resource Graph](../../resource-graph/overview.md). Chcete-li zadat dotaz na tyto informace mimo Azure Portal, přečtěte si téma [získání změn prostředků](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
