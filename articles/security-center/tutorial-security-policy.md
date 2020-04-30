---
title: Práce se zásadami zabezpečení | Microsoft Docs
description: Tento článek popisuje, jak pracovat se zásadami zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024876"
---
# <a name="working-with-security-policies"></a>Práce se zásadami zabezpečení

Tento článek vysvětluje, jak se konfigurují zásady zabezpečení, a jak je zobrazit v Security Center. 

## <a name="introduction-to-security-policies"></a>Úvod do zásad zabezpečení

Zásady zabezpečení definují požadovanou konfiguraci vašich úloh a pomáhají zajistit dodržování požadavků na zabezpečení vašich společností nebo regulačních orgánů.

Azure Security Center provedla doporučení zabezpečení založená na zvolených zásadách. Zásady Security Center jsou založené na iniciativách zásad vytvořených v Azure Policy. Pomocí [Azure Policy](../governance/policy/overview.md) můžete spravovat zásady a nastavovat zásady napříč skupinami pro správu a mezi několika předplatnými.

Security Center nabízí následující možnosti pro práci se zásadami zabezpečení:

* **Zobrazit a upravit předdefinované výchozí zásady** – Pokud povolíte Security Center, automaticky se přiřadí předdefinovaná iniciativa "ASC default" všem Security Center registrovaným předplatným (úrovně Free nebo Standard). Pokud chcete tento podnět přizpůsobit, můžete v něm povolit nebo zakázat jednotlivé zásady. V seznamu [integrovaných zásad zabezpečení](security-center-policy-definitions.md) se dozvíte, jaké možnosti jsou dostupné předem.

* **Přidat vlastní zásady** – Pokud chcete přizpůsobit iniciativy zabezpečení použité pro vaše předplatné, můžete to udělat v rámci Security Center. Pak se dostanou doporučení, pokud vaše počítače nevyhovují vytvořeným zásadám. Pokyny k sestavování a přiřazování vlastních zásad najdete v tématu [použití vlastních zásad zabezpečení](custom-security-policies.md).

