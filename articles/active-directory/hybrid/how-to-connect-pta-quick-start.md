---
title: Azure AD předávací ověřování – rychlý start | Dokumentace Microsoftu
description: Tento článek popisuje, jak začít pracovat s Azure Active Directory (Azure AD) předávací ověřování.
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba5455680647b90b113d31c55816a2e0b0131b33
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617797"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Předávací ověřování služby Azure Active Directory: Rychlý start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Nasazení Azure AD předávací ověřování

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům se přihlásit k místním i cloudovým aplikacím pomocí přihlašovali stejnými hesly. Předávací ověřování přihlášení uživatele pomocí ověřování hesla přímo proti místní služby Active Directory.

>[!IMPORTANT]
>Pokud migrujete ze služby AD FS (nebo jiné technologie federation) na předávací ověřování, doporučujeme, abyste postupovali podle našeho podrobné nasazení Průvodce publikování [tady](https://aka.ms/adfstoPTADPDownload).

Postupujte podle těchto pokynů můžete nasadit ve svém tenantovi předávací ověřování:

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola požadavků

Ujistěte se, že jsou splněné následující požadavky.

### <a name="in-the-azure-active-directory-admin-center"></a>V Centru pro správu Azure Active Directory

1. Vytvoření účtu výhradně cloudového globálního správce ve svém tenantovi Azure AD. Tímto způsobem konfiguraci svého tenanta můžete spravovat, by měl místních služeb selhat nebo přestanou být dostupné. Další informace o [přidání účtu výhradně cloudového globálního správce](../active-directory-users-create-azure-portal.md). Dokončení tohoto kroku je důležité zajistit, že nezůstanete zamknutí mimo vašeho tenanta.
2. Přidejte jeden nebo více [vlastních názvů domén](../active-directory-domains-add-azure-portal.md) do svého tenanta Azure AD. Uživatelé se můžete přihlásit pomocí jedné z těchto názvů domény.

### <a name="in-your-on-premises-environment"></a>V místním prostředí

1. Identifikujte server se systémem Windows Server 2012 R2 nebo novější spustit Azure AD Connect. Pokud již, není povoleno [protokol TLS 1.2 na serveru](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Přidejte server do stejné doménové struktuře služby Active Directory jako uživatelé, jejichž hesla je potřeba ověřit.
2. Nainstalujte [nejnovější verzi služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) na serveru identifikován v předchozím kroku. Pokud už máte Azure AD Connect běží, ujistěte se, že verze je 1.1.750.0 nebo novější.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 máte problém související se synchronizace hodnot hash hesel. Pokud jste _není_ používat synchronizaci hodnot hash hesel ve spojení se předávací ověřování, přečtěte si [poznámky k verzi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Určit jeden nebo více dalších serverů (s Windows serverem 2012 R2 nebo novější, s protokolem TLS 1.2 povoleno) kde můžete spouštět samostatný agentů ověřování. Tyto další servery jsou potřebné k zajištění vysoké dostupnosti žádostí o přihlášení. Přidáte servery do stejné doménové struktuře služby Active Directory jako uživatelé, jejichž hesla je potřeba ověřit.

    >[!IMPORTANT]
    >V produkčním prostředí doporučujeme, abyste měli aspoň 3 agentů ověřování systémem ve svém tenantovi. Platí omezení systému 40 agentů ověřování každého tenanta. A jako osvědčený postup považovat všechny servery vrstvy 0 systémy spuštěná agentů ověřování (viz [odkaz](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Pokud je brána firewall mezi servery a službou Azure AD, nakonfigurujte následující položky:
   - Ujistěte se, že můžete provést agentů ověřování *odchozí* žádostí do služby Azure AD prostřednictvím následující porty:

     | Číslo portu | Jak se používá |
     | --- | --- |
     | **80** | Soubory ke stažení seznamů odvolaných certifikátů (CRL) při ověřování certifikátu SSL |
     | **443** | Zpracovává všechny odchozí komunikaci se službou |
     | **8080** (volitelné) | Agentů ověřování oznamují svůj stav každých deset minut přes port 8080, pokud je port 443 není k dispozici. Tento stav se zobrazí na portálu Azure AD. Port 8080 je _není_ používá pro přihlašování uživatelů. |
     
     Pokud brána firewall vynucuje pravidla podle původního uživatele, otevřete tyto porty pro provoz služby Windows, na kterých běží jako síťové služby.
   - Pokud je vaše brána firewall nebo proxy server umožňuje DNS na seznam povolených, seznam povolených připojení k  **\*. msappproxy.net** a  **\*. servicebus.windows.net**. Pokud ne, povolit přístup k [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), který se každý týden aktualizuje.
   - Agentů ověřování potřebovat přístup k **login.windows.net** a **login.microsoftonline.com** pro počáteční registraci. Otevřete firewall pro tyto adresy URL také.
   - Pro ověření certifikátu, odblokovat následující adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, a **www \.microsoft.com:80**. Protože tyto adresy URL se používají k ověření certifikátu s dalšími produkty Microsoftu, které už můžete mít tyto adresy URL odblokováno.

## <a name="step-2-enable-the-feature"></a>Krok 2: Povolení této funkce

Povolit předávací ověřování prostřednictvím [služby Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Na serveru primární nebo pracovní Azure AD Connect můžete povolit předávací ověřování. Důrazně doporučujeme, abyste povolili z primárního serveru. Pokud nastavujete pracovní server Azure AD Connect v budoucnu, můžete **musí** pokračujte výběrem předávací ověřování jako možnost přihlášení, zvolíte jinou možnost způsobí **zakázat** Předávací ověřování v tenantovi a přepsání nastavení na primárním serveru.

Pokud Azure AD Connect instalujete poprvé, zvolte [vlastní instalační cesta](how-to-connect-install-custom.md). Na **přihlášení uživatele** zvolte **předávací ověřování** jako **způsob přihlášení**. Při úspěšném dokončení předávací ověřovací Agent nainstalovaný na stejném serveru jako Azure AD Connect. Kromě toho že předávací ověřování je povolena funkce ve svém tenantovi.

![Azure AD Connect: Přihlášení uživatele](./media/how-to-connect-pta-quick-start/sso3.png)

Pokud jste již nainstalovali Azure AD Connect s použitím [Expresní instalace](how-to-connect-install-express.md) nebo [vlastní instalaci](how-to-connect-install-custom.md) cestu, vyberte **změnit přihlášení uživatele** úloh v Azure AD Připojení a pak vyberte **Další**. Potom vyberte **předávací ověřování** jako metodu přihlašování. Při úspěšném dokončení předávací ověřovací Agent je nainstalovaný na stejném serveru jako Azure AD Connect a aby byla povolená funkce ve svém tenantovi.

![Azure AD Connect: Změna přihlašovacích údajů uživatele](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Předávací ověřování je funkce úrovni tenanta. Zapnutí ovlivňuje přihlášení pro uživatele napříč _všechny_ spravovaných domén ve vašem tenantovi. Pokud přecházíte z Active Directory Federation Services (AD FS) na předávací ověřování, byste měli počkat alespoň 12 hodin před ukončením infrastruktury služby AD FS. Tato čekací doba je zajistit, že uživatelé můžete zachovat přihlášení k Exchange ActiveSync během přechodu. Další nápovědu o migraci ze služby AD FS na předávací ověřování, podívejte se na náš podrobný nasazení plán publikování [tady](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Krok 3: Otestovat funkci

Postupujte podle těchto pokynů a ověřte, že je povoleno předávací ověřování správně:

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. Vyberte **Azure Active Directory** v levém podokně.
3. Vyberte **služby Azure AD Connect**.
4. Ověřte, že **předávací ověřování** funkce se zobrazí jako **povoleno**.
5. Vyberte **předávací ověřování**. **Předávací ověřování** podokno obsahuje seznam serverů, kde jsou nainstalovaní agenti vaše ověření.

![Centrum správce Azure Active Directory: Podokno Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centrum správce Azure Active Directory: Podokno předávací ověřování](./media/how-to-connect-pta-quick-start/pta8.png)

V této fázi uživatelů ze všech spravovaných domén v tenantovi můžete přihlásit pomocí předávacího ověřování. Uživatele z federovaných domén se však nadále přihlašovat pomocí služby AD FS nebo jiného zprostředkovatele federace, kterou jste dříve nakonfigurovali. Při převodu domény z Federovaná do služby managed, všichni uživatelé z této domény, automaticky spustí, přihlášení pomocí předávacího ověřování. Předávací ověřování funkce neovlivňuje uživatelů pouze cloudu.

## <a name="step-4-ensure-high-availability"></a>Krok 4: Zajištění vysoké dostupnosti

Pokud plánujete nasadit předávací ověřování v produkčním prostředí, měli byste nainstalovat další samostatné agentů ověřování. Nainstalujte tyto ověřovacího agenta na servery _jiných_ než jeden začít Azure AD Connect. Toto nastavení poskytuje vysokou dostupnost pro požadavky přihlášení uživatele.

>[!IMPORTANT]
>V produkčním prostředí doporučujeme, abyste měli aspoň 3 agentů ověřování systémem ve svém tenantovi. Platí omezení systému 40 agentů ověřování každého tenanta. A jako osvědčený postup považovat všechny servery vrstvy 0 systémy spuštěná agentů ověřování (viz [odkaz](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Instalace více agentů předávací ověřování zajišťuje vysokou dostupnost, ale není deterministický rozložení zátěže mezi agentů ověřování. Chcete-li zjistit, kolik agentů ověřování je nutné pro vašeho tenanta, zvažte maximální a průměrné zatížení žádostí o přihlášení, které byste měli vidět ve svém tenantovi. Jako srovnávací test může zpracovávat jeden ověřovací Agent 300 až 400 ověření za sekundu na standardní 4jádrový procesor, 16 GB paměti RAM serveru.

Pokud chcete odhadnout síťový provoz, použijte následující pokyny velikosti:
- Velikost datové části je každý požadavek (0.5K + 1 tisíc * num_of_agents) bajtů. To znamená data ze služby Azure AD k ověřování agenta. Tady "num_of_agents" označuje, že počet agentů ověřování zaregistrovaný ve svém tenantovi.
- Každou odpověď má velikost datové části 1 kB; tj. data z agentů ověřování do služby Azure AD.

Pro většinu zákazníků jsou dostačující pro vysokou dostupnost a kapacitu celkem tři agentů ověřování. Měli byste nainstalovat agentů ověřování blízko řadičů domén tak zlepšit latenci přihlášení.

Pokud chcete začít, postupujte podle těchto pokynů ke stahování softwaru ověřovací Agent:

1. Chcete-li stáhnout nejnovější verzi agenta ověřování (verze 1.5.193.0 nebo novější), přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta.
2. Vyberte **Azure Active Directory** v levém podokně.
3. Vyberte **Azure AD Connect**vyberte **předávací ověřování**a pak vyberte **stáhnout agenta**.
4. Vyberte **přijmout podmínky a stáhnout** tlačítko.

![Centrum správce Azure Active Directory: Ověřovací Agent nebyl tlačítko pro stažení](./media/how-to-connect-pta-quick-start/pta9.png)

![Centrum správce Azure Active Directory: Stáhnout agenta podokno](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Můžete také přímo [stáhnout software ověřovací Agent nebyl](https://aka.ms/getauthagent). Přečtěte si a přijměte ověřovací Agent [podmínkami služby](https://aka.ms/authagenteula) _před_ její instalaci.

Existují dva způsoby, jak nasadit samostatný ověřovací Agent:

Nejdřív to zvládnete interaktivně jenom spuštěním staženého spustitelného souboru ověřovacího agenta a poskytuje přihlašovací údaje globálního správce vašeho tenanta po zobrazení výzvy.

Za druhé můžete vytvořit a spustit skript bezobslužné nasazení. To je užitečné, pokud chcete nasadit více agentů ověřování najednou, nebo instalace agentů ověřování na Windows serverech, které nemají uživatelské rozhraní povolená, nebo pomocí vzdálené plochy nelze získat přístup. Toto jsou pokyny o tom, jak pomocí tohoto postupu:

1. Spusťte následující příkaz k instalaci agenta ověřování: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Ověřovací Agent můžete zaregistrovat v naší službě pomocí Windows Powershellu. Vytvořte objekt pověření prostředí PowerShell `$cred` , která obsahuje uživatelské jméno globálního správce a heslo pro vašeho tenanta. Spusťte následující příkaz a nahraďte *\<uživatelské jméno\>* a  *\<heslo\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Přejděte na **C:\Program Files\Microsoft Azure AD Connect ověřovací Agent** a spusťte následující skript využívající `$cred` objekt, který jste vytvořili:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Pokud ověřovací Agent je nainstalovaný na virtuálním počítači, nemůže klonovat virtuální počítač, který chcete nastavit jinou ověřovací Agent. Tato metoda je **nepodporované**.

## <a name="step-5-configure-smart-lockout-capability"></a>Krok 5: Nakonfigurovat možnosti inteligentního uzamčení

Inteligentní uzamčení pomáhá při uzamčení nesprávnými účastníky, kteří se snaží uhodnout hesla uživatelů nebo pomocí metody hrubou silou. Tím, že nakonfigurujete nastavení inteligentním uzamčením ve službě Azure AD a / nebo nastavení odpovídající uzamčení v místní službě Active Directory, dají se útoky odfiltrovat dřív, než dorazí služby Active Directory. Čtení [v tomto článku](../authentication/howto-password-smart-lockout.md) Další informace o tom, jak nakonfigurovat nastavení inteligentním uzamčením ve svém tenantovi k ochraně vašich uživatelských účtů.

## <a name="next-steps"></a>Další postup
- [Migrace ze služby AD FS na předávací ověřování](https://aka.ms/adfstoptadp) – podrobné pokyny k migraci ze služby AD FS (nebo jiné technologie federation) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md): Zjistěte, jak nakonfigurovat možnosti inteligentního uzamčení ve svém tenantovi k ochraně uživatelské účty.
- [Aktuální omezení](how-to-connect-pta-current-limitations.md): Zjistěte, jaké postupy se podporují pomocí předávacího ověřování a ty, které nejsou.
- [Podrobné technické informace](how-to-connect-pta-how-it-works.md): Vysvětlení fungování funkce předávací ověřování.
- [Nejčastější dotazy k](how-to-connect-pta-faq.md): Najděte odpovědi na nejčastější dotazy.
- [Řešení potíží s](tshoot-connect-pass-through-authentication.md): Zjistěte, jak řešit běžné problémy s funkcí předávací ověřování.
- [Podrobné informace o zabezpečení](how-to-connect-pta-security-deep-dive.md): Získáte technické informace o funkci předávací ověřování.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md): Další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fórum Azure Active Directory pomocí do souboru žádostí o nové funkce.
