---
title: Mezipaměť Azure pro Redis – nejčastější dotazy | Dokumentace Microsoftu
description: Další odpovědi na běžné dotazy, vzory a osvědčené postupy pro Azure Cache pro Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: yegu
ms.openlocfilehash: 65e8553969aa92848b1c4496724a7b7754b5d659
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895592"
---
# <a name="azure-cache-for-redis-faq"></a>Nejčastější dotazy ke službě Azure Cache for Redis
Přečtěte si odpovědi na běžné dotazy, vzory a osvědčené postupy pro Azure Cache pro Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když můj dotaz zde nenalezl?
Pokud zde není uveden váš dotaz, dejte nám vědět a pomůžeme vám najít odpověď.

* Můžete odeslat dotaz do komentářů na konci tyto Nejčastější dotazy a Spolupracujte s týmem Azure Cache a ostatních členů komunity o tomto článku.
* K dosažení širší cílovou skupinu, můžete odeslat dotaz na [fórum MSDN pro Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) a Spolupracujte s týmem Azure Cache a ostatní členové komunity.
* Pokud chcete vytvořit žádost o funkci, můžete odeslat žádosti a nápady, jak ho [mezipaměti Azure redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* Můžete taky poslat e-mailu nám na [externí zpětné vazby mezipaměti Azure](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Azure mezipaměti Redis základy
Nejčastější dotazy v této části se věnují několika základní informace o mezipaměti Azure pro Redis.

* [Co je Azure mezipaměti Redis?](#what-is-azure-cache-for-redis)
* [Jak mám začít s Azure Cache pro Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Následující nejčastější dotazy se věnují základní koncepty a dotazy týkající se Azure Cache pro Redis a jsou zodpovězeny v některé z dalších částí – nejčastější dotazy.

* [Jaké mezipaměti Azure Redis a jeho velikost mám použít?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Jaké mezipaměti Azure Redis klientů můžete použít?](#what-azure-cache-for-redis-clients-can-i-use)
* [Je pro Redis místní emulátor pro Azure Cache?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak monitorovat stav a výkon své mezipaměti?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Plánování nejčastější dotazy
* [Jaké mezipaměti Azure Redis a jeho velikost mám použít?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Mezipaměť Azure Redis výkonu](#azure-cache-for-redis-performance)
* [V jaké oblasti by měl mi najít mé mezipaměti?](#in-what-region-should-i-locate-my-cache)
* [Jak se fakturují pro mezipaměť Azure Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Můžete použít Azure Cache pro Redis pomocí cloudu Azure Government, Azure China Cloud nebo Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Nejčastějších dotazech týkajících se vývoje
* [Jaké možnosti konfigurace StackExchange.Redis udělat?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Jaké mezipaměti Azure Redis klientů můžete použít?](#what-azure-cache-for-redis-clients-can-i-use)
* [Je pro Redis místní emulátor pro Azure Cache?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Jak mohu spustit příkazy Redis?](#how-can-i-run-redis-commands)
* [Proč Azure mezipaměti Redis nemá MSDN knihovny tříd jako některé z dalších služeb Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Můžu použít Azure Cache pro Redis jako mezipaměť relace PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Co jsou databáze Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Nejčastější dotazy k zabezpečení
* [Pokud by měl povolit port bez SSL pro připojení k Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Nejčastější dotazy k produkčním prostředí
* [Jaké jsou některé osvědčené postupy produkčního prostředí?](#what-are-some-production-best-practices)
* [Jaké jsou některé aspekty při používání běžné příkazy Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak můžu srovnávací testy a testování výkonu mezipaměť?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Důležité podrobnosti o růstu fondu vláken](#important-details-about-threadpool-growth)
* [Povolit uvolňování paměti serveru získat větší propustnost na straně klienta při používání StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Důležité informace o výkonu po připojení](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorování a řešení potíží s nejčastější dotazy
Nejčastější dotazy v této části se věnují běžné monitorování a dotazy ohledně řešení potíží. Další informace o monitorování a řešení potíží s Azure pro instance Redis Cache najdete v tématu [monitorování služby Azure Cache pro Redis](cache-how-to-monitor.md) a [řešení potíží s Azure Cache pro Redis](cache-how-to-troubleshoot.md).

* [Jak monitorovat stav a výkon své mezipaměti?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Proč se mi zobrazuje časové limity?](#why-am-i-seeing-timeouts)
* [Proč se můj klient odpojen z mezipaměti?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Nejčastější dotazy k předchozí z variant mezipaměti
* [Kterou z variant mezipaměti Azure je pro mě nejlepší?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Co je Azure mezipaměti Redis?
Pro Redis Azure Cache je založená na oblíbené open source softwaru [Redis](https://redis.io/). Poskytuje přístup k zabezpečené vyhrazené mezipaměti Azure pro Redis spravované microsoftem a přístupná z libovolné aplikace v Azure. Podrobnější přehled najdete [mezipaměti Azure Redis](https://azure.microsoft.com/services/cache/) stránky produktu na Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Jak mám začít s Azure Cache pro Redis?
Existuje několik způsobů, jimiž můžete začít s Azure Cache pro Redis.

* Můžete vyzkoušet jedno z našich kurzů, které jsou k dispozici pro [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), a [Python](cache-python-get-started.md).
* Můžete sledovat [tom, jak vytvářet výkonné aplikace pomocí Microsoft Azure Cache pro Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Si můžete prohlédnout v dokumentaci klienta pro klienty, které odpovídají vývojový jazyk projektu naleznete v tématu Jak používat Redis. Existuje mnoho klientů Redis, které je možné s mezipamětí Azure Redis. Seznam klientů Redis najdete v tématu [ https://redis.io/clients ](https://redis.io/clients).

Pokud ještě nemáte účet Azure, můžete:

* [Otevřít bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Získáte kredity, které můžete použít k vyzkoušení placených služeb Azure. I po vyčerpání kreditů si můžete účet ponechat a používat bezplatné funkce a služby Azure.
* [Aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Jaké mezipaměti Azure Redis a jeho velikost mám použít?
Každá mezipaměť Azure nabídku Redis poskytuje různé úrovně **velikost**, **šířky pásma**, **vysoké dostupnosti**, a **SLA** možnosti.

Následují faktory pro výběr variant mezipaměti.

* **Paměť**: Úrovně Basic a Standard nabízejí 250 MB – 53 GB. Úroveň Premium nabízí až 530 GB. Další informace najdete v tématu [mezipaměti Azure redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).
* **Výkon sítě**: Pokud máte úlohu, která vyžaduje vysokou propustnost, na úrovni Premium nabízí větší šířku pásma v porovnání s Standard nebo Basic. Také v každé úrovni mít větší velikost mezipaměti větší šířku pásma z důvodu základní virtuální počítač, který je hostitelem mezipaměti. Zobrazit [následující tabulky](#cache-performance) Další informace.
* **Propustnost**: Úroveň Premium nabízí maximální propustnost k dispozici. Pokud klienta nebo serveru mezipaměti dosáhne omezení šířky pásma, můžete obdržet vypršení časového limitu na straně klienta. Další informace najdete v tématu v následující tabulce.
* **Vysoká dostupnost/SLA**: Mezipaměti Redis Azure zaručuje, že mezipaměť Standard nebo Premium je k dispozici alespoň 99,9 % času. Další informace o smlouvě SLA najdete v tématu [mezipaměti Azure redis Cache ceny](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Smlouva SLA pokrývá jenom připojení ke koncovým bodům mezipaměti. Součástí smlouvy SLA není ochrana proti ztrátě dat. Doporučujeme použití funkce trvalosti dat Redis na úrovni Premium ke zvýšení odolnosti proti ztrátě dat.
* **Trvalost dat redis**: Úroveň Premium umožňuje zachovat data v mezipaměti v účtu služby Azure Storage. V mezipaměti Basic nebo Standard všechna data uložena pouze v paměti. Pokud jsou základní problémy infrastrukturu může být potenciální ztráty dat. Doporučujeme použití funkce trvalosti dat Redis na úrovni Premium ke zvýšení odolnosti proti ztrátě dat. Mezipaměti Redis Azure nabízí RDB a AOF (už brzo) možnosti trvalosti Redis. Další informace najdete v tématu [konfigurace trvalosti pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-persistence.md).
* **Redis Cluster**: Vytvoření mezipaměti větší než 53 GB, nebo sdílení dat mezi více uzlů Redis, můžete Redis clustering, která je dostupná na úrovni Premium. Každý uzel se skládá z dvojice primární/replika mezipaměti pro zajištění vysoké dostupnosti. Další informace najdete v tématu [postup konfigurace clusterů pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-clustering.md).
* **Rozšířené zabezpečení a síťové izolace**: Nasazení Azure Virtual Network (VNET) nabízí vyšší míru zabezpečení a izolaci pro mezipaměť Azure Redis, stejně jako podsítě, zásady řízení přístupu a jiné funkce pro další omezení přístupu. Další informace najdete v tématu [konfigurace podpory služby Virtual Network pro mezipaměť Azure Premium pro Redis](cache-how-to-premium-vnet.md).
* **Konfigurace Redis**: Ve vrstvách Standard a Premium můžete nakonfigurovat pro oznámení Keyspace Redis.
* **Maximální počet připojení klientů**: Úroveň Premium nabízí maximální počet klientů, které se mohou připojit k úložišti Redis, s vyšší počet připojení pro větší velikosti mezipaměti. Clustering nezvyšuje počet připojení, které jsou k dispozici pro clusterové mezipaměti. Další informace najdete v tématu [mezipaměti Azure Redis ceny](https://azure.microsoft.com/pricing/details/cache/).
* **Vyhrazených jader k serveru Redis**: Na úrovni Premium mají všechny velikosti mezipaměti vyhrazená jádra pro Redis. Na úrovních Basic nebo Standard, velikost C1 a výše máte vyhrazená jádra pro Redis server.
* **Redis je jednovláknový** tak s více než dvě jádra neposkytuje výhody oproti s právě dvě jádra, ale větší velikosti virtuálních počítačů obvykle mají větší šířku pásma než menší velikost. Pokud klienta nebo serveru mezipaměti dosáhne omezení šířky pásma, obdržíte vypršení časového limitu na straně klienta.
* **Vylepšení výkonu**: Mezipaměť na úrovni Premium jsou nasazené na hardware, který má rychlejšími procesory, poskytuje lepší výkon ve srovnání na úroveň Basic nebo Standard. Mezipaměti úrovně Premium má vyšší propustnost a nižší latenci.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Mezipaměť Azure Redis výkonu
Následující tabulka uvádí maximální šířka pásma hodnotami zjištěnými při testování různých velikostí úrovně Standard a Premium ukládá do mezipaměti pomocí `redis-benchmark.exe` z virtuálního počítače IaaS s mezipamětí Azure Redis koncového bodu. Propustnost SSL používá redis srovnávacích testů s stunnelu pro připojení k mezipaměti Azure pro koncový bod Redis.

>[!NOTE] 
>Tyto hodnoty není zaručeno a neexistuje žádná smlouva SLA pro tyto čísla, ale musí být typické. By se měly načíst otestovat vlastní aplikaci k určení velikosti mezipaměti správné pro vaši aplikaci.
>Tato čísla může změnit, protože publikujeme pravidelně novější výsledky.
>

Z této tabulky jsme lze nakreslit následující závěry:

* Propustnost pro mezipaměti, které mají stejnou velikost je vyšší ve vrstvě Premium, než na úrovni Standard. Například s 6 GB mezipaměti je propustnost P1 180 000 RPS než 100 000 pro C3.
* S clusteringem Redis propustnost zvyšuje lineárně zvýšit počet horizontálních oddílů (uzlů) v clusteru. Například pokud vytvoříte cluster P4 10 horizontálními oddíly, pak k dispozici propustnost je 400 000 * 10 = 4 miliony RPS.
* Propustnost pro větší velikosti klíče je vyšší ve vrstvě Premium, než na úrovni Standard.

| Cenová úroveň | Velikost | Procesorová jádra | Dostupná šířka pásma | Velikost hodnoty 1 KB | Velikost hodnoty 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Velikosti Standard mezipaměti** | | |**Megabity za sekundu (Mb/s) nebo megabajtů za sekundu (MB/s)** |**Požadavky na druhý bez SSL (předávajících stran)** |**Požadavky na druhý protokol SSL (předávajících stran)** |
| C0 |250 MB |Shared |100 / 12.5 |15 000 |7 500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100 000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102,000 |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Velikosti mezipaměti úrovně Premium** | |**Počet jader procesoru na horizontálních oddílů** | **Megabity za sekundu (Mb/s) nebo megabajtů za sekundu (MB/s)** |**Požadavky na druhý (předávajících stran) bez SSL, za horizontální oddíl** |**Požadavky na druhý protokol SSL (předávajících stran), za horizontální oddíl** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400 000 |373,000 |

Pokyny k nastavení stunnelu nebo stáhli Redis tools jako `redis-benchmark.exe`, najdete v článku [jak mohu spustit příkazy Redis?](#cache-commands) oddílu.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>V jaké oblasti by měl mi najít mé mezipaměti?
Pro zajištění nejlepšího výkonu a nejnižší latenci vyhledejte mezipaměti Azure Redis ve stejné oblasti jako vaše klientská aplikace mezipaměti.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak se fakturují pro mezipaměť Azure Redis?
Mezipaměť Azure Redis ceny je [tady](https://azure.microsoft.com/pricing/details/cache/). Stránce s cenami uvádí ceny jako hodinové sazby. Mezipaměť se fakturuje po minutách od okamžiku vytvoření mezipaměti až do mezipaměti se odstraní. Neexistuje žádná možnost pro zastavení nebo pozastavení fakturace do mezipaměti.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Můžete použít Azure Cache pro Redis pomocí cloudu Azure Government, Azure China Cloud nebo Microsoft Azure Germany?
Ano, mezipaměti Redis Azure je dostupná v cloudu Azure Government Azure China Cloud a Microsoft Azure Germany. Adresy URL pro přístup a Správa mezipaměti Azure Redis jsou jiné v tyto cloudy ve srovnání s veřejným cloudem Azure. 

| Cloud   | Přípona DNS pro Redis            |
|---------|---------------------------------|
| Public  | *.redis.cache.windows.net       |
| Vláda USA  | *.redis.cache.usgovcloudapi.net |
| Německo | *.redis.cache.cloudapi.de       |
| Čína   | *.redis.cache.chinacloudapi.cn  |

Další informace o informace týkající se použití mezipaměti Azure pro Redis s jinými cloudy najdete v následujících tématech.

- [Azure Government databáze – Azure mezipaměti Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Cloud Azure China - Azure mezipaměti Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Informace o používání Azure Cache pro Redis pomocí Powershellu v cloudu Azure Government Azure China Cloud a Microsoft Azure Germany najdete v tématu [jak se připojit k jiné cloudy - mezipaměti Azure redis Cache PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Jaké možnosti konfigurace StackExchange.Redis udělat?
StackExchange.Redis má hodně možností. Tato část pojednává o některých běžných nastavení. Podrobné informace o možnostech StackExchange.Redis, naleznete v tématu [StackExchange.Redis konfigurace](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Popis | Doporučení |
| --- | --- | --- |
| AbortOnConnectFail |Pokud nebude znovu nastavenou na hodnotu true, připojení připojit po selhání sítě. |Nastavte na hodnotu false a nechat StackExchange.Redis automaticky znovu připojit. |
| ConnectRetry |Počet opakování pokusů o připojení během počáteční připojení. |Viz následující poznámky pro doprovodné materiály. |
| ConnectTimeout |Časový limit v ms pro operace připojení. |Viz následující poznámky pro doprovodné materiály. |

Obvykle jsou výchozí hodnoty klienta dostatečná. Můžete podrobně upravit možnosti na základě vašich úloh.

* **Počet opakování**
  * Obecné pokyny pro ConnectRetry a ConnectTimeout je rychle vygenerovala chybu a zkuste to znovu. Tento návod vychází z vašich úloh a kolik času na průměr přebírá pro svého klienta do příkaz Redis a přijetí odpovědi.
  * Umožňuje automaticky znovu připojila, namísto kontroluje se stav připojení a opětovné připojení sami StackExchange.Redis. **Vyhněte se použití vlastnosti ConnectionMultiplexer.IsConnected**.
  * Snowballing – někdy může narazíte na problém, kde je to zkoušet dál a opakované pokusy rozrůst a nikdy obnoví. Pokud dojde k snowballing, měli byste zvážit, pomocí algoritmu opakování exponenciálního omezení rychlosti, jak je popsáno v [obecné pokyny k opakovaným](../best-practices-retry-general.md) publikoval(a) skupina Microsoft Patterns a postupy.
* **Hodnoty časového limitu**
  * Vezměte v úvahu vaše úlohy a nastavte hodnoty odpovídajícím způsobem. Pokud ukládáte velké hodnoty, nastavte časový limit na vyšší hodnotu.
  * Nastavte `AbortOnConnectFail` na false a umožněte StackExchange.Redis znovu připojit za vás.
  * Použijte jednu instanci ConnectionMultiplexer pro aplikaci. Vám pomůže LazyConnection vytvořte jednu instanci, která je vrácena vlastností připojení, jak je znázorněno v [připojení k mezipaměti horizontálních oddílů pomocí třídy ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Nastavte `ConnectionMultiplexer.ClientName` vlastnost jedinečný název instance aplikace pro diagnostické účely.
  * Použití více `ConnectionMultiplexer` instance pro vlastní úlohy.
      * Tento model můžete použít, pokud máte proměnlivé zátěže v aplikaci. Příklad:
      * Můžete mít jednu multiplexor pro nakládání s klíči velkých.
      * Můžete mít jednu multiplexor pro práci s malé klíče.
      * Můžete nastavit různé hodnoty pro vypršení časového limitu připojení a logika opakování pro každý ConnectionMultiplexer, který používáte.
      * Nastavte `ClientName` vlastnost na každém multiplexor pomoct s diagnostikou.
      * Tyto pokyny mohou vést k více zjednodušené latencí na `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Jaké mezipaměti Azure Redis klientů můžete použít?
Jednou z nejlepších věcí na Redis je, že existují mnoho klientů podporuje mnoho různých programovacích jazyků. Aktuální seznam klientů najdete v tématu [klienti Redis](https://redis.io/clients). Kurzy, které zahrnují několik různých jazycích a klientů najdete na stránce [použití mezipaměti Azure pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a je na stejné úrovni články v tabulce obsahu.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Je pro Redis místní emulátor pro Azure Cache?
Neexistuje žádné místní emulátor pro mezipaměť Azure Redis, ale může spouštět MSOpenTech verzi server.exe redis z [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/) ve vašem místním počítači a připojte se k němu získat fungují na podobném principu do místní mezipaměti emulátor, jak je znázorněno v následujícím příkladu:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Volitelně můžete nakonfigurovat [redis.conf](https://redis.io/topics/config) souboru tak, aby lépe odpovídaly [výchozí nastavení mezipaměti](cache-configure.md#default-redis-server-configuration) pro vaše online mezipaměti Azure Redis v případě potřeby.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Jak mohu spustit příkazy Redis?
Můžete použít některý z příkazů uvedený na [příkazy Redis](https://redis.io/commands#) s výjimkou příkazy uvedené v [Redis nepodporuje v mezipaměti Azure Redis příkazy](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Máte několik možností, jak spouštět příkazy Redis.

* Pokud máte Standard nebo Premium mezipaměti můžete spouštět příkazy Redis pomocí [konzola Redis](cache-configure.md#redis-console). Konzola Redis poskytuje zabezpečený způsob, jak spouštět příkazy Redis na webu Azure Portal.
* Můžete také použít nástroje příkazového řádku Redis. K jejich použití, proveďte následující kroky:
* Stáhněte si [nástroje příkazového řádku Redis](https://github.com/MSOpenTech/redis/releases/).
* Připojení k mezipaměti pomocí `redis-cli.exe`. Předat mezipaměti koncový bod pomocí přepínače -h a klíč pomocí – a jak je znázorněno v následujícím příkladu:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Nástroje příkazového řádku Redis nebude fungovat s portem SSL, ale nástroj, který můžete použít například `stunnel` k bezpečnému propojení nástroje SSL port pomocí následujícího postupu v [oznamujeme zprostředkovatel stavu relací ASP.NET pro redis Cache ve verzi Preview Verze](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogový příspěvek.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Proč Azure mezipaměti Redis nemá MSDN knihovny tříd jako některé z dalších služeb Azure?
Microsoft Azure Cache Redis je založená na oblíbené open source mezipaměti Azure pro Redis a je možný přes celou řadu [klienti Redis](https://redis.io/clients) pro řadu programovacích jazyků. Každý klient má své vlastní rozhraní API, která provede volání do mezipaměti Azure Redis pomocí instance [příkazy Redis](https://redis.io/commands).

Protože každá klient se liší, není jednu centrální třída reference na webu MSDN a každý klient udržuje svůj vlastní referenční dokumentaci. Kromě referenční dokumentaci jsou k dispozici několik kurzů ukazuje, jak začít pracovat s mezipamětí Azure pro Redis používající různé jazyky a ukládat do mezipaměti klientů. Pro přístup k tyto kurzy, naleznete v tématu [použití mezipaměti Azure pro Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a je na stejné úrovni články v tabulce obsahu.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Můžu použít Azure Cache pro Redis jako mezipaměť relace PHP?
Ano, pro účely mezipaměti Azure Redis jako mezipaměť relace PHP, zadejte připojovací řetězec do mezipaměti Azure pro instanci Redis v `session.save_path`.

> [!IMPORTANT]
> Při použití mezipaměti Azure pro Redis jako mezipaměť relace PHP, musí adresa URL kódování klíč zabezpečení použít pro připojení k mezipaměti, jak je znázorněno v následujícím příkladu:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Pokud není klíč kódování URL, může se zobrazit výjimka se zprávou jako: `Failed to parse session.save_path`
>
>

Další informace o použití mezipaměti Azure Redis jako mezipaměť relace PHP s klientem PhpRedis najdete v tématu [obslužné rutiny PHP relace](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Co jsou databáze Redis?

Databáze redis jsou jenom logické oddělení dat v rámci stejné instance Redis. Mezipaměti jsou sdílena mezi databází a skutečná paměť spotřeby na danou databázi závisí na klíče/hodnoty uložené v této databázi. Například C6 mezipaměti má 53 GB paměti. Je možné převést všechny 53 GB do jedné databáze nebo vám ho rozdělte mezi několika databázemi. 

> [!NOTE]
> Když pomocí Azure Cache úrovně Premium s aktivovaným clusteringem Redis, je k dispozici pouze databáze 0. Toto omezení je vnitřní omezení Redis a není specifická pro mezipaměť Azure Redis. Další informace najdete v tématu [potřeba dělat žádné změny Moje klientská aplikace použít clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Pokud by měl povolit port bez SSL pro připojení k Redis?
Redis server nepodporuje nativně protokol SSL, ale nemá Azure mezipaměti Redis. Pokud se připojujete k mezipaměti Azure pro Redis a váš klient podporuje protokol SSL, jako je StackExchange.Redis, měli byste použít protokol SSL.

>[!NOTE]
>Port bez SSL je ve výchozím nastavení zakázané pro nové mezipaměti Azure pro instance Redis. Pokud váš klient nepodporuje SSL, pak je nutné povolit port bez SSL pomocí následujícího postupu v [přístup porty](cache-configure.md#access-ports) část [konfigurace mezipaměti v mezipaměti Azure pro Redis](cache-configure.md) článku.
>
>

Redis nástroje, jako `redis-cli` nefungují s portem SSL, ale nástroj, který můžete použít například `stunnel` k bezpečnému propojení nástroje SSL port pomocí následujícího postupu v [oznamujeme zprostředkovatel stavu relací ASP.NET pro Redis Verze Preview](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogový příspěvek.

Pokyny ke stahování nástroje Redis, najdete v článku [jak mohu spustit příkazy Redis?](#cache-commands) oddílu.

### <a name="what-are-some-production-best-practices"></a>Jaké jsou některé osvědčené postupy produkčního prostředí?
* [Osvědčené postupy StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfigurace a koncepty](#configuration-and-concepts)
* [Testování výkonu](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Osvědčené postupy StackExchange.Redis
* Nastavte `AbortConnect` na hodnotu false, pak umožní ConnectionMultiplexer automaticky znovu připojit. [Podrobnosti najdete tady](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Znovu použít ConnectionMultiplexer – nevytvářejte nový pro každý požadavek. `Lazy<ConnectionMultiplexer>` Vzor [je vidět tady](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) se doporučuje.
* Redis funguje nejlépe s menší hodnoty, proto zvažte dělení větší data do více klíčů. V [této diskuse Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb se považují za velké. Čtení [v tomto článku](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) pro problém s příklad, který může být způsobeno velké hodnoty.
* Konfigurace vašeho [nastavení fondu vláken](#important-details-about-threadpool-growth) aby se zabránilo vypršení časového limitu.
* Použijte alespoň connectTimeout výchozí hodnoty 5 sekund. Tento interval pohrál StackExchange.Redis dostatek času pro opětovné vytvoření připojení v případě blip sítě.
* Mějte na náklady výkonu související s různé operace, které jsou spuštěné. Například `KEYS` příkaz O(n) operace a mělo by se vyhnout. [Redis.io lokality](https://redis.io/commands/) obsahuje podrobnosti o složitost čas pro každou operaci, která ho podporuje. Klikněte na každém z nich zobrazíte složitost pro každou operaci.

#### <a name="configuration-and-concepts"></a>Konfigurace a koncepty
* Použití úrovně Standard a Premium pro produkční systémy. Úroveň Basic je jeden uzel systém se žádná data replikace a žádná smlouva SLA. Také můžete použijte mezipaměť o velikosti alespoň C1. C0 mezipaměti se obvykle používají pro scénáře vývoje/testování jednoduché.
* Mějte na paměti, že je Redis **In-Memory** datového úložiště. Čtení [v tomto článku](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) tak, aby měli přehled o scénářích, kde může dojít ke ztrátě dat.
* Vývoj systému tak, že dokáže zpracovat připojení blips [z důvodu opravy chyb a převzetí služeb při selhání](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testování výkonu
* Začněte tím, že pomocí `redis-benchmark.exe` získat představu možné propustnost před zápisem vlastní výkonu testy. Protože `redis-benchmark` nepodporuje SSL, je nutné [povolit port bez SSL na webu Azure portal](cache-configure.md#access-ports) před spuštěním testu. Příklady najdete v tématu [jak srovnávací testy a testování výkonu mezipaměť?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Klient použít pro testování virtuální počítač by měl být ve stejné oblasti jako mezipaměť Azure pro instanci Redis.
* Doporučujeme použít virtuální počítač řady Dv2 pro vašeho klienta, dokud budou mít lepší hardwaru a měl dát nejlepších výsledků.
* Ujistěte se, že váš klient zvolíte virtuální počítač má alespoň tolik výpočetního prostředí a testování šířky pásma funkce jako mezipaměť.
* Povolte VRSS v klientském počítači. Pokud jste na Windows. [Podrobnosti najdete tady](https://technet.microsoft.com/library/dn383582.aspx).
* Instance Redis úrovně Premium mají lépe síťová latence a propustnosti vzhledem k tomu, na kterém běží lepší hardwaru pro procesoru a sítě.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jaké jsou některé aspekty při používání běžné příkazy Redis?
* Byste neměli spouštět některé příkazy Redis, což trvat dlouhou dobu pro dokončení bez informací o dopadu těchto příkazů.
  * Například, nespouštějte [klíče](https://redis.io/commands/keys) příkaz v produkčním prostředí, protože to může trvat dlouhou dobu vrátit v závislosti na počtu klíčů. Redis je server s jedním vláknem a zpracovává příkazy jeden po druhém. Pokud máte další příkazy vydané po klíče, nebudou zpracovány dokud Redis zpracuje příkaz klíče. [Redis.io lokality](https://redis.io/commands/) obsahuje podrobnosti o složitost čas pro každou operaci, která ho podporuje. Klikněte na každém z nich zobrazíte složitost pro každou operaci.
* Velikostí klíče - použít klíč/hodnota malé nebo velké klíč/hodnota? Obecně platí závisí na scénáři. Pokud vaše situace vyžaduje větší klíče, můžete upravit hodnota ConnectionTimeout a opakujte hodnot a upravit svoji logiku opakování. Z pohledu serveru Redis jsou dodržovány menší hodnoty mít lepší výkon.
* Tyto aspekty neznamenají, že nemůžete uložit vyšší hodnoty v Redis; musíte být vědomi následující aspekty. Bude vyšší latencí. Pokud máte jednu sadu dat, která je větší a ten, který je menší, můžete použít několik instancí ConnectionMultiplexer, každý nakonfigurován s jinou sadou hodnot časového limitu a zkuste to znovu, jak je popsáno v předchozí [co dělat StackExchange.Redis Možnosti konfigurace proveďte](#cache-configuration) oddílu.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak můžu srovnávací testy a testování výkonu mezipaměť?
* [Povolte diagnostiku mezipaměti](cache-how-to-monitor.md#enable-cache-diagnostics), abyste mohli [monitorovat](cache-how-to-monitor.md) stav svojí mezipaměti. Metriky lze zobrazit na webu Azure Portal a můžete je také [stáhnout a revidovat](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) pomocí nástrojů dle vašeho výběru.
* Vám pomůže redis benchmark.exe zátěžový test vašeho serveru Redis.
* Zajistěte, aby byly zátěžové testování klienta a ukládání do mezipaměti Azure Redis ve stejné oblasti.
* Použití redis-cli.exe a sledování mezipaměti pomocí příkazu informace.
* Pokud zatížení způsobuje fragmentaci velkého množství paměti, by měla vertikálně navýšit kapacitu na větší velikost mezipaměti.
* Pokyny ke stahování nástroje Redis, najdete v článku [jak mohu spustit příkazy Redis?](#cache-commands) oddílu.

Následující příkazy jsou příkladem použití redis benchmark.exe. Pro přesné výsledky spusťte tyto příkazy z virtuálního počítače ve stejné oblasti jako mezipaměť.

* Pomocí datové části je 1 kB žádosti o zřetězena nastavení testu

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test zřetězena získat požadavků pomocí datové části je 1 kB.
  POZNÁMKA: Spustit test sady uvedené nahoře první naplňte mezipaměť

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Důležité podrobnosti o růstu fondu vláken
Fondu vláken CLR má dva typy vláken – "Pracovní" a "Portu dokončení I/O%N" (neboli portu IOCP) vlákna.

* Pracovní vlákna se používají pro takové věci, jako je zpracování `Task.Run(…)`, nebo `ThreadPool.QueueUserWorkItem(…)` metody. Tato vlákna používají různé součásti v CLR také, když pracovní musí dojít na vlákně na pozadí.
* Portu IOCP vlákna se používají, když asynchronní vstupně-výstupní operace se stane (třeba čtení ze sítě).

Fondu vláken poskytuje nové pracovní vlákna nebo vlákna dokončení vstupně-výstupních operací na vyžádání (bez žádné omezení) dokud nedosáhne nastavení "Minimální" pro každý typ vlákna. Ve výchozím nastavení je nastavit minimální počet vláken na počet procesorů v systému.

Jakmile narazí na počet vláken existující (zaneprázdněn) "minimální" počet vláken, fondu vláken se omezení rychlost, jakou ji vkládá nová vlákna na jedno vlákno na jeden 500 milisekund. Obvykle Pokud váš systém získá zřízeno práce potřebují portu IOCP vlákna, zpracuje, které pracují velmi rychle. Ale pokud burst práce je větší než nakonfigurované nastavení "Minimální", bude určité zpoždění zpracování určitou část práce fondu vláken čeká na jednu ze dvou kroků, které se provedou.

1. Existující vlákno neuvolní zpracovat práce.
2. Žádné existující vlákno nebude zdarma po dobu 500ms, takže se vytvoří nové vlákno.

V podstatě znamená, že když počet zaneprázdněných vláken je větší než minimální vláken, pravděpodobně platíte 500ms zpoždění před zpracováním síťového provozu aplikací. Je také důležité si uvědomit, že když existující vlákno zůstává nečinné po dobu delší než 15 sekund (podle pamatuji si), se vyčistí a tento cyklus růstu a snižování můžete opakovat.

Pokud se podíváme na příklad chybová zpráva z StackExchange.Redis (sestavení 1.0.450 nebo novější), zobrazí se, že nyní vypíše statistiky fondu vláken (viz pracovního procesu a portu IOCP podrobnosti níže).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

V předchozím příkladu můžete uvidíte, že pro vlákno portu IOCP jsou 6 zaneprázdněná vlákna a systém je nakonfigurován tak, že umožňují 4 minimální počet vláken. V tomto případě klient by pravděpodobně viděli dvě zpoždění 500 ms protože 6 > 4.

Všimněte si, že StackExchange.Redis mohli dostat vypršení časového limitu, když získá omezený růstu portu IOCP nebo pracovní vlákna.

### <a name="recommendation"></a>Doporučení
Tyto informace zadané, důrazně doporučujeme, že zákazníci nastavit hodnotu minimální konfiguraci portu IOCP a pracovních vláken na něco větší než je výchozí hodnota. Poskytujeme nelze univerzální doprovodné materiály k co tato hodnota by měla být správné hodnoty pro jednu aplikaci proto bude příliš vysoká nebo Nízká pro jinou aplikaci. Tato nastavení mohou ovlivnit výkon dalších částí složitých aplikací, takže každý zákazník potřebuje pro vyladění toto nastavení pro jejich konkrétní potřeby. Dobrý výchozí bod je 200 a 300, otestujte a upravit podle potřeby.

Jak nakonfigurovat toto nastavení:

* V technologii ASP.NET, použijte ["minIoThreads" nebo "minWorkerThreads" nastavení konfigurace] [ "minIoThreads" configuration setting] pod `<processModel>` konfiguračního prvku v souboru web.config. Pokud používáte v rámci Azure WebSites, toto nastavení není dostupná možnosti konfigurace. Ale měli stále budete moci nakonfigurovat tato nastavení programově (viz níže) z vaší metody Application_Start v global.asax.cs.

  > [!NOTE] 
  > Hodnota zadaná v tento prvek konfigurace je *na jádro* nastavení. Například pokud máte 4jádrový počítač a chcete nastavení minIOThreads být 200 za běhu, použijete `<processModel minIoThreads="50"/>`.
  >

* Mimo technologii ASP.NET a Azure WebSites global.asax, použijte [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) API.

  > [!NOTE]
  > Hodnotu zadanou pomocí tohoto rozhraní API je globální nastavení, by to ovlivnilo celé doméně AppDomain. Pokud máte 4jádrový počítač a chcete nastavit minWorkerThreads a minIOThreads až 50 jeden procesor a jsou za běhu, můžete využít ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Povolit uvolňování paměti serveru získat větší propustnost na straně klienta při používání StackExchange.Redis
Povolování uvolňování paměti serveru může optimalizovat klienta a zajišťuje lepší výkon a propustnost při použití StackExchange.Redis. Další informace o uvolňování paměti serveru a jak se dá povolit najdete v následujících článcích:

* [Chcete-li povolit uvolňování paměti serveru](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Základy kolekce paměti](/dotnet/standard/garbage-collection/fundamentals)
* [Uvolňování paměti a výkonu](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Důležité informace o výkonu po připojení

Každá cenová úroveň má jiné omezení pro připojení klientů, paměti a šířky pásma. Zatímco jednotlivé velikosti mezipaměti umožňuje *až* určitý počet připojení, každé připojení k Redis režie k němu má přidružené. Příkladem takových zatížení může být využití procesoru a paměti v důsledku šifrování TLS/SSL. Limit pro velikost daného mezipaměti maximální připojení předpokládá lehce načíst mezipaměti. Pokud načtení z připojení režii *plus* zatížení z klientské operace přesahuje kapacitu pro systém, mezipaměti může docházet potíže s kapacitou, i pokud nebyl překročen limit připojení pro aktuální velikost mezipaměti.

Další informace o omezeních různých připojení pro jednotlivé úrovně najdete v tématu [mezipaměti Azure Redis ceny](https://azure.microsoft.com/pricing/details/cache/). Další informace o připojení a další výchozí konfigurace najdete v tématu [konfigurace serveru Redis výchozí](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Jak monitorovat stav a výkon své mezipaměti?
Microsoft Azure Cache pro instance Redis můžete sledovat v [webu Azure portal](https://portal.azure.com). Můžete zobrazit metriky, připnout grafy metrik na úvodní panel, přizpůsobení datum a čas, množství grafy monitorování, přidání a odebrání grafy metrik a nastavit upozornění při splnění určitých podmínek. Další informace najdete v tématu [monitorování mezipaměti Azure Redis](cache-how-to-monitor.md).

Ukládání do mezipaměti Azure pro Redis **nabídce prostředků** také obsahuje několik nástrojů pro monitorování a řešení potíží s vaší mezipaměti.

* **Diagnostikovat a řešit problémy** poskytuje informace o běžných problémech a strategie pro jejich řešení.
* **Služba Resource health** sleduje váš prostředek a řekne vám, jestli funguje podle očekávání. Další informace o službě Azure Resource health najdete v tématu [přehled Azure Resource health](../resource-health/resource-health-overview.md).
* **Nová žádost o podporu** nabízí možnosti pro žádost o podporu ke svojí mezipaměti.

Tyto nástroje umožňují monitorování stavu vaší mezipaměti Azure pro instance Redis a snadněji tak můžete spravovat ukládání do mezipaměti aplikace. Další informace najdete v části "Podpora a řešení potíží nastavení" z [konfigurace mezipaměti Azure pro Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Proč se mi zobrazuje časové limity?
Dojde k vypršení časového limitu v klientovi, který použijete ke komunikaci s Redis. Při odesílání příkazu k serveru Redis, příkaz je zařazeny do fronty a serveru Redis nakonec vybere příkaz a spustí ho. Ale klient může vypršení časového limitu během tohoto procesu a pokud se výjimka je vyvolána na straně volajícího. Další informace o řešení problémů při vypršení časového limitu najdete v tématu [řešení potíží na straně klienta](cache-how-to-troubleshoot.md#client-side-troubleshooting) a [výjimkám časového limitu StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Proč se můj klient odpojen z mezipaměti?
Tady jsou některé z běžných důvodů mezipaměti odpojit.

* Způsobí, že na straně klienta
  * Klientská aplikace byla znovu nasadil.
  * Klientská aplikace provést operaci škálování.
    * V případě cloudových služeb nebo webových aplikací může to být způsobeno automatickým Škálováním.
  * Změnit síťovou vrstvou na straně klienta.
  * V klientovi nebo síťové uzly mezi klientem a serverem došlo k přechodné chyby.
  * Bylo dosaženo prahové limity šířky pásma.
  * Procesor vázán operace trvalo moc dlouho.
* Způsobí, že na straně serveru
  * Na nabídku standardní mezipaměti inicializovat ukládání do mezipaměti Azure pro službu Redis převzetí služeb při selhání z primárního uzlu na sekundární uzel.
  * Azure byl opravy instance nasazená do mezipaměti
    * To může být pro aktualizace serveru Redis nebo Obecná údržba virtuálního počítače.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Kterou z variant Mezipaměti Azure si mám vybrat?
> [!IMPORTANT]
> Podle loňském turnaji [oznámení](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), služba Azure Managed Cache Service a mezipaměť In-Role Azure **mají vyřazený** na 30. listopadu 2016. Naše doporučení je použití [mezipaměti Azure Redis](https://azure.microsoft.com/services/cache/). Informace o migraci najdete v tématu [migrace ze služby Managed Cache Service k mezipaměti Redis Azure](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Mezipaměť Azure pro Redis je obecně dostupné ve velikosti až 53 GB a má smlouvu SLA 99,9 % dostupnosti. Nové [úroveň premium](cache-premium-tier-intro.md) nabízí velikosti až 530 GB a podpora pro clustering, virtuální síť a trvalost, se smlouvou SLA 99,9 %.

Mezipaměti Redis Azure umožňuje zákazníkům používat zabezpečené, vyhrazené mezipaměti Azure pro Redis, spravovanou microsoftem. Díky této nabídce přístup k bohaté sadě funkcí a ekosystému Redis a spolehlivé hostování a sledování od Microsoftu.

Na rozdíl od tradičních mezipamětí, které se zabývají pouze páry klíč hodnota je pro jeho vysoce výkonné datové typy Redis. Redis také podporuje spouštění atomické operace na tyto typy, jako je připojení k řetězec; zvyšování hodnoty do hodnoty hash; doručením (push) do seznamu. výpočetní průnik, sjednocení a rozdíl; nebo získání člena s nejvyšším pořadím v seřazené sady. Další funkce zahrnují podporu pro transakce, publikování a odběr, Lua skriptování, klíče s omezenou time-to-live a nastavení konfigurace více zaškrtávacímu tradiční mezipaměti Redis, aby.

Dalším aspektem klíče na úspěšné provedení Redis je otevřít zdroj v pořádku, živý ekosystém postavené na to. Toto je zohledněno v různorodou sadu klientů Redis k dispozici v různých jazycích. Tento ekosystém a širokou škálu klientů povolit mezipaměť Azure Redis použít prakticky pro každou úlohu, kterou jste by v rámci služby Azure.

Další informace o zahájení práce s mezipamětí Azure pro Redis najdete v tématu [postupy použití mezipaměti Azure Redis](cache-dotnet-how-to-use-azure-redis-cache.md) a [mezipaměti Azure Redis dokumentaci](index.md).

### <a name="managed-cache-service"></a>Služba Managed Cache service
[Managed Cache service skončil 30. listopadu 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Chcete-li zobrazit Archivovaná dokumentace, naleznete v tématu [archivované spravovaná mezipaměť dokumentace ke službě](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Mezipaměť hostovaná v instanci role
[Mezipaměť in-Role skončil 30. listopadu 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Chcete-li zobrazit Archivovaná dokumentace, naleznete v tématu [Archivovaná dokumentace k mezipaměti In-Role](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
