---
title: Povolení přihlášení k bezpečnostnímu klíči s nezabezpečenými hesly pro Azure AD (Preview) – Azure Active Directory
description: Povolení nezabezpečeného přihlášení klíče zabezpečení do Azure AD pomocí klíčů zabezpečení FIDO2 (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66102a92ce279b594d61c8b2d484c89a8532057a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172003"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Povolit přihlašování k neheslovým klíčům zabezpečení (Preview)

Pro podniky, které používají hesla dnes a mají prostředí pro sdílené počítače, poskytují bezpečnostní klíče bezproblémové možnosti, jak se pracovníkům ověřovat bez zadání uživatelského jména nebo hesla. Klíče zabezpečení poskytují lepší produktivitu pro pracovníky a mají lepší zabezpečení.

Tento dokument se zaměřuje na povolení ověřování pomocí hesla založeného na klíčích zabezpečení. Na konci tohoto článku se budete moci přihlásit k webovým aplikacím pomocí účtu Azure AD pomocí bezpečnostního klíče FIDO2.

|     |
| --- |
| Bezpečnostní klíče FIDO2 jsou funkcí veřejné verze Preview Azure Active Directory. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.|
|     |

## <a name="requirements"></a>Požadavky

- [Multi-Factor Authentication Azure](howto-mfa-getstarted.md)
- [Souhrnná registrace informací o zabezpečení ve verzi Preview](concept-registration-mfa-sspr-combined.md)
- Kompatibilní [klíče zabezpečení FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Operace WebAuthN vyžaduje Windows 10 verze 1809 nebo vyšší * *

Chcete-li použít klíče zabezpečení pro přihlášení k webovým aplikacím a službám, je nutné mít prohlížeč, který podporuje protokol WebAuthN. Mezi ně patří Microsoft Edge, Chrome, Firefox a Safari.

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro verzi Preview

V zařízeních, ve kterých budete pilotní nasazení, musí běžet Windows 10 verze 1809 nebo vyšší. Nejlepší prostředí je ve Windows 10 verze 1903 nebo vyšší.

## <a name="enable-passwordless-authentication-method"></a>Povolit metodu ověřování nejenom pro hesla

### <a name="enable-the-combined-registration-experience"></a>Povolit kombinované možnosti registrace

Funkce registrace pro metody ověřování nevyužívající hesla jsou závislé na kombinované registraci ve verzi Preview. Postupujte podle kroků v článku [Povolení registrace kombinovaných zabezpečení (Preview)](howto-registration-mfa-sspr-combined.md), aby se aktivovala kombinovaná registrační verze.

### <a name="enable-fido2-security-key-method"></a>Povolit metodu klíče zabezpečení FIDO2

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory** > **zabezpečení** > **metody ověřování** > **zásady ověřování (Preview)** .
1. V části **klíč zabezpečení FIDO2**metody vyberte následující možnosti:
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybrat uživatele
1. **Uložte** konfiguraci.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrace uživatelů a Správa klíčů zabezpečení FIDO2

1. Přejděte na [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Přihlaste se, pokud ještě není.
1. Klikněte na **informace o zabezpečení**.
   1. Pokud má uživatel již alespoň jednu zaregistrovanou metodu Azure Multi-Factor Authentication, může ihned zaregistrovat klíč zabezpečení FIDO2.
   1. Pokud nemají zaregistrovanou alespoň jednu metodu služby Azure Multi-Factor Authentication, musí ji přidat.
1. Přidejte klíč zabezpečení FIDO2, a to tak, že kliknete na **Přidat metodu** a zvolíte **klíč zabezpečení**.
1. Vyberte **zařízení USB** nebo **zařízení NFC**.
1. Připravte si klíč a klikněte na tlačítko **Další**.
1. Zobrazí se pole a požádá uživatele o vytvoření nebo zadání kódu PIN pro klíč zabezpečení a pak pro tento klíč provede požadované gesto, ať už je to biometrika nebo dotykové ovládání.
1. Uživatel bude vrácen do kombinovaného prostředí pro registraci a požádán o poskytnutí smysluplného názvu pro klíč, aby uživatel mohl zjistit, který z nich je v případě, že má více. Klikněte na **Další**.
1. Kliknutím na **Hotovo** proces dokončete.

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

V následujícím příkladu už uživatel zřídil svůj bezpečnostní klíč FIDO2. Uživatel se může přihlásit na webu pomocí klíče zabezpečení FIDO2 v podporovaném prohlížeči ve Windows 10 verze 1809 nebo vyšší.

![Přihlášení k bezpečnostnímu klíči Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Řešení potíží a zpětná vazba

Pokud chcete sdílet zpětnou vazbu nebo narazit na problémy při prohlížení této funkce, sdílejte ji prosím přes aplikaci Centrum zpětné vazby z Windows.

1. Spusťte **Centrum zpětné vazby** a ujistěte se, že jste přihlášení.
1. Odeslat názor v rámci následující kategorizace:
   1. Kategorie: zabezpečení a ochrana osobních údajů
   1. Subcategory: FIDO
1. Pro zachycení protokolů použijte možnost: **znovu vytvořit můj problém**

## <a name="known-issues"></a>Známé problémy

### <a name="security-key-provisioning"></a>Zřizování klíče zabezpečení

Ve verzi Public Preview není k dispozici zřízení a zrušení zřizování správců klíčů zabezpečení.

### <a name="upn-changes"></a>Změny UPN

Pracujeme na podpoře funkce, která umožňuje změnu UPN na hybridních připojených zařízeních Azure AD a na zařízeních připojených k Azure AD. Pokud se změní hlavní název uživatele (UPN), nemůžete už upravovat bezpečnostní klíče FIDO2, aby se změna projevila. Řešením je resetování zařízení a uživatel se musí znovu zaregistrovat.

## <a name="next-steps"></a>Další kroky

[FIDO2 bezpečnostní klíč Windows 10 – přihlášení](howto-authentication-passwordless-security-key-windows.md)

[Povolit ověřování FIDO2 pro místní prostředky](howto-authentication-passwordless-security-key-on-premises.md)

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
