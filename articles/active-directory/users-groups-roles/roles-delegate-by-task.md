---
title: Delegujte nejméně privilegovaných rolí pomocí Správce úloh – Azure Active Directory | Dokumentace Microsoftu
description: Role pro delegování identity úlohy v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59c06ae83327683942885190e4b401617dc020f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468313"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Role správce ve Správci úloh v Azure Active Directory

V tomto článku najdete informace potřebné k omezení oprávnění správce uživatele tak, že přiřadíte nejméně privilegované role v Azure Active Directory (Azure AD). Vás bude správce úkoly uspořádané podle oblasti funkce a nejméně privilegovaných rolí potřebná k provedení každý úkol, společně s další bez oprávnění globálního správce rolí, které můžete provést úlohu.

## <a name="application-proxy"></a>Proxy aplikací

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurovat aplikace proxy aplikací | Správce aplikací | 
Konfigurace vlastností skupiny konektoru | Správce aplikací | 
Vytvoření registrace aplikace, když je zakázaná možnost pro všechny uživatele | Vývojář aplikací | Správce cloudové aplikace, správce aplikace
Vytvořit skupinu konektorů | Správce aplikací | 
Odstranit skupinu konektorů | Správce aplikací | 
Zákaz proxy aplikací | Správce aplikací | 
Stáhnout službu konektoru | Správce aplikací | 
Číst všechny konfigurace | Správce aplikací | 

## <a name="b2c"></a>B2C

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Vytvoření adresáře Azure AD B2C | Všichni uživatelé – typu Host ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vytvoření aplikace B2C | Globální správce | 
Vytváření podnikových aplikací | Správce cloudových aplikací | Správce aplikací
Vytvářet, číst, aktualizovat a odstraňovat B2C policies | Globální správce | 
Vytvářet, číst, aktualizovat a odstranit zprostředkovatele identity | Globální správce | 
Vytvářet, číst, aktualizovat a odstranit toky uživatelů pro resetování hesla | Globální správce | 
Vytvářet, číst, aktualizovat a odstranit toky uživatelů pro upravování profilu | Globální správce | 
Vytvoření, čtení, aktualizace a odstranění toků přihlášení uživatele | Globální správce | 
Vytvářet, číst, aktualizovat a odstranit tok registrace uživatele |Globální správce | 
Vytvoření, čtení, aktualizace a odstranění atributů uživatele | Globální správce | 
Vytvářet, číst, aktualizovat a odstranit uživatele | Globální správce ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Číst všechny konfigurace | Globální správce | 
Protokoly auditu pro čtení B2C | Globální správce ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

## <a name="company-branding"></a>Branding společnosti

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurace brandingu společnosti | Globální správce | 
Číst všechny konfigurace | Uživatelé s oprávněním ke čtení adresářů | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Vlastnosti společnosti

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurace vlastností společnosti | Globální správce | 

## <a name="connect"></a>Připojení

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Předávacího ověřování | Globální správce | 
Číst všechny konfigurace | Globální správce | 
Transparentní jednotné přihlašování | Globální správce | 

## <a name="connect-health"></a>Stav připojení

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Přidání nebo odstranění služby | Vlastník ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Použít opravy došlo k chybě synchronizace | Přispěvatel ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Vlastník
Konfigurace oznámení | Přispěvatel ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Vlastník
Konfigurace nastavení | Vlastník ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurace synchronizace oznámení | Přispěvatel ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Vlastník
Sestavy zabezpečení služby AD FS pro čtení | Čtenář zabezpečení | Přispěvatel, vlastník
Číst všechny konfigurace | Čtečka ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, vlastník
Chyby synchronizace pro čtení | Čtečka ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, vlastník
Služby synchronizace pro čtení | Čtečka ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, vlastník
Zobrazit metriky a výstrahy | Čtečka ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, vlastník
Zobrazit metriky a výstrahy | Čtečka ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, vlastník
Výstrahy a zobrazení metrik služby synchronizace | Čtečka ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Přispěvatel, vlastník


