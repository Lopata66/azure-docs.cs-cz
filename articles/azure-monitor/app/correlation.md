---
title: Korelace telemetrie Azure Application Insights | Microsoft Docs
description: Korelace telemetrie Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: tracking-python
ms.openlocfilehash: b4facaee44a0bc5c7d64376ca80e5aaf8d0768d0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323158"
---
# <a name="telemetry-correlation-in-application-insights"></a>Korelace telemetrie v Application Insights

V celém světě mikroslužeb vyžaduje každá Logická operace provedení práce v různých součástech služby. Jednotlivé komponenty můžete monitorovat samostatně pomocí [Application Insights](./app-insights-overview.md). Application Insights podporuje korelaci distribuované telemetrie, kterou použijete k detekci, která komponenta zodpovídá za selhání nebo snížení výkonu.

Tento článek vysvětluje datový model používaný Application Insights ke korelaci telemetrie odesílaného více komponentami. Zahrnuje postupy a protokoly šíření kontextu. Zahrnuje také implementaci korelace taktiku v různých jazycích a platformách.

## <a name="data-model-for-telemetry-correlation"></a>Datový model pro korelaci telemetrie

Application Insights definuje [datový model](./data-model.md) pro korelaci distribuovaných telemetrie. Pro přidružení telemetrie k logické operaci má každá položka telemetrie kontextové pole s názvem `operation_Id` . Tento identifikátor je sdílen všemi položkami telemetrie v distribuovaném trasování. Takže i v případě, že dojde ke ztrátě telemetrie z jedné vrstvy, můžete stále přidružit telemetrii nahlášenou jinými komponentami.

Distribuovaná Logická operace se typicky skládá ze sady menších operací, které jsou požadavky zpracovávané jednou z komponent. Tyto operace jsou definovány [telemetrie požadavků](./data-model-request-telemetry.md). Každá položka telemetrie požadavků má svou vlastní `id` identifikaci jedinečnou a globálně. A všechny položky telemetrie (například trasování a výjimky), které jsou přidruženy k žádosti, by měly být nastaveny na `operation_parentId` hodnotu požadavku `id` .

Každá odchozí operace, jako je volání HTTP jiné součásti, je reprezentována [telemetrie závislosti](./data-model-dependency-telemetry.md). Telemetrie závislostí definuje také vlastní `id` globálně jedinečný. Požadavek telemetrie, iniciované tímto voláním závislosti, používá `id` jako svůj `operation_parentId` .

Můžete sestavit zobrazení distribuované logické operace pomocí `operation_Id` , a `operation_parentId` `request.id` s `dependency.id` . Tato pole také definují pořadí volání telemetrie.

V prostředí mikroslužeb můžou trasování z komponent přejít na jiné položky úložiště. Každá součást může mít vlastní klíč instrumentace v Application Insights. Pro získání telemetrie pro logickou operaci Application Insights dotazovat data z každé položky úložiště. Pokud je počet položek úložiště velký, budete potřebovat nápovědu, kde můžete hledat další. Datový model Application Insights definuje dvě pole pro vyřešení tohoto problému: `request.source` a `dependency.target` . První pole identifikuje komponentu, která iniciovala požadavek závislosti. Druhé pole určuje, která komponenta vrátila odpověď na volání závislostí.

## <a name="example"></a>Příklad

Podívejme se na příklad. Aplikace s názvem ceny akcií zobrazuje aktuální cenu na trhu na populaci pomocí externího rozhraní API s názvem Stock. Aplikace burzovních cen má stránku s názvem skladová stránka, kterou klientský webový prohlížeč otevře pomocí `GET /Home/Stock` . Aplikace se dotazuje na skladové rozhraní API pomocí volání HTTP `GET /api/stock/value` .

Výslednou telemetrii můžete analyzovat spuštěním dotazu:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Ve výsledcích si všimněte, že všechny položky telemetrie sdílejí kořen `operation_Id` . Když je ze stránky provedeno volání AJAX, `qJSXU` je k telemetrie závislostí přiřazeno nové jedinečné ID () a ID pageView se používá jako `operation_ParentId` . Požadavek serveru pak použije ID AJAX jako `operation_ParentId` .

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Skladová stránka                |              | STYz               | STYz         |
| závislosti | ZÍSKAT/Home/Stock           | qJSXU        | STYz               | STYz         |
| Request    | ZÍSKAT domácí/burzovní            | KqKwlrSt9PA = | qJSXU              | STYz         |
| závislosti | ZÍSKAT/API/Stock/Value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Při volání `GET /api/stock/value` externí služby potřebujete znát identitu tohoto serveru, abyste mohli `dependency.target` odpovídajícím způsobem nastavit pole. Pokud externí služba nepodporuje monitorování, `target` je nastavena na název hostitele služby (například `stock-prices-api.com` ). Pokud však služba identifikuje sebe sama vrácením předdefinované hlavičky HTTP, `target` obsahuje identitu služby, která umožňuje Application Insights sestavit distribuované trasování pomocí dotazování telemetrie z této služby.

