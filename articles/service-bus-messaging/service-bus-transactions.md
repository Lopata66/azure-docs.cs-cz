---
title: Přehled zpracování transakcí v Azure Service Bus | Dokumentace Microsoftu
description: Přehled služby Azure Service Bus atomické transakce a odeslat prostřednictvím
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2018
ms.author: aschhab
ms.openlocfilehash: a839a4cad824a74bde388317cf3aaddf9c5bd47f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332342"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Přehled služby Service Bus zpracování transakcí

Tento článek popisuje možnosti transakce služby Microsoft Azure Service Bus. Velká část diskuse je znázorněn ve [AMQP transakce s ukázkou služby Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Tento článek je omezená na základní informace o zpracování transakcí a *odeslat prostřednictvím* funkcí ve službě Service Bus, zatímco ukázka atomické transakce je širší a složitější v oboru.

## <a name="transactions-in-service-bus"></a>Transakce ve službě Service Bus

A *transakce* seskupuje dva nebo více operací do *provádění oboru*. Podle povahy musíte tyto transakce zajistit, že všechny operace, které patří k dané skupině operací úspěšné, nebo selže společně. V tomto ohledu transakce fungují jako jednu jednotku, která se často označuje jako *atomicitu*.

Service Bus je zprostředkovatel transakčních zpráv a zajišťuje transakční integrity pro všechny interní operace s jeho úložiště zpráv. Všechny přenosy zpráv v rámci služby Service Bus, jako je například přesun zpráv [fronty nedoručených zpráv](service-bus-dead-letter-queues.md) nebo [automatické přesměrování](service-bus-auto-forwarding.md) zpráv mezi entitami, které jsou transakční. V důsledku toho pokud služby Service Bus přijme zprávu, je již uloženy a popisek s pořadovým číslem. Od té chvíle případné přenosy zpráv v rámci služby Service Bus jsou koordinovat operace napříč entitami a ani povede ke ztrátě (úspěšný zdroj a cíl selže) nebo k duplikaci (zdroj selže a úspěšné cíl) zprávy.

Service Bus podporuje operace seskupení u jedné entity zasílání zpráv (fronty, tématu, odběru) v rámci oboru transakce. Například odešlete několik zpráv pro jednu frontu z v rámci oboru transakcí a zprávy pouze budou potvrzeny do protokolu do fronty po úspěšném dokončení transakce.

## <a name="operations-within-a-transaction-scope"></a>Operace v rámci oboru transakcí

Operace, které lze provést v rámci oboru transakce jsou následující:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)** : Odeslat, SendAsync, SendBatch SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** : Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Zobrazí operace nejsou zahrnuty, protože se předpokládá, že aplikace získá zprávy pomocí [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) smyčka příjmu režimu do některých nebo s [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) zpětné volání, a teprve potom se otevře oboru transakce pro zpracování zprávy.

Dispozice zprávy (dokončeno, zrušení, dead-letter odložit) potom se provede v rámci oboru a v závislosti na celkový výsledek transakce.

## <a name="transfers-and-send-via"></a>Převody a "odeslat prostřednictvím"

Umožňuje transakční předání dat z fronty procesoru a potom do jiné fronty služby Service Bus podporuje *přenosy*. V rámci operace přenosu odesílatel nejdříve odešle zprávu *fronty přenosu*, a fronty přenosu okamžitě přesune zprávu do fronty požadovaného cíle pomocí stejné implementaci robustní přenos, který auto-forward Funkce spoléhá na. Zpráva se nikdy zavazuje fronty přenosu protokolu tak, že se zobrazí uživatelům fronty přenosu.

Výkon této transakční funkce pocítíte, když samotné fronty přenosu je zdrojem odesílatele zprávy o zadávání. Jinými slovy, Service Bus se můžou přenášet zprávy do cílové fronty "přes" frontě přenosů při provádění úplná (nebo odložit, nebo nedoručené zprávy) operace na vstupní zprávy v rámci jedné atomické operace. 

### <a name="see-it-in-code"></a>Prohlédněte si portál v kódu

Chcete-li nastavit tyto převody, můžete vytvořit odesílatele zprávy, který cílí na cílové fronty prostřednictvím fronty přenosu. Máte také příjemce, která si přetáhne zprávy z této fronty stejné. Příklad:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Jednoduché transakce pak používá tyto prvky, jako v následujícím příkladu. K odkazování Úplný příklad, přečtěte si [zdrojového kódu na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích pro další informace o fronty služby Service Bus:

* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Řetězení entit služby Service Bus s automatickým přeposíláním](service-bus-auto-forwarding.md)
* [Auto-forward vzorku](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomické transakce s ukázkou služby Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Fronty Azure a služby Service Bus fronty porovnání](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


