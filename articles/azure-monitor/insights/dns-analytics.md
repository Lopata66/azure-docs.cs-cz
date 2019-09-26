---
title: DNS Analytics řešení v Azure Monitor | Microsoft Docs
description: Nastavte a použijte řešení DNS Analytics v Azure Monitor k získání přehledu o infrastruktuře DNS v oblasti zabezpečení, výkonu a provozu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: ad61743751ace9ca0c7eba12ffcea5f15e1157d5
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316187"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Získejte přehled o vaší infrastruktuře DNS s řešení DNS Analytics ve verzi Preview

![Symbol DNS Analytics](./media/dns-analytics/dns-analytics-symbol.png)

Tento článek popisuje, jak nastavit a používat řešení Azure DNS Analytics v Azure Monitor k získání přehledu o infrastruktuře DNS v oblasti zabezpečení, výkonu a provozu.

DNS Analytics vám umožní:

- Identifikace klientů, které se pokoušejí přeložit škodlivé domény názvy.
- Identifikace zastaralých záznamů.
- Identifikujte často doménový názvy a prahová klienti DNS.
- Zobrazení žádosti o zatížení serverů DNS.
- Zobrazit chyby registrace dynamického DNS.

Řešení shromažďuje, analyzuje a koreluje DNS Windows analýzy a protokoly auditu a dalších souvisejících dat ze serverů DNS.

## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které podporují toto řešení:

