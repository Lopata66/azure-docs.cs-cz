---
title: Řešení potíží s B2B collaboration – Azure Active Directory | Dokumentace Microsoftu
description: Řešení běžných problémů se spoluprací Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec05c513d89dc8a590cbd0f586f382d699edc5e3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454115"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Řešení potíží s spolupráce Azure Active Directory s B2B

Tady jsou některé nápravná opatření pro běžné problémy se spoluprací B2B Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Přidání externího uživatele, ale nevidíte v globálním adresáři nebo výběr osob

V případech, kdy nejsou naplněny externí uživatele v seznamu objekt může trvat několik minut, než replikaci.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Uživatel typu Host B2B se nezobrazuje v Sharepointu Online a Onedrivu výběr osob 
 
Možnost vyhledat existující uživatele typu Host v Sharepointu Online (SPO) výběr osob je vypnuto ve výchozím nastavení tak, aby odpovídaly starší verzi chování.

Tuto funkci můžete povolit pomocí nastavení "ShowPeoplePickerSuggestionsForGuestUsers" na úrovni kolekce klienta a serveru. Můžete nastavit funkci pomocí rutiny Set-SPOTenant a Set-SPOSite, který umožní členům hledat všechny existující uživatele typu Host do adresáře. Změny v rámci tenanta neovlivní již zřízených SPO webů.

## <a name="invitations-have-been-disabled-for-directory"></a>Pozvánky jsou zakázané pro adresář

Pokud se zobrazí oznámení, že nemáte oprávnění pozvat uživatele, ujistěte se, že je váš uživatelský účet oprávnění k pozvání externího uživatele v části Nastavení uživatele:

![Nastavení externího uživatele](media/troubleshoot/external-user-settings.png)

Pokud jste naposledy upravili tato nastavení nebo uživateli přiřadit roli odesílatel pozvánky hostů, může existovat zpoždění 15 až 60 minut předtím, než se změny projevily.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Uživatel, který pozváni je dochází k chybě při uplatnění.

Běžné chyby patří:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Pozvaného uživatele správce zakázal uživatelům EmailVerified bránit vytváření v rámci jejich tenanta

Když pozvání uživatele, jejichž organizace používá Azure Active Directory, ale pokud neexistuje konkrétní uživatelský účet (například uživatel neexistuje v doméně contoso.com Azure AD). Správce contoso.com může mít zásadu na místě, aby bránil uživatelům ve vytváření. Uživatel musí obraťte se na svého správce určit, pokud jsou povoleny externí uživatele. Možná bude nutné správce externího uživatele umožňuje uživatelům ověřit e-mailu z vlastní domény (najdete v tomto [článku](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) na umožnit uživatelům ověřit e-mailu).

![Chyba oznamující, že tento tenant neumožňuje e-mailová ověření přidat uživatele.](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Externí uživatel ještě neexistuje ve federované domény

Pokud používáte ověřování federace a uživatele ve službě Azure Active Directory ještě neexistuje, nejde pozvat uživatele.

Pokud chcete tento problém vyřešit, musí správce externího uživatele synchronizovat uživatelského účtu do služby Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak se fakturuje se u\#", která obvykle není platný znak, synchronizovat s Azure AD?

"\#" je rezervovaným znakem v UPN pro spolupráci B2B ve službě Azure AD nebo externí uživatele, protože pozvaného účtu user@contoso.com stane user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Proto \# v UPN pocházející z místní nejsou povoleny pro přihlášení k webu Azure portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Při přidávání externích uživatelů do skupiny synchronizované se zobrazí chyba

Externí uživatelé lze přidat pouze do "přiřazeno" nebo "Zabezpečení" skupiny a ne do skupiny, které jsou standardní místně.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Moje externího uživatele nepřijala e-mailu pro uplatnění

Pozvaný potřeba zkontrolovat u svého poskytovatele internetových služeb nebo nevyžádané pošty filtru zajistěte, aby se na následující adresu: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Všimnu si toho, že vlastní zprávu nezíská součástí pozvání čas od času

Pro dosažení souladu se zákony o ochraně osobních údajů, rozhraní API neumožňují vkládání vlastních zpráv v e-mailovou pozvánku při:

- Odesílající uživatel nemá e-mailovou adresu ve zvoucí tenantovi
- Když pozvánku odesílá instančního objektu služby App Service

Pokud tento scénář je pro vás důležité, můžete potlačit e-mailová pozvánka naše rozhraní API a odeslat e-mailu mechanismem podle svého výběru. Poraďte se vaše organizace právním poradcem pro Ujistěte se, že všechny e-maily, které odesíláte, že tímto způsobem souladu se zákony o ochraně osobních údajů.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Uživatel typu Host s tenantem just-in-time nebo "virální" není schopen resetování hesla

Pokud je identity tenant just-in-time (JIT) nebo virálního tenanta (to znamená, že je samostatný, nespravovaného tenanta pro Azure), pouze uživatele typu Host své heslo můžou resetovat. Někdy se organizace [převzít správu virálního tenantů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) , které vytvářejí, když zaměstnanci používají jejich pracovní e-mailové adresy k registraci pro služby. Po organizace převezme virálního tenanta, můžete pouze správce v této organizaci resetovat heslo uživatele nebo povolit samoobslužné resetování HESLA. V případě potřeby jako zvoucí organizaci, můžete odebrat uživatelský účet guest z adresáře a znovu odeslat pozvánku.

## <a name="next-steps"></a>Další postup

- [Získat podporu pro spolupráci B2B](get-support.md)