## <a name="custom-domain-names"></a>Vlastní názvy domén

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Spravovat domény | Globální správce | 
Číst všechny konfigurace | Uživatelé s oprávněním ke čtení adresářů | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Create Azure AD Domain Services instance | Globální správce | 
Provádět všechny úlohy služby Azure AD Domain Services | Skupiny Azure AD DC Administrators ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Číst všechny konfigurace | Čtenář předplatného Azure, který obsahuje službu AD DS | 

## <a name="devices"></a>Zařízení

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Zakázání zařízení | Správce cloudových zařízení | 
Povolení zařízení | Správce cloudových zařízení | 
Základní konfigurace pro čtení | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Klíče Bitlockeru pro čtení | Čtenář zabezpečení | Heslo správce, správce zabezpečení

## <a name="enterprise-applications"></a>Podnikové aplikace

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Souhlas s žádná delegovaná oprávnění | Správce cloudových aplikací | Správce aplikací
Souhlas s oprávnění aplikací bez Microsoft Graph nebo Azure AD Graph | Správce cloudových aplikací | Správce aplikací
Souhlas s aplikací oprávnění pro Microsoft Graph nebo Azure AD Graph | Globální správce | 
Dávat souhlas aplikacím přístup k vlastním datům | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vytvoření aplikace organizace | Správce cloudových aplikací | Správce aplikací
Správa Proxy aplikací | Správce aplikací | 
Správa nastavení uživatelů | Globální správce | 
Kontroly přístupu pro čtení, skupiny nebo aplikace | Čtenář zabezpečení | Správce zabezpečení, Správce uživatelů
Číst všechny konfigurace | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Aktualizovat přiřazení aplikace organizace | Vlastník aplikace Enterprise ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudové aplikace, správce aplikace
Aktualizovat počet vlastníků aplikace organizace | Vlastník aplikace Enterprise ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudové aplikace, správce aplikace
Aktualizovat vlastnosti aplikace organizace | Vlastník aplikace Enterprise ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudové aplikace, správce aplikace
Aktualizace zřizování podnikových aplikací | Vlastník aplikace Enterprise ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudové aplikace, správce aplikace
Aktualizovat aplikaci enterprise samoobslužných služeb | Vlastník aplikace Enterprise ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudové aplikace, správce aplikace
Aktualizovat vlastnosti jednotného přihlašování | Vlastník aplikace Enterprise ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce cloudové aplikace, správce aplikace

## <a name="groups"></a>Skupiny

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Přiřadit licenci | Správce uživatelů | 
Vytvořit skupinu | Správce uživatelů | 
Vytvoření, aktualizace nebo odstranění kontroly přístupu, skupiny nebo aplikace | Správce uživatelů | 
Správa platnosti skupiny | Správce uživatelů | 
Správa nastavení skupin | Globální správce | 
Načíst všechny konfiguraci (s výjimkou skrytých členství) | Uživatelé s oprávněním ke čtení adresářů | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Čtení skrytých členství | Člen skupiny | Vlastník skupiny, heslo správce, správce Exchange, správce Sharepointu, týmy správce, Správce uživatelů
Čtení členství ve skupinách s skrytých členství | Správce technické podpory | Uživatel správce, správce týmy
Odvolat licence | Správce licencí | Správce uživatelů
Aktualizovat členství ve skupině | Vlastník skupiny ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce uživatelů
Vlastníci skupiny aktualizací | Vlastník skupiny ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce uživatelů
Vlastnosti skupiny aktualizace | Vlastník skupiny ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Správce uživatelů

## <a name="identity-protection"></a>Identity Protection

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurace oznámení výstrah| Správce zabezpečení | 
Konfigurace a povolení nebo zakázání zásad vícefaktorového ověřování| Správce zabezpečení | 
Konfigurovat a povolit nebo zakázat zásady rizik přihlašování| Správce zabezpečení | 
Konfigurovat a povolit nebo zakázat zásady rizik uživatelů | Správce zabezpečení | 
Konfigurace týdenního přehledu | Správce zabezpečení| 
Skrýt všechny rizikové události | Správce zabezpečení | 
Vyřešit nebo zavřít ohrožení zabezpečení | Správce zabezpečení | 
Číst všechny konfigurace | Čtenář zabezpečení | 
Číst všechny rizikové události | Čtenář zabezpečení | 
Přečtěte si ohrožení zabezpečení | Čtenář zabezpečení | 

