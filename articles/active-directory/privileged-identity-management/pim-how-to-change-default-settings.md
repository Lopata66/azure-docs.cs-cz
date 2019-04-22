---
title: Konfigurace nastavení role Azure AD v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cb8944578caf00fac5ca430e411f044a875f6af
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492273"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Konfigurace nastavení role Azure AD v PIM

Správce privilegovaných rolí, odkud můžete přizpůsobit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) ve své organizaci, včetně změnou prostředí pro uživatele, který je aktivaci přiřazení oprávněné role.

## <a name="open-role-settings"></a>Otevřete nastavení role

Postupujte podle těchto kroků a otevřete nastavení pro role Azure AD.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role Azure AD**.

1. Klikněte na tlačítko **nastavení**.

    ![Role Azure AD – nastavení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Klikněte na tlačítko **role**.

1. Klikněte na roli, jehož nastavení chcete konfigurovat.

    ![Role Azure AD – nastavení role](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stránce nastavení pro každou roli existuje několik nastavení, která můžete konfigurovat. Tato nastavení ovlivní jenom uživatele, kteří jsou **oprávněné** přiřazení, ne **trvalé** přiřazení.

## <a name="activations"></a>Aktivace

Použití **aktivací** posuvník nastavit maximální dobu v hodinách, že role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí 1 až 72 hodin.

## <a name="notifications"></a>Oznámení

Použití **oznámení** přepínač k určení, jestli správci obdrží e-mailová oznámení, když se aktivují role. To může být užitečné pro zjišťování nezákonných nebo neoprávněné aktivace.

Pokud je nastavena na **povolit**, oznámení se odesílají do:

- Správce privilegované role
- Správce zabezpečení
- Globální správce

Další informace najdete v tématu [e-mailová oznámení v PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Lístek incidentu nebo žádosti

Použití **lístek incidentu nebo žádosti** přepínač k určení, jestli se vyžaduje oprávněné správce zahrnovat číslo lístku, pokud se aktivovat svoje role. To může být užitečné, když provádíte auditování přístupu k roli.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Použití **ověřování službou Multi-Factor Authentication** přepínač k určení, jestli se má vyžadovat, aby uživatelé ověřit svoji identitu pomocí MFA, před aktivací jejich role. Mají pouze k ověření tohoto jednou na relaci, není vždy, když se aktivovat roli. Existují dva tipy, jak vzít v úvahu při povolení vícefaktorového ověřování:

* Uživatelé, kteří mají účty Microsoft pro jejich e-mailové adresy (obvykle @outlook.com, ale ne vždy) nejde zaregistrovat pro Azure MFA. Pokud chcete přiřazení rolí uživatelům s účty Microsoft, by měl daly trvalých správců nebo zakázání MFA pro danou roli.
* Vícefaktorové ověřování pro vysoce privilegované role nelze zakázat pro službu Azure AD a Office 365. Toto je funkce bezpečný přístup z více, protože pracovníci v těchto rolích by měly být pečlivě chráněné:  
  
  * Správce fakturace
  * Správce cloudových aplikací
  * Správce dodržování předpisů
  * Správce podmíněného přístupu
  * Správce služeb CRM
  * Schvalovatel přístupu ke Customer LockBoxu
  * Zapisovače do adresáře
  * Správce Exchange
  * Globální správce
  * Správce služby Information Protection
  * Správce služby Intune
  * Správce služeb Power BI
  * Správce privilegované role
  * Správce zabezpečení
  * Správce služeb SharePointu
  * Správce Skypu pro firmy
  * Správce uživatelů

Další informace najdete v tématu [vícefaktorové ověřování (MFA) a PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Vyžadovat schválení

Pokud chcete k aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Nastavte **vyžadovat schválení** přepnout na **povoleno**. V podokně rozšíří možnosti vybrat schvalovatele.

    ![Role Azure AD – nastavení – vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Pokud jste **neměňte** zadejte jakékoli schvalovatelé, výchozí schvalovatelé se správci privilegovaných rolí. Správce privilegovaných rolí by vyžadoval schválení **všechny** žádosti o aktivaci pro tuto roli.

1. Chcete-li zadat schvalovatele, klikněte na tlačítko **vybrat schvalovatele**.

    ![Role Azure AD – nastavení – vyžadovat schválení](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Vyberte jednoho nebo více schvalovatelů a potom klikněte na tlačítko **vyberte**. Můžete vybrat uživatele nebo skupiny. Doporučuje se aspoň 2 schvalovatele. Schvalování sebe sama není povolený.

    Vyberte požadované možnosti se zobrazí v seznamu vybraných schvalovatelů.

1. Po zadání všech vašich nastavení role, klikněte na tlačítko **Uložit** uložte provedené změny.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Další postup

- [Přiřazení role Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Konfigurace výstrah zabezpečení pro role Azure AD v PIM](pim-how-to-configure-security-alerts.md)
