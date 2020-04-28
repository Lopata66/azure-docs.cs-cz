---
title: Řešení potíží s uzamknutím účtu v Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné problémy, které způsobují, že uživatelské účty budou v Azure Active Directory Domain Services uzamčeny.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743448"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Řešení problémů s uzamknutím účtu pomocí spravované domény Azure AD Domain Services

Aby se předešlo opakovaným pokusům o přihlášení, Azure služba AD DS uzamkne účty po definované prahové hodnotě. K uzamčení účtu může dojít i v případě nehody bez incidentu útoku na přihlášení. Pokud třeba uživatel opakovaně zadá nesprávné heslo nebo se služba pokusí použít původní heslo, účet se zamkne.

V tomto článku najdete informace o tom, proč dochází k uzamčení účtu a jak můžete nakonfigurovat chování a jak zkontrolovat audity zabezpečení pro řešení potíží s událostmi uzamčení.

## <a name="what-is-an-account-lockout"></a>Co je uzamčení účtu?

Uživatelský účet v Azure služba AD DS je uzamčený, když byla splněna definovaná prahová hodnota pro neúspěšné pokusy o přihlášení. Toto chování uzamčení účtu je navrženo tak, aby vás chránilo před opakovanými pokusy o přihlašování hrubou silou, které můžou znamenat automatizovaný digitální útok.

**Ve výchozím nastavení platí, že pokud se v 2 minutách vyskytne 5 špatný počet pokusů o zadání hesla, účet se zablokuje na 30 minut.**

Výchozí prahové hodnoty pro uzamknutí účtu se konfigurují pomocí jemně odstupňovaných zásad hesel. Pokud máte konkrétní sadu požadavků, můžete tyto výchozí prahové hodnoty uzamčení účtu přepsat. Nedoporučuje se ale zvyšovat prahové limity, abyste se pokusili snížit počet uzamčení účtů. Nejprve vyřešte potíže se zdrojem uzamčení účtu.

### <a name="fine-grained-password-policy"></a>Jemně odstupňované zásady hesel

Jemně odstupňované zásady hesel (FGPPs) umožňují použít specifická omezení pro zásady hesel a uzamčení účtů pro různé uživatele v doméně. Podrobné zásady má vliv jenom na uživatele v rámci spravované domény Azure služba AD DS. Uživatelé cloudu a uživatelé domény synchronizovaný do spravované domény Azure služba AD DS ve službě Azure AD jsou ovlivněné zásadami hesel v rámci Azure služba AD DS. Jejich účty ve službě Azure AD nebo v místním adresáři nejsou ovlivněny.

Zásady se distribuují prostřednictvím přidružení skupiny ve spravované doméně Azure služba AD DS a veškeré změny, které provedete, se uplatní při přihlášení dalšího uživatele. Změna zásad neodemkne uživatelský účet, který je už uzamčený.

Další informace o podrobných zásadách pro hesla a rozdílech mezi uživateli vytvořenými přímo v Azure služba AD DS v porovnání se službou Azure AD najdete v tématu [Konfigurace zásad hesel a uzamčení účtů][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Běžné důvody uzamčení účtu

Nejčastější důvody, proč je účet uzamčený bez jakéhokoli škodlivého záměru nebo faktorů, zahrnují následující scénáře:

* **Uživatel byl uzamčen.**
    * Uživatel po změně hesla pokračoval v používání předchozího hesla? Výchozí zásada uzamčení účtu o 5 neúspěšných pokusů za 2 minuty může být způsobená tím, že uživatel nechtěně opakuje původní heslo.
* **Existuje aplikace nebo služba, které mají staré heslo.**
    * Pokud se účet používá v aplikacích nebo službách, můžou se tyto prostředky opakovaně pokoušet přihlásit pomocí starého hesla. Toto chování způsobí, že dojde k uzamknutí účtu.
    * Zkuste minimalizovat použití účtu napříč několika různými aplikacemi nebo službami a zaznamenejte si, kde se používají přihlašovací údaje. Pokud dojde ke změně hesla účtu, aktualizujte odpovídající aplikace nebo služby.
* **Heslo bylo změněno v jiném prostředí a nové heslo dosud nebylo synchronizováno.**
    * Pokud se heslo účtu změní mimo Azure služba AD DS, například v prostředí Prem služba AD DS, může trvat několik minut, než se změna hesla synchronizuje přes Azure AD a do Azure služba AD DS.
    * Uživatel, který se pokusí přihlásit k prostředku prostřednictvím služby Azure služba AD DS před dokončením procesu synchronizace hesel způsobí, že se účet zamkne.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Řešení potíží s uzamčeními účtů pomocí auditů zabezpečení

Pokud chcete řešit potíže, když dojde k událostem uzamčení účtu a odkud přicházejí, [Povolte audity zabezpečení pro Azure služba AD DS][security-audit-events]. Události auditu se zaznamenávají jenom z doby, kdy tuto funkci povolíte. V ideálním případě byste měli povolit audity zabezpečení *předtím, než* dojde k problému s uzamknutím účtu. Pokud má uživatelský účet opakovaně problémy s uzamčením, můžete povolit audity zabezpečení, které jsou připravené k dalšímu výskytu situace.

Po povolení auditů zabezpečení se v následujících ukázkových dotazech ukáže, jak zkontrolovat *události uzamčení účtu*, kód *4740*.

Zobrazit všechny události uzamčení účtu za posledních sedm dní:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Zobrazit všechny události uzamčení účtu za posledních sedm dní pro účet s názvem *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Zobrazit všechny události uzamčení účtu v rozmezí 26. června 2019 v 9:00 a od 1. července 2019 o půlnoci seřazené vzestupně podle data a času:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Další kroky

Další informace o jemně odstupňovaných zásadách pro hesla pro úpravu prahových hodnot uzamčení účtů najdete v tématu [Konfigurace zásad hesel a uzamčení účtů][configure-fgpp].

Pokud stále máte problémy s připojením k VIRTUÁLNÍmu počítači do spravované domény Azure služba AD DS, [Najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