## <a name="licenses"></a>Licence

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Přiřadit licenci | Správce licencí | Správce uživatelů
Číst všechny konfigurace | Uživatelé s oprávněním ke čtení adresářů | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Odvolat licence | Správce licencí | Správce uživatelů
Vyzkoušejte a kupte si předplatné | Správce fakturace | 


## <a name="monitoring---audit-logs"></a>Monitorování – protokoly auditu

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Protokoly auditu pro čtení | Čtenář sestav | Čtenář zabezpečení, správce zabezpečení

## <a name="monitoring---sign-ins"></a>Monitorování – přihlášení

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Čtení protokolů přihlášení | Čtenář sestav | Čtenář zabezpečení, správce zabezpečení

## <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Odstranit všechna existující hesla aplikací vygenerovaná vybranými uživateli | Globální správce | 
Zakázání MFA | Globální správce | 
Povolení MFA | Globální správce | 
Správa nastavení služby MFA | Globální správce | 
Vyžadovat, aby vybraní uživatelé znovu zadali způsoby kontaktování | Správce ověřování | 
Obnovit vícefaktorové ověřování u všech zapamatovaných zařízení  | Správce ověřování | 

## <a name="mfa-server"></a>Server MFA

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Blokování a odblokování uživatelů | Globální správce | 
Konfigurace uzamčení účtu | Globální správce | 
Konfigurace pravidla ukládání do mezipaměti | Globální správce | 
Konfigurace upozornění na podvod | Globální správce
Konfigurace oznámení | Globální správce | 
Konfigurace bylo povoleno jednorázové přihlášení | Globální správce | 
Konfigurace nastavení telefonních hovorů. | Globální správce | 
Konfigurovat zprostředkovatele | Globální správce | 
Nakonfigurujte nastavení serveru | Globální správce | 
Přečíst sestavy aktivit | Globální správce | 
Číst všechny konfigurace | Globální správce | 
Umožňuje načíst stav serveru | Globální správce |  

## <a name="organizational-relationships"></a>Organizační vztahy

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Spravovat zprostředkovatele identity | Globální správce | 
Správa nastavení | Globální správce | 
Spravovat podmínky použití | Globální správce | 
Číst všechny konfigurace | Globální správce | 

## <a name="password-reset"></a>Resetování hesla

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurovat metody ověřování | Globální správce |
Konfigurace vlastního nastavení | Globální správce |
Konfigurace oznámení | Globální správce |
Konfigurovat místní integraci | Globální správce |
Konfigurace vlastností resetování hesla | Správce uživatelů | Globální správce
Konfigurace registrace | Globální správce |
Číst všechny konfigurace | Správce zabezpečení | Správce uživatelů |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Přiřazení uživatelů k rolím | Správce privilegovaných rolí | 
Konfigurace nastavení role | Správce privilegovaných rolí | 
Zobrazit aktivitu auditu | Čtenář zabezpečení | 
Členství v rolích zobrazení | Čtenář zabezpečení | 

## <a name="roles-and-administrators"></a>Role a správci

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Správa přiřazení rolí | Správce privilegovaných rolí | 
Kontrola přístupu pro čtení role Azure AD  | Čtenář zabezpečení | Správce zabezpečení, správce privilegovaných rolí
Číst všechny konfigurace | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Zabezpečení – metody ověřování

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurovat metody ověřování | Globální správce | 
Číst všechny konfigurace | Globální správce | 

