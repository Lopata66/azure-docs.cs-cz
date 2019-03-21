---
title: Role klasického správce předplatného, role Azure RBAC a role správce Azure AD | Microsoft Docs
description: Tento článek popisuje různé role v Azure – role klasického správce předplatného, role řízení přístupu na základě role (RBAC) Azure a role správce služby Azure AD (Azure Active Directory).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 1b7183da84b994da8694440183e367f143722002
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295165"
---
# <a name="classic-subscription-administrator-roles-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Role klasického správce předplatného, role Azure RBAC a role správce Azure AD

Pokud s Azure teprve začínáte, může být pro vás trochu obtížné vyznat se v různých rolích v Azure. Tento článek vám pomůže porozumět následujícím rolím a tomu, kdy je použít:
- Role klasického správce předplatného
- Role řízení přístupu na základě rolí (RBAC) Azure
- Role správce služby Azure AD (Azure Active Directory)

## <a name="how-the-roles-are-related"></a>Jak spolu role souvisejí

Lepšímu porozumění rolí v Azure pomůže znalost trochy historie. Když Azure byla původně, přístup k prostředkům správu pomocí pouhých tří rolí správce: Účet správce, Správce služeb a spolupracujících správců. Později bylo přidáno řízení přístupu na základě role (RBAC) pro prostředky Azure. Azure RBAC je novější systém autorizace, který poskytuje podrobnou správu přístupu k prostředkům Azure. Řízení přístupu na základě role zahrnuje mnoho předdefinovaných rolí, může být přiřazeno v různých oborech a umožňuje vytvářet vlastní role. Pokud chcete spravovat prostředky v Azure AD, například uživatele, skupiny nebo domény, máte k dispozici několik rolí správce Azure AD.

Následující diagram představuje souhrnné zobrazení toho, jak spolu souvisejí role klasického správce předplatného, role Azure RBAC a role správce Azure AD.

