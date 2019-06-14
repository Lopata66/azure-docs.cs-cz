---
title: AMQP 1.0 v Azure Service Bus založené na žádost odpověď operace | Dokumentace Microsoftu
description: Seznam operací založené na požadavku nebo odpovědi služby Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c22ba0b57ed1161e1f7e2082d2ba21f27b656da1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402679"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 ve službě Microsoft Azure Service Bus: operace založené na žádost odpověď

Tento článek definuje seznam operací založené na požadavku nebo odpovědi služby Microsoft Azure Service Bus. Tyto informace podle návrhu pracovních AMQP správu verze 1.0.  
  
Podrobné úroveň protokolu AMQP 1.0 protokol průvodce, která vysvětluje, jak Service Bus implementuje a je založený na technické specifikace OASIS AMQP, najdete v článku [protokolu AMQP 1.0 v příručce protokol Azure Service Bus a Event Hubs][průvodce protokolem amqp 1.0].  
  
## <a name="concepts"></a>Koncepty  
  
### <a name="entity-description"></a>Popis entity  

Popis entity odkazuje na buď služby Service Bus [QueueDescription třídy](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription třídy](/dotnet/api/microsoft.servicebus.messaging.topicdescription), nebo [SubscriptionDescription třídy](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) objektu.  
  
### <a name="brokered-message"></a>Zprostředkované zprávy  

