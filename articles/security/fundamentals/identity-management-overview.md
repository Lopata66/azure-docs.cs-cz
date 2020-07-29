---
title: Funkce zabezpečení Azure, které vám pomůžou se správou identit | Microsoft Docs
description: " Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které vám pomůžou se správou identit. Řešení pro správu identit a přístupu od Microsoftu umožňují IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a do cloudu a umožnit tak další úrovně ověřování, například Multi-Factor Authentication a zásady podmíněného přístupu. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: d6c82b1070f76cedcd84b8cd8a0a7bb6f683110d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285318"
---
# <a name="azure-identity-management-security-overview"></a>Přehled zabezpečení služby Azure Identity Management

 Správa identit je proces ověřování a autorizace [objektů zabezpečení](/windows/security/identity-protection/access-control/security-principals). Zahrnuje také řízení informací o těchto objektech zabezpečení (identity). Objekty zabezpečení (identity) můžou zahrnovat služby, aplikace, uživatele, skupiny atd. Řešení pro správu identit a přístupu od Microsoftu umožňují IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a do cloudu. Taková ochrana umožňuje další úrovně ověřování, například Multi-Factor Authentication a zásady podmíněného přístupu. Monitorování podezřelých aktivit prostřednictvím pokročilých sestav zabezpečení, auditování a upozorňování pomáhá zmírnit možné problémy se zabezpečením. [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) poskytuje jednotné přihlašování (SSO) k tisícům aplikací SaaS (Cloud software as a Service) a přístup k webovým aplikacím, které spouštíte místně.
 
Díky výhodám zabezpečení Azure Active Directory (Azure AD) můžete:

* Umožňuje vytvořit a spravovat jednu identitu pro každého uživatele v celém podniku a přitom zajistit synchronizaci uživatelů, skupin a zařízení. 
* Poskytněte přístup k vašim aplikacím bez jednotného přihlašování, včetně tisíců předem integrovaných aplikací SaaS.
* Zajistit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat vícefaktorové ověřování na základě pravidel.
* Zajištění zabezpečeného vzdáleného přístupu k místním webovým aplikacím prostřednictvím Azure Proxy aplikací služby AD.

Cílem tohoto článku je poskytnout přehled základních funkcí zabezpečení Azure, které vám pomůžou se správou identit. Poskytujeme také odkazy na články, které poskytují podrobné informace o jednotlivých funkcích, abyste se mohli dozvědět víc.  

Článek se zaměřuje na následující základní možnosti správy identit Azure:

* Jednotné přihlašování
* Reverzní proxy server
* Multi-Factor Authentication
* Řízení přístupu na základě role (RBAC)
* Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení
* Správa identit a přístupu uživatelů
* Registrace zařízení
* Privileged Identity Management
* Ochrana identit
* Hybridní Správa identit/Azure AD Connect
* Kontroly přístupu Azure AD

## <a name="single-sign-on"></a>Jednotné přihlašování

SSO znamená přístup k všem aplikacím a prostředkům, které musíte udělat v podniku, a to tak, že se jednou přihlásíte pomocí jednoho uživatelského účtu. Jakmile se přihlásíte, budete mít přístup ke všem aplikacím, které potřebujete, aniž by bylo nutné je ověřit (například zadat heslo) podruhé.

Mnoho organizací spoléhá na SaaS aplikace, jako je například Office 365, box a Salesforce, pro produktivitu uživatelů. Historicky zaměstnanci IT potřebují pro jednotlivé aplikace SaaS vytvářet a aktualizovat uživatelské účty a uživatelé si museli pamatovat heslo pro každou aplikaci SaaS.

Azure AD rozšiřuje místní prostředí Active Directory do cloudu a umožňuje uživatelům používat jejich primární účet organizace k přihlašování nejen ke svým zařízením připojeným k doméně a prostředkům společnosti, ale také ke všem webovým a SaaS aplikacím, které potřebují pro své úlohy.

Nejen uživatelé nepotřebují spravovat více sad uživatelských jmen a hesel, můžete automaticky zřídit nebo zrušit přístup k aplikaci na základě jejich organizačních skupin a jejich stavu zaměstnanců. Azure AD zavádí zabezpečení a přístup k řízení zásad správného řízení, pomocí kterých můžete centrálně spravovat přístup uživatelů napříč SaaS aplikacemi.

Další informace:

* [Přehled jednotného přihlašování](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrace Azure Active Directory jednotného přihlašování s aplikacemi SaaS](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Reverzní proxy server

Azure Proxy aplikací služby AD umožňuje publikovat místní aplikace, jako jsou weby [služby SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [aplikace Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)a aplikace založené na [službě IIS](https://www.iis.net/), v rámci privátní sítě a poskytuje zabezpečený přístup uživatelům mimo vaši síť. Proxy aplikací poskytuje vzdálený přístup a jednotné přihlašování pro mnoho typů místních webových aplikací s tisíci SaaS aplikacemi, které Azure AD podporuje. Zaměstnanci se můžou k aplikacím přihlašovat z domova na svých vlastních zařízeních a ověřovat prostřednictvím tohoto cloudového proxy serveru.

Další informace:

* [Povolení služby Azure Proxy aplikací služby AD](/azure/active-directory/manage-apps/application-proxy-enable)
* [Publikování aplikací pomocí proxy aplikace služby Azure AD](/azure/active-directory/active-directory-application-proxy-publish)
* [Jednotné přihlašování s využitím proxy aplikací](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Práce s podmíněným přístupem](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication je metoda ověřování, která vyžaduje použití více než jedné metody ověřování a přidává kritickou druhou vrstvu zabezpečení pro přihlášení a transakce uživatelů. Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plnit požadavky uživatelů na jednoduchý proces přihlašování. Poskytuje silné ověřování prostřednictvím řady možností ověřování: telefonní hovory, textové zprávy nebo oznámení mobilní aplikace nebo ověřovací kódy a tokeny OAuth třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak funguje Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

RBAC je systém autorizace založený na Azure Resource Manageru, který poskytuje přesnou správu přístupu k prostředkům v Azure. RBAC umožňuje podrobné řízení úrovně přístupu uživatelů. Můžete například omezit uživatele tak, aby spravovali jenom virtuální sítě a jiného uživatele a spravovali všechny prostředky ve skupině prostředků. Azure obsahuje několik předdefinovaných rolí, které můžete využít. V následujícím seznamu najdete čtyři základní předdefinované role. První tři se vztahují ke všem typům prostředků.

- [Owner (vlastník](/azure/role-based-access-control/built-in-roles#owner) ) – má úplný přístup ke všem prostředkům, včetně práva delegovat přístup ostatním uživatelům. 
- [Přispěvatel](/azure/role-based-access-control/built-in-roles#contributor) – může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup jiným uživatelům.
- [Čtenář](/azure/role-based-access-control/built-in-roles#reader) – může zobrazit existující prostředky Azure.
- [Správce přístupu uživatele](/azure/role-based-access-control/built-in-roles#user-access-administrator) – umožňuje spravovat přístup uživatelů k prostředkům Azure.

Další informace:

* [Co je řízení přístupu na základě role Azure (Azure RBAC)?](/azure/role-based-access-control/overview)
* [Předdefinované role Azure](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení, které identifikují nekonzistentní vzory přístupu, vám můžou přispět k ochraně vaší firmy. K získání přehledu o integritě a zabezpečení adresáře vaší organizace můžete použít sestavy přístupu a využití Azure AD. S těmito informacemi může správce adresáře lépe určit, kde můžou být potenciální bezpečnostní rizika, aby mohli přiměřeně naplánovat zmírnění těchto rizik.

Sestavy v Azure Portal spadají do následujících kategorií:

* **Sestavy anomálií**: obsahují události přihlášení, které jsme zjistili, že jsou neobvyklé. Naším cílem je, abyste věděli o takové činnosti a bylo možné určit, jestli je událost podezřelá.
* **Integrované sestavy aplikací**: poskytují přehled o tom, jak se ve vaší organizaci používají cloudové aplikace. Azure AD nabízí integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: označuje chyby, které mohou nastat při zřizování účtů pro externí aplikace.
* **Sestavy specifické pro uživatele**: zobrazení dat aktivit přihlašování zařízení pro určitého uživatele.
* **Protokoly aktivit**: obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dnů, a změny aktivity skupiny a aktivity resetování hesla a registrace.

Další informace:

* [Zobrazení sestav přístupů a používání](/azure/active-directory/active-directory-view-access-usage-reports)
* [Začínáme s vytvářením sestav Azure Active Directory](/azure/active-directory/active-directory-reporting-getting-started)
* [Azure Active Directory průvodce vytvářením sestav](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu uživatelů

Azure AD B2C je vysoce dostupná, globální služba pro správu identit pro zákaznické aplikace, která se škáluje na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Vaši uživatelé se můžou přihlásit ke všem aplikacím prostřednictvím přizpůsobitelného prostředí pomocí svých stávajících účtů sociálních sítí nebo vytvořením nových přihlašovacích údajů.

V minulosti si vývojáři aplikací, kteří chtěli zaregistrovat zákazníky a přihlásili jim své aplikace, museli napsat svůj vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure AD B2C nabízí vaší organizaci lepší způsob integrace správy identit uživatelů do aplikací s využitím zabezpečené, standardizované platformy a rozsáhlé sady rozšiřitelných zásad.

Když použijete Azure AD B2C, můžou si vaši zákazníci zaregistrovat své aplikace pomocí svých stávajících účtů sociálních sítí (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nových přihlašovacích údajů (e-mailová adresa a heslo nebo uživatelského jména a hesla).

Další informace:

* [Co je Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C ve verzi Preview: Registrace a přihlašování uživatelů aplikace](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C Preview: typy aplikací](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Registrace zařízení

Registrace zařízení Azure AD je základem pro scénáře [podmíněného přístupu](/azure/active-directory/active-directory-conditional-access-device-registration-overview) na základě zařízení. Když je zařízení zaregistrované, poskytne vám registrace zařízení Azure AD identitu, kterou používá k ověření zařízení, když se uživatel přihlásí. Ověřené zařízení a atributy zařízení se pak dají použít k prosazování zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu i v místním prostředí.

Při kombinaci s řešením správy mobilních zařízení, jako je Intune, se atributy zařízení ve službě Azure AD aktualizují o další informace o zařízení. Pak můžete vytvořit pravidla podmíněného přístupu, která vynutily přístup ze zařízení, aby splňovaly vaše standardy zabezpečení a dodržování předpisů.

Další informace:

* [Začínáme s registrací zařízení Azure AD](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatická registrace zařízení ve službě Azure AD pro zařízení připojená k doméně Windows](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Nastavení automatické registrace zařízení se systémem Windows připojených k doméně pomocí Azure AD](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Pomocí Azure AD Privileged Identity Management můžete spravovat, řídit a monitorovat své privilegované identity a přistupovat k prostředkům v Azure AD a také k dalším online služby Microsoftu, jako je třeba Office 365 a Microsoft Intune.

Uživatelé někdy potřebují provádět privilegované operace v prostředcích Azure nebo Office 365 nebo v jiných aplikacích SaaS. To často znamená, že organizace musí uživatelům udělit trvalý privilegovaný přístup ve službě Azure AD. Takový přístup je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůžou dostatečně monitorovat, co dělají uživatelé s oprávněními správce. Kromě toho platí, že pokud dojde k ohrožení bezpečnosti uživatelského účtu s privilegovaným přístupem, může toto porušení ovlivnit celkové cloudové zabezpečení organizace. Azure AD Privileged Identity Management pomáhá zmírnit toto riziko.

Pomocí Azure AD Privileged Identity Management můžete:

* Podívejte se, kteří uživatelé jsou správci služby Azure AD.
* Povolit přístup pro správu JIT (just-in-time) na vyžádání ke službám Microsoftu, jako je třeba Office 365 a Intune.
* Získejte sestavy o historii přístupu správce a změnách v přiřazeních správce.
* Získejte výstrahy týkající se přístupu k privilegované roli.

Další informace:

* [Co je Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Přiřazení rolí adresáře Azure AD v PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrana identit

Azure AD Identity Protection je služba zabezpečení, která poskytuje konsolidovanou pohled na detekci rizik a potenciální ohrožení zabezpečení, která ovlivňují identity vaší organizace. Identity Protection využívá stávající možnosti detekce anomálií Azure AD, které jsou k dispozici prostřednictvím sestav aktivit Azure AD neobvyklé. Identity Protection také přináší nové typy detekce rizik, které mohou detekovat anomálie v reálném čase.

Další informace:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Kanál 9: Azure AD a identity show: identita Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybridní Správa identit/Azure AD Connect

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tomu se říká hybridní identita. Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity. To umožní poskytovat společnou identitu pro uživatele pro aplikace Office 365, Azure a SaaS integrované s Azure AD. Má následující funkce:

* Synchronizace
* Integrace AD FS a federace
* Předávací ověřování
* Monitorování stavu

Další informace:

* [Dokument White Paper hybridní identity](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog týmu Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Kontroly přístupu Azure AD

Kontroly přístupu Azure Active Directory (Azure AD) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení privilegovaných rolí.

Další informace:

* [Kontroly přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)
