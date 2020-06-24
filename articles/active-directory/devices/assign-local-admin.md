---
title: Jak spravovat místní správce na zařízeních připojených k Azure AD
description: Přečtěte si, jak přiřadit role Azure do místní skupiny Správci zařízení s Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4285c7d8cd6c9feee84eaae3c49ffd1f21d5df27
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253082"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Jak spravovat místní skupinu Administrators na zařízeních připojených k Azure AD

Abyste mohli spravovat zařízení s Windows, musíte být členem místní skupiny Administrators. V rámci procesu připojení Azure Active Directory (Azure AD) aktualizuje Azure AD členství této skupiny na zařízení. Aktualizaci členství můžete přizpůsobit tak, aby vyhovovala vašim obchodním požadavkům. Aktualizace členství je například užitečná, pokud chcete pracovníkům technické podpory povolit úkoly vyžadující práva správce na zařízení.

Tento článek vysvětluje, jak funguje aktualizace členství a jak je můžete přizpůsobit během připojení k Azure AD. Obsah tohoto článku se nevztahuje k **hybridní** službě Azure AD JOIN.

## <a name="how-it-works"></a>Jak to funguje

Když připojíte zařízení s Windows ke službě Azure AD pomocí služby Azure AD JOIN, Azure AD přidá do místní skupiny Administrators na zařízení tyto objekty zabezpečení:

- Role globálního správce služby Azure AD
- Role Správce zařízení Azure AD 
- Uživatel, který provádí službu Azure AD JOIN   

Když do místní skupiny Administrators přidáte role Azure AD, můžete aktualizovat uživatele, kteří můžou spravovat zařízení kdykoli ve službě Azure AD, aniž by to mělo na zařízení žádné změny. V současné době nemůžete přiřadit skupiny k roli správce.
Azure AD taky přidá roli Správce zařízení Azure AD do místní skupiny Administrators, aby podporovala princip nejnižších oprávnění (PoLP). Kromě globálních správců můžete také povolit uživatelům, kterým byla přiřazena *pouze* role Správce zařízení ke správě zařízení. 

## <a name="manage-the-global-administrators-role"></a>Správa role globální správci

Chcete-li zobrazit a aktualizovat členství v roli globálního správce, přečtěte si téma:

- [Zobrazit všechny členy role správce v Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Přiřazení uživatele k rolím správce v Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Správa role Správce zařízení 

V Azure Portal můžete spravovat roli Správce zařízení na stránce **zařízení** . Otevření stránky **zařízení** :

1. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com) jako globální správce.
1. Vyhledejte a vyberte *Azure Active Directory*.
1. V části **Spravovat** klikněte na **zařízení**.
1. Na stránce **zařízení** klikněte na **nastavení zařízení**.

Pokud chcete upravit roli Správce zařízení, nakonfigurujte **Další místní správce na zařízeních připojených k Azure AD**.  

![Další místní správci](./media/assign-local-admin/10.png)

>[!NOTE]
> Tato možnost vyžaduje klienta Azure AD Premium. 

Správci zařízení mají přiřazená všechna zařízení připojená k Azure AD. Nemůžete nastavit rozsah správců zařízení na určitou sadu zařízení. Aktualizace role Správce zařízení nutně nemá bezprostřední dopad na ovlivněné uživatele. V zařízeních, ve kterých je uživatel již přihlášen *, dojde k aktualizaci oprávnění při následujících* akcích:

- do 4 hodin bylo předáno, aby služba Azure AD vydávala nový primární obnovovací token s příslušnými oprávněními. 
- Pokud chcete aktualizovat svůj profil, odhlaste se a přihlaste se k němu, ne zamknout nebo odemknout.

## <a name="manage-regular-users"></a>Správa běžných uživatelů

Ve výchozím nastavení Azure AD přidá uživatele, který provádí službu Azure AD JOIN, do skupiny správců v zařízení. Pokud chcete běžným uživatelům zabránit v tom, aby se stali místními správci, máte následující možnosti:

- Automatický [pilot pro Windows](/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows autopilot vám nabízí možnost zabránit primárnímu uživateli, aby se mohl připojit od místního správce. To můžete provést [vytvořením profilu autopilotu](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Hromadná registrace](/intune/windows-bulk-enroll) – připojení k Azure AD, které se provádí v kontextu hromadné registrace, probíhá v kontextu automaticky vytvořeného uživatele. Uživatelé, kteří se přihlašují po přihlášení k zařízení, se do skupiny Administrators nepřipojí.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ruční zvýšení oprávnění uživatele na zařízení 

Kromě použití procesu připojení ke službě Azure AD můžete také ručně zvýšit normálního uživatele tak, aby se stal místním správcem na jednom konkrétním zařízení. Tento krok vyžaduje, abyste již byli členem místní skupiny Administrators. 

Od verze **Windows 10 1709** můžete tuto úlohu provést z **Nastavení-> účty – > dalších uživatelů**. Vyberte **Přidat pracovního nebo školního uživatele**, do pole **uživatelský účet** zadejte hlavní název uživatele (UPN) a v části **typ účtu** vyberte *správce* .  
 
Kromě toho můžete přidat uživatele také pomocí příkazového řádku:

- Pokud jsou vaši uživatelé klienta synchronizováni z místní služby Active Directory, použijte `net localgroup administrators /add "Contoso\username"` .
- Pokud jsou vaši uživatelé tenanta vytvořeni ve službě Azure AD, použijte`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Požadavky 

Nelze přiřadit skupiny k roli Správce zařízení, pouze jednotliví uživatelé jsou povoleni.

Správci zařízení mají přiřazená všechna zařízení připojená k Azure AD. Nemůžou být vymezené na konkrétní sadu zařízení.

Když odeberete uživatele z role Správce zařízení, pořád mají oprávnění místního správce na zařízení, pokud se k němu přihlásí. Oprávnění se odvolá při příštím přihlášení nebo po 4 hodinách, kdy se vystaví nový primární obnovovací token.

## <a name="next-steps"></a>Další kroky

- Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).
- Další informace o podmíněném přístupu na základě zařízení najdete v tématu [Konfigurace zásad podmíněného přístupu na základě zařízení Azure Active Directory](../conditional-access/require-managed-devices.md).
