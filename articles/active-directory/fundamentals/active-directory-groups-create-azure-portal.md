---
title: Vytvoření základní skupiny a přidání členů – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak vytvořit základní skupiny pomocí služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47c742e4f6d2ba8a96e9897f43231e509e8aa63
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476072"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory
Základní skupinu můžete vytvořit na portálu Azure Active Directory (Azure AD). Pro účely tohoto článku přidá základní skupinu do jednoho prostředku vlastník prostředku (správce) a skupina zahrnuje konkrétní členy (zaměstnance), kteří k danému prostředku potřebují přístup. Složitější scénáře, které zahrnují dynamická členství a vytváření pravidel, najdete v [dokumentaci ke správě uživatelů ve službě Azure Active Directory](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Vytvoření základní skupiny a přidání členů
Vytvořit základní skupinu a přidat do ní členy můžete současně.

### <a name="to-create-a-basic-group-and-add-members"></a>Vytvoření základní skupiny a přidání členů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, **Skupiny** a pak vyberte **Nová skupina**.

    ![Stránka Azure AD, zobrazuje se skupinami](media/active-directory-groups-create-azure-portal/group-full-screen.png)

3. Na stránce **Skupina** vyplňte požadované informace.

    ![Stránka nové skupiny s vyplněnými ukázkovými informacemi](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Typ skupiny (vyžadováno)** : Vyberte předdefinovaný typ skupiny. To zahrnuje:
        
       - **Zabezpečení.** Slouží ke správě členů a přístupu počítače ke sdíleným prostředkům u skupiny uživatelů. Můžete například vytvořit skupinu zabezpečení pro konkrétní zásady zabezpečení. Tímto způsobem můžete udělit sadu oprávnění všem členům najednou a nemusíte přidávat oprávnění členům jednotlivě. Další informace o správě přístupu k prostředkům najdete v článku o [správě přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md).
        
       - **Office 365**: Poskytuje možnosti spolupráce tím, že umožňuje členům přístup ke sdílené poštovní schránce, kalendáři, souborům, sharepointovému webu a dalším prostředkům. Tato možnost vám také umožňuje udělit přístup ke skupině lidem mimo vaši organizaci. Další informace o skupinách Office 365 najdete v článku o [skupinách Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Název skupiny (vyžadováno)** : Přidejte název skupiny. Zvolte název, který si zapamatujete a který bude dávat smysl. Chcete-li zjistit, pokud název se již používá pro jiné skupiny se provede kontrola. Pokud název se už používá, aby předešel duplicitní, budete vyzváni k úpravě název vaší skupiny.

   - **Popis skupiny**: Volitelně můžete přidat také popis skupiny.

   - **Typ členství (vyžadováno)** : Vyberte předdefinovaný typ členství. To zahrnuje:

     - **Přiřazeno**: Umožňuje přidat konkrétní uživatele jako členy této skupiny s jedinečnými oprávněními. Pro účely tohoto článku používáme tuto možnost.

     - **Dynamický uživatel**: Umožňuje použít pravidla dynamického členství automaticky přidávat a odebírat členy. Pokud se změní atributy člena, systém se podívá do pravidel dynamických skupin u adresáře a zjistí, zda člen splňuje požadavky pravidla (je přidán) nebo zda již požadavky pravidel nesplňuje (je odebrán).

     - **Dynamické zařízení**: Umožňuje použít dynamická skupinová pravidla a automaticky tak přidávat a odebírat zařízení. Pokud se změní atributy zařízení, systém se podívá do pravidel dynamických skupin u adresáře a zjistí, zda zařízení splňuje požadavky pravidla (je přidáno) nebo zda již požadavky pravidel nesplňuje (je odebráno).

       >[!Important]
       >Dynamickou skupinu můžete vytvořit buď pro zařízení, nebo uživatele, ale nikoli pro obojí. Stejně tak nemůžete vytvořit skupinu zařízení na základě atributů vlastníků zařízení. Pravidla členství zařízení mohou odkazovat pouze na atributy zařízení. Další informace o vytvoření dynamické skupiny uživatelů a zařízení, najdete v části [vytvoření dynamické skupiny a zkontrolovat stav](../users-groups-roles/groups-create-rule.md).

4. Vyberte **Vytvořit**.

    Vaše skupina je vytvořená a připravená na přidání členů.

5. Na stránce **Skupina** vyberte oblast **Členové** a potom na stránce **Vybrat členy** začněte vyhledávat členy, které chcete přidat do skupiny.

    ![Výběr členů pro skupinu během procesu vytváření skupiny](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

6. Po přidání členů zvolte **Vybrat**.

    Stránka **Přehled skupiny** se aktualizuje a zobrazí počet členů, kteří jsou nyní přidaní do skupiny.

    ![Stránka Přehled skupiny se zvýrazněným počtem členů](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Zapněte nebo vypněte Uvítacího e-mailu

Při vytvoření, všechny nové skupiny Office 365, zda se dynamické nebo statické členství, úvodní oznámení odesláno všem uživatelům, kteří jsou přidány do skupiny. Když se změní libovolné atributy uživatele nebo zařízení, všechna dynamická pravidla skupin v organizaci se zpracovávají potenciální změny členství. Uživatelé, kteří jsou přidány také obdrží úvodní oznámení. Můžete ji na toto chování vypnout [Powershellu v Exchangi](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Další postup
Teď, když máte přidanou skupinu a alespoň jednoho uživatele, můžete provést tyto akce:

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Správa členství ve skupinách](active-directory-groups-membership-azure-portal.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Správa přístupu k aplikacím SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)

- [Správa skupin pomocí powershellových příkazů](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
