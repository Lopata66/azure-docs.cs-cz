---
title: Počet zpráv Azure Service Bus | Dokumentace Microsoftu
description: Načtěte počet zpráv Azure Service Bus.
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
ms.openlocfilehash: adfd8c5849cfee69805715378a3f56ec9f685b00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050743"
---
# <a name="message-counters"></a>Čítače zpráv

Počet zpráv držených ve front a odběrů pomocí Azure Resource Manageru a služby Service Bus může načíst [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) rozhraní API v rozhraní .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pomocí Powershellu můžete získat počet následujícím způsobem:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Podrobnosti o počtu zpráv

Znalost, počet aktivních zpráv jsou užitečné při určování, zda fronta vytvoří nevyřízenou položku, která vyžaduje více prostředků ke zpracování, než co aktuálně nasazené. Následující podrobnosti o čítači jsou k dispozici v [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) třídy:

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): Zprávy ve frontě nebo odběru, které jsou v aktivním stavu a je připravený k doručení.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Zprávy ve frontě nedoručených zpráv.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Zprávy v naplánované stavu.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): Zprávy, které se nepodařilo přenos do další fronty nebo tématu a byla přesunuta do fronty nedoručených zpráv, přenosu.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): Zprávy čekající na vyřízení přenos do další fronty nebo tématu.

Pokud aplikace chce škálovat prostředky na základě délky fronty, je to měl dělat implementovaný s měřené tempu. Získání čítače zpráv je náročná operace uvnitř zprostředkovatele zpráv a provádění se často přímo a nepříznivě ovlivňuje výkon entity.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
