---
title: Principy konektorů Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Popisuje základní informace o konektory Proxy aplikací Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51ad6ea2abcc18b985e9c45fbfb1ffba98fb2c1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66113089"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Principy konektorů Proxy aplikací Azure AD

Konektory se Proxy aplikací Azure AD je to možné. Jsou to jednoduché, snadné ho nasadit a udržovat a velmi výkonné. Tento článek popisuje, jaké konektory jsou, jak fungují a některé návrhy, jak optimalizovat nasazení vašeho řešení. 

## <a name="what-is-an-application-proxy-connector"></a>Co je konektor Proxy aplikací?

Konektory jsou zjednodušené agentů nacházejí v místním usnadnění odchozí připojení ke službě Proxy aplikací. Konektory musí být nainstalována v systému Windows Server, který má přístup k back-end aplikace. Konektory můžete uspořádat do skupiny konektorů se všemi skupinami zpracování provoz na konkrétní aplikace.

## <a name="requirements-and-deployment"></a>Požadavky a nasazení

Pokud chcete úspěšně nasadit Proxy aplikace, budete potřebovat alespoň jeden konektor, ale doporučujeme dva nebo více pro větší odolnost proti chybám. Nainstalujte connector v počítači se systémem Windows Server 2012 R2 nebo novější. Konektor potřebuje ke komunikaci s Proxy aplikace služby a místních aplikací, které publikujete. 

### <a name="windows-server"></a>Windows server
Potřebujete server s Windows serverem 2012 R2 nebo novější na kterých je nainstalován konektor Proxy aplikací. Server potřebuje pro připojení k Proxy aplikace služby v Azure a místních aplikací, které publikujete.

Windows server musí mít TLS 1.2 zapnutý předtím, než nainstalujete konektor Proxy aplikací. Existující konektory se starším než 1.5.612.0 budou nadále fungovat na dřívější verze TLS až do odvolání. Povolení protokolu TLS 1.2:

1. Nastavte následující klíče registru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Restartujte server


