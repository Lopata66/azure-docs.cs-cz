---
title: Rychlý start k přístupu a vytvořit nového tenanta – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak najít Azure Active Directory a jak vytvořit nového tenanta pro vaši organizaci.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fafa3974eb01b36015254307ba1a52a9bc221da
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492197"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Rychlý start: Vytvoření nového tenanta v Azure Active Directory
Pomocí portálu Azure Active Directory (Azure AD) můžete provádět všechny úlohy správy, včetně vytvoření nového tenanta pro vaši organizaci. 

V tomto rychlém startu se dozvíte, jak se dostat na web Azure Portal a do služby Azure Active Directory a jak vytvořit základního tenanta pro vaši organizaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com/) vaší organizace pomocí účtu globálního správce.

![Azure portal obrazovky s možností Azure AD](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Vytvoření nového tenanta pro vaši organizaci
Po přihlášení k webu Azure Portal můžete vytvořit nového tenanta pro svou organizaci. Váš nový tenant reprezentuje vaši organizaci a pomáhá vám spravovat konkrétní instanci cloudových služeb společnosti Microsoft pro vaše interní a externí uživatele.

### <a name="to-create-a-new-tenant"></a>Vytvoření nového tenanta
1. Vyberte **vytvořit prostředek**vyberte **Identity**a pak vyberte **Azure Active Directory**.

    Zobrazí se stránka **Vytvořit adresář**.

    ![Stránka pro vytvoření služby Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

2.  Na stránce **Vytvořit adresář** zadejte následující informace:
    
    - Do pole _Název organizace_ zadejte **Contoso**.

    - Do pole _Počáteční název domény_ zadejte **Contoso**.

    - V poli _Země nebo oblast_ ponechte možnost **USA**.

3. Vyberte **Vytvořit**.

Váš nový tenant se vytvoří s doménu contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tuto aplikaci nebudete používat, můžete tenanta odstranit provedením následujících kroků:

- Vyberte **Azure Active Directory** a pak na stránce **Contoso - přehled** vyberte **Odstranit adresář**.

    Tím odstraníte tenanta a k němu přidružené informace.

    ![Stránka s přehledem, s zvýrazněné tlačítko Odstranit adresář](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Další postup
- Změňte nebo přidejte další názvy domén, viz [Přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md)

- Přidejte uživatele, viz [Přidání nebo odstranění nového uživatele](add-users-azure-active-directory.md)

- Přidejte skupiny a členy, viz [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- Přečtěte si informace o tom, jak vám [přístup na základě role pomocí služby Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) a [podmíněný přístup](../../role-based-access-control/conditional-access-azure-management.md) může pomoci spravovat přístup k aplikacím a prostředkům vaší organizace.

- Přečtěte si o službě Azure AD, včetně [základních licenčních informací, terminologie a souvisejících funkcích](active-directory-whatis.md).
