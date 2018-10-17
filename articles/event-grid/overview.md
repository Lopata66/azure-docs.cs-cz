---
title: Přehled Azure Event Gridu
description: Popisuje Azure Event Grid a jeho koncepty.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 08/17/2018
ms.author: babanisa
ms.openlocfilehash: fbe9b79cd407f74686d572aa1e5c7ac1d837cd25
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223404"
---
# <a name="an-introduction-to-azure-event-grid"></a>Úvod k Azure Event Gridu

Azure Event Grid umožňuje snadno sestavovat aplikace pomocí architektur založených na událostech. Nejprve vyberte prostředek Azure, k jehož odběru se chcete přihlásit, a pak zadejte obslužnou rutinu události nebo koncový bod webhooku, kam chcete událost odeslat. Event Grid má integrovanou podporu událostí pocházejících ze služeb Azure, jako jsou služby Storage Blob a skupiny prostředků. Event Grid podporuje také vlastní události s využitím vlastních témat. 

Pomocí filtrů můžete směrovat konkrétní události do různých koncových bodů, zahájit vícesměrové vysílání do více koncových bodů a ujistit se o spolehlivém doručení událostí.

V současné době je Azure Event Grid k dispozici ve všech veřejných oblastech. Zatím není k dispozici v cloudech Azure Germany, Azure China ani Azure Government.

Tento článek obsahuje přehled Azure Event Gridu. Pokud chcete začít pracovat s Event Gridem, přečtěte si článek [Vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md). 

![Funkční model služby Event Grid](./media/overview/functional-model.png)

Poznámka: Tento obrázek ukazuje, jak Event Grid propojuje zdroje a obslužné rutiny, ale neuvádí úplný seznam podporovaných integrací.

## <a name="event-sources"></a>Zdroje událostí

Úplné podrobnosti o možnostech jednotlivých zdrojů a související články najdete v tématu věnovaném [zdrojům událostí](event-sources.md). V současné době podporují odesílání událostí do Event Gridu následující služby Azure:

* Předplatná Azure (operace správy)
* Container Registry
* Vlastní témata
* Event Hubs
* IoT Hub
* Media Services
* Skupiny prostředků (operace správy)
* Service Bus
* Storage Blob
* Storage univerzální v2 (GPv2)

## <a name="event-handlers"></a>Obslužné rutiny událostí

Úplné podrobnosti o možnostech jednotlivých obslužných rutin a související články najdete v tématu věnovaném [obslužným rutinám událostí](event-handlers.md). V současné době podporují zpracování událostí z Event Gridu následující služby Azure: 

* Azure Automation
* Azure Functions
* Event Hubs
* Hybridní připojení
* Logic Apps
* Microsoft Flow
* Queue Storage
* Webhooky

## <a name="concepts"></a>Koncepty

Azure Event Grid obsahuje pět konceptů, které vám pomůžou rychle začít s prací:

* **Události** – to, co se stalo.
* **Zdroje událostí** – kde k události došlo.
* **Témata** – koncový bod, kam vydavatelé odesílají události.
* **Odběry událostí** – koncový bod nebo integrovaný mechanismus pro směrování událostí, někdy i do více obslužných rutin. Pomocí odběrů taky obslužné rutiny inteligentně filtrují příchozí události.
* **Obslužné rutiny událostí** – aplikace nebo služba reagující na danou událost.

Další informace o těchto konceptech najdete v článku [Koncepty ve službě Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Možnosti

Toto jsou některé klíčové vlastnosti služby Azure Event Grid:

* **Jednoduchost** – kliknutím můžete nasměrovat události prostředku Azure do kterékoli obslužné rutiny události nebo koncového bodu.
* **Rozšířené filtrování** – můžete filtrovat podle typu události nebo cesty publikování události a zajistit tak, aby obslužné rutiny událostí dostávaly jenom relevantní události.
* **Větvení** – k odběru stejné události můžete přihlásit více koncových bodů a rozesílat kopie události do libovolného počtu umístění.
* **Spolehlivost** – využijte možnost zopakování po 24 hodinách s exponenciálním opakováním, která zvyšuje spolehlivost doručování událostí.
* **Platby za události** – plaťte jenom za to, do jaké míry Event Grid využíváte.
* **Vysoká propustnost** – sestavujte ve službě Event Grid úlohy s vysokým objemem díky podpoře milionů událostí za sekundu.
* **Integrované události** – integrované události s definovanými prostředky umožňují rychle zahájit práci.
* **Vlastní události** – využijte Event Grid ke směrování, filtrování a spolehlivému doručování vlastních událostí ve vaší aplikaci.

Porovnání služeb Event Grid, Event Hubs a Service Bus najdete v článku [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Co můžu dělat se službou Event Grid?

Azure Event Grid nabízí několik funkcí, které výrazně zlepšují provoz bez serverů, automatizaci operací a integraci: 

### <a name="serverless-application-architectures"></a>Architektury aplikací bez serveru

![Aplikace bez serveru](./media/overview/serverless_web_app.png)

Event Grid propojuje zdroje dat a obslužné rutiny událostí. Pomocí služby Event Grid třeba můžete pokaždé, když se do kontejneru úložiště objektů blob přidá nová fotka, okamžitě aktivovat funkci bez serveru, která spustí analýzu obrázku. 

### <a name="ops-automation"></a>Automatizace operací

![Automatizace operací](./media/overview/Ops_automation.png)

Event Grid umožňuje urychlit automatizaci a zjednodušit vynucování zásad. Event Grid může například upozornit službu Azure Automation při vytvoření virtuálního počítače nebo aktivaci služby SQL Database. Tyto události můžou sloužit k automatické kontrole odpovídajících konfigurací služeb, vkládání metadat do provozních nástrojů, označování virtuálních počítačů nebo zakládání pracovních položek.

### <a name="application-integration"></a>Integrace aplikací

![Integrace aplikací](./media/overview/app_integration.png)

Event Grid propojuje vaši aplikaci s dalšími službami. Můžete třeba vytvořit vlastní téma pro odesílání dat událostí aplikace do služby Event Grid a využít tak její spolehlivé doručování, pokročilé směrování a přímou integraci s Azure. Nebo můžete pomocí služeb Event Grid a Logic Apps zpracovávat data kdekoli bez psaní kódu. 

## <a name="how-much-does-event-grid-cost"></a>Kolik stojí Event Grid?

Azure Event Grid využívá model plateb za události, takže platíte jenom za to, co využijete. Prvních 100 000 operací za měsíc je zdarma. Operace jsou definované jako příchozí události, pokusy o doručení, volání správy a filtrování podle přípony předmětu. Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Další kroky

* [Směrování událostí objektů blob úložiště](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Pomocí služby Event Grid můžete reagovat na události objektů blob úložiště.
* [Vytváření vlastních událostí a přihlášení k jejich odběru](custom-event-quickstart.md)  
  Pusťte se rovnou do práce a pomocí rychlého startu pro Azure Event Grid začněte odesílat vlastní události do libovolného koncového bodu.
* [Použití Logic Apps jako obslužné rutiny událostí](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Kurz týkající se sestavení aplikace pomocí Logic Apps, která bude reagovat na události sdílené službou Event Grid.
* [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)  
  Kurz, ve kterém se služba Azure Functions používá ke streamování dat ze služby Event Hubs do SQL Data Warehouse.
* [Referenční informace k rozhraní REST API služby Event Grid](/rest/api/eventgrid)  
  Poskytuje další technické informace o službě Azure Event Grid a referenční informace o správě odběrů událostí, směrování a filtrování.
