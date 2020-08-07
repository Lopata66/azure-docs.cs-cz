---
title: Co je zařízení připojené k Azure AD?
description: Přečtěte si, jak vám Správa identit zařízení může pomáhat při správě zařízení, která přistupují k prostředkům ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31374b851d90e43e7380024923c3ad025859b7a0
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923726"
---
# <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Služba Azure AD JOIN je určena pro organizace, které chtějí být pouze cloudové nebo cloudové. Každá organizace může nasadit zařízení připojená k Azure AD bez ohledu na velikost nebo odvětví. Připojení k Azure AD funguje i v hybridním prostředí a umožňuje přístup ke cloudovým i místním aplikacím a prostředkům.

| Připojení k Azure AD | Popis |
| --- | --- |
| **Definice** | Připojeno pouze k Azure AD vyžadování účtu organizace pro přihlášení k zařízení |
| **Primární cílová skupina** | Vhodné pro cloudové i hybridní organizace. |
|   | Platí pro všechny uživatele v organizaci. |
| **Vlastnictví zařízení** | Organizace |
| **Operační systémy** | Všechna zařízení s Windows 10 s výjimkou Windows 10 Home |
|   | [Windows Server 2019 Virtual Machines běžící v Azure](howto-vm-sign-in-azure-ad-windows.md) (jádro serveru se nepodporuje) |
| **Zřizování** | Samoobslužná služba: Windows OOBE nebo nastavení |
|   | Hromadná registrace |
|   | Windows Autopilot |
| **Možnosti přihlášení zařízení** | Účty organizace pomocí: |
|   | Heslo |
|   | Windows Hello pro firmy |
|   | Klíče zabezpečení FIDO 2.0 (Preview) |
| **Správa zařízení** | Správa mobilních zařízení (příklad: Microsoft Intune) |
|   | Spoluspráva pomocí Microsoft Intune a koncového bodu Microsoft Configuration Manager |
| **Klíčové funkce** | Jednotné přihlašování pro cloudové i místní prostředky |
|   | Podmíněný přístup prostřednictvím registrace MDM a vyhodnocení dodržování předpisů MDM |
|   | Samoobslužné resetování hesla a Resetování PIN kódu Windows Hello na zamykací obrazovce |
|   | Enterprise State Roaming napříč zařízeními |

Zařízení připojená k Azure AD jsou přihlášená k používání účtu Azure AD organizace. Přístup k prostředkům v organizaci je možné dál omezit na základě účtu Azure AD a [zásad podmíněného přístupu](../conditional-access/howto-conditional-access-policy-compliant-device.md) , které se používají pro identitu zařízení.

Správci můžou zabezpečit a dále řídit zařízení připojená k Azure AD pomocí nástrojů pro správu mobilních zařízení (MDM), jako je Microsoft Intune nebo ve scénářích spolusprávy pomocí služby Microsoft Endpoint Configuration Manager. Tyto nástroje poskytují způsob, jak vyhovět konfiguracím vyžadovaným v organizaci, jako je třeba šifrování úložiště, složitost hesla, instalace softwaru a aktualizace softwaru. Správci můžou k dispozici pro zařízení připojená k Azure AD pomocí Configuration Manager ke [správě aplikací ze Microsoft Store pro firmy a vzdělávání](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Připojení k Azure AD se dá provést pomocí možností samoobslužné služby, jako je například spuštění z prvního počítače, hromadné registrace nebo automatický [pilot pro Windows](/intune/enrollment-autopilot).

Zařízení připojená k Azure AD stále můžou udržovat přístup k místním prostředkům v rámci jednotného přihlašování, když se nacházejí v síti organizace. Zařízení, která jsou připojená k Azure AD, se můžou i nadále ověřovat u místních serverů, jako jsou soubory, tisk a další aplikace.

## <a name="scenarios"></a>Scénáře

Přestože je služba Azure AD Join primárně určená pro organizace, které nemají místní infrastrukturu Windows Server Active Directory, můžete ji využít i v následujících scénářích:

- Chcete přejít na cloudovou infrastrukturu s využitím Azure AD a MDM, jako je například Intune.
- Nemůžete použít připojení k místní doméně například v případě, že potřebujete získat kontrolu nad mobilními zařízeními, jako jsou tablety a telefony.
- Vaši uživatelé primárně potřebují přístup k Office 365 nebo dalším aplikacím SaaS integrovaným s Azure AD.
- Chcete spravovat skupinu uživatelů v Azure AD, a ne v Active Directory. Tento scénář může platit například pro sezónní pracovní procesy, dodavatele nebo studenty.
- Chcete poskytnout možnosti připojení pracovníkům ve vzdálených pobočkách s omezenou místní infrastrukturou.

Zařízení připojená k Azure AD můžete nakonfigurovat pro všechna zařízení s Windows 10 s výjimkou Windows 10 Home.

Cílem zařízení připojených k Azure AD je zjednodušit:

- Nasazení Windows na pracovní zařízení
- Přístup k aplikacím a prostředkům organizace z jakéhokoli zařízení s Windows
- Cloudovou správu pracovních zařízení
- Uživatelé se můžou přihlašovat ke svým zařízením pomocí Azure AD nebo synchronizovat pracovní nebo školní účty služby Active Directory.

![Zařízení připojená k Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

K nasazení služby Azure AD Join je možné použít jakoukoli z následujících metod:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Hromadné nasazení](/intune/windows-bulk-enroll)
- [Samoobslužné prostředí](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Další kroky

- [Plánování implementace připojení ke službě Azure AD](azureadjoin-plan.md)
- [Jak spravovat místní skupinu Administrators na zařízeních připojených k Azure AD](assign-local-admin.md)
- [Správa identit zařízení přes Azure Portal](device-management-azure-portal.md)
- [Správa zastaralých zařízení v Azure AD](manage-stale-devices.md)
