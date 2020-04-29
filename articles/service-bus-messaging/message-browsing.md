---
title: Azure Service Bus – procházení zpráv
description: Procházení a prohlížení zpráv Service Bus umožňuje klientovi Azure Service Bus zobrazit výčet všech zpráv, které jsou umístěny ve frontě nebo předplatném.
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
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77539361"
---
# <a name="message-browsing"></a>Procházení zpráv

Procházení nebo prohlížení zpráv umožňuje klientovi Service Bus vytvořit výčet všech zpráv umístěných ve frontě nebo předplatném, obvykle pro účely diagnostiky a ladění.

Operace prohlížení vrátí všechny zprávy, které existují ve frontě nebo protokolu zpráv odběru, nejen ty, které jsou k dispozici `Receive()` pro okamžité `OnMessage()` získání nebo smyčku. `State` Vlastnost každé zprávy obsahuje informace o tom, zda je zpráva aktivní (k dispozici pro příjem), [odložena](message-deferral.md)nebo [naplánována](message-sequencing.md).

Spotřebované a vydané zprávy s vypršenou platností jsou vyčištěny asynchronním "uvolňováním paměti" a nemusí nutně přesně vypršet, pokud vyprší platnost zpráv, a proto `Peek` mohou vracet zprávy, jejichž platnost již vypršela, a v případě, že dojde k dalšímu vyvolání operace Receive v rámci fronty nebo předplatného.

To je obzvláště důležité při pokusu o obnovení odložených zpráv z fronty. Zpráva, pro kterou se předala [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) Instant, už nemá nárok na pravidelné načítání jakýmkoli jiným způsobem, a to ani v případě, že je vrátí náhled. Vrácení těchto zpráv je záměrné, protože náhled je nástroj pro diagnostiku, který odráží aktuální stav protokolu.

Prohlížení také vrátí zprávy, které byly uzamčeny a aktuálně jsou zpracovávány jinými příjemci, ale ještě nebyly dokončeny. Vzhledem k tomu, že funkce náhledu vrátí odpojený snímek, stav zámku zprávy nelze pozorovat při prohlížení zpráv a vlastnosti [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) vyvolávají chybu [InvalidOperationException](/dotnet/api/system.invalidoperationexception) , když se aplikace pokusí o jejich čtení.

## <a name="peek-apis"></a>Náhled rozhraní API

Metody [prohlížet/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existují ve všech klientských knihovnách .NET a Java a na všech objektech přijímače: **MessageReceiver**, **MessageSession**. Náhled funguje ve všech frontách a předplatných a jejich příslušných frontách nedoručených zpráv.

Při opakovaném volání bude metoda náhled vyčíslit všechny zprávy, které existují v protokolu front nebo odběrů, v pořadí pořadového čísla od nejnižších pořadových čísel až po nejvyšší. Toto je pořadí, ve kterém byly zprávy zařazeny do fronty, a nikoli pořadí, ve kterém mohou být zprávy nakonec načteny.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) načte více zpráv a vrátí je jako výčet. Pokud nejsou k dispozici žádné zprávy, je objekt výčtu prázdný, nikoli null.

Můžete také naplnit přetížení metody pomocí [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , na kterém se má spustit, a potom volat přetížení metody bez parametrů k dalšímu výčtu. Funkce **PeekBatch** jsou ekvivalentní, ale načítá sadu zpráv najednou.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
