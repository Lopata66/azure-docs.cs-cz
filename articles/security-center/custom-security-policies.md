---
title: Vytváření vlastních zásad zabezpečení v Azure Security Center | Microsoft Docs
description: Definice vlastních zásad Azure monitorují Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/03/2020
ms.author: memildin
ms.openlocfilehash: 8d2b43ab57ea7a3b1dc1d13bcdea9932ccecb9dc
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559027"
---
# <a name="using-custom-security-policies"></a>Používání vlastních zásad zabezpečení

Kvůli lepšímu zabezpečení systémů a prostředí Azure Security Center vygeneruje doporučení zabezpečení. Tato doporučení jsou založená na osvědčených postupech, které jsou součástí obecných výchozích zásad zabezpečení dodaných všem zákazníkům. Můžou také pocházet ze zkušeností Security Center v oboru a zákonných standardů.

Pomocí této funkce můžete přidat *vlastní iniciativy.* Pak dostanete doporučení, pokud vaše prostředí nedodržuje zásady, které vytvoříte. Všechny vlastní iniciativy, které vytvoříte, se zobrazí společně s integrovanou iniciativou na řídicím panelu dodržování předpisů, jak je popsáno v kurzu [zlepšení dodržování předpisů](security-center-compliance-dashboard.md).

Jak je popsáno v [dokumentaci k Azure Policy](../governance/policy/concepts/definition-structure.md#definition-location), když zadáte umístění pro vlastní iniciativu, musí se jednat o skupinu pro správu nebo o předplatné. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Přidání vlastní iniciativy do předplatného 

1. Na bočním panelu Security Center otevřete stránku **zásady zabezpečení** .

1. Vyberte předplatné nebo skupinu pro správu, ke kterým chcete přidat vlastní podnět.

    [![Výběr předplatného, pro které vytvoříte vlastní zásadu](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Je nutné přidat vlastní standardy na úrovni předplatného (nebo vyšší), aby je bylo možné vyhodnotit a zobrazit v Security Center. 
    >
    > Když přidáte vlastní Standard, přiřadí se k tomuto oboru *iniciativa* . Proto doporučujeme vybrat nejširší rozsah vyžadovaný pro toto přiřazení.

1. Na stránce Zásady zabezpečení v části vaše vlastní iniciativy klikněte na **Přidat vlastní iniciativu**.

    [![Klikněte na přidat vlastní iniciativu.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zobrazí se následující stránka:

    ![Vytvořit nebo přidat zásadu](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stránce přidat vlastní podněty si Projděte seznam vlastních zásad, které jsou už ve vaší organizaci vytvořené. Pokud se vám zobrazí ten, který chcete přidružit k předplatnému, klikněte na **Přidat**. Pokud v seznamu, který vyhovuje vašim potřebám, není iniciativa, tento krok přeskočte.

1. Vytvoření nové vlastní iniciativy:

    1. Klikněte na **vytvořit nový**.
    1. Zadejte umístění a název definice.
    1. Vyberte zásady, které chcete zahrnout, a klikněte na **Přidat**.
    1. Zadejte požadované parametry.
    1. Klikněte na **Uložit**.
    1. Na stránce přidat vlastní iniciativy klikněte na aktualizovat. Vaše nová iniciativa bude zobrazená jako dostupná.
    1. Klikněte na **Přidat** a přiřaďte ho k vašemu předplatnému.

    > [!NOTE]
    > Vytváření nových iniciativ vyžaduje přihlašovací údaje vlastníka předplatného. Další informace o rolích Azure najdete [v tématu oprávnění v Azure Security Center](security-center-permissions.md).

    Vaše nová iniciativa se projeví a dopad můžete zobrazit následujícími dvěma způsoby:

    * Na bočním panelu Security Center v části zásady & Kompatibilita vyberte **dodržování předpisů**. Otevře se řídicí panel dodržování předpisů, ve kterém se zobrazí vaše nová vlastní iniciativa spolu s integrovanými iniciativami.
    
    * Začnete dostávat doporučení, pokud vaše prostředí nedodržuje zásady, které jste definovali.

1. Pokud si chcete zobrazit výsledná doporučení pro vaši zásadu, otevřete stránku doporučení kliknutím na **doporučení** na postranním panelu. Doporučení se zobrazí s popiskem "vlastní" a budou k dispozici do přibližně jedné hodiny.

    [![Vlastní doporučení](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Vylepšete vlastní doporučení s podrobnými informacemi

Integrovaná doporučení dodaná s Azure Security Center obsahují podrobnosti, jako jsou úrovně závažnosti a pokyny k nápravě. Pokud chcete přidat tento typ informací k vašim vlastním doporučením, aby se zobrazil v Azure Portal nebo odkudkoli přistupujete k doporučením, budete muset použít REST API. 

Existují dva typy informací, které lze přidat:

- **RemediationDescription** – řetězec
- **Závažnost** – výčet [nízká, střední, vysoká]

Metadata by měla být přidána do definice zásady pro zásadu, která je součástí vlastní iniciativy. Měla by být ve vlastnosti "securityCenter", jak je znázorněno níže:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Níže je uveden příklad vlastní zásady, včetně vlastnosti metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Další příklad použití vlastnosti securityCenter naleznete v [této části dokumentace k REST API](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit vlastní zásady zabezpečení. 

Další související materiály najdete v následujících článcích: 

- [Přehled zásad zabezpečení](tutorial-security-policy.md)
- [Seznam předdefinovaných zásad zabezpečení](./policy-reference.md)