## <a name="security---conditional-access"></a>Zabezpečení – podmíněný přístup

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Konfigurace vícefaktorového ověřování důvěryhodné IP adresy | Správce podmíněného přístupu | 
Vytvořit vlastní ovládací prvky | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření pojmenovaná umístění | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření zásad | Správce podmíněného přístupu | Správce zabezpečení
Vytvoření podmínek použití | Správce podmíněného přístupu | Správce zabezpečení
Vytvořit certifikát připojení VPN | Správce podmíněného přístupu | Správce zabezpečení
Odstranit klasické zásady | Správce podmíněného přístupu | Správce zabezpečení
Odstranění podmínek použití | Správce podmíněného přístupu | Správce zabezpečení
Odstranit certifikát připojení VPN | Správce podmíněného přístupu | Správce zabezpečení
Zakázat klasické zásady | Správce podmíněného přístupu | Správce zabezpečení
Správa vlastních ovládacích prvků | Správce podmíněného přístupu | Správce zabezpečení
Správa pojmenovaná umístění | Správce podmíněného přístupu | Správce zabezpečení
Spravovat podmínky použití | Správce podmíněného přístupu | Správce zabezpečení
Číst všechny konfigurace | Čtenář zabezpečení | Správce zabezpečení
Čtení pojmenovaná umístění | Čtenář zabezpečení | Podmíněný přístup správce, správce zabezpečení

## <a name="security---identity-security-score"></a>Zabezpečení – skóre zabezpečení identit

Úkol | Nejméně privilegovaných rolí | Další role | 
---- | --------------------- | ----------------
Číst všechny konfigurace | Čtenář zabezpečení | Správce zabezpečení
Skóre zabezpečení pro čtení | Čtenář zabezpečení | Správce zabezpečení
Aktualizovat stav události | Správce zabezpečení | 

## <a name="security---risky-sign-ins"></a>Zabezpečení – riziková přihlášení

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Číst všechny konfigurace | Čtenář zabezpečení | 
Přečtěte si riziková přihlášení | Čtenář zabezpečení | 

## <a name="security---users-flagged-for-risk"></a>Zabezpečení – uživatelé označení příznakem rizika

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Zavřít všechny události | Správce zabezpečení | 
Číst všechny konfigurace | Čtenář zabezpečení | 
Čtení uživatelů označených příznakem rizika | Čtenář zabezpečení | 

## <a name="users"></a>Uživatelé

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Přidání uživatele do role adresáře | Správce privilegovaných rolí | 
Přidá uživatele do skupiny | Správce uživatelů | 
Přiřadit licenci | Správce licencí | Správce uživatelů
Vytvořit uživatele typu Host | Odesílatel pozvánek hostů | Správce uživatelů
Vytvořit uživatele | Správce uživatelů | 
Odstranit uživatele | Správce uživatelů | 
Zneplatnit obnovovací tokeny omezených správců (viz dokumentace) | Správce uživatelů | 
Zneplatnit obnovovací tokeny bez oprávnění správce (viz dokumentace) | Správce hesel | Správce uživatelů
Zneplatnit obnovovací tokeny privilegovaných správců (viz dokumentace) | Globální správce | 
Základní konfigurace pro čtení | Výchozí role uživatele ([naleznete v dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Resetování hesel omezených správců (viz dokumentace) | Správce uživatelů | 
Resetování hesla bez oprávnění správce (viz dokumentace) | Správce hesel | Správce uživatelů
Resetovat heslo privilegovaných správců | Globální správce | 
Odvolat licence | Správce licencí | Správce uživatelů
Aktualizovat všechny vlastnosti, s výjimkou hlavní název uživatele | Správce uživatelů | 
Aktualizovat hlavní název uživatele (viz dokumentace) omezených správců | Správce uživatelů | 
Umožňuje aktualizovat vlastnost hlavní název uživatele na privilegované admins (viz dokumentace) | Globální správce | 
Aktualizovat uživatelské nastavení | Globální správce | 


## <a name="support"></a>Podpora

Úkol | Nejméně privilegovaných rolí | Další role
---- | --------------------- | ----------------
Odeslat lístek podpory | Správce služeb | Aplikace správce, správce, správce cloudové aplikace, správce dodržování předpisů, Dynamics 365 správce fakturace, Desktop Analytics správce, správce Exchange, správce hesel, Information Protection Správce, Správce služby Intune, Skype pro firmy správce, správce Power BI, správce privilegovaných ověřování, Správce služby SharePoint, správce komunikaci týmů, týmy správce, Správce uživatelů Správce Analytics pracoviště

## <a name="next-steps"></a>Další postup

* [Postup přiřazení nebo odebrání role Správce služby azure AD](directory-manage-roles-portal.md)
* [Odkazovat na role Správce služby Azure AD](directory-assign-admin-roles.md)