| **Připojený zdroj** | **Podpora** | **Popis** |
| --- | --- | --- |
| [Agenti systému Windows](../platform/agent-windows.md) | Ano | Řešení shromažďuje informace DNS z agentů Windows. |
| [Agenti systému Linux](../learn/quick-collect-linux-computer.md) | Ne | Řešení neshromažďuje informace DNS z přímí agenti systému Linux. |
| [Skupina pro správu System Center Operations Manager](../platform/om-agents.md) | Ano | Řešení shromažďuje informace DNS z agentů v připojené skupině pro správu nástroje Operations Manager. Přímé připojení od agenta Operations Manager k Azure Monitor není vyžadováno. Data se přesměrovávají ze skupiny pro správu do pracovního prostoru Log Analytics. |
| [Účet služby Azure Storage](../platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure se nepoužívá tímto řešením. |

### <a name="data-collection-details"></a>Podrobné informace o shromažďování dat

Řešení shromažďuje inventář DNS a dat týkajících se události DNS ze serverů DNS instalaci agenta Log Analytics. Tato data se pak nahrají do Azure Monitor a zobrazí se na řídicím panelu řešení. Shromažďuje data související s inventářem, jako je například počet serverů DNS, zóny a záznamy o prostředcích, spuštěním rutin Powershellu DNS. Data se aktualizují jednou každé dva dny. Událost související data se shromažďují v reálném čase z [analýzy a protokoly auditu](https://technet.microsoft.com/library/dn800669.aspx#enhanc) poskytuje rozšířené protokolování DNS a Diagnostika ve Windows serveru 2012 R2.

## <a name="configuration"></a>Konfigurace

Ke konfigurování řešení, použijte následující informace:

- Musíte mít [Windows](../platform/agent-windows.md) nebo [nástroje Operations Manager](../platform/om-agents.md) agent na každém serveru DNS, který chcete monitorovat.
- Řešení DNS Analytics můžete přidat do pracovního prostoru Log Analytics z [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). Můžete také použít proces popsaný v tématu [přidání Azure monitor řešení z galerie řešení](solutions.md).

Shromažďování dat bez nutnosti další konfigurace spuštění řešení. Přizpůsobení shromažďování dat lze však použijte následující konfiguraci.

### <a name="configure-the-solution"></a>Konfigurovat řešení

Na řídicím panelu řešení, klikněte na tlačítko **konfigurace** otevřete stránku konfigurace DNS Analytics. Existují dva typy změn konfigurace, které můžete použít:

- **Názvy domén na seznamu povolených**. Řešení nezpracovává vyhledávací dotazy. Udržuje seznam povolených přípon názvů domén. Řešení nezpracovávají vyhledávací dotazy, které odkazují na názvy domén, které odpovídají přípon názvů domén v tomto seznamu povolených IP adres. Nezpracovávání názvů povolených domén pomáhá optimalizovat data odesílaná do Azure Monitor. Výchozí seznam povolených obsahuje názvy populárních veřejných domén, například www.google.com a www.facebook.com. Zobrazení seznamu dokončení výchozí posunutím.

  Můžete upravit seznam přidat příponu názvu domény, kterou chcete zobrazit přehledy vyhledávání pro. Můžete také odebrat příponu názvu domény, které nechcete zobrazit přehledy vyhledávání pro.

- **Prahová hodnota prahová klienta**. Klienti DNS, které překračují prahovou hodnotu pro počet požadavků na vyhledávání jsou zvýrazněné **klienti DNS** okno. Výchozí prahová hodnota je 1 000. Můžete upravit prahovou hodnotu.

    ![Přidat na seznam povolených názvů domén](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Sady Management Pack

Pokud používáte Microsoft Monitoring Agent pro připojení k pracovnímu prostoru Log Analytics, je nainstalována následující sady management pack:

- Sada Microsoft DNS data collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Pokud vaši skupinu pro správu Operations Manageru je připojený k pracovnímu prostoru Log Analytics, nainstaluje se následující sady management Pack v nástroji Operations Manager při přidání tohoto řešení. Neexistuje žádná požadované konfigurace ani Údržba těchto sad management Pack:

- Sada Microsoft DNS data collector Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Konfigurace DNS Analytics Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Použití řešení DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Dlaždice DNS obsahuje počet serverů DNS, ve kterých se shromažďují data. Zahrnuje také počet požadavků provedených klientům přeložit škodlivé domény za posledních 24 hodin. Když kliknete na dlaždici, otevře se řídicí panel řešení.

![Dlaždice DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Řídicí panel řešení

Na řídicím panelu řešení zobrazuje souhrnné informace pro různé funkce řešení. Obsahuje také odkazy na podrobnosti přehledu pro forenzní analýza a Diagnostika. Ve výchozím nastavení se zobrazí data za posledních sedm dní. Datum a časový rozsah můžete změnit pomocí **ovládacího prvku pro výběr data a času**, jak je znázorněno na následujícím obrázku:

![Ovládacího prvku pro výběr času](./media/dns-analytics/dns-time.png)

Na řídicím panelu řešení zobrazuje následující listy:

**Zabezpečení DNS**. Sestavy klientů DNS, která se snaží komunikovat s škodlivé domény. Pomocí kanálů analýzy hrozeb Microsoft DNS Analytics dokáže klientských IP adres, který se pokoušíte získat přístup škodlivé domény. V mnoha případech nakažené malwarem zařízení "vytáčet" na střed "příkazy a ovládání" škodlivé domény pomocí překladu názvu domény před škodlivým softwarem.

![Okno zabezpečení DNS](./media/dns-analytics/dns-security-blade.png)

Po kliknutí na IP adresu klienta do seznamu hledání v protokolu otevře a zobrazí podrobnosti vyhledávání odpovídajících dotazu. V následujícím příkladu DNS Analytics zjistil, že komunikaci provedenou pomocí [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot):

![Zobrazuje ircbot výsledků prohledávání protokolu](./media/dns-analytics/ircbot.png)

Tyto informace vám pomůžou identifikovat:

- Klient protokolu IP, který spustil komunikace.
- Název domény, který se přeloží škodlivou IP adresou.
- IP adresy, které překládá název domény.
- Škodlivé IP adresy.
- Závažnosti problému.
- Důvod Adaptivně škodlivá IP adresa.
- Čas detekce.

**Domény dotazovat**. Poskytuje nejčastěji se vyskytujících názvy domén, která je dotazována klienty DNS ve vašem prostředí. Můžete zobrazit seznam všech názvů domén dotazovat. Můžete také procházet hierarchii vyhledávání žádost o název konkrétní domény v prohledávání protokolu.

![Okno byl odeslán domén](./media/dns-analytics/domains-queried-blade.png)

**Klienti DNS**. Hlásí klienti *porušení mezní hodnotu* pro počet dotazů ve zvoleném časovém období. Můžete zobrazit seznam všech klientů DNS a podrobnosti dotazy provedené je v prohledávání protokolu.

![Okně klienti DNS](./media/dns-analytics/dns-clients-blade.png)

**Registrace dynamického serveru DNS**. Selhání registrace zadejte název sestavy. Všechny chyby registrace pro adresu [záznamy o prostředcích](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (typ A a AAAA) jsou zvýrazněny spolu s klientských IP adres, který k žádosti o registraci. Tyto informace pak můžete najít hlavní příčinu selhání registrace pomocí následujících kroků:

1. Najdete zónu, který je autoritativní pro název, který klient se pokouší aktualizovat.

1. Pomocí řešení zkontrolujte informace o inventáři této zóny.

1. Ověřte, zda je povolena dynamická aktualizace pro zónu.

1. Zkontrolujte, zda zóny je nakonfigurovaný pro zabezpečené dynamické aktualizace, nebo ne.

    ![Dynamické okno registrace DNS](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Název žádosti o registraci**. Horní dlaždice ukazuje spojnice trendu úspěšné i neúspěšné požadavky dynamické aktualizace DNS. Nižší dlaždice obsahuje hlavní 10 klienty, kteří odesílají aktualizace neúspěšné DNS na servery DNS, seřazený podle počtu neúspěšných požadavků.

![Název okna požadavky registrace](./media/dns-analytics/name-reg-req-blade.png)

**Ukázkový DDI analytických dotazů**. Obsahuje seznam nejběžnějších vyhledávacích dotazů, které načítají data nezpracovaná analytics přímo.


![Ukázkové dotazy](./media/dns-analytics/queries.png)

Tyto dotazy můžete použít jako výchozí bod pro vytvoření vlastní dotazy pro vytváření přizpůsobených sestav. Dotazy na odkaz na stránku prohledávání protokolu DNS Analytics, ve kterém se zobrazí výsledky:

- **Seznam serverů DNS**. Zobrazuje seznam všech serverů DNS s jejich přidružené plně kvalifikovaný název domény, název domény, název doménové struktury a IP adresy serveru.
- **Seznam zón DNS**. Zobrazí seznam všech zón DNS s názvem přidružené zóny, dynamická aktualizace stavu, názvových serverů a stav podepsání DNSSEC.
- **Nevyužité záznamy prostředků**. Zobrazuje seznam všech záznamů prostředků nepoužité/zastaralé. Tento seznam obsahuje název záznamu prostředku, typ záznamu o prostředku, přidružený server DNS záznam generování a název zóny. Tento seznam můžete použít k identifikaci záznamy o prostředcích DNS, které jsou již používány. Na základě těchto informací, můžete pak odebrat tyto položky ze serverů DNS.
- **Dotazové zatížení serverů DNS**. Zobrazí informace, takže získáte perspektivy zatížení DNS na serverech DNS. Tyto informace mohou pomoci při plánování kapacity pro servery. Můžete přejít **metriky** karty změní typ zobrazení na grafická vizualizace. Toto zobrazení pomáhá pochopit, jak se distribuuje zatížení DNS na serverech DNS. Rychlost vývoje pro každý server ukazuje dotaz DNS.

    ![Servery DNS, dotaz výsledků prohledávání protokolu](./media/dns-analytics/dns-servers-query-load.png)

- **Dotazové zatížení zón DNS**. Zobrazuje statistiku DNS zóny dotazů za sekundu ze všech zón na serverech DNS spravovaných tímto řešením. Klikněte na tlačítko **metriky** kartu změnit zobrazení z podrobné záznamy do grafická vizualizace výsledků.
- **Události konfigurace služby**. Zobrazí všechny události změny konfigurace služby DNS a související zprávy. Potom můžete filtrovat tyto události podle času události, ID události, serveru DNS, nebo kategorie úkolů. Data můžete auditovat změny provedené konkrétními DNS servery v určitých časech.
- **Analytický protokol DNS**. Zobrazí všechny analytické události ve všech serverech DNS spravovaných tímto řešením. Potom můžete filtrovat tyto události podle času události, ID události, serveru DNS, IP adresu klienta, který vytvořil vyhledávacího dotazu a kategorie úkolu typu dotazu. Analytické události DNS serveru povolit aktivity sledování na serveru DNS. Analytické událost se protokoluje pokaždé, když server odesílá nebo přijímá informace DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Hledání s využitím prohledávání protokolu DNS Analytics

Na stránce prohledávání protokolů můžete vytvořit dotaz. Můžete filtrovat výsledky hledání s použitím ovládacích prvků omezující vlastnost. Můžete také vytvořit upřesňující dotazy transformace, filtrovat a sestavy na výsledky. Spustit s použitím následující dotazy:

1. V **vyhledávacího dotazu pole**, typ `DnsEvents` zobrazíte všechny události DNS generovaných serverů DNS spravovaných tímto řešením. Seznam výsledků obsahuje data protokolu pro všechny události související s vyhledávací dotazy, dynamické registrace a změny konfigurace.

    ![DnsEvents prohledávání protokolů](./media/dns-analytics/log-search-dnsevents.png)  

    a. Chcete-li zobrazit data protokolu pro vyhledávací dotazy, vyberte **LookUpQuery** jako **podtyp** filtr ze sloupce omezující vlastnost ovládacího prvku na levé straně. Zobrazí se tabulka obsahující seznam všech událostí vyhledávací dotaz pro vybrané časové období.

    b. Chcete-li zobrazit data protokolu pro dynamické registrace, vyberte **DynamicRegistration** jako **podtyp** filtr ze sloupce omezující vlastnost ovládacího prvku na levé straně. Zobrazí se tabulka obsahující seznam všech událostí dynamické registrace pro vybrané časové období.

    c. Chcete-li zobrazit data protokolu pro změny konfigurace, vyberte **ConfigurationChange** jako **podtyp** filtr ze sloupce omezující vlastnost ovládacího prvku na levé straně. Zobrazí se tabulka obsahující seznam všech událostí změny konfigurace pro vybrané časové období.

1. V **vyhledávacího dotazu pole**, typ `DnsInventory` zobrazíte všechny související s inventářem data DNS serverů DNS spravovaných tímto řešením. Seznam výsledků obsahuje data protokolu pro záznamy o prostředcích, servery DNS a zóny DNS.

    ![DnsInventory prohledávání protokolů](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Řešení potíží

Běžné kroky při řešení potíží:

1. Chybějící data vyhledávání DNS – Chcete-li tento problém vyřešit, zkuste resetovat konfiguraci nebo načíst stránku konfigurace jenom jednou na portálu. Pro obnovení stačí změnit nastavení na jinou hodnotu a pak ho změnit zpátky na původní hodnotu a uložit konfiguraci.

## <a name="feedback"></a>Váš názor

Pokud chcete poskytnout zpětnou vazbu, přejděte na [stránku Log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) , kde najdete nápady, jak pracovat s funkcemi DNS Analytics. 

## <a name="next-steps"></a>Další kroky

V [protokolech dotazů](../log-query/log-query-overview.md) zobrazíte podrobné záznamy protokolu DNS.
