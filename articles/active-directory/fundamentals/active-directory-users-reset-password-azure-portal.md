---
title: Resetovat heslo uživatele – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak resetovat heslo uživatele pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 382ffab090613a2b14ce710a45be10b4c36e5222
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561736"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Resetovat heslo uživatele pomocí služby Azure Active Directory

Jako správce můžete resetovat heslo uživatele, pokud zapomenete heslo, pokud uživatel získá uzamčení a vyloučení z zařízení nebo uživatele nebylo doručeno heslo.

>[!Note]
>Pokud váš tenant Azure AD je domovský adresář pro uživatele, nebude možné obnovit své heslo. To znamená, že pokud se uživatel přihlašuje pro vaši organizaci pomocí účtu z jiné organizace, účet Microsoft nebo účet Google, nebudete mít k resetování hesla.<br><br>Pokud váš uživatel má zdroj autority jako Windows Server Active Directory, pouze budete moct resetovat heslo, pokud jste zapnuli zpětný zápis hesla.<br><br>Pokud váš uživatel má zdroj autority jako externí služby Azure AD, nebude možné resetovat heslo. Jenom na uživatele nebo správce v externí službě Azure AD můžou resetovat heslo.

>[!Note]
>Pokud nejste správcem a místo toho hledáte pokyny ohledně toho, jak resetovat vlastní heslo pracovního nebo školního, přečtěte si téma [obnovit heslo pracovního nebo školního](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Resetování hesla.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele nebo správce hesel. Další informace o dostupných rolí najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Vyberte **Azure Active Directory**vyberte **uživatelé**, vyhledejte a vyberte uživatele, který potřebuje obnovení a pak vyberte **resetovat heslo**.

    **Alain Charon – profil** se zobrazí stránka s **resetovat heslo** možnost.

    ![Stránka profilu uživatele, se zvýrazněnou možností resetování hesla](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. V **resetovat heslo** stránce **resetovat heslo**.

    Dočasné heslo je automaticky generovaná pro daného uživatele.

4. Zkopírujte heslo a předat uživateli. Uživatel muset změnit heslo při další proces přihlašování.

    >[!Note]
    >Dočasné heslo je platné stále. Při příštím přihlášení uživatele hesla budou i nadále fungovat, bez ohledu na to kolik času uplynulo od vygeneroval dočasné heslo.

## <a name="next-steps"></a>Další postup

Poté, co jsme resetovali heslo uživatele, můžete provést následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidání nebo změně informací profilu](active-directory-users-profile-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

Nebo můžete provést složitější uživatelské scénáře, jako je například přiřazení delegáty, pomocí zásad, sdílení a uživatelské účty. Další informace o dalších dostupných akcí najdete v tématu [dokumentace ke službě Azure Active Directory uživatele management](../users-groups-roles/index.yml).
