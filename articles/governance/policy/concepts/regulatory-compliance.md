---
title: Dodržování legislativních předpisů v definicích iniciativ
description: Popisuje způsob použití definice iniciativ k seskupení zásad podle regulativní domény, jako je Access Control, Správa konfigurace a další.
ms.date: 05/11/2020
ms.topic: conceptual
ms.openlocfilehash: 3bcc876cf8fd608d700e39bda11c94395beace80
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84205978"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Dodržování předpisů v Azure Policy

Dodržování legislativních předpisů v Azure Policy poskytuje předdefinované definice iniciativ pro zobrazení seznamu **místních a** **kompatibilních domén** na základě zodpovědnosti (_zákazníka_, _Microsoft_, _Shared_).
U odpovědných ovládacích prvků Microsoftu poskytujeme další podrobnosti o našich výsledcích auditu na základě ověření třetí strany a našich prováděcích detailů k dosažení tohoto dodržování předpisů.
Odpovědné ovládací prvky společnosti Microsoft jsou `type` [statické](./definition-structure.md#type).

> [!NOTE]
> Dodržování legislativních předpisů je funkce ve verzi Preview. V případě aktualizovaných vestavěných iniciativ jsou iniciativy ovládacími prvky mapovány na odpovídající Standard dodržování předpisů. Stávající standardní iniciativy dodržování předpisů se aktualizují, aby podporovaly dodržování legislativních předpisů.

## <a name="regulatory-compliance-defined"></a>Definice dodržování předpisů

Dodržování legislativních předpisů je postavené na části [seskupení](./initiative-definition-structure.md#policy-definition-groups) definice iniciativy. V vestavěných případech každé seskupení v definici iniciativy definuje název (**ovládací prvek**), kategorii (**doménu dodržování předpisů**) a poskytuje odkaz na objekt [policyMetadata](./initiative-definition-structure.md#metadata-objects) , který obsahuje informace o tomto **ovládacím prvku**. Definice iniciativy dodržování předpisů v legislativě musí mít `category` vlastnost nastavenou na **dodržování předpisů**. V opačném případě standardní definice iniciativ, iniciativa dodržování předpisů podporuje [parametry](./initiative-definition-structure.md#parameters) pro vytváření dynamických přiřazení.

Zákazníci mohou vytvořit své vlastní iniciativy týkající se dodržování legislativních předpisů. Tyto definice mohou být původní nebo zkopírované ze stávajících integrovaných definic. Pokud jako referenci použijete integrovanou definici iniciativy dodržování předpisů v legislativě, doporučujeme monitorovat zdroj definic dodržování předpisů v [Azure Policy úložišti GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Pokud chcete propojit vlastní iniciativu dodržování předpisů pro Azure Security Center řídicím panelem, přečtěte si téma [Azure Security Center – použití vlastních zásad zabezpečení](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Dodržování předpisů v portálu

Po vytvoření definice iniciativy se [skupinami](./initiative-definition-structure.md#policy-definition-groups)obsahuje stránka podrobnosti o **dodržování předpisů** na portálu pro tuto iniciativu Další informace. 

Nové kartě jsou **ovládací prvky** přidány na stránku. Filtrování je dostupné v případě, že jsou definice v doméně a zásadách **dodržování předpisů** seskupené podle `title` pole z objektu **policyMetadata** . Každý řádek představuje **ovládací prvek** , který zobrazuje stav dodržování předpisů, **doména dodržování předpisů** je součástí, informace o zodpovědnosti a počet nekompatibilních a nekompatibilních definic zásad, které se **řídí**.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Ukázka přehledu dodržování legislativních předpisů pro předdefinovanou definici NIST SP 800-53 R4.":::

Výběrem **ovládacího prvku** se otevře stránka s podrobnostmi o tomto ovládacím prvku. **Přehled** obsahuje informace z `description` a `requirements` . Na kartě **zásady** jsou všechny definice jednotlivých zásad v iniciativě, které přispívají k tomuto **ovládacímu prvku**. Karta **Kompatibilita prostředků** poskytuje podrobné zobrazení jednotlivých prostředků, které jsou vyhodnocovány pomocí zásad členů aktuálně zobrazeného **ovládacího prvku**.

> [!NOTE]
> Typ hodnocení **spravovaný společností Microsoft** je pro definici [statické](./definition-structure.md#type) zásady `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Ukázka definic zásad dodržování předpisů v rámci kontroly nad hranicí domény systému a ochrany v doméně pro ochranu komunikace v rámci předdefinované definice NIST SP 800-53 R4.":::

Na stejné stránce **ovládacího prvku** se změna na kartu **Kompatibilita prostředků** zobrazí všechny prostředky, které tyto definice zásad tohoto **ovládacího prvku**obsahují. Filtry jsou k dispozici pro název nebo ID, stav dodržování předpisů, typ prostředku a umístění.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Ukázka prostředků dodržování předpisů, které jsou ovlivněné definicemi zásad v rámci kontroly nad rámec ochrany v doméně systému a komunikace v doméně ochrany NIST SP 800-53 R4 předdefinovaná definice.":::

## <a name="regulatory-compliance-in-sdk"></a>Dodržování předpisů v sadě SDK

Pokud je v definici iniciativy povolené dodržování legislativních předpisů, pak každá z nich zaznamenává záznam, události a sady SDK stavů zásad a vrátí další vlastnosti. Tyto další vlastnosti jsou seskupené podle stavu dodržování předpisů a poskytují informace o tom, kolik skupin je v jednotlivých stavech.

Následující kód je příkladem přidaných výsledků `summarize` volání:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Další kroky

- Zobrazení [struktury definice iniciativy](./initiative-definition-structure.md)
- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [Vysvětlení efektů zásad](./effects.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
