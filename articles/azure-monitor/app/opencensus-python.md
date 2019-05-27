---
title: Trasování OpenCensus Python pomocí Azure Application Insights | Dokumentace Microsoftu
description: Pokyny pro nastavit trasování OpenCensus Python s místní server pro předávání a Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9db483e15197e6cdaaaa5981410630184cc6ca
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957239"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Shromažďovat distribuované trasování z Pythonu (Preview)

Application Insights teď podporuje distribuované trasování aplikací v Pythonu pomocí integrace s [OpenCensus](https://opencensus.io) a naše nové [místní předávání](./../../azure-monitor/app/opencensus-local-forwarder.md). Tento článek vás provede procesem nastavování OpenCensus pro Python a získávají se vaše data trasování do Application Insights.

## <a name="prerequisites"></a>Požadavky

- Mít předplatné Azure.
- Python by měly být nainstalovány, tento článek používá [Python 3.7.0](https://www.python.org/downloads/), i když dřívějších verzích bude pravděpodobně pracovat s menší úpravu.
- Postupujte podle pokynů k instalaci [místní předávání jako služba Windows](./../../azure-monitor/app/opencensus-local-forwarder.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Vytvořit prostředek Application Insights

Nejprve je nutné vytvořit prostředek Application Insights, která bude generovat key(ikey) instrumentaci. Ikey pak slouží ke konfiguraci místního serveru pro předávání k odesílání distribuované trasování z aplikace OpenCensus instrumentována pro Application Insights.   

1. Vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/opencensus-python/0001-create-resource.png)

   Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Typ aplikace** | Obecné | Typ aplikace, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název pro novou skupinu prostředků, která bude hostovat data App Insights |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na možnost **Vytvořit**.

## <a name="configure-local-forwarder"></a>Nakonfigurovat místní server pro předávání

1. Vyberte **Přehled** > **Základy** a zkopírujte **instrumentační klíč** vaší aplikace.

   ![Snímek obrazovky Instrumentační klíč](./media/opencensus-python/0003-instrumentation-key.png)

2. Upravit vaše `LocalForwarder.config` a přidejte svůj Instrumentační klíč. Pokud jste postupovali podle pokynů [předpoklad](./../../azure-monitor/app/opencensus-local-forwarder.md) soubor se nachází v `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Restartování aplikace **místní předávání** služby.

## <a name="opencensus-python-package"></a>Balíček Pythonu OpenCensus

1. Nainstalujte balíček otevřete sčítání pro Python a Exportér pomocí pip nebo pipenv z příkazového řádku:

    ```console
    python -m pip install opencensus
    python -m pip install opencensus-ext-ocagent

    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` předpokládá, že máte nastaven pro vaši instalaci Pythonu proměnné prostředí PATH. Pokud jste nenakonfigurovali toto, je třeba poskytnout úplné adresář na umístění spustitelný soubor Pythonu který jako výsledek v příkazu: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. První umožňuje generovat některá data trasování místně. V Pythonu NEČINNÉ nebo vašem editoru podle výběru zadejte následující kód:

    ```python
    from opencensus.trace.tracer import Tracer

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

3. Spuštění kódu opakovaně vás vyzve k zadání hodnoty. S každou položku se vytisknou hodnotu do prostředí a odpovídající část **SpanData** vygeneruje OpenCensus modul Pythonu. Definuje OpenCensus projektu [ _trasování jako strom rozsahy_](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. To je užitečné pro demonstrační účely, takže v konečném důsledku chceme, aby generoval SpanData tak, aby ji mohou být zachyceny pomocí našich **služby předávání místní** a odeslané do služby Application Insights. Upravte kód z předchozího kroku takto:

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.ext.ocagent.trace_exporter import TraceExporter
    from opencensus.trace import tracer as tracer_module

    import os

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        export_LocalForwarder = TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))

        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

5. Pokud uložíte a zkuste spustit modul, může se zobrazit `ModuleNotFoundError` pro `grpc`. V tomto případě spusťte následující příkaz k instalaci [grpcio balíčku](https://pypi.org/project/grpcio/) pomocí:

    ```console
    python -m pip install grpcio
    ```

6. Teď, když spustíte skript Pythonu z výše by měl stále výzva k zadání hodnoty, ale teď je pouze hodnotu tisku v prostředí.

7. Zkontrolujte, že **místní předávání** sbírá kontrola trasování `LocalForwarder.config` souboru. Pokud jste postupovali podle kroků v [předpoklad](https://docs.microsoft.com/azure/application-insights/local-forwarder), bude nacházet v `C:\LF-WindowsServiceHost`.

    Na obrázku níže souboru protokolu vidíte, že před spuštěním druhý skriptu, které jsme přidali Exportér `OpenCensus input BatchesReceived` je 0. Jakmile jsme začali používat aktualizovaný skript `BatchesReceived` zvýšena stejný počet hodnot, které jsme zadali:
    
    ![Formulář Nový prostředek App Insights](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít Application Insights **přehled** stránky na webu Azure Portal, chcete-li zobrazit podrobné informace o aktuálně spuštěné aplikaci. Vyberte **Live Stream metrik**.

   ![Snímek obrazovky podokna přehled s živý stream metrik, které jsou vybrané v červeným rámečkem](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Pokud spustíte znovu druhý skript Pythonu a začněte zadáním hodnoty, zobrazí se živá data trasování, jako je e-mailu ve službě Application Insights ze služby předávání místní.

   ![Snímek obrazovky se zobrazí data o výkonu živý stream metrik](./media/opencensus-python/0006-stream.png)

3. Přejděte zpět **přehled** stránku a vybrat **Mapa aplikace** pro vizuální rozložení vztahů závislosti a časování volání mezi komponentami vaší aplikace.

    ![Snímek obrazovky základní aplikace mapy](./media/opencensus-python/0007-application-map.png)

    Protože jsme byly trasování pouze jedno volání metody, není jako zajímavé Mapa aplikace. Ale můžete škálovat mapu aplikace k vizualizaci mnohem více distribuované aplikace:

   ![Mapa aplikace](media/opencensus-python/application-map.png)

4. Vyberte **zkoumání výkonu** provádět podrobnou analýzu výkonu a určení původní příčiny snížení výkonu.

    ![Snímek obrazovky podokna výkonu](./media/opencensus-python/0008-performance.png)

5. Výběr **ukázky** a potom kliknutím na některé z ukázek, které se zobrazují v pravém podokně se spustí prostředí podrobnosti transakce začátku do konce. Když naše ukázková aplikace se pouze zobrazí jednu událost, složitější aplikaci umožní prozkoumat začátku do konce transakcí na úrovni jednotlivých událostí zásobníku volání.

     ![Snímek obrazovky začátku do konce transakce rozhraní](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>OpenCensus trace for Python

Jsme probrali pouze základní informace o zapojení do OpenCensus pro Python s místní server pro předávání a Application Insights. Oficiální pokyny se zabývá pokročilejší témata zahrnují třeba:

* [Vzorkovačů](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Integrace Flask](https://opencensus.io/api/python/trace/usage.html#flask)
* [Integrace Django](https://opencensus.io/api/python/trace/usage.html#django)
* [MySQL Integration](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>Další postup

* [Použití Průvodce OpenCensus Pythonu](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Sledování výkonu začátku do konce](./../../azure-monitor/learn/tutorial-performance.md)
