---
title: Azure Service Bus – odložení zprávy
description: Tento článek vysvětluje, jak odložit doručování Azure Service Busch zpráv. Zpráva zůstane ve frontě nebo odběru, ale odloží se bokem.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538392"
---
# <a name="message-deferral"></a>Odložení zpráv

Když klient fronty nebo předplatného obdrží zprávu, že je ochotna zpracovat, ale pro které není aktuálně možné zpracování v důsledku zvláštních okolností uvnitř aplikace, má možnost odvodit načtení zprávy do pozdějšího bodu. Zpráva zůstane ve frontě nebo odběru, ale odloží se bokem.

Odložení je funkce specificky vytvořená pro scénáře zpracování pracovního postupu. Prostředí pracovních postupů mohou vyžadovat zpracování určitých operací v určitém pořadí a může trvat odložení zpracování některých přijatých zpráv až do dokončení předepsané předchozí práce, která je informována jinými zprávami.

Jednoduchým ilustrativním příkladem je pořadí zpracování objednávky, ve kterém se v systému zobrazí oznámení o platbě od externího zprostředkovatele plateb, než se z obchodu dokončí výšení odpovídajícího nákupního Objednávky do systému plnění. V takovém případě může systém plnění odložit zpracování oznámení o platbě, dokud není objednávka, ke které se má přidružit. Ve scénářích Rendezvous, kde zprávy z různých zdrojů doplňují pracovní postup, může být v reálném čase pořadí spouštění skutečně správné, ale zprávy odrážející výsledky mohou být doručeny mimo pořadí.

Časově rozlišené prostředky v konečném uspořádání zpráv z pořadí doručení do pořadí, ve kterém je lze zpracovat, ale tyto zprávy bez obav ponechají v úložišti zpráv, pro které je nutné zpracování odložit.

## <a name="message-deferral-apis"></a>Rozhraní API pro odložení zpráv

Rozhraní API je [BrokeredMessage. odklad](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) nebo [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) v klientovi .NET Framework, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) v klientovi .NET Standard a [IMessageReceiver. pozdržet](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) nebo [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) v klientovi Java. 

Odložené zprávy zůstávají v hlavní frontě společně se všemi ostatními aktivními zprávami (na rozdíl od zpráv s nedoručenými zprávami, které jsou v podfrontě uložené), ale už se nedají přijímat pomocí běžných funkcí Receive/metody ReceiveAsync. Odložené zprávy lze zjistit pomocí [procházení zpráv](message-browsing.md) , pokud aplikace ztratí jejich sledování.

Chcete-li načíst odloženou zprávu, její vlastník je zodpovědný za zapamatování [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) při jejich odložení. Každý příjemce, který zná pořadové číslo odložené zprávy, může později obdržet zprávu explicitně pomocí `Receive(sequenceNumber)`.

Pokud zprávu nelze zpracovat, protože konkrétní prostředek pro zpracování této zprávy je dočasně nedostupný, ale zpracování zprávy by se po několik minut pozastavilo, zapamatujte si **SequenceNumber** v [naplánované zprávě](message-sequencing.md) , aby se publikovala během několika minut, a znovu načíst odloženou zprávu při doručení naplánované zprávy. Pokud obslužná rutina zprávy závisí na databázi pro všechny operace a tato databáze je dočasně nedostupná, neměli byste používat odložení, ale místo toho bude možné zprávy přijímat úplně, dokud nebude znovu dostupná databáze.


## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
