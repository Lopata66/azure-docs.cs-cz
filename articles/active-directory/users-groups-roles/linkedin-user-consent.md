---
title: Účet LinkedIn připojení sdílení dat a vyjádření souhlasu – Azure Active Directory | Dokumentace Microsoftu
description: Vysvětluje, jak integrace LinkedIn sdílí data prostřednictvím aplikace Microsoftu ve službě Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e6e24f4e726e25ae65ecceaeb161f8e16d61721
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469629"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Sdílení dat prostřednictvím připojení se účet LinkedIn a vyjádření souhlasu

Umožníte uživatelům ve vaší organizaci Active Directory (Azure AD) vyjádřit souhlas se připojit své pracovní nebo školní účet ke svému účtu LinkedIn. Jakmile se uživatel připojí svoje účty, jsou k dispozici v některých aplikacím a službám Microsoftu informace a vybraná vystoupení z Linkedinu. Uživatelé také mohou očekávat své síťové prostředí na Linkedinu vylepšené a rozšiřují o informace od Microsoftu.

Pokud chcete zobrazit informace Linkedinu v aplikacím a službám Microsoftu, uživatelé musí udělit souhlas připojit své účty Microsoft a LinkedIn. Uživatelům se výzva k připojit své účty při prvním kliknutí zobrazíte informace LinkedIn jiného uživatele na kartě profil v Outlooku, Onedrivu nebo Sharepointu Online. S účtem LinkedIn nejsou povolené plně pro vaše uživatele, dokud se souhlas ještě neznáte a připojit své účty.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Výhody sdílení informací LinkedIn

Přístup k informacím o Linkedinu v rámci aplikacím a službám Microsoftu usnadňuje uživatelům připojení, zapojení a vytvářet profesionální vztahy s kolegy, zákazníky a partnery uvnitř i mimo vaši organizaci. Noví uživatelé mohou rychle začít rychlejší připojení s kolegy, získání informací o nich a snadného přístupu k další informace. Tady je příklad toho, jak se zobrazuje na kartě profil v aplikacích Microsoft, LinkedIn informace:

![Povolení integrace LinkedIn ve vaší organizaci](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Povolit a oznamujeme integrace LinkedIn

Musíte být správce Azure Active Directory ke správě nastavení pro vaši organizaci. Můžete ji povolit pro všechny uživatele, nebo pro konkrétní skupinu uživatelů.

1. Chcete-li povolit nebo zakázat integraci, postupujte podle kroků v [souhlas integrace LinkedIn ve vaší organizaci Azure AD](linkedin-integration.md).
2. Když oznamujeme integrace LinkedIn ve vaší organizaci, přejděte na vaši uživatelé v části Nejčastější dotazy o [LinkedIn informace v aplikacím a službám Microsoftu](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Tento článek poskytuje informace o tom, kde informace LinkedIn se zobrazí, jak se připojit účty a další.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Souhlas uživatele pro přístup k datům v Microsoft a LinkedIn

Data, která je přístupná z Linkedinu není trvale uložený ve službě Microsoft. Data, která je přístupná z Microsoft není trvale uložen s Linkedinem, s výjimkou kontakty. Microsoft Contacts jsou uložené na Linkedinu dokud uživatelé odebrat je, jak je popsáno v [odstraňování kontaktů importované z Linkedinu](https://www.linkedin.com/help/linkedin/answer/43377).

Když se uživatelé připojí svoje účty, jsou k dispozici v některých aplikacích Microsoftu, jako je karta profilu informace a přehledy z Linkedinu. Uživatelé také mohou očekávat své síťové prostředí na Linkedinu vylepšené a rozšiřují o informace od Microsoftu.
Pokud uživatelé ve vaší organizaci připojit svou práci LinkedIn a Microsoft nebo školních účtů, mají dvě možnosti:

* Udělení oprávnění pro přístup z obou účtů data. To znamená, že udělit oprávnění pro jejich společnost Microsoft nebo pracovní účet pro přístup k datům z jejich účet LinkedIn a to u [svůj účet LinkedIn, přístup k datům z jejich pracovní nebo školní účet](https://www.linkedin.com/help/linkedin/answer/84077).
* Udělení oprávnění pro LinkedIn data své společnosti Microsoft přístup k pracovní a školní účet.

Uživatelé mohou odpojení účtů a odeberte oprávnění pro přístup k datům v okamžiku, a [uživatelé můžou řídit, jak zobrazit své vlastní profilu LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), včetně Určuje, zda jejich profil může zobrazovat v aplikacích Microsoftu.

### <a name="linkedin-account-data"></a>Data účtu LinkedIn

Když připojíte účtů Microsoft a LinkedIn, dáváte linkedinu svolení poskytovat následující data společnosti Microsoft:

* Data profilu – zahrnuje identita LinkedIn, kontaktní informace a informace o sdílení s ostatními ve vaší [profilu LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Zajímá data – zahrnuje zájmy na Linkedinu, jako jsou lidé a témata postupovat podle kurzů skupiny a obsah je, jako je a sdílet.
* Předplatná data – zahrnuje předplatné LinkedIn aplikací a služeb společně s přidružená data. 
* Připojení dat – zahrnuje vaše [síť LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) také profily a kontaktní údaje z vašich připojení stupeň 1. dne.

Data, která je přístupná z Linkedinu není trvale uložený ve službě Microsoft. Další informace o používání společnosti Microsoft osobní údaje, najdete v článku [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Microsoft pracovní nebo školní data účtu

Když se připojíte účtů Microsoft a LinkedIn, udělujete Microsoftu na LinkedIn, které poskytují následující data:

* Data profilu – obsahuje informace, jako jsou vaše křestní jméno, název, profilové fotky, e-mailovou adresu, správce a poslední osoby, které spravujete.
* Data v kalendáři – zahrnuje ve vašich kalendářích, jejich časy, umístění a kontaktní údaje účastníků schůzky. Informace o schůzce, jako je program, obsah nebo splnění název není součástí kalendářní data.
* Zajímá data – zahrnuje zájmu spojené s vaším účtem, založené na používání služeb Microsoft, jako je Cortana a Bing pro firmy.
* Předplatná data – zahrnuje předplatná, poskytnuté vaší organizací Microsoft aplikacích a službách, jako je Office 365.
* Kontaktuje data – zahrnuje seznamy kontaktů v Outlooku, Skypu a dalších službách účtu Microsoft, včetně kontaktních informací pro lidé často komunikaci nebo spolupráci s. Kontakty bude pravidelně importované uložený a používá LinkedIn, třeba navrhnout připojení pomáhá uspořádat kontakty a zobrazit aktualizace o kontaktech.

Data, která je přístupná z Microsoft není trvale uložen s Linkedinem, s výjimkou kontakty. Microsoft Contacts jsou uloženy na LinkedIn, dokud neodeberete uživatelé. Další informace o [odstraňování kontaktů importované z Linkedinu](https://www.linkedin.com/help/linkedin/answer/43377).

Další informace o použití na LinkedIn osobní údaje, najdete v článku [zásady ochrany osobních údajů LinkedIn](https://www.linkedin.com/legal/privacy-policy). Pro LinkedIn služby, přenos dat a úložiště, může tok dat z Evropské unie Spojených států a zpět a ochranu vašich osobních údajů je chráněný, jak je popsáno v [přenosů dat v rámci Evropské unie](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Další postup

* [LinkedIn v aplikacích Microsoftu pomocí svého pracovního nebo školního účtu](https://www.linkedin.com/help/linkedin/answer/84077)
