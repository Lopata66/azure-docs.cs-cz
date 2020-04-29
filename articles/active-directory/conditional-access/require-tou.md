---
title: Podmíněný přístup vyžaduje podmínky použití-Azure Active Directory
description: V tomto rychlém startu se dozvíte, jak můžete vyžadovat, aby se podmínky použití přijaly před tím, než se přístup k vybraným cloudovým aplikacím udělí Azure Active Directory podmíněný přístup.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74380107"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Rychlý Start: vyžadovat, aby se před přístupem ke cloudovým aplikacím přijaly podmínkami použití

Než budete mít přístup k určitým cloudovým aplikacím ve vašem prostředí, možná budete chtít získat souhlas od uživatelů formou přijetí podmínek použití (podmínky použití). Podmíněný přístup Azure Active Directory (Azure AD) poskytuje:

- Jednoduchá metoda konfigurace podmínky použití
- Možnost vyžadovat přijetí podmínek použití prostřednictvím zásad podmíněného přístupu  

V tomto rychlém startu se dozvíte, jak nakonfigurovat [zásadu podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) , která vyžaduje přijetí podmínky použití pro vybranou cloudovou aplikaci ve vašem prostředí.

![Vytvoření zásad](./media/require-tou/5555.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto rychlém startu budete potřebovat:

- **Přístup k edici Azure AD Premium** – podmíněný přístup Azure AD je Azure AD Premium funkce.
- **Testovací účet s názvem Isabella Simonsen** – Pokud nevíte, jak vytvořit testovací účet, podívejte se na téma [Přidání cloudových uživatelů](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Otestování přihlášení

Cílem tohoto kroku je získat dojem o přihlášení bez zásad podmíněného přístupu.

**Testování přihlášení:**

1. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com/) jako Isabella Simonsen.
1. Odhlaste se.

## <a name="create-your-terms-of-use"></a>Vytvoření podmínek použití

V této části najdete postup vytvoření ukázkové podmínky použití. Při vytváření podmínky použití vyberte hodnotu pro **vymáhání pomocí šablon zásad podmíněného přístupu**. Když vyberete **vlastní zásady** , otevře se dialogové okno, ve kterém se vytvoří nové zásady podmíněného přístupu hned po vytvoření podmínky použití.

**Vytvoření podmínek použití:**

1. V aplikaci Microsoft Word vytvořte nový dokument.
1. Zadejte **Moje podmínkami použití**a pak dokument uložte do počítače jako **mytou. PDF**.
1. Přihlaste se ke svému [Azure Portal](https://portal.azure.com) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. V Azure Portal klikněte v levém navigačním panelu na možnost **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Na stránce **Azure Active Directory** v části **zabezpečení** klikněte na **podmíněný přístup**.

   ![Podmíněný přístup](./media/require-tou/03.png)

1. V části **Spravovat** klikněte na **podmínky použití**.

   ![Podmínky použití](./media/require-tou/04.png)

1. V nabídce v horní části klikněte na **nové výrazy**.

   ![Podmínky použití](./media/require-tou/05.png)

1. Na stránce **nové požadavky na použití** :

   ![Podmínky použití](./media/require-tou/112.png)

   1. Do textového pole **název** zadejte **My podmínky použití**.
   1. Do textového pole **Zobrazovaný název** zadejte **My podmínky použití**.
   1. Nahrajte vaše požadavky na použití souboru PDF.
   1. Jako **jazyk**vyberte **angličtinu**.
   1. Jak **vyžadují, aby uživatelé rozšířili podmínek použití**, vyberte **zapnuto**.
   1. Jako **Vynutilit šablony zásad podmíněného přístupu**vyberte **vlastní zásady**.
   1. Klikněte na **Vytvořit**.

## <a name="create-your-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

V této části se dozvíte, jak vytvořit požadované zásady podmíněného přístupu. Scénář v tomto rychlém startu používá:

- Azure Portal jako zástupný symbol pro cloudovou aplikaci, která vyžaduje přijetí vaší podmínky použití. 
- Ukázkový uživatel, který otestuje zásady podmíněného přístupu.  

V zásadách nastavte:

| Nastavení | Hodnota |
| --- | --- |
| Uživatelé a skupiny | Isabella Simonsen |
| Cloudové aplikace | Správa Microsoft Azure |
| Udělení přístupu | Moje podmínky použití |

![Vytvoření zásad](./media/require-tou/1234.png)

**Konfigurace zásad podmíněného přístupu:**

1. Na **nové** stránce zadejte do textového pole **název** text **vyžadovat podmínky použití pro Isabella**.

   ![Název](./media/require-tou/71.png)

1. V části **přiřazení** klikněte na **Uživatelé a skupiny**.

   ![Uživatelé a skupiny](./media/require-tou/06.png)

1. Na stránce **Uživatelé a skupiny** :

   ![Uživatelé a skupiny](./media/require-tou/24.png)

   1. Klikněte na **Vybrat uživatele a skupiny**a pak vyberte **Uživatelé a skupiny**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Isabella Simonsen**a pak klikněte na **Vybrat**.
   1. Na stránce **Uživatelé a skupiny** klikněte na **Hotovo**.
1. Klikněte na **cloudové aplikace**.

   ![Cloudové aplikace](./media/require-tou/08.png)

1. Na stránce **cloudové aplikace** :

   ![Vybrat cloudové aplikace](./media/require-tou/26.png)

   1. Klikněte na **vybrat aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Microsoft Azure Management**a pak klikněte na **Vybrat**.
   1. Na stránce **cloudové aplikace** klikněte na **Hotovo**.
1. V části **řízení přístupu** klikněte na **udělit**.

   ![Řízení přístupu](./media/require-tou/10.png)

1. Na stránce **udělení** :

   ![Oprávnění](./media/require-tou/111.png)

   1. Vyberte **udělit přístup**.
   1. Vyberte **Moje podmínky použití**.
   1. Klikněte na **Vybrat**.
1. V části **Povolit zásady** klikněte **na zapnuto**.

   ![Povolení zásady](./media/require-tou/18.png)

1. Klikněte na **Vytvořit**.

## <a name="evaluate-a-simulated-sign-in"></a>Vyhodnotit simulované přihlašování

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít zjistit, jestli funguje podle očekávání. Jako první krok použijte nástroj Zásady podmíněného přístupu, který je v případě, že chcete simulovat přihlášení testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.  

Pro inicializaci nástroje pro vyhodnocování zásad **What If** nastavte:

- **Isabella Simonsen** jako uživatel
- **Správa Microsoft Azure** jako cloudová aplikace

Kliknutím na **What If** se vytvoří sestava simulace, která zobrazuje:

- **Vyžadovat podmínky použití pro Isabella** v rámci **zásad, které se použijí**
- **Moje podmínky použití** jako **udělení ovládacích prvků**.

![Nástroj Policy if](./media/require-tou/79.png)

**Vyhodnocení zásad podmíněného přístupu:**

1. Na stránce [zásady podmíněného přístupu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klikněte v nabídce v horní části na **What If**.  

   ![Citlivostní analýza](./media/require-tou/14.png)

1. Klikněte na **Uživatelé**, vyberte **Isabella Simonsen**a pak klikněte na **Vybrat**.

   ![Uživatel](./media/require-tou/15.png)

1. Výběr cloudové aplikace:

   ![Cloudové aplikace](./media/require-tou/16.png)

   1. Klikněte na **cloudové aplikace**.
   1. Na **stránce cloudové aplikace**klikněte na **vybrat aplikace**.
   1. Klikněte na **Vybrat**.
   1. Na stránce **Vybrat** vyberte **Microsoft Azure Management**a pak klikněte na **Vybrat**.
   1. Na stránce cloudové aplikace klikněte na **Hotovo**.
1. Klikněte na **What If**.

## <a name="test-your-conditional-access-policy"></a>Testování zásad podmíněného přístupu

V předchozí části jste se dozvěděli, jak vyhodnotit simulované přihlašování. Kromě simulace byste měli také otestovat zásady podmíněného přístupu, aby se zajistilo, že funguje podle očekávání.

Pokud chcete zásady otestovat, zkuste se přihlásit ke svému [Azure Portal](https://portal.azure.com) pomocí účtu testování **Simonsen Isabella** . Mělo by se zobrazit dialogové okno, které vyžaduje, abyste přijali podmínky použití.

![Podmínky použití](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte testovacího uživatele a zásadu podmíněného přístupu:

- Pokud nevíte, jak odstranit uživatele Azure AD, přečtěte si téma [odstranění uživatelů ze služby Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Pokud chcete zásadu odstranit, vyberte zásadu a pak na panelu nástrojů Rychlý přístup klikněte na **Odstranit** .

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/33.png)

- Pokud chcete odstranit svoje podmínkami použití, vyberte ji a pak na panelu nástrojů nahoře klikněte na **Odstranit výrazy** .

    ![Ověřování pomocí služby Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyžadovat MFA pro konkrétní aplikace](app-based-mfa.md)
> [blokovat přístup, když se zjistí riziko relace](app-sign-in-risk.md)