Další informace o požadavcích na síť pro konektor serveru najdete v tématu [začít pracovat s Proxy aplikací a nainstalovat konektor](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Údržba
Konektory a služba postará o jednotlivých úlohách vysokou dostupnost. Můžete třeba přidat či odebrat dynamicky. Pokaždé, když přijde nový požadavek přesměruje ho na některý konektory, které je aktuálně k dispozici. Pokud konektor není dočasně k dispozici, nebude reagovat na tento provoz.

Konektory jsou bezstavové a neobsahují žádná data konfiguraci na počítači. Pouze data, která jsou v nich uložené je nastavení pro připojení služby a její ověřovací certifikát. Při připojování ke službě, o přijetí změn všech požadovaných konfiguračních dat a aktualizovat ho každých několik minut.

Konektory také dotazování serveru a zjistěte, jestli je dostupná novější verze konektoru. Pokud ho najde, konektory aktualizovat sami.

Můžete monitorovat své konektory z počítače, které, na kterém běží, pomocí protokolu událostí a čítače výkonu. Nebo můžete zobrazit jejich stav na stránce Proxy aplikací na webu Azure portal:

 ![Konektory Proxy aplikací Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Není nutné ručně odstranit konektory, které nejsou používány. Konektor je spuštěný, zůstane aktivní jak připojí ke službě. Nepoužité konektory jsou označené jako _neaktivní_ a odeberou se po 10 dnů nečinnosti. Pokud chcete odinstalovat konektor, i když, odinstalujte službu konektoru i aktualizační službu ze serveru. Restartujte počítač, aby se službě úplně odebrala.

## <a name="automatic-updates"></a>Automatické aktualizace

Azure AD poskytuje automatické aktualizace pro všechny konektory, které nasadíte. Za předpokladu, službou Application Proxy Connector Updater k automatické aktualizaci konektoru. Pokud nevidíte konektor aktualizační službu na serveru, budete muset [znovu nainstalovat konektor](application-proxy-add-on-premises-application.md) zobrazíte všechny aktualizace. 

Pokud nechcete čekat automatických aktualizací na váš konektor, můžete provést ruční upgrade. Přejděte [stránku pro stažení konektoru](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serveru, kde je svůj konektor vyhledat a vybrat **Stáhnout**. Tento proces zahajuje upgrade pro místní konektor. 

U klientů s více konektorů jako cíl automatické aktualizace jeden konektor v daný okamžik v každé skupině redukován ve vašem prostředí. 

Po aktualizaci vašeho konektoru, pokud se můžete setkat s výpadky:  
- Máte jenom jeden konektor doporučujeme nainstalovat druhý konektor a [vytvořit skupinu konektorů, která](application-proxy-connector-groups.md). To zabraňuje výpadkům a zajištění vyšší dostupnosti.  
- Konektor byl uprostřed transakcí při zahájení aktualizace. I když dojde ke ztrátě počáteční transakce, váš prohlížeč automaticky operaci opakovat nebo můžete aktualizovat stránku. Když žádost se zopakuje, provoz se rozšíří do zálohování konektoru.

Pokud chcete zobrazit informace o dřív vydaných verzí a změny, které jsou patří, naleznete v tématu [Application Proxy - historie vydaných verzí](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Vytvoření skupiny konektorů

Skupiny konektorů umožňují přiřadit konkrétní konektory k poskytování určité aplikace. Můžete seskupit několik konektorů a potom přiřadit všechny aplikace, aby skupina. 

Skupiny konektorů usnadňují správu velkého nasazení. Také pomáhají zvýšit latenci pro klienty, kteří mají aplikace hostované v různých oblastech, protože můžete vytvořit skupiny konektorů založená na poloze k poskytování pouze místní aplikace. 

Další informace o skupinách konektoru najdete v tématu [publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Plánování kapacit 

Je důležité, abyste měli jistotu, že jste naplánovali dostatečnou kapacitu mezi konektorů pro zpracování očekávaného provozu. Doporučujeme vám, že má každá skupina konektor aspoň dva konektory k poskytování vysoké dostupnosti a škálování. V případě, že budete muset služby počítače v libovolném bodě je mít tři konektory optimální. 

V obecné, tím víc uživatelů, které máte, tím větší počítače s budete potřebovat. Níže je tabulka poskytuje přehled svazku a očekávaná latence, kterou může zpracovávat jiné počítače. Všimněte si je všechny založené na očekávaný transakce za druhé (TPS) místo uživatelem od použití vzorů se liší a nelze jej použít pro předpověď zatížení. Bude také některé rozdíly, na základě velikosti odpovědi a doba odezvy aplikace back-end – větší velikosti odpovědi a pomalejší doby odezvy bude účtovat nižší TPS Max. Doporučujeme také s další počítače tak, aby náklad napříč počítači vždy poskytuje dostatek vyrovnávací paměti. Kapacita navíc zajistí, abyste měli vysokou dostupnost a odolnost proti chybám.

|Jádra|Paměť RAM|Byl očekáván latence (MS)-P99|Maximální počet TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Tento počítač používat vlastní nastavení pro vyvolání některé výchozí limity připojení nad rámec .NET doporučené nastavení. Doporučujeme spustit test s výchozím nastavením, než se obrátíte na podporu, abyste mohli tento limit změnit pro vašeho tenanta.
 
>[!NOTE]
>Není k dispozici velký rozdíl v maximální TPS mezi 4, 8 a 16 jader počítače. Hlavní rozdíl mezi těmito probíhá očekávaná latence.  

## <a name="security-and-networking"></a>Zabezpečení a sítě

Konektory lze nainstalovat kdekoli v síti, která umožňuje jejich k odesílání požadavků na službu Proxy aplikací. Důležité je, že má počítač spuštěným konektorem také přístup k vašim aplikacím. Konektory můžete nainstalovat uvnitř vaší podnikové síti nebo na virtuálním počítači, na kterém běží v cloudu. Konektory lze spustit v hraniční síti, označované také jako demilitarizovaná zóny (DMZ), ale není nutné, protože je výstupní veškerý provoz, proto síti zůstane v bezpečí.

Konektory odeslat pouze odchozích požadavků. Odeslání odchozího provozu pro službu Proxy aplikací a k publikovaným aplikacím. Není nutné otevřít příchozí porty, protože proudí obou směrech po vytvoření relace. Také není nutné konfigurovat příchozí přístup přes brány firewall. 

Další informace o konfiguraci odchozí pravidla brány firewall najdete v tématu [práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md).


## <a name="performance-and-scalability"></a>Výkon a škálovatelnost

Škálování pro službu Proxy aplikací je transparentní, ale škálovací faktor pro konektory. Musíte mít dostatek konektorů pro zpracování provozu ve špičce. Vzhledem k tomu, že konektory jsou bezstavové, nejsou ovlivněny počet uživatelů a relací. Místo toho reagují, počet požadavků a jejich velikost datové části. Pomocí standardních webových přenosů průměrná počítače může zpracovávat několik tisíc požadavků za sekundu. Konkrétní kapacity závisí na vlastnosti přesné počítače. 

Výkon konektoru je vázán procesoru a sítě. Výkon procesoru je potřeba pro šifrování SSL a dešifrování, zatímco sítě je potřeba získat rychlé připojení k aplikacím a příslušné službě online Services v Azure.

Naproti tomu paměti je takový problém pro konektory. Online služby se postará o velkou část zpracování a všechny neověřené provoz. Všechno, co se dá dělat v cloudu se provádí v cloudu. 

Pokud z nějakého důvodu nedostupný konektoru nebo počítač, provoz se začne na jiný konektor ve skupině. Tato odolnost proti chybám je také proč doporučujeme mít více konektorů.

Dalším faktorem, který má vliv na výkon je kvalita sítě mezi konektorů, včetně: 

* **Služba online**: Pomalá nebo vysokou latencí připojení ke službě Proxy aplikací v Azure mají vliv na výkon konektoru. Pro zajištění nejlepšího výkonu připojení k Azure pomocí Expressroute vaší organizace. V opačném případě máte síťovým týmem Ujistěte se, že připojení k Azure jsou zpracovány jako efektivně. 
* **Back-endové aplikace**: V některých případech jsou další proxy mezi konektoru a back-endové aplikace, které mohou zpomalit nebo zakázat připojení. Tento scénář lze vyřešit, otevřete prohlížeč ze serveru konektoru a pokusí o přístup k aplikaci. Pokud spustíte konektorů v Azure, ale aplikace jsou v místním, nemusí být prostředí co vaši uživatelé očekávají.
* **Řadiče domény**: Pokud konektory provádět jednotné přihlašování (SSO) pomocí omezeného delegování protokolu Kerberos, se kontaktovat řadiče domény před odesláním požadavku do back-endu. Konektory mají mezipaměť lístky protokolu Kerberos, ale v prostředí zaneprázdněný rychlost odezvy řadičů domény může ovlivnit výkon. Tento problém je běžné pro konektory, které běží v Azure, ale komunikovat s řadiči domény, které jsou místně. 

Další informace o optimalizaci sítě najdete v tématu [aspekty topologie sítě, při použití Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Připojení k doméně

Konektory lze spustit na počítači, který není připojený k doméně. Pokud chcete do aplikace, které používají integrované ověřování Windows (IWA) jednotné přihlašování (SSO), ale musíte na počítači připojeném k doméně. V takovém případě počítače konektor musí být připojené k doméně, která může provádět [Kerberos](https://web.mit.edu/kerberos) omezené delegování jménem uživatelů pro publikování aplikace.

Konektory lze také připojit do domény nebo doménové struktury, které mají částečnou důvěryhodností nebo do řadiče domény jen pro čtení.

## <a name="connector-deployments-on-hardened-environments"></a>Nasazení konektoru na posílené prostředí

Nasazení konektoru obvykle je jednoduché a nepotřebuje žádnou zvláštní konfiguraci. Existují však některé jedinečné stavy, měli byste zvážit:

* Organizace, které omezuje odchozí provoz musí [požadované porty otevřete](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
* Kompatibilní se standardem FIPS počítače může být potřeba změní vlastní konfiguraci a povolení konektoru procesy, které chcete generovat a ukládat certifikát.
* Organizace, které zamknout své prostředí na základě procesů, které vydávat síťové požadavky nutné ujistěte, že jsou povoleny obě služby konektoru pro přístup k všechny požadované porty a IP adresy.
* V některých případech může odchozí dopředné proxy přerušit obousměrný certifikát ověřování a způsobí selhání komunikace.

## <a name="connector-authentication"></a>Konektor ověřování

Cílem je zajistit zabezpečenou službu, konektory k ověření směrem k službě, a službu obsahující k ověření směrem k konektoru. Toto ověření se provádí pomocí certifikátů klienta a serveru, když konektory naváže se připojení. Tímto způsobem uživatelské jméno a heslo správce nejsou uložené na počítači konektoru.

Certifikáty používané jsou specifické pro službu Proxy aplikací. Vytvořené při počáteční registraci a jsou automaticky obnovíte pomocí konektorů každých několik měsíců. 

Pokud konektor není připojený ke službě pro několik měsíců, může být zastaralý svoje certifikáty. V takovém případě odinstalovat a znovu nainstalujte connector, aby aktivační událost registrace. Můžete spustit následující příkazy Powershellu:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Pohled pod kapotu

Konektory jsou založeny na Windows Server webové aplikace Proxy, mají většinu stejné nástroje pro správu, včetně protokolů událostí Windows

 ![Správa protokolů událostí v prohlížeči událostí](./media/application-proxy-connectors/event-view-window.png)

a čítače výkonu Windows. 

 ![Přidání čítačů do konektoru pomocí sledování výkonu](./media/application-proxy-connectors/performance-monitor.png)

Konektory jste správce a relace protokoly. Protokoly správce obsahují klíče události a jejich chyby. Relace protokolů zahrnout všechny transakce a jejich podrobnosti zpracování. 

Zobrazit protokoly, přejděte do prohlížeče událostí, otevřete **zobrazení** nabídky a povolit **zobrazit analytické a ladit protokoly**. Pak povolte jejich spuštění shromažďování událostí. Tyto protokoly se nezobrazují v Proxy webových aplikací ve Windows serveru 2012 R2 jako konektory, které jsou založeny na novější verzi.

Můžete zkontrolovat stav služby v okně služby. Konektor se skládá ze dvou služeb Windows: skutečný konektoru a aktualizátoru. Musíte obou z nich spustit celou dobu.

 ![Místní služby Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Další postup


* [Publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů](application-proxy-connector-groups.md)
* [Práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Řešení potíží s chybami Proxy aplikací a konektoru](application-proxy-troubleshoot.md)
* [Postup při bezobslužné instalaci Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)