![Různé role v Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

Správce účtu, správce služeb a spolusprávce jsou tři role klasického správce předplatného v Azure. Klasičtí správci předplatného mají úplný přístup k předplatnému Azure. Mohou spravovat prostředky pomocí portálu Azure Portal, rozhraní API Azure Resource Manageru a rozhraní API modelu nasazení Classic. Účet, který slouží k registraci v Azure, je automaticky nastaven jako účet správce účtu a správce služeb. Potom je možné přidat další spolusprávce. Správce služeb a spolusprávci mají stejný přístup jako uživatelé s přiřazenou rolí vlastníka (role Azure RBAC) v oboru předplatného. Následující tabulka popisuje rozdíly mezi třemi rolemi klasického správce předplatného.

| Klasický správce předplatného | Omezení | Oprávnění | Poznámky |
| --- | --- | --- | --- |
| Správce účtu | 1 na účet Azure | <ul><li>Přístup do [Centra účtů Azure](https://account.azure.com/Subscriptions)</li><li>Správa všech předplatných v účtu</li><li>Vytváření nových předplatných</li><li>Rušení předplatných</li><li>Změna fakturace předplatného</li><li>Změna správce služeb</li></ul> | Koncepčně se jedná o vlastníka fakturace předplatného.<br>Správce účtu nemá přístup k webu Azure Portal. |
| Správce služeb | 1 na předplatné Azure | <ul><li>Správa služeb na portálu [Azure Portal](https://portal.azure.com)</li><li>Přiřazení role spolusprávce uživatelům</li></ul> | Ve výchozím nastavení u nového předplatného je správce účtu současně i správcem služeb.<br>Správce služeb má stejný přístup jako uživatel, který má přidělenu roli vlastníka v oboru předplatného.<br>Správce služeb má úplný přístup k webu Azure Portal. |
| Spolusprávce | 200 na předplatné | <ul><li>Má stejná přístupová oprávnění jako správce služeb, ale nemůže měnit přidružení předplatných k adresářům Azure.</li><li>Může uživatelům přiřazovat role spolusprávce, ale nemůže měnit správce služeb.</li></ul> | Spolusprávce má stejný přístup jako uživatel, který má přidělenu roli vlastníka v oboru předplatného. |

Na webu Azure Portal, můžete spravovat spolupracujících správců nebo zobrazit pomocí Správce služby **klasického správce** kartu.

![Správci předplatného Azure classic na portálu Azure portal](./media/rbac-and-directory-admin-roles/classic-administrators.png)

Na webu Azure Portal můžete zobrazit nebo změnit správce služeb nebo zobrazit účet správce v okně vlastností vašeho předplatného.

![Správce účtu a správce služeb na portálu Azure Portal](./media/rbac-and-directory-admin-roles/account-admin.png)

Další informace najdete v tématu [správci předplatného Azure classic](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Účet Azure a předplatná Azure

Účet Azure reprezentuje fakturační vztah. Účet Azure je identita uživatele, jedno nebo více předplatných Azure a přidružená skupina prostředků Azure. Osoba, která vytvoří účet, je správcem účtu pro všechna předplatná vytvořená v daném účtu. Tato osoba je také výchozím správcem služeb pro předplatné.

Předplatná Azure vám usnadňují organizaci přístupu k prostředkům Azure. Zároveň vám pomohou řídit způsob, jak je používání prostředků vykazováno, fakturováno a placeno. Každé předplatné může mít jiné nastavení fakturace a plateb. Můžete tedy mít jiná předplatná a jiné plány pro různé pobočky, oddělení, projekty a podobně. Každá služba patří do předplatného a pro programové operace se může vyžadovat ID předplatného.

Účty a předplatná se spravují v [Centru účtů Azure](https://account.azure.com/Subscriptions).

## <a name="azure-rbac-roles"></a>Role Azure RBAC

Azure RBAC je systém autorizace založený na [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md), který poskytuje podrobnou správu přístupu k prostředkům Azure, jako jsou výpočetní služby a úložiště. Azure RBAC zahrnuje více než 70 předdefinovaných rolí. Existují čtyři základní role RBAC. První tři se vztahují ke všem typům prostředků:

| Role Azure RBAC | Oprávnění | Poznámky |
| --- | --- | --- |
| [Vlastník](built-in-roles.md#owner) | <ul><li>Úplný přístup ke všem prostředkům</li><li>Delegování přístupu na jiné uživatele</li></ul> | Správce služeb a spolusprávci mají přiřazenu roli vlastníka v oboru předplatného.<br>Platí pro všechny typy prostředků. |
| [Přispěvatel](built-in-roles.md#contributor) | <ul><li>Vytváření a správa všech typů prostředků Azure</li><li>Nemůže udělovat přístup ostatním</li></ul> | Platí pro všechny typy prostředků. |
| [Čtenář](built-in-roles.md#reader) | <ul><li>Zobrazení prostředků Azure</li></ul> | Platí pro všechny typy prostředků. |
| [Správce uživatelských přístupů](built-in-roles.md#user-access-administrator) | <ul><li>Správa uživatelských přístupů k prostředkům Azure</li></ul> |  |

Zbývající předdefinované role umožňují správu konkrétních prostředků Azure. Role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) například uživateli umožňuje vytvářet a spravovat virtuální počítače. Seznam předdefinovaných rolí najdete v tématu [předdefinované role pro prostředky Azure](built-in-roles.md).

Řízení přístupu na základě role (RBAC) podporují pouze portál Azure Portal a rozhraní API Azure Resource Manageru. Uživatelé, skupiny a aplikace s přiřazenými rolemi RBAC nemohou používat [rozhraní API modelu nasazení Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md).

Na portálu Azure Portal se přiřazení rolí pomocí RBAC zobrazují v okně **Řízení přístupu (IAM)**. V tomto okně lze nalézt v rámci portálu, jako je například skupiny pro správu, předplatná, skupiny prostředků a různé prostředky.

![Okno Řízení přístupu (IAM) na portálu Azure Portal](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Po kliknutí **role** kartu, zobrazí se seznam předdefinovaných a vlastních rolí.

![Předdefinované role na portálu Azure Portal](./media/rbac-and-directory-admin-roles/roles-list.png)

Další informace najdete v tématu [spravovat přístup k prostředkům Azure pomocí RBAC a webu Azure portal](role-assignments-portal.md).

## <a name="azure-ad-administrator-roles"></a>Role správce Azure AD

Role správce Azure AD slouží ke správě prostředků Azure AD v adresáři, například k vytváření nebo úpravě uživatelů, přiřazení administrativních rolí dalším uživatelům, resetování hesel uživatelů, správě uživatelských licencí a správě domén. Následující tabulka popisuje několik důležitějších rolí správce Azure AD.

| Role správce Azure AD | Oprávnění | Poznámky |
| --- | --- | --- |
| [Globální správce](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) | <ul><li>Správa přístupu ke všem administrativním funkcím v Azure Active Directory i službám federovaným do Azure Active Directory</li><li>Přiřazení rolí správce dalším uživatelům</li><li>Resetování hesel uživatelů a všech ostatních správců</li></ul> | Osoba, která se zaregistruje v tenantovi Azure Active Directory, se stává globálním správcem. |
| [Správce uživatelů](../active-directory/users-groups-roles/directory-assign-admin-roles.md#user-administrator) | <ul><li>Vytváření a správa všech aspektů uživatelů a skupin</li><li>Správa lístků podpory</li><li>Monitorování stavu služby</li><li>Změna hesel pro uživatele, správce helpdesku a další správce uživatelů</li></ul> |  |
| [Správce fakturace](../active-directory/users-groups-roles/directory-assign-admin-roles.md#billing-administrator) | <ul><li>Nové nákupy</li><li>Správa předplatných</li><li>Správa lístků podpory</li><li>Monitorování stavu služby</li></ul> |  |

Na portálu Azure Portal najdete seznam rolí správce Azure AD v okně **Role a správci**. Seznam všech rolí správce Azure AD najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

![Role správce Azure AD na portálu Azure Portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Rozdíly mezi rolemi Azure RBAC a rolemi správce Azure AD

Na obecné úrovni role Azure RBAC řídí oprávnění ke správě prostředků Azure a role správce Azure AD řídí oprávnění ke správě prostředků Azure Active Directory. Následující tabulka obsahuje přehled některých rozdílů.

| Role Azure RBAC | Role správce Azure AD |
| --- | --- |
| Správa přístupu k prostředkům Azure | Správa přístupu k prostředkům Azure Active Directory |
| Podpora vlastních rolí | Nemůže vytvářet vlastní role |
| Možnost zadání oboru na více úrovních (skupina pro správu, předplatné, skupina prostředků, prostředek) | Obor na úrovni tenanta |
| Dostupnost informací o roli na portálu Azure Portal, v Azure CLI, Azure PowerShellu, šablonách Azure Resource Manageru, rozhraní REST API | Informace o rolích je přístupná v Centru pro správu portálu služeb Microsoft 365 ze Správce Azure, Microsoft Graphu, Azure AD Powershellu |

### <a name="do-azure-rbac-roles-and--azure-ad-administrator-roles-overlap"></a>Překrývají se role Azure RBAC a role správce Azure AD?

Ve výchozím nastavení role Azure RBAC a role správce Azure AD nepokrývají Azure a Azure AD. Pokud ale globální správce vybere na portálu Azure Portal přepínač **Globální správce může spravovat předplatná Azure a skupiny pro správu** a tím zvýší úroveň svého přístupu, udělí se mu role [Správce uživatelských přístupů](built-in-roles.md#user-access-administrator) (což je role RBAC) pro všechna předplatná u konkrétního tenanta. Role správce uživatelských přístupů uživateli umožňuje udělit dalším uživatelům přístup k prostředkům Azure. Tento přepínač může být užitečný pro opakované získání přístupu k předplatnému. Další informace najdete v článku o [zvýšení úrovně přístupu pro správce Azure AD](elevate-access-global-admin.md).

Několik rolí správce Azure AD pokrývá Azure AD a Microsoft Office 365, například role globálního správce a správce uživatelů. Pokud jste například členem role Globální správce, máte funkce globálního správce v Azure AD a Office 365 a můžete mimo jiné provádět změny v Microsoft Exchangi a Microsoft SharePointu. Ve výchozím nastavení ale globální správce nemá přístup k prostředkům Azure.

![Role Azure RBAC versus role správce Azure AD](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Další postup

- [Co je řízení přístupu na základě rolí (RBAC) pro prostředky Azure?](overview.md)
- [Oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)
- [Správci předplatného Azure classic](classic-administrators.md)
