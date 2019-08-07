---
title: Řešení potíží s Application Insights ve webovém projektu Java
description: Průvodce odstraňováním potíží – monitorování živých aplikací Java pomocí Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: a26302b0c0b4361fe3e7aae6aba798f433c72ade
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742182"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Řešení potíží a otázky a odpovědi v nástroji Application Insights
Otázky nebo problémy s [Azure Application Insights v jazyce Java][java]? Tady je několik tipů.

## <a name="build-errors"></a>Chyby sestavení
**V zatmění nebo IntelliJ nápadu při přidávání sady Application Insights SDK prostřednictvím Maven nebo Gradle se zobrazí chyby ověření sestavení nebo kontrolního součtu.**

* Pokud element Dependency `<version>` používá vzor se zástupnými znaky (např. Maven nebo (Gradle) `version:'2.0.+'`) Zkuste zadat konkrétní verzi místo toho, jako `2.0.1`je. `<version>[2.0,)</version>` Nejnovější verzi najdete v poznámkách k [verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases) .

## <a name="no-data"></a>Žádná data
**Přidal (a) jsem Application Insights úspěšně a spustil jsem aplikaci, ale na portálu se nikdy nezobrazila žádná data.**

* Počkejte minutu a klikněte na tlačítko Aktualizovat. Grafy se pravidelně aktualizují, ale můžete je také aktualizovat ručně. Interval aktualizace závisí na časovém rozsahu grafu.
* Ověřte, že máte klíč instrumentace definovaný v souboru ApplicationInsights. XML (ve složce Resources ve vašem projektu), nebo nakonfigurovaný jako proměnná prostředí.
* Ověřte, že soubor XML `<DisableTelemetry>true</DisableTelemetry>` neobsahuje žádný uzel.
* V bráně firewall možná budete muset pro odchozí přenosy na dc.services.visualstudio.com otevřít porty TCP 80 a 443. Zobrazit [úplný seznam výjimek brány firewall](../../azure-monitor/app/ip-addresses.md)
* Na panelu Microsoft Azure Start se podívejte na mapu stavu služby. Pokud se zobrazí upozornění, počkejte, až se vrátí do OK, a pak zavřete a znovu otevřete okno aplikace Application Insights.
* [Zapněte protokolování](#debug-data-from-the-sdk) přidáním `<SDKLogger />` prvku pod kořenovým uzlem v souboru ApplicationInsights. XML (ve složce Resources v projektu) a vyhledejte položky s AI: INFORMACE, varování/chyba pro všechny podezřelé protokoly. 
* Ujistěte se, že soubor ApplicationInsights. XML byl úspěšně načten sadou Java SDK, a to tak, že prohlíží výstupní zprávy konzoly pro příkaz "konfigurační soubor byl úspěšně nalezen".
* Pokud konfigurační soubor nebyl nalezen, zkontrolujte výstupní zprávy, zda je prohledáván konfigurační soubor, a ujistěte se, že ApplicationInsights. XML je umístěn v jednom z těchto umístění hledání. Jako pravidlo pro palec můžete umístit konfigurační soubor poblíž Application Insights SDK jar. Například: v Tomcat by to znamenalo složku WEB-INF/Classes. Během vývoje můžete umístit ApplicationInsights. XML do složky Resources vašeho webového projektu.
* Podívejte se také na [stránku problémy s GitHubem](https://github.com/Microsoft/ApplicationInsights-Java/issues) , kde najdete známé problémy se sadou SDK.
* Abyste se vyhnuli jakýmkoli problémům se konflikty verzí, zajistěte, aby používaly stejnou verzi Application Insights Core, webu, agenta a přihlášení.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Zobrazil (a) jsem data, ale zastavila se
* Podívejte se na [blog stavu](https://blogs.msdn.com/b/applicationinsights-status/).
* Dosáhli jste měsíční kvóty datových bodů? Pokud chcete zjistit, otevřete nastavení/kvótu a ceny. Pokud ano, můžete upgradovat svůj plán nebo platit za další kapacitu. Podívejte se na téma [cenové schéma](https://azure.microsoft.com/pricing/details/application-insights/).
* Máte nedávno upgradovanou sadu SDK? Ujistěte se prosím, že v adresáři projektu jsou jenom jedinečné jar SDK. K dispozici není žádná dvě z různých verzí sady SDK.
* Hledáte správný prostředek AI? Porovnejte iKey své aplikace s prostředkem, u kterého očekáváte telemetrii. Měly by být stejné.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nezobrazují se všechna data, která očekávám
* Otevřete stránku využití a odhadované náklady a ověřte, zda je [vzorkování](../../azure-monitor/app/sampling.md) v provozu. (100% přenos znamená, že vzorkování není v provozu.) Službu Application Insights lze nastavit tak, aby přijímala pouze zlomek telemetrie, která přichází z vaší aplikace. To vám pomůže udržet se v rámci měsíční kvóty telemetrie.
* Máte zapnuté vzorkování sady SDK? Pokud ano, budou se data vzorkovat podle sazby určené pro všechny příslušné typy.
* Používáte starší verzi sady Java SDK? Od verze 2.0.1 jsme zavedli mechanismus odolnosti proti chybám, který zpracovává občasné chyby sítě a back-endu a také Trvalost dat na místních jednotkách.
* Získáváte omezení z důvodu nadměrné telemetrie? Pokud zapnete protokolování informací, zobrazí se zpráva protokolu aplikace je omezená. Náš aktuální limit je 32 položek telemetrie za sekundu.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agent Java nemůže zachytit data závislostí.
* Nakonfigurovali jste agenta Java pomocí následujícího [Nastavení agenta Java](java-agent.md) ?
* Zajistěte, aby byl jar agenta Java a soubor AI-Agent. XML umístěny do stejné složky.
* Ujistěte se, že závislost, kterou se pokoušíte automaticky shromažďovat, je podporovaná pro automatické shromažďování. V současné době podporujeme pro kolekci závislostí Redis jenom MySQL, MsSQL, Oracle DB a Azure cache.
* Používáte JDK 1,7 nebo 1,8? V současné době nepodporujeme shromažďování závislostí v JDK 9.

## <a name="no-usage-data"></a>Žádná data o využití
**Zobrazuje se data o požadavcích a časech odezvy, ale neexistují žádná zobrazení stránky, prohlížeče ani uživatelská data.**

Úspěšně jste nastavili aplikaci, aby odesílala telemetrii ze serveru. Teď jste v dalším kroku nastavili [webové stránky pro odesílání telemetrie z webového prohlížeče][usage].

Případně, pokud je váš klient aplikace na [telefonu nebo jiném zařízení][platforms], můžete z něj poslat telemetrii.

Použijte stejný klíč instrumentace k nastavení telemetrie klienta i serveru. Data se zobrazí ve stejném prostředku Application Insights a budete moci korelovat události z klienta a serveru.


## <a name="disabling-telemetry"></a>Zakázání telemetrie
**Jak můžu zakázat shromažďování telemetrie?**

V kódu:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Nebo**

Aktualizujte soubor ApplicationInsights. XML (ve složce Resources ve vašem projektu). Do kořenového uzlu přidejte následující:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Pomocí metody XML je nutné restartovat aplikaci při změně hodnoty.

## <a name="changing-the-target"></a>Změna cíle
**Jak můžu změnit, na který prostředek Azure můj projekt odesílá data?**

* [Získejte klíč instrumentace nového prostředku.][java]
* Pokud jste do projektu přidali Application Insights pomocí Azure Toolkit for Eclipse, klikněte pravým tlačítkem myši na svůj webový projekt, vyberte položku **Azure**, **nakonfigurujte Application Insights**a změňte klíč.
* Pokud jste klíč instrumentace nakonfigurovali jako proměnnou prostředí, aktualizujte hodnotu proměnné prostředí pomocí New iKey.
* V opačném případě aktualizujte klíč v souboru ApplicationInsights. XML ve složce Resources v projektu.

## <a name="debug-data-from-the-sdk"></a>Ladění dat ze sady SDK

**Jak můžu zjistit, co dělá sada SDK?**

Pokud chcete získat další informace o tom, co se děje v rozhraní `<SDKLogger/>` API, přidejte do kořenového uzlu konfiguračního souboru ApplicationInsights. XML.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Můžete také instruovat protokolovací nástroj na výstup do souboru:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Jaře Boot Starter

Pokud chcete povolit protokolování sady SDK s aplikacemi pružiny, které používají Application Insights jaře Boot Starter, přidejte `application.properties` do souboru následující:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

nebo k tisku na standardní chybu:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agent Java

Pokud chcete povolit protokolování agenta JVM, proveďte aktualizaci [souboru AI-agent. XML](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Vlastnosti příkazového řádku Java
_Od verze 2.4.0_

Povolení protokolování pomocí možností příkazového řádku bez změny konfiguračních souborů:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

nebo k tisku na standardní chybu:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Úvodní obrazovka Azure
**Díváte [se na Azure Portal](https://portal.azure.com). Řekněte vám mapa něco o mé aplikaci?**

Ne, zobrazuje stav serverů Azure po celém světě.

*Jak můžu najít data o mé aplikaci, na úvodní desce Azure (domovskou obrazovku)?*

Za předpokladu, že jste [aplikaci nastavili pro Application Insights][java], klikněte na Procházet, vyberte Application Insights a vyberte prostředek aplikace, který jste pro svou aplikaci vytvořili. Chcete-li v budoucnu získat rychlejší přístup k aplikaci, můžete ji připnout na úvodní desku.

## <a name="intranet-servers"></a>Intranetové servery
**Můžu na svém intranetu monitorovat Server?**

Ano, pokud váš server může prostřednictvím veřejného Internetu odesílat telemetrii na portál Application Insights.

V bráně firewall možná budete muset otevřít porty TCP 80 a 443 pro odchozí provoz do dc.services.visualstudio.com a f5.services.visualstudio.com.

## <a name="data-retention"></a>Uchovávání dat
**Jak dlouho se data na portálu uchovávají? Je zabezpečení?**

Podívejte [se na uchovávání dat a ochranu osobních údajů][data].

## <a name="debug-logging"></a>Protokolování ladění
Application Insights používá `org.apache.http`. Toto je přemístěné v rámci Application Insights Core jar pod `com.microsoft.applicationinsights.core.dependencies.http`oborem názvů. To umožňuje Application Insights zpracovávat scénáře, kde různé verze stejného `org.apache.http` typu existují v jednom základu kódu.

>[!NOTE]
>Pokud povolíte protokolování na úrovni ladění pro všechny obory názvů v aplikaci, budou tyto moduly přijaty všemi spuštěnými moduly `org.apache.http` , včetně `com.microsoft.applicationinsights.core.dependencies.http`přejmenování. Application Insights nebude moct pro tato volání použít filtrování, protože se v knihovně Apache provádí volání protokolu. Protokolování úrovně ladění vytvoří značnou část dat protokolu a nedoporučuje se pro živé provozní instance.


## <a name="next-steps"></a>Další postup
**Nastavil jsem Application Insights pro aplikaci Java Server. Co dalšího můžu udělat?**

* [Monitorování dostupnosti webových stránek][availability]
* [Monitorování využití webové stránky][usage]
* [Sledování využití a diagnostikování problémů v aplikacích pro zařízení][platforms]
* [Napsat kód pro sledování využití vaší aplikace][track]
* [Zachytávání diagnostických protokolů][javalogs]

## <a name="get-help"></a>Podpora
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Vystavení problému na GitHubu](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

