---
title: Azure Service Bus začátku do konce trasování a Diagnostika | Dokumentace Microsoftu
description: Přehled služby Service Bus klient diagnostiky a trasování začátku do konce
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 6e5895392db1d75a985674bf2f878a84bc8dd926
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107031"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuované trasování a korelace prostřednictvím zasílání zpráv Service Bus

Možnost jedním z běžných problémů při vývoji mikroslužeb je operace trasování z klienta do všech služeb, které jsou součástí zpracování. Je užitečné pro ladění, analýza výkonu, A / B testování a další scénáře typické diagnostiky.
Jednou ze součástí sady tento problém se ke sledování práce logických částí. Zahrnuje zprávu zpracování výsledku a latence a volání externích závislostí. Jiné části je korelace tyto diagnostické události mimo hranice procesu.

Pokud odesílatel odešle zprávu do fronty, obvykle dochází v rámci jiné logické operace, inicializuje některé klienta nebo služby. Stejné operace pokračuje spotřebitel, jakmile přijme zprávu. Výrobce a příjemců (i jiné služby, které zpracovávají operaci), pravděpodobně z důvodu vysílat události telemetrie do trasování toku operace a výsledek. Pro korelaci těchto událostí a trasování operace začátku do konce, musí každá služba, která odesílá telemetrická data razítka každé události trasování kontextu.

Microsoft Azure Service Bus messaging definoval datovou část vlastnosti, které producenti a spotřebitelé by měl použít k předávání těchto trasování kontextu.
Je na základě protokolu [protokolu HTTP korelace](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Property Name        | Popis                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Jedinečný identifikátor externího volání od výrobce do fronty. Odkazovat na [Request-Id v protokolu HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) důvody, požadavky a formátu |
|  Correlation-Context | Operace kontext, který se šíří napříč všemi službami, které jsou součástí operace zpracování. Další informace najdete v tématu [korelace kontextu protokolu HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Automatické trasování klienta rozhraní .NET služby Service Bus

Od verze 3.0.0 [klienta služby Service Bus Microsoft Azure pro .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) poskytuje bodům instrumentace trasování, které můžete využívat trasování systémy nebo kusu kódu klienta.
Instrumentace umožňuje sledování všech volání služby Service Bus pro zasílání zpráv služby ze strany klienta. Pokud se pomocí provádí zpracování zpráv [vzor obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), je také instrumentováno zpracování zprávy

### <a name="tracking-with-azure-application-insights"></a>Sledování pomocí Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) poskytuje bohaté možnosti, jako je automagical požadavků a sledování závislostí pro monitorování výkonu.

V závislosti na typu vašeho projektu nainstalujte sadu SDK Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) – instalace beta2 verze 2.5 nebo vyšší
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) – instalace verze 2.2.0-beta2 nebo vyšší.
Tyto odkazy poskytují podrobné informace o instalaci sady SDK, vytváření prostředků a konfigurace sady SDK (v případě potřeby). Aplikace – technologie ASP.NET, najdete v tématu [Azure Application Insights pro aplikace konzoly](../azure-monitor/app/console.md) článku.

Pokud používáte [vzor obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) ke zpracování zpráv, skončíte: všechna volání služby Service Bus provádí vaše služba se automaticky sledovat a korelují s ostatními položkami telemetrická data. V opačném případě najdete v následujícím příkladu pro zpracování zpráv ruční sledování.

#### <a name="trace-message-processing"></a>Zpracování zpráv trasování

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

V tomto příkladu `RequestTelemetry` se použije v hlášení pro každou zpracovanou zprávu s časovým razítkem, doba trvání a výsledek (úspěch). Telemetrická data také obsahuje sadu vlastností korelace.
Vnořené trasování a výjimky během zpracování zpráv jsou také označené s vlastnostmi korelace představující jako "podřízené" `RequestTelemetry`.

V případě, že provedete volání na externí komponenty, podporovaná při zpracování zprávy, jsou také automaticky sledovat a korelační. Odkazovat na [sledování vlastních operací pomocí Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) pro ruční sledování a korelace.

### <a name="tracking-without-tracing-system"></a>Sledování bez trasování systému
V případě, že váš systém trasování nepodporuje automatické volání služby Service Bus pro sledování můžete hledat do přidání těchto podpory do trasování systému nebo do vaší aplikace. Tato část popisuje diagnostické události odeslané klientem služby Service Bus .NET.  