## <a name="correlation-headers"></a>Hlavičky korelace

Application Insights se převádí na [kontext trasování W3C](https://w3c.github.io/trace-context/), který definuje:

- `traceparent`: Provede globálně jedinečné ID operace a jedinečný identifikátor volání.
- `tracestate`: Přenese kontext trasování specifický pro systém.

Nejnovější verze Application Insights SDK podporuje protokol kontextu trasování, ale může být nutné se k němu přihlásit. (Bude zachována zpětná kompatibilita s předchozím protokolem korelace, který podporuje sada SDK Application Insights.)

[Korelační protokol HTTP, označovaný také jako Request-ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), se už nepoužívá. Tento protokol definuje dvě hlavičky:

- `Request-Id`: Provede globálně jedinečné ID volání.
- `Correlation-Context`: Přenese kolekci dvojic název-hodnota pro distribuované vlastnosti trasování.

Application Insights také definuje [rozšíření](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) pro protokol HTTP korelace. Používá `Request-Context` páry název-hodnota ke šíření kolekce vlastností používaných přímým volajícím nebo volaným. Sada Application Insights SDK používá tuto hlavičku k nastavení `dependency.target` polí a `request.source` .

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Povolení podpory distribuovaného trasování W3C pro klasické aplikace ASP.NET
 
  > [!NOTE]
  >  Od `Microsoft.ApplicationInsights.Web` a `Microsoft.ApplicationInsights.DependencyCollector` není nutná žádná konfigurace.

Trasování W3C – podpora kontextu je implementována zpětně kompatibilním způsobem. Očekává se korelace pro práci s aplikacemi, které jsou instrumentované s předchozími verzemi sady SDK (bez podpory W3C).

Pokud chcete dál používat starší `Request-Id` protokol, můžete zakázat trasování kontextu pomocí této konfigurace:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Pokud používáte starší verzi sady SDK, doporučujeme, abyste ji aktualizovali nebo použili následující konfiguraci pro povolení trasování kontextu.
Tato funkce je k dispozici `Microsoft.ApplicationInsights.Web` v `Microsoft.ApplicationInsights.DependencyCollector` balíčcích a počínaje verzí 2.8.0-Beta1.
Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, udělejte tyto změny `ApplicationInsights.config` :

- V části `RequestTrackingTelemetryModule` přidejte `EnableW3CHeadersExtraction` prvek a nastavte jeho hodnotu na `true` .
- V části `DependencyTrackingTelemetryModule` přidejte `EnableW3CHeadersInjection` prvek a nastavte jeho hodnotu na `true` .
- Přidejte `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers` . Bude vypadat podobně jako v tomto příkladu:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace ASP.NET Core

 > [!NOTE]
  > Počínaje `Microsoft.ApplicationInsights.AspNetCore` verzí 2.8.0 není nutná žádná konfigurace.
 
Trasování W3C – podpora kontextu je implementována zpětně kompatibilním způsobem. Očekává se korelace pro práci s aplikacemi, které jsou instrumentované s předchozími verzemi sady SDK (bez podpory W3C).

Pokud chcete dál používat starší `Request-Id` protokol, můžete zakázat trasování kontextu pomocí této konfigurace:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Pokud používáte starší verzi sady SDK, doporučujeme, abyste ji aktualizovali nebo použili následující konfiguraci pro povolení trasování kontextu.

Tato funkce je ve `Microsoft.ApplicationInsights.AspNetCore` verzi 2.5.0-Beta1 a ve `Microsoft.ApplicationInsights.DependencyCollector` verzi 2.8.0-Beta1.
Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, nastavte `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` na `true` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Povolit podporu distribuovaného trasování W3C pro aplikace Java

#### <a name="java-30-agent"></a>Agent Java 3,0

  Agent Java 3,0 podporuje W3C mimo pole a není nutná žádná další konfigurace. 

#### <a name="java-sdk"></a>Java SDK
- **Příchozí konfigurace**

  - Pro aplikace v jazyce Java EE přidejte do `<TelemetryModules>` značky v ApplicationInsights.xml následující:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - V případě aplikací pro spouštění pružiny přidejte tyto vlastnosti:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Odchozí konfigurace**

  Přidejte následující AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Režim zpětné kompatibility je ve výchozím nastavení povolený a `enableW3CBackCompat` parametr je nepovinný. Používejte ji pouze v případě, že chcete vypnout zpětnou kompatibilitu.
  >
  > V ideálním případě byste tuto funkci vypnuli, až budou všechny vaše služby aktualizované na novější verze sad SDK, které podporují protokol W3C. Důrazně doporučujeme co nejdříve přesunout na tyto novější sady SDK.

> [!IMPORTANT]
> Ujistěte se, že jsou příchozí a odchozí konfigurace přesně stejné.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Povolit podporu distribuovaného trasování W3C pro webové aplikace

Tato funkce je v systému `Microsoft.ApplicationInsights.JavaScript` . Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, použijte `distributedTracingMode` config. AI_AND_W3C je k dispozici kvůli zpětné kompatibilitě se staršími službami, které instrumentují Application Insights.

- **nastavení NPM (ignorovat při použití nastavení fragmentu)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Nastavení fragmentu (ignorovat při použití instalačního programu npm)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing a Application Insights

Datové modely [OpenTracing a specifikace datového modelu](https://opentracing.io/) pro Application Insights mapují následujícím způsobem:

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`řetězce`span.kind = server`                    |
| `Dependency`                           | `Span`řetězce`span.kind = client`                    |
| `Id`z `Request` a`Dependency`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `Reference`typu `ChildOf` (nadřazený rozsah)     |

Další informace najdete v tématu [Application Insights datovém modelu telemetrie](./data-model.md).

Definice konceptů OpenTracing najdete v tématu [specifikace](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing a [sémantické konvence](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Korelace telemetrie v OpenCensus Pythonu

OpenCensus Python sleduje `OpenTracing` specifikace datového modelu popsané výše. Podporuje také [kontext trasování W3C](https://w3c.github.io/trace-context/) bez nutnosti jakékoli konfigurace.

### <a name="incoming-request-correlation"></a>Korelace příchozích požadavků

OpenCensus Python koreluje hlavičky W3C Trace-Context z příchozích požadavků do rozsahů, které jsou generovány z požadavků samotných. OpenCensus to provede automaticky s integrací pro tyto oblíbené webové aplikace: baňky, Django a jehlany. Stačí pouze naplnit hlavičky kontextu trasování W3C pomocí [správného formátu](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) a odeslat je do žádosti. Tady je ukázková aplikace, která demonstruje toto:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Tento kód spustí ukázkovou aplikaci na vašem místním počítači, která naslouchá na portu `8080` . Pro korelaci kontextu trasování odešlete požadavek na koncový bod. V tomto příkladu můžete použít `curl` příkaz:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Když se podíváte na [Formát záhlaví kontextu trasování](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), můžete odvodit následující informace:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Pokud se podíváte na položku žádosti, která byla odeslána do Azure Monitor, uvidíte pole naplněná pomocí informací v hlavičce trasování. Tato data najdete v části protokoly (Analytics) v prostředku Azure Monitor Application Insights.

![Vyžádat telemetrii v protokolech (analýza)](./media/opencensus-python/0011-correlation.png)

`id`Pole je ve formátu `<trace-id>.<span-id>` , kde `trace-id` je získán z hlavičky trasování, která byla předána v požadavku a `span-id` je vygenerováno pole s osmi bajty pro tento rozsah.

`operation_ParentId`Pole je ve formátu `<trace-id>.<parent-id>` , kde `trace-id` a, `parent-id` z hlavičky trasování, která byla předána v žádosti.

### <a name="log-correlation"></a>Korelace protokolů

OpenCensus Python umožňuje korelovat protokoly přidáním ID trasování, identifikátoru ID a příznaku vzorkování pro zaznamenávání záznamů. Tyto atributy přidáte tak, že nainstalujete [integraci protokolování](https://pypi.org/project/opencensus-ext-logging/)OpenCensus. Následující atributy budou přidány do `LogRecord` objektů Python: `traceId` , `spanId` a `traceSampled` . Všimněte si, že se to projeví jenom u protokolovacích nástrojů, které se vytvoří po integraci.

Zde je ukázková aplikace, která demonstruje tuto:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Po spuštění tohoto kódu se v konzole nástroje zobrazí následující:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Všimněte si, že je k `spanId` dispozici pro zprávu protokolu v rámci rozsahu. To je totéž `spanId` , co patří do rozpětí s názvem `hello` .

Data protokolu můžete exportovat pomocí `AzureLogHandler` . Další informace najdete v [tomto článku](./opencensus-python.md#logs).

## <a name="telemetry-correlation-in-net"></a>Korelace telemetrie v .NET

V průběhu času .NET definovalo několik způsobů, jak sladit protokoly telemetrie a diagnostiky:

- `System.Diagnostics.CorrelationManager`umožňuje sledování [LogicalOperationStack a ActivityId](/dotnet/api/system.diagnostics.correlationmanager?view=netcore-3.1).
- `System.Diagnostics.Tracing.EventSource`a trasování událostí pro Windows (ETW) definuje metodu [SetCurrentThreadActivityId](/dotnet/api/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid?view=netcore-3.1#overloads) .
- `ILogger`používá [rozsahy protokolů](/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) a kabel HTTP nahoru "aktuální" šíření kontextu.

Tyto metody ale nepovolily automatickou podporu distribuovaného trasování. `DiagnosticSource`podporuje automatickou korelaci mezi počítači. Knihovny .NET podporují `DiagnosticSource` a umožňují automatické šíření mezipočítačového kontextu korelace prostřednictvím přenosu, jako je například http.

[Příručka pro uživatele aktivity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) v `DiagnosticSource` tématu vysvětluje základy sledování aktivit.

ASP.NET Core 2,0 podporuje extrakci hlaviček protokolu HTTP a spuštění nových aktivit.

`System.Net.Http.HttpClient`Počínaje verzí 4.1.0 podporuje automatické vkládání korelačních hlaviček protokolu HTTP a sledování volání HTTP jako aktivit.

Pro klasický ASP.NET je k dispozici nový modul HTTP, [Microsoft. ASPNET. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/). Tento modul implementuje korelaci telemetrie pomocí `DiagnosticSource` . Spustí aktivitu na základě hlaviček příchozích požadavků. Také koreluje telemetrii z různých fází zpracování požadavků, a to i v případě, že je každá fáze zpracování služby Internetová informační služba (IIS) spuštěna v jiném spravovaném vlákně.

Sada Application Insights SDK, počínaje verzí 2.4.0-Beta1, používá `DiagnosticSource` a `Activity` ke shromáždění telemetrie a jejím přidružení k aktuální aktivitě.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Korelace telemetrie v jazyce Java

[Agent Java](./java-in-process-agent.md) a [Java SDK](../../azure-monitor/app/java-get-started.md) verze 2.0.0 nebo novější podporují automatickou korelaci telemetrie. Automaticky se naplní `operation_id` pro veškerou telemetrii (jako jsou trasování, výjimky a vlastní události) vydaná v rámci požadavku. Také šíří hlavičky korelace (popsané dříve) pro volání služby-služba prostřednictvím protokolu HTTP, pokud je nakonfigurován [Agent Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Agent Application Insights Java automaticky shromažďuje požadavky a závislosti pro JMS, Kafka, síťovinu/webtoků a další. V případě sady Java SDK jsou pro funkci korelace podporovány pouze volání prostřednictvím Apache HttpClient. Automatické šíření kontextu napříč technologiemi zasílání zpráv (například Kafka, RabbitMQ a Azure Service Bus) není v sadě SDK podporováno. 

> [!NOTE]
> Pro shromáždění vlastní telemetrie, kterou potřebujete k instrumentaci aplikace pomocí sady Java 2,6 SDK. 

### <a name="role-names"></a>Názvy rolí

Možná budete chtít přizpůsobit způsob, jakým se názvy komponent zobrazují v [mapě aplikace](../../azure-monitor/app/app-map.md). K tomu můžete ručně nastavit `cloud_RoleName` pomocí jedné z následujících akcí:

- V případě Application Insightsho agenta Java 3,0 nastavte název cloudové role následujícím způsobem:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    Název cloudové role můžete také nastavit pomocí proměnné prostředí `APPLICATIONINSIGHTS_ROLE_NAME` .

- S Application Insights Java SDK 2.5.0 a novější můžete zadat `cloud_RoleName` přidáním `<RoleName>` do ApplicationInsights.xml souboru:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Pokud používáte jarní spouštění s Application Insightsm Starter Boot Starter, stačí nastavit vlastní název aplikace v souboru Application. Properties:

  `spring.application.name=<name-of-app>`

  Jaře Boot Starter automaticky přiřadí `cloudRoleName` hodnotu, kterou zadáte pro `spring.application.name` vlastnost.

## <a name="next-steps"></a>Další kroky

- Zápis [vlastní telemetrie](./api-custom-events-metrics.md).
- Pokročilé scénáře korelace v ASP.NET Core a ASP.NET najdete v tématu [sledování vlastních operací](custom-operations-tracking.md).
- Přečtěte si další informace o [nastavení cloud_RoleName](./app-map.md#set-cloud-role-name) pro jiné sady SDK.
- Připojte všechny komponenty mikroslužby na Application Insights. Podívejte se na [podporované platformy](./platforms.md).
- Seznamte se s [datovým modelem](./data-model.md) pro Application Insights typy.
- Naučte se, jak můžete [zvětšit a filtrovat telemetrii](./api-filtering-sampling.md).
- Přečtěte si [referenční informace o konfiguraci Application Insights](configuration-with-applicationinsights-config.md).