* **Přidání zásad dodržování předpisů regulativního** úřadu – řídicí panel dodržování předpisů v Security Center zobrazuje stav všech posouzení v rámci vašeho prostředí v kontextu určitého standardu nebo nařízení (například Azure SNS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Další informace najdete v tématu [zlepšení dodržování legislativních předpisů](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Správa zásad zabezpečení

Zobrazení zásad zabezpečení ve službě Security Center:

1. Na řídicím panelu **Security Center** vyberte **zásady zabezpečení**.

    ![Okno Správa zásad](./media/security-center-policies/security-center-policy-mgt.png)

   Na obrazovce **Správa zásad** můžete zobrazit počet skupin pro správu, odběry a pracovní prostory i strukturu skupiny pro správu.

1. Vyberte předplatné nebo skupinu pro správu, jejichž zásady chcete zobrazit.

1. Zobrazí se stránka zásady zabezpečení pro toto předplatné nebo skupinu pro správu. Zobrazuje dostupné a přiřazené zásady.

   ![obrazovka zásady](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Pokud je popisek "g Zděděný" společně s výchozí zásadou, znamená to, že zásada byla přiřazena skupině pro správu a zděděna předplatným, které si prohlížíte.


1. Vyberte si z dostupných možností na této stránce:

    1. Pokud chcete pracovat s oborem zásad, vyberte **Přidat další standardy**. Další informace najdete v tématu [aktualizace balíčků s dynamickým dodržováním předpisů](update-regulatory-compliance-packages.md).

    1. Chcete-li přiřadit a spravovat vlastní iniciativy, vyberte možnost **Přidat vlastní iniciativy**. Další informace najdete v tématu [použití vlastních zásad zabezpečení](custom-security-policies.md).

    1. Pokud chcete zobrazit a upravit výchozí zásady, vyberte **Zobrazit efektivní zásady** a pokračujte podle popisu níže. 

       ![obrazovka zásady](./media/security-center-policies/policy-screen.png)
       
       Tato obrazovka **zásad zabezpečení** odráží akci provedenou zásadami přiřazenými k předplatnému nebo skupině pro správu, kterou jste vybrali.
       
       * Pomocí odkazů v horní části otevřete **přiřazení** zásady, které platí pro předplatné nebo skupinu pro správu. Tyto odkazy vám umožní přístup k přiřazení a úpravám nebo zakázání zásad. Pokud se například zobrazí, že konkrétní přiřazení zásad efektivně odmítá službu Endpoint Protection, použijte odkaz pro úpravu nebo zakázání zásad.
       
       * V seznamu zásad se můžete podívat na efektivní použití zásad pro vaše předplatné nebo skupinu pro správu. Zobrazí se nastavení všech zásad, které se vztahují k oboru, a zobrazí se kumulativní výsledek akcí provedených v této zásadě. Například pokud je v jednom přiřazení zásady zakázané, ale v jiném je nastavená na AuditIfNotExist, pak se Kumulativní efekt aplikuje na AuditIfNotExist. Vyšší aktivní efekt vždy má přednost.
       
       * Účinek zásad může být: Append, audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Další informace o tom, jak se efekty používají, najdete v tématu [vysvětlení efektů zásad](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Když si zobrazíte přiřazené zásady, uvidíte několik přiřazení a uvidíte, jak se každé přiřazení konfiguruje sami.


## <a name="who-can-edit-security-policies"></a>Kdo může upravovat zásady zabezpečení?

Zásady zabezpečení můžete upravovat prostřednictvím Azure Policyového portálu prostřednictvím REST API nebo pomocí Windows PowerShellu.

Security Center používá řízení přístupu na základě rolí (RBAC). To poskytuje předdefinované role, které se dají v Azure přiřadit uživatelům, skupinám a službám. Když uživatelé otevřou Security Center, uvidí jenom informace, které souvisejí s prostředky, ke kterým mají přístup. To znamená, že uživatelé mají přiřazenou roli *vlastník*, *Přispěvatel*nebo *Čtenář* k předplatnému prostředku. I tyto role jsou dvě konkrétní role Security Center:

- **Čtecí modul zabezpečení**: má práva k zobrazení Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale nemůže provádět změny.
- **Správce zabezpečení**: mají stejná oprávnění k zobrazení jako *Čtenář zabezpečení*a můžou také aktualizovat zásady zabezpečení a zrušit doporučení a upozornění.


## <a name="disable-security-policies"></a>Zakázat zásady zabezpečení
Pokud výchozí zásady zabezpečení generují doporučení, které není pro vaše prostředí relevantní, můžete je zastavit tím, že zakážete definici zásady, která posílá doporučení.
Další informace o doporučeních najdete v tématu [Správa doporučení zabezpečení](security-center-recommendations.md).

1. V Security Center v části **zásady & dodržování předpisů** vyberte **zásady zabezpečení**.

   ![Správa zásad](./media/tutorial-security-policy/policy-management.png)

2. Vyberte předplatné nebo skupinu pro správu, pro které chcete doporučení zakázat.

   > [!NOTE]
   > Pamatujte, že skupina pro správu uplatňuje zásady na svá předplatná. Pokud tedy zakážete zásady předplatného, přičemž toto předplatné patří do skupiny pro správu, která pořád používá stejné zásady, budete doporučení k zásadám nadále dostávat. Tyto zásady budou nadále uplatňovány z úrovně správy a doporučení se budou pořád generovat.

1. Vyberte **Zobrazit efektivní zásady**.

   ![zakázat zásadu](./media/tutorial-security-policy/view-effective-policy.png)

1. Vyberte přiřazenou zásadu.

   ![zakázat zásadu](./media/tutorial-security-policy/security-policy.png)

1. V části **parametry** vyhledejte zásadu, která vyvolá doporučení, které chcete zakázat, a v rozevíracím seznamu vyberte možnost **zakázáno** .

   ![zakázat zásadu](./media/tutorial-security-policy/disable-policy.png)

1. Vyberte **Uložit**.

   > [!NOTE]
   > Změny zásad, které se projeví, můžou trvat až 12 hodin.



## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o zásadách zabezpečení. Související informace najdete v následujících článcích:

* Pokyny k nastavení zásad pomocí prostředí PowerShell najdete v tématu [rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí modulu Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Pokyny k úpravám zásad zabezpečení v Azure Policy najdete v tématu [Vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

* Pokyny, jak nastavit zásadu napříč předplatnými nebo ve skupinách pro správu pomocí Azure Policy, najdete v tématu [co je Azure Policy?](../governance/policy/overview.md)