Představuje zprávu ve službě Service Bus, která se mapuje na zprávu protokolu AMQP. Mapování je definována v [Průvodce protokolem Service Bus AMQP](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Připojit k uzlu Správa entit  

Všechny operace popsané v tomto dokumentu se řídí vzorem žádost odpověď, jsou omezená na entitu a vyžadují připojení k uzlu správy entity.  
  
### <a name="create-link-for-sending-requests"></a>Vytvoření odkazu na odesílání žádostí  

Vytvoří odkaz na uzlu Správa pro odesílání požadavků.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Vytvoření odkazu na příjem odpovědí  

Vytvoří odkaz pro přijetí odpovědi z uzlu Správa.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Přenos zprávy požadavku  

Převede zprávu požadavku.  
Stav transakce je možné volitelně doplnit pro operace, které podporuje transakce.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Zpráva odpovědi  

Přijímá zprávy s odpovědí z odkazu odpovědi.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Zprávy s odpovědí je v následujícím tvaru:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Adresa entity služby Service Bus  

Entit služby Service Bus je potřeba řešit následujícím způsobem:  
  
|Typ entity|Adresa|Příklad:|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|předplatné|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operace zpráv  
  
### <a name="message-renew-lock"></a>Obnovit uzamčení zprávy  

Rozšířením zámek zprávy za dobu určenou v popis entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
 Tělo zprávy požadavku musí obsahovat oddíl amqp hodnotu obsahující mapy s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|pole identifikátoru uuid|Ano|Tokeny uzamčení zprávy k obnovení.|  

> [!NOTE]
> Jsou tokeny Zámek `DeliveryTag` vlastnost u přijaté zprávy. Podívejte se na následující příklad v [sady .NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) načte tyto. Token, který může také zobrazit DeliveryAnnotations jako "x-opt zámek token" ale, to není zaručeno a `DeliveryTag` se bude upřednostňovat. 
> 
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo.|  
|statusDescription|string|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi musí obsahovat oddíl amqp hodnotu obsahující mapy s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|vypršení platnosti|pole časového razítka|Ano|Zpráva tokenu nové platnost zámku odpovídající tokeny žádost o zámek.|  
  
### <a name="peek-message"></a>Náhled zprávy  

Prohlížením zprávy bez blokování.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Ano|Pořadové číslo, ze kterého se má spustit náhled.|  
|`message-count`|int|Ano|Maximální počet zpráv pro náhled.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – nemá další zprávy<br /><br /> 204: Bez obsahu – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam mapování|Ano|Seznam zpráv, ve kterých každý mapy představuje zprávu.|  
  
Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|message|pole bajtů|Ano|Kódování přenosu zpráv protokolu AMQP 1.0.|  
  
### <a name="schedule-message"></a>Plán zprávy  

Naplánuje zprávy. Tato operace podporuje transakce.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam mapování|Ano|Seznam zpráv, ve kterých každý mapy představuje zprávu.|  
  
Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|id zprávy|string|Ano|`amqpMessage.Properties.MessageId` jako datový typ string|  
|id relace|string|Ne|`amqpMessage.Properties.GroupId as string`|  
|klíč oddílu|string|Ne|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|klíč prostřednictvím oddílu|string|Ne|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|pole bajtů|Ano|Kódování přenosu zpráv protokolu AMQP 1.0.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo.|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** oddíl obsahující mapy s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouho|Ano|Pořadové číslo plánované zprávy. Pořadové číslo se používá pro zrušení.|  
  
### <a name="cancel-scheduled-message"></a>Zrušit naplánovanou zprávu  

Plánované zprávy zruší.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouho|Ano|Pořadová čísla plánované zprávy pro zrušení.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo.|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** oddíl obsahující mapy s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouho|Ano|Pořadové číslo plánované zprávy. Pořadové číslo se používá pro zrušení.|  
  
## <a name="session-operations"></a>Operace relace  
  
### <a name="session-renew-lock"></a>Obnovit uzamčení relace  

Rozšířením zámek zprávy za dobu určenou v popis entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|id relace|string|Ano|ID relace.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – nemá další zprávy<br /><br /> 204: Bez obsahu – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** oddíl obsahující mapy s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|vypršení platnosti|timestamp|Ano|Nové vypršení platnosti.|  
  
### <a name="peek-session-message"></a>Operace Peek relace zpráv  

Relace zpráv prohlížením bez blokování.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|číslo od pořadí|long|Ano|Pořadové číslo, ze kterého se má spustit náhled.|  
|počet zpráv|int|Ano|Maximální počet zpráv pro náhled.|  
|id relace|string|Ano|ID relace.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – nemá další zprávy<br /><br /> 204: Bez obsahu – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** oddíl obsahující mapy s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam mapování|Ano|Seznam zpráv, ve kterých každý mapy představuje zprávu.|  
  
 Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|message|pole bajtů|Ano|Kódování přenosu zpráv protokolu AMQP 1.0.|  
  
### <a name="set-session-state"></a>Nastavit stav relace  

Nastaví stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|id relace|string|Ano|ID relace.|  
|Stav relace|pole bajtů|Ano|Neprůhledný binární data.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|statusDescription|string|Ne|Popis stavu.|  
  
### <a name="get-session-state"></a>Získat stav relace  

Získá stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|id relace|string|Ano|ID relace.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Stav relace|pole bajtů|Ano|Neprůhledný binární data.|  
  
### <a name="enumerate-sessions"></a>Výčet relací  

Vytvoří výčet relací u třídy entity pro zasílání zpráv.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|Ano|Filtrovat, aby obsahovaly pouze relace aktualizované po daném okamžiku.|  
|Přeskočit|int|Ano|Přeskočte počet relací.|  
|nahoru|int|Ano|Maximální počet relací.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – nemá další zprávy<br /><br /> 204: Bez obsahu – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Přeskočit|int|Ano|Počet přeskočených relací, pokud stavový kód 200.|  
|ID relace|pole řetězců|Ano|Pole ID, pokud je stavový kód 200 relace.|  
  
## <a name="rule-operations"></a>Operace pravidla  
  
### <a name="add-rule"></a>Přidat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Název pravidla|string|Ano|Název pravidla bez zahrnutí názvu předplatného a tématu.|  
|Popis pravidla|map|Ano|Popis pravidla uvedená v další části.|  
  
**Popis pravidla** mapování musí zahrnovat následující položky, ve kterém **filtrem sql** a **korelační filtr** se vzájemně vylučují:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Filtr SQL|map|Ano|`sql-filter`, jak je uvedeno v následující části.|  
|Korelační filtr|map|Ano|`correlation-filter`, jak je uvedeno v následující části.|  
|sql-rule-action|map|Ano|`sql-rule-action`, jak je uvedeno v následující části.|  
  
Mapa filtrem sql, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Výraz|string|Ano|Výraz filtru SQL.|  
  
**Korelační filtr** mapování musí obsahovat alespoň jeden z následujících položek:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|id korelace|string|Ne||  
|id zprávy|string|Ne||  
|na|string|Ne||  
|odpovědi|string|Ne||  
|label|string|Ne||  
|id relace|string|Ne||  
|odpověď k relaci id|string|Ne||  
|content-type|string|Ne||  
|properties|map|Ne|Mapuje se na Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
**Akce pravidla sql** mapování musí zahrnovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Výraz|string|Ano|Výraz SQL akce.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|statusDescription|string|Ne|Popis stavu.|  
  
### <a name="remove-rule"></a>Odebrat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Název pravidla|string|Ano|Název pravidla bez zahrnutí názvu předplatného a tématu.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|statusDescription|string|Ne|Popis stavu.|  
  
### <a name="get-rules"></a>Získat pravidla

#### <a name="request"></a>Žádost

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:

|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  

Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|nahoru|int|Ano|Počet pravidel pro načtení stránky.|  
|Přeskočit|int|Ano|Počet pravidel pro přeskočení. Definuje počáteční index (+ 1) na seznam pravidel. | 

#### <a name="response"></a>Odpověď

Zprávy s odpovědí obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|pravidla| pole mapy|Ano|Pole pravidla. Každé pravidlo představuje mapu.|

Každá položka mapování pole obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Popis pravidla|pole popisu objektů|Ano|`com.microsoft:rule-description:list` pomocí protokolu AMQP popsané kód 0x0000013700000004| 

`com.microsoft.rule-description:list` je pole objektů popsané. Pole zahrnuje následující položky:

|Index|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
| 0 | pole popisu objektů | Ano | `filter` jak je uvedeno níže. |
| 1 | pole popisu objektu | Ano | `ruleAction` jak je uvedeno níže. |
| 2 | string | Ano | Název pravidla. |

`filter` může být buď z následujících typů:

| Název popisovače | Popisovač kódu | Hodnota |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korelační filtr |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Skutečný filtr představující 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | False filtr představující 1 = 0 |

`com.microsoft:sql-filter:list` je popisuje pole, která zahrnuje:

|Index|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Ano | Výraz filtru SQL |

`com.microsoft:correlation-filter:list` je popisuje pole, která zahrnuje:

|Index (pokud existuje)|Typ hodnoty|Hodnota obsahu|  
|---------|----------------|--------------|
| 0 | string | ID korelace |
| 1 | string | ID zprávy |
| 2 | string | Do |
| 3 | string | Odpovědět |
| 4 | string | Štítek |
| 5 | string | ID relace |
| 6 | string | ID relace zpáteční adresy|
| 7 | string | Typ obsahu |
| 8 | Mapa | Mapa aplikace definované vlastnosti |

`ruleAction` může být jeden z následujících typů:

| Název popisovače | Popisovač kódu | Hodnota |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Prázdné pravidlo akce – akce žádná pravidla, která je k dispozici |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL Rule Action |

`com.microsoft:sql-rule-action:list` je pole objektů popsáno, jehož první položka je řetězec, který obsahuje výraz akce pravidla SQL.

## <a name="deferred-message-operations"></a>Operace odloženou zprávu  
  
### <a name="receive-by-sequence-number"></a>Získat, když pořadové číslo  

Získá odložené zprávy podle pořadového čísla.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouho|Ano|Pořadová čísla.|  
|receiver-settle-mode|ubyte|Ano|**Příjemce vyrovnání** režimu, jak je uvedeno v protokolu AMQP 1.0 core.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|statusDescription|string|Ne|Popis stavu.|  
  
Textu odpovědi se musí skládat z **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam mapování|Ano|Seznam zpráv, kde každý mapy představuje zprávu.|  
  
Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|token uzamčení|Uuid|Ano|Pokud token uzamčení `receiver-settle-mode` 1.|  
|message|pole bajtů|Ano|Kódování přenosu zpráv protokolu AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Aktualizovat stav dispozice  

Aktualizuje stav dispozice odložené zpráv. Tato operace podporuje transakce.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|Operace|string|Ano|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Ne|Operace serveru vypršel časový limit v milisekundách.|  
  
Tělo zprávy požadavku musí obsahovat **amqp hodnotu** část obsahující **mapy** s následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|Ano|Dokončeno<br /><br /> opuštění<br /><br /> Pozastaveno|  
|Lock – tokeny|pole identifikátoru uuid|Ano|Tokeny uzamčení zprávy k aktualizaci stavu dispozice.|  
|deadletter-reason|string|Ne|Může nastavit, pokud je nastaven stav dispozice **pozastaveno**.|  
|Popis nedoručených zpráv|string|Ne|Může nastavit, pokud je nastaven stav dispozice **pozastaveno**.|  
|properties-to-modify|map|Ne|Seznam služby Service Bus zprostředkovaných vlastností zprávy upravit.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Hodnota obsahu|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěšné, jinak se nezdařilo|  
|statusDescription|string|Ne|Popis stavu.|

## <a name="next-steps"></a>Další postup

Další informace o AMQP a Service Bus, najdete na následujících odkazech:

* [Přehled protokolu AMQP Service Bus]
* [Průvodce protokolem AMQP 1.0]
* [AMQP ve službě Service Bus pro systém Windows Server]

[Přehled protokolu AMQP Service Bus]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP ve službě Service Bus pro systém Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
