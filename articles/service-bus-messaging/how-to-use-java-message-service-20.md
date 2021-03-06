---
title: Použití rozhraní Java Message Service 2,0 API s Azure Service Bus Premium
description: Jak používat službu JMS (Java Message Service) s Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801415"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>Použití rozhraní Java Message Service 2,0 API s Azure Service Bus Premium (Preview)

Tento článek vysvětluje, jak používat oblíbená rozhraní **JMS (Java Message Service) 2,0** k interakci s Azure Service BUSM prostřednictvím protokolu AMQP 1,0 (Advanced Message Queue Protocol).

> [!NOTE]
> Podpora rozhraní JMS (Java Message Service) 2,0 API je dostupná jenom na **úrovni Premium Azure Service Bus** a je momentálně ve **verzi Preview**.
>

## <a name="get-started-with-service-bus"></a>Začínáme se službou Service Bus

V tomto průvodci se předpokládá, že už máte Service Bus obor názvů. Pokud to neuděláte, můžete [vytvořit obor názvů a frontu](service-bus-create-namespace-portal.md) pomocí [Azure Portal](https://portal.azure.com). 

Další informace o tom, jak vytvořit Service Bus obory názvů a fronty, najdete v tématu Začínáme [s Service Bus fronty prostřednictvím Azure Portal](service-bus-quickstart-portal.md).

## <a name="what-jms-features-are-supported"></a>Jaké funkce JMS jsou podporovány?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Stažení klientské knihovny JMS (Java Message Service)

Aby bylo možné využívat všechny funkce, které jsou k dispozici na úrovni Azure Service Bus Premium, je nutné přidat níže uvedenou knihovnu do cesty sestavení projektu.

[Azure – ServiceBus – JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Pokud chcete do cesty sestavení přidat [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) , využijte preferovaný nástroj pro správu závislostí pro váš projekt, jako je například [Maven](https://maven.apache.org/) nebo [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Kódování aplikací Java

Po naimportování závislostí můžou být aplikace Java napsány nezávislá způsobem poskytovatele JMS.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Připojení k Azure Service Bus pomocí JMS

Pokud se chcete připojit k Azure Service Bus pomocí klientů JMS, potřebujete **připojovací řetězec** , který je dostupný v zásadách sdíleného přístupu v [Azure Portal](https://portal.azure.com) pod **primárním připojovacím řetězcem**.

1. Vytvoření instance `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Vytvořte instanci `ServiceBusJmsConnectionFactory` s příslušným `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Použijte `ConnectionFactory` k vytvoření `Connection` a pak `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    nebo `JMSContext` (pro klienty JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### <a name="write-the-jms-application"></a>Zápis aplikace JMS

Po `Session` `JMSContext` vytvoření instance nebo může vaše aplikace používat známá rozhraní JMS API k provádění operací správy a dat.

Informace o tom, která rozhraní API se v této verzi Preview podporují, najdete v seznamu [podporovaných funkcí JMS](how-to-use-java-message-service-20.md#what-jms-features-are-supported) .

Níže jsou uvedeny některé ukázkové fragmenty kódu, které vám pomohou začít s JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Odesílání zpráv do fronty a tématu

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Příjem zpráv z fronty

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Příjem zpráv ze sdíleného trvalého předplatného v tématu

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Shrnutí

Tato příručka předvádí, jak mohou klientské aplikace Java využívající JMS (Java Message Service) prostřednictvím AMQP 1,0 pracovat s Azure Service Bus.

Service Bus AMQP 1,0 můžete také použít z jiných jazyků, včetně .NET, C, Pythonu a PHP. Komponenty sestavené pomocí těchto různých jazyků můžou spolehlivě vyměňovat zprávy a s plnou věrností pomocí podpory AMQP 1,0 v Service Bus.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Service Bus a podrobnostech o entitách JMS (Java Message Service) najdete na odkazech níže. 
* [Service Bus – fronty, témata a předplatná](service-bus-queues-topics-subscriptions.md)
* [Service Bus – entity služby zprávy Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Podpora AMQP 1,0 v Azure Service Bus](service-bus-amqp-overview.md)
* [Příručka pro vývojáře Service Bus AMQP 1,0](service-bus-amqp-dotnet.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Rozhraní API služby zpráv Java (externí doc Oracle)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Naučte se migrovat z ActiveMQ na Service Bus](migrate-jms-activemq-to-servicebus.md)
