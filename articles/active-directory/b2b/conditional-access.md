---
title: Podmíněný přístup pro uživatele spolupráce B2B – Azure Active Directory | Dokumentace Microsoftu
description: Spolupráce Azure Active Directory s B2B podporuje vícefaktorové ověřování (MFA) pro selektivní přístup k vašim podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5234443e234d232a9711274bea2f73427266f6e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113482"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Podmíněný přístup pro uživatele spolupráce B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Ověřování službou Multi-Factor Authentication pro uživatele B2B
Se spoluprací Azure AD B2B může organizace přinutit zásady ověřování službou Multi-Factor Authentication (MFA) pro uživatele B2B. Tyto zásady je možné vynucovat na tenanta, aplikace nebo úroveň jednotlivého uživatele stejným způsobem, že jsou povolené pro zaměstnance na plný úvazek a členy vaší organizace. Zásady vícefaktorového ověřování se vynucují v organizaci poskytující prostředky.

Příklad:
1. Správce nebo informace o pracovních procesů ve společnosti A vyzývá uživatele k aplikaci ze společnosti B *Foo* ve firmě A.
2. Aplikace *Foo* ve společnosti A je nakonfigurován tak, aby vyžadovala vícefaktorové ověřování pro přístup.
3. Když uživatel ze společnosti B pokusí o přístup k aplikaci *Foo* ve společnosti tenanta, zobrazí se výzva k dokončení ověřovacím testem MFA.
4. Uživatel můžete nastavit jejich MFA s firmy a zvolí možnost jejich vícefaktorové ověřování.
5. V tomto scénáři se dá použít pro všechny identity (Azure AD nebo MSA, například, pokud uživatelé ve společnosti B ověření pomocí ID sociální)
6. Firmy A musí mít dostatek licencí Azure AD Premium, které podporují vícefaktorové ověřování. Uživatel ze společnosti B spotřebuje tuto licenci od společnosti A.

Pozvání tenantů je zodpovědná za MFA pro uživatele v organizaci partnera vždy i v případě organizaci partnera poskytujícího možnosti MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Nastavení MFA pro uživatele spolupráce B2B
Chcete-li zjistit, jak snadné je nastavení vícefaktorového ověřování pro uživatele spolupráce B2B, přečtěte si postup v následujícím videu:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Vyzkoušejte si MFA pro uživatele B2B nabízejí uplatnění.
Podívejte se na následující animace zobrazíte uplatnění prostředí:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA pro uživatele spolupráce B2B resetování
V současné době správce můžete vyžadovat uživatele spolupráce B2B až po korektury si znovu pouze pomocí následujících rutin Powershellu:

1. Připojení k Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Získat všichni uživatelé s potvrzením do metody

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Zde naleznete příklad:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Resetujte – metoda vícefaktorové ověřování pro konkrétního uživatele tak, aby vyžadovala uživatele spolupráce B2B znovu nastavit metody výš. Příklad:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Proč jsme na prostředek tenantů provádět vícefaktorové ověřování?

V aktuální verzi vícefaktorové ověřování je vždy v prostředku tenantů, z důvodu, předvídatelnost. Řekněme například, společnosti Fabrikam je pozvaný uživatel Contoso (Jan) a povolila Fabrikam MFA pro uživatele B2B.

Pokud Contoso má povolenou zásadu vícefaktorového ověřování pro počítač App1, ale ne počítači App2, pak když se podíváte na Contoso vícefaktorové ověřování deklarací identity v tokenu, nám může se zobrazit následující problém:

* 1. den: Uživatel má MFA ve společnosti Contoso a přistupuje k serveru App1 a pak žádné další MFA řádku je zobrazena ve společnosti Fabrikam.

* Dne 2: Uživatel použil 2 aplikace ve společnosti Contoso, takže teď při přístupu ke společnosti Fabrikam, musí registrovat pro MFA existuje.

Tento proces může být matoucí a může vést k dokončení přihlášení vyřaďte.

Kromě toho i v případě, že Contoso má schopnost vícefaktorové ověřování, není vždy, že by případ Fabrikam důvěřovat zásad vícefaktorového ověřování Contoso.

Nakonec prostředků tenanta MFA funguje taky pro účty spravované služby a identifikátory sociálních sítí a pro partnerské organizace, které nemají nastavení vícefaktorového ověřování.

Doporučení pro vícefaktorové ověřování pro uživatele B2B je proto vždy vyžadovat vícefaktorové ověřování ve zvoucí tenantovi. Tento požadavek může vést k double vícefaktorové ověřování v některých případech, ale při každém přístupu k pozvání tenanta, je předvídatelný prostředí pro koncové uživatele: Jan musí zaregistrovat pro MFA s pozval tenanta.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Na základě zařízení, na základě umístění a na základě rizik podmíněného přístupu pro uživatele B2B

Pokud Contoso povolí zásady podmíněného přístupu podle zařízení pro firemní data, je zabráněno přístup ze zařízení, která nejsou spravovaná společností Contoso a není kompatibilní se zásadami společnosti Contoso zařízení.

Pokud uživatel B2B zařízení nespravuje ve společnosti Contoso, zablokuje se přístup uživatelů B2B z partnerských organizací v jakémkoli kontextu tyto zásady vynucují. Contoso však můžete vytvořit seznamy vyloučení obsahující konkrétní partnerskou uživatele chcete vyloučit ze zásad podmíněného přístupu na základě zařízení.

#### <a name="location-based-conditional-access-for-b2b"></a>Podmíněný přístup založený na umístění pro B2B

Zásady podmíněného přístupu na základě umístění můžete vynutit pro uživatele B2B, pokud zvoucí organizaci je možné vytvořit důvěryhodný rozsah IP adres, definující jejich partnerských organizacích.

#### <a name="risk-based-conditional-access-for-b2b"></a>Na základě rizik podmíněného přístupu pro B2B

V současné době na základě rizik zásady přihlašování nelze použít pro uživatele B2B, protože hodnocení rizik se provádí na domovské organizace uživatele B2B.

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování spolupráce B2B ve službě Azure AD](licensing-guidance.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