Klient .NET služby Service Bus je instrumentováno pomocí .NET trasování primitiv [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) a [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` slouží jako trasování kontextu při `DiagnosticSource` virtuálních sítí je mechanismus oznámení. 

Pokud neexistuje žádný naslouchací proces událostí DiagnosticSource, instrumentace je vypnuté, udržování nulovými náklady instrumentace. DiagnosticSource poskytuje všechny ovládacího prvku k naslouchacímu procesu:
- naslouchací proces ovládací prvky, které zdrojů a události tak, aby naslouchala na
- frekvence událostí ovládacích prvků naslouchací proces a vzorkování
- události se posílají s datovou částí, která poskytuje úplný kontext, takže můžete používat a upravte objekt zprávy během události

Seznamte se s [DiagnosticSource uživatelská příručka](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) před pokračováním v provádění.

Vytvoříme v aplikaci ASP.NET Core, který zapisuje protokoly s Microsoft.Extension.Logger naslouchacího procesu pro události služby Service Bus.
Používá [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) knihovny k odběru DiagnosticSource (je také snadné předplatit DiagnosticSource bez něho)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

V tomto příkladu naslouchací proces zaznamená dobu trvání, výsledek, jedinečný identifikátor a počáteční čas pro každou operaci služby Service Bus.

#### <a name="events"></a>Události

Pro všechny operace jsou zasílány dvě události: 'Start' a 'Stop'. Nejvíce pravděpodobně vás zajímá jenom 'Stop' události. Se získat výsledek operace, jakož i času spuštění a trvání jako vlastnosti aktivity.

Datová část události obsahuje naslouchací proces s kontextem operace, replikuje příchozí parametry rozhraní API a návratovou hodnotu. Datová část události 'Stop' má všechny vlastnosti 'Start' datové části události tak můžete ignorovat 'Start' události úplně.

Všechny události mají také vlastnosti "Entity" a "Koncový bod", se vynechá ve níže uvedená tabulka
  * `string Entity` – Název sady entit (fronty, tématu, atd.)
  * `Uri Endpoint` -Adresa URL koncového bodu Service Bus

Každá událost 'Stop' má `Status` vlastnost s `TaskStatus` asynchronní operace byla dokončena s, který je také v následující tabulce pro zjednodušení vynechán.

Tady je úplný seznam instrumentované operace:

| Název operace | Sledované rozhraní API | Vlastnosti konkrétní datová část|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` – Seznam zpráv odesílaných |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` -Zpracovávanou zprávu<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Posun naplánovanou zprávu<br/>`long SequenceNumber` -Pořadové číslo zprávy plánované ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` -Pořadové číslo zprávy te budou zrušeny | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` – Maximální počet zpráv, které může být přijata.<br/>`IList<Message> Messages` – Seznam přijatých zpráv ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` -Počáteční bod, ze kterého chcete procházet dávku zpráv.<br/>`int RequestedMessageCount` -Počet zpráv pro načtení.<br/>`IList<Message> Messages` – Seznam přijatých zpráv ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` – Seznam obsahující pořadová čísla pro příjem.<br/>`IList<Message> Messages` – Seznam přijatých zpráv ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` – Seznam obsahující tokeny zámek odpovídající zpráv k dokončení.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` -Token uzamčení zprávy k opuštění odpovídající. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` -Token uzamčení zprávy k odložení odpovídající. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` -Token uzamčení zprávy odpovídající nedoručených zpráv. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` -Token uzamčení odpovídající obnovit uzamčení zprávy.<br/>`DateTime LockedUntilUtc` -Nový zámek vypršení platnosti tokenu datum a čas ve formátu UTC. ('Stop' datová část události)|
| Microsoft.Azure.ServiceBus.Process | Funkce lambda obslužné rutiny zpráv podle [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Zpracovávanou zprávu. |
| Microsoft.Azure.ServiceBus.ProcessSession | Funkce lambda relace obslužné rutiny zpráv podle [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Zpracovávanou zprávu.<br/>`IMessageSession Session` -Relace zpracovává |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` -Pravidlo popis, který obsahuje pravidlo pro přidání. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` – Název pravidla, které chcete odebrat. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` -Všechna pravidla přidružená k předplatnému. (Pouze datovou 'Stop' část) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` Nejsou k dispozici ve zprávách – sessionId. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` Nejsou k dispozici ve zprávách – sessionId.<br/>`byte [] State` -Stav relace ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` Nejsou k dispozici ve zprávách – sessionId.<br/>`byte [] State` – Stav relace |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` Nejsou k dispozici ve zprávách – sessionId. |
| Microsoft.Azure.ServiceBus.Exception | žádné instrumentované rozhraní API| `Exception Exception` – Instance výjimka |

V každé události mohly přistupovat k `Activity.Current` , který uchovává kontextu aktuální operace.

#### <a name="logging-additional-properties"></a>Další vlastnosti protokolování

`Activity.Current` poskytuje podrobné kontextu aktuální operace a jejích nadřazených tříd. Další informace najdete v tématu [dokumentace k aktivitám](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) další podrobnosti.
Instrumentace služby Service Bus poskytuje další informace v `Activity.Current.Tags` -drží `MessageId` a `SessionId` vždy, když jsou k dispozici.

Aktivity, které sledují "Přijímat", "Náhled" a "ReceiveDeferred" událost také může mít `RelatedTo` značky. Obsahuje seznam různých `Diagnostic-Id`(s) zpráv, které byly přijaty ve výsledku.
Tato operace může způsobit mohlo přijímat několika nesouvisejících zprávy. Také `Diagnostic-Id` nezná při spuštění operace, tak operace "Receive" může časově tagu pouze operace "Proces". Je užitečné při analýze problémy s výkonem ke kontrole, jak dlouho trvalo zpráva.

Účinný způsob, jak protokolovat značky se má iterovat přes ně, takže přidáním klíčových slov v předchozím příkladu vypadá 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrování a vzorkování

V některých případech je třeba přihlásit pouze část události ke snížení výkonu zatížení nebo úložiště spotřeby. Může protokolovat události 'Stop' pouze (jako v předchozím příkladu) nebo ukázkový procento událostí. 
`DiagnosticSource` Zadejte způsob, jak dosáhnout s `IsEnabled` predikátu. Další informace najdete v tématu [filtrování na základě kontextu v DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` může být volána více než jednou pro jednu operaci pro minimalizaci vlivu na výkon.

`IsEnabled` je volána v následujícím pořadí:

1. `IsEnabled(<OperationName>, string entity, null)` například `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Všimněte si, že na konci není žádná 'Start' nebo 'Stop'. Můžete filtrovat na konkrétní operace nebo fronty. Pokud zpětného volání vrátí `false`, se nebude posílat události pro operace

   * Pro operace "Proces" a "Entitami ProcessSession", obdržíte také `IsEnabled(<OperationName>, string entity, Activity activity)` zpětného volání. Umožňuje filtrovat události podle `activity.Id` nebo značky vlastnosti.
  
2. `IsEnabled(<OperationName>.Start)` například `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Kontroluje, zda by měl být vyvolala událost 'Start'. Výsledek se týká pouze události 'Start', ale další instrumentace není závislý na něj.

Neexistuje žádná `IsEnabled` 'Stop' události.

Pokud některé výsledku operace je výjimka, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` je volána. Pouze můžete přihlásit k odběru událostí 'Exception' a zabránit rest instrumentace. V takovém případě máte stále zpracovávat takové výjimky. Protože je zakázané další instrumentaci, by neměli očekávat kontextu trasování, které jsou předávány do producenta zpráv od příjemce.

Můžete použít `IsEnabled` také implementovat strategie vzorkování. Na základě odběru vzorků `Activity.Id` nebo `Activity.RootId` zajišťuje konzistentní vzorkování přes všechny pytli (tak dlouho, dokud je postoupena trasování systému nebo váš vlastní kód).

Za přítomnosti více `DiagnosticSource` naslouchací procesy pro stejný zdroj je dostatečná pro právě jeden naslouchací proces tak, aby přijímal událostí, takže `IsEnabled` není zaručeno, že má být volána,

## <a name="next-steps"></a>Další postup

* [Application Insights Correlation](../azure-monitor/app/correlation.md)
* [Application Insights monitorovat závislosti](../azure-monitor/app/asp-net-dependencies.md) zobrazíte, pokud REST, SQL nebo jiných externích prostředků vás zpomalují.
* [Sledování vlastních operací pomocí Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
