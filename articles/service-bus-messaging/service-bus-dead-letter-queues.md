---
title: Fronty nedoručených zpráv služby Service Bus | Dokumentace Microsoftu
description: Přehled fronty nedoručených zpráv Azure Service Bus
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: aschhab
ms.openlocfilehash: af67b27dacf3bb86c2dd5c878a2751e027a53acb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003122"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Přehled fronty nedoručených zpráv služby Service Bus

Fronty Azure Service Bus a odběrů tématu poskytují sekundární dílčí fronty, s názvem *fronty nedoručených zpráv* (DLQ). Fronty nedoručených zpráv nemusí být explicitně vytvořeny a nelze odstranit nebo jinak spravovat nezávisle na hlavní entity.

Tento článek popisuje fronty nedoručených zpráv ve službě Service Bus. Velká část diskuse je znázorněn ve [Ukázka fronty nedoručených zpráv](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) na Githubu.
 
## <a name="the-dead-letter-queue"></a>Fronty nedoručených zpráv

Účelem fronty nedoručených zpráv je pro uložení zpráv, které nelze doručit žádnému příjemci nebo zprávy, které nebylo možné zpracovat. Zprávy můžete pak odebrána z DLQ a prozkoumat. Aplikace může s pomocí operátoru opravit problémy a znovu odeslat zprávu protokolu skutečnost, že došlo k chybě a provést opravné akce. 

Z hlediska rozhraní API a protokol DLQ je ve většině případů podobně jako jakékoli jiné frontě, s tím rozdílem, že zprávy lze předat vždy pouze prostřednictvím operace onta nedoručených zpráv nadřazené entity. Kromě toho není dodržena time-to-live, a nemůžete zobrazí se zpráva DLQ nedoručených zpráv. Fronty nedoručených zpráv plně podporuje transakční operace a doručování neboli peek-lock.

Všimněte si, že neexistuje žádné automatické čištění DLQ se. Zprávy zůstávají ve DLQ, dokud je explicitně načíst z DLQ a volání [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na nedoručených zpráv.

## <a name="moving-messages-to-the-dlq"></a>Přesun zpráv DLQ

Existuje několik aktivit ve službě Service Bus, které způsobují zprávy k získání nahrány do DLQ z v rámci samotný modul pro zasílání zpráv. Aplikace můžete také explicitně přesunout zprávy DLQ se. 

Získá zprávu přesunu zprostředkovatelem, dvě vlastnosti jsou přidány do zprávy jako zprostředkovatel volá jeho vnitřní verzi [nedoručených zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metodu na zpráva: `DeadLetterReason` a `DeadLetterErrorDescription`.

Aplikace můžete definovat vlastní kódy `DeadLetterReason` vlastnost, ale systém nastaví následující hodnoty.

| Podmínka | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Vždy |HeaderSizeExceeded |Kvóta velikosti pro tento datový proud byla překročena. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing a SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |došlo k výjimce. Zpráva |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Zprávě vypršela platnost a zařadila se do fronty nedoručených zpráv. |
| SubscriptionDescription.RequiresSession |Id relace má hodnotu null. |Entita povolená relací nepodporuje zprávy, jejichž identifikátor relace má hodnotu null. |
| ! fronty nedoručených zpráv |MaxTransferHopCountExceeded |Null |
| Aplikace explicitní dead písmem. |Zadaná aplikace |Zadaná aplikace |

## <a name="exceeding-maxdeliverycount"></a>Překročení MaxDeliveryCount

Fronty a předplatná [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) vlastnost; Výchozí hodnota je 10. Vždy, když byla zpráva doručena pod zámkem ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale byla buď explicitně opuštěna nebo vypršela platnost zámku, zprávy [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) je zvýší. Když [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) překračuje [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), zpráva bude přesunuta do DLQ, určení `MaxDeliveryCountExceeded` kód důvodu.

Toto chování nelze zakázat, ale můžete nastavit [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na velmi velké množství.

## <a name="exceeding-timetolive"></a>Překročení TimeToLive

Když [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) je nastavena na **true** (výchozí hodnota je **false**), u nichž vyprší platnost všech zpráv se přesouvají na DLQ, určení `TTLExpiredException` kód důvodu.

Upozorňujeme, že jsou zprávy s vypršenou platností pouze odstraněna a přesunout do DLQ se při přijímání změn z hlavní fronty nebo odběru; aspoň jednoho aktivního příjemce. Toto chování je záměrné.

## <a name="errors-while-processing-subscription-rules"></a>Chyby při zpracování pravidel odběru

Když [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) vlastnost je povolena pro předplatné, všechny chyby, ke kterým spustí pravidlo filtru SQL předplatného jsou zachyceny v DLQ podél chybná zpráva.

## <a name="application-level-dead-lettering"></a>Aplikace úrovně dead-lettering

Kromě dead-lettering funkcí poskytovaných systémem aplikace můžete DLQ se explicitně odmítnutí nepřijatelné zpráv. To může zahrnovat zprávy, které nelze správně zpracovat z důvodu jakýkoli druh systémovým potížím, zprávy, které obsahují poškozené datové části nebo zprávy, které jejich ověření nebude možné při použití některých schéma zabezpečení na úrovni zprávy.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering ve scénářích ForwardTo nebo SendVia

Zprávy se odešlou do fronty nedoručených zpráv přenosu za následujících podmínek:

- Zpráva prochází více než 4 front nebo témat, které jsou [zřetězit](service-bus-auto-forwarding.md).
- Cílová fronta nebo téma se zakázala nebo odstranila.
- Cílová fronta nebo téma překračuje velikost maximální entity.

K načtení těchto zprávách dead lettered, můžete vytvořit pomocí příjemce [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) metody nástrojů.

## <a name="example"></a>Příklad:

Následující fragment kódu vytvoří příjemce zprávy. Ve smyčce příjmu pro hlavní frontou, kód načte zprávu s [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), která žádá zprostředkovatele okamžitě vrátit všechny zprávy snadno k dispozici, nebo budou vráceny s žádný výsledek. Pokud kód obdrží zprávu, okamžitě zruší ho, které zvýší `DeliveryCount`. Jakmile systém přesune zprávu do DLQ, hlavní fronta je prázdná a opakování ve smyčce ukončeno, jako [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) vrátí **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Cesta k frontě nedoručených zpráv
Fronty nedoručených zpráv můžete přistupovat pomocí následující syntaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscription/<subscription path>/$deadletterqueue
```

Pokud používáte sadu .NET SDK, můžete získat cestu do fronty nedoručených zpráv pomocí metody SubscriptionClient.FormatDeadLetterPath(). Tato metoda přebírá název název nebo odběru tématu a přípony s **/$DeadLetterQueue**.


## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích pro další informace o fronty služby Service Bus:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Fronty Azure a služby Service Bus fronty porovnání](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

