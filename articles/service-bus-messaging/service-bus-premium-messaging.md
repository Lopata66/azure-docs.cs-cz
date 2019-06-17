---
title: Přehled cenových úrovní zasílání zpráv Azure Service Bus Premium a Standard | Dokumentace Microsoftu
description: Úrovně zasílání zpráv Service Bus Premium a Standard
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: 600577ebf05a8bc89dbec35d3b3ee5162aa246e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872734"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Úrovně zasílání zpráv Service Bus Premium a Standard

Zasílání zpráv Service Bus, které zahrnuje entity jako jsou fronty a témata, v sobě kombinuje funkce pro zasílání zpráv v rámci podniku s bohatou sémantikou publikování a odběru na úrovni cloudu. Zasílání zpráv Service Bus se používá jako páteřní prvek mnoha sofistikovaných cloudových řešení.

Úroveň *Premium* zasílání zpráv Service Bus řeší běžné požadavky zákazníků z pohledu rozsahu, výkonu a dostupnosti pro klíčové aplikace. Pro produkční scénáře se doporučuje úroveň Premium. Přestože mají tyto dvě úrovně skoro stejné sady funkcí, jsou určené pro použití v odlišných situacích.

V následující tabulce je zvýrazněno několik nejvýraznějších rozdílů.

| Premium | Standard |
| --- | --- |
| Vysoká propustnost |Variabilní propustnost |
| Předvídatelný výkon |Variabilní latence |
| Pevné ceny |Variabilní průběžná cena |
| Možnost vertikálně navýšit a snížit kapacitu |neuvedeno |
| Velikost zprávy do 1 MB |Velikost zprávy do 256 kB |

**Zasílání zpráv Service Bus Premium** zajišťuje izolaci prostředků na úrovni CPU a paměti, takže každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá *jednotka zasílání zpráv*. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Můžete koupit 1, 2, 4 nebo 8 jednotek pro každý obor názvů Service Bus úrovně Premium pro zasílání zpráv. Jedna úloha nebo entita může zahrnovat několik jednotek zasílání zpráv a počet jednotek zasílání zpráv můžete změnit podle libosti. Výsledkem je předvídatelný a opakovatelný výkon vašeho řešení postaveného na Service Bus.

Vedle toho, že je tento výkon předvídatelnější, je také rychlejší. Zasílání zpráv Service Bus úrovně Premium staví na databázovém jádru představeném ve službě [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Se zasíláním zpráv na úrovni Premium je výkon ve špičce mnohem vyšší než na úrovni Standard.

## <a name="premium-messaging-technical-differences"></a>Technické rozdíly zasílání zpráv na úrovni Premium

V následujících částech je uvedeno několik rozdílů mezi úrovněmi zasílání zpráv Premium a Standard.

### <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

V Zasílání zpráv na úrovni Premium se dělené fronty a témata nepodporují. Další informace o dělení najdete v oddílu [Dělené fronty a témata](service-bus-partitioning.md).

### <a name="express-entities"></a>Expresní entity

Protože zasílání zpráv úrovně Premium běží v kompletně izolovaném prostředí, nejsou expresní entity v oborech názvů úrovně Premium podporované. Další informace o expresní funkci najdete v popisu vlastnosti [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Pokud je váš kód spuštěný v rámci zasílání zpráv úrovně Standard a chcete přejít na úroveň Premium, ověřte, že vlastnost [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) je nastavena na hodnotu **false** (výchozí hodnota).

## <a name="premium-messaging-resource-usage"></a>Využití prostředků zasílání zpráv úrovně Premium
Obecně platí všechny operace s entitou může způsobit využití procesoru a paměti. Tady jsou některé z těchto operací: 

- Operace správy, jako je například CRUD (vytvoření, načtení, Update a Delete) operace fronty, témata a odběry.
- Operace modulu runtime (odesílání a příjem zpráv)
- Monitorování operací a výstrahy

Další využití procesoru a paměti již není kromě ale ceny. Pro úroveň zasílání zpráv úrovně Premium je jeden cena za jednotku zprávy.

Využití procesoru a paměti jsou sledovány a zobrazí vám z následujících důvodů: 

- Poskytuje přehled o interní informace o systému
- Seznamte se s kapacity prostředků zakoupili.
- Který plánování kapacity vám pomůže rozhodnout na škálování směrem nahoru nebo dolů.

## <a name="get-started-with-premium-messaging"></a>Začínáme se zasíláním zpráv na úrovni Premium

Využití zasílání zpráv na úrovni Premium je jednoduché a je podobné standardnímu zasílání zpráv. Začněte [vytvořením oboru názvů](service-bus-create-namespace-portal.md) na webu [Azure Portal](https://portal.azure.com). Zkontrolujte, že jste v části **Cenová úroveň** vybrali **Premium**. Kliknutím na **Zobrazit všechny podrobnosti o cenách** zobrazíte další informace o jednotlivých úrovních.

![create-premium-namespace][create-premium-namespace]

Můžete také vytvářet [obory názvů Premium pomocí šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Další postup

Pokud se o zasílání zpráv Service Bus chcete dozvědět více, podívejte se na následující odkazy:

* [Představení zasílání zpráv Azure Service Bus úrovně Premium (příspěvek na blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Představení zasílání zpráv Azure Service Bus úrovně Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Přehled zasílání zpráv Service Bus](service-bus-messaging-overview.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
