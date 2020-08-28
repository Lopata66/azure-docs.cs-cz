---
title: Přehled zpracování transakcí v Azure Service Bus
description: Tento článek poskytuje přehled zpracování transakcí a funkci Odeslat prostřednictvím v Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f51e570775fbce8a316d98b5198fa906173dc755
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999950"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Přehled zpracování Service Bus transakcí

Tento článek popisuje možnosti transakce Microsoft Azure Service Bus. Mnohé diskuze jsou znázorněné [AMQP transakcemi s ukázkami Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Tento článek je omezený na Přehled zpracování transakcí a funkci *Odeslat prostřednictvím* v Service Bus, zatímco je ukázka atomických transakcí v oboru širší a složitější.

## <a name="transactions-in-service-bus"></a>Transakce v Service Bus

*Transakce* seskupí dvě nebo více operací dohromady do *oboru provádění*. V takovém případě taková transakce musí zajistit, aby všechny operace patřící do dané skupiny operací byly buď úspěšné, nebo selžou společně. V tomto případě transakce slouží jako jedna jednotka, která je často označována jako *atomická*.

Service Bus je zprostředkovatel transakčních zpráv a zajišťuje transakční integritu pro všechny interní operace proti úložištím zpráv. Všechny přenosy zpráv uvnitř Service Bus, jako je přesun zpráv do [fronty nedoručených](service-bus-dead-letter-queues.md) zpráv nebo [Automatické předávání](service-bus-auto-forwarding.md) zpráv mezi entitami, jsou transakční. V takovém případě, pokud Service Bus akceptuje zprávu, již byla uložena a označena pořadovým číslem. Od výše uvedeného je jakékoli přenosy zpráv v rámci Service Bus koordinované operace napříč entitami a nedojde k výpadkům (zdroj úspěšných a neúspěšných cílů) nebo k duplikaci (zdroj selže a cíl je úspěšný) ve zprávě.

Service Bus podporuje operace seskupení u jedné entity zasílání zpráv (fronty, tématu, odběru) v rámci oboru transakce. Například můžete odeslat několik zpráv do jedné fronty z oboru transakce a zprávy budou potvrzeny pouze do protokolu fronty po úspěšném dokončení transakce.

## <a name="operations-within-a-transaction-scope"></a>Operace v rámci oboru transakce

Operace, které lze provést v rámci oboru transakce, jsou následující:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: `Complete` , `CompleteAsync` , `Abandon` , `AbandonAsync` , `Deadletter` , `DeadletterAsync` , `Defer` , `DeferAsync` , `RenewLock` , `RenewLockAsync` 

Operace Receive nejsou zahrnuty, protože se předpokládá, že aplikace získává zprávy pomocí režimu [ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) , uvnitř některé z cyklů příjmu nebo pomocí zpětného volání [při chybě,](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) a poté otevře obor transakce pro zpracování zprávy.

Dispozice zprávy (dokončeno, opustit, nedoručená zpráva) pak probíhá v rámci rozsahu a závisí na celkovém výsledku transakce.

## <a name="transfers-and-send-via"></a>Přenosy a "odeslání přes"

Pokud chcete povolit transakční předají dat z fronty na procesor a následně do jiné fronty, Service Bus podporuje *přenosy*. Odesílatel v operaci přenosu nejprve pošle zprávu do *fronty přenosů*a fronta přenosu okamžitě přesune zprávu do zamýšlené cílové fronty pomocí stejné implementace robustního přenosu, na které se spoléhá funkce dopřed. Zpráva se nikdy nezavazuje do protokolu fronty přenosu tak, že se bude zobrazovat pro příjemce ve frontě přenosu.

Výkon této transakční funkce se bude poznat, když je samotná fronta přenosu zdrojem vstupních zpráv odesílatele. Jinými slovy Service Bus může přenést zprávu do cílové fronty "prostřednictvím fronty přenosu, při provádění úplné operace (nebo odložení nebo nedoručené zprávy) na vstupní zprávě, a to vše v jedné atomické operaci. 

### <a name="see-it-in-code"></a>Zobrazit v kódu

K nastavení takových přenosů vytvoříte odesílatele zprávy, který cílí na cílovou frontu prostřednictvím fronty přenosu. Máte také přijímač, který vyžádá zprávy ze stejné fronty. Příklad:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Jednoduchá transakce poté používá tyto prvky, jako v následujícím příkladu. Pokud chcete odkazovat na úplný příklad, přečtěte si [zdrojový kód na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

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

## <a name="timeout"></a>Časový limit
Časový limit transakce vypršel po 2 minutách. Časovač transakce začíná při zahájení první operace v transakci. 

## <a name="next-steps"></a>Další kroky

Další informace o frontách Service Bus najdete v následujících článcích:

* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Zřetězení Service Bus entit pomocí procesu autopřesměrovávání](service-bus-auto-forwarding.md)
* [Ukázka dopředné](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomické transakce s Service Bus ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Ve srovnání s frontami Azure a frontami Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


