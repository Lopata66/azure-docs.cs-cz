---
title: Nejčastější dotazy ke službě Azure peering
description: Přečtěte si o nejčastějších dotazech služby Microsoft Azure peering
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872047"
---
# <a name="peering-service-faq"></a>Nejčastější dotazy ke službě peering Service

Tento článek vysvětluje Nejčastější dotazy týkající se připojení služby partnerského vztahu Azure.


**Koho se dotazují na cílového zákazníka?**

A. Cíloví zákazníci jsou podniky, které se připojují ke cloudu Microsoftu přes Internet jako přenos.

**Otázka. můžou si zákazníci zaregistrovat službu peering Service s více zprostředkovateli?** 

A. Ano, zákazníci se můžou zaregistrovat ke službě partnerského vztahu s více zprostředkovateli ve stejné oblasti nebo v různých oblastech, ale ne se stejnou předponou.

**Otázka. můžou zákazníci vybrat jedinečného poskytovatele internetových služeb pro své weby na geografickou oblast?**

A. Ano, zákazníci to můžou udělat. Vyberte partnerského poskytovatele internetových služeb podle oblastí, které vyhovují vašim obchodním a provozním potřebám.

**Otázka: co je PoP Microsoft Edge?**

A. Jedná se o fyzické místo, kde se Microsoft vzájemně připojuje k ostatním sítím. V umístění PoP Microsoft Edge se hostují služby, jako jsou například přední dveře Azure a Azure CDN. Další informace najdete v tématu [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features).

## <a name="peering-service-unique-characteristics"></a>Služba partnerského vztahu: jedinečné charakteristiky

**Dotaz. jak se služba partnerského vztahu liší od normálního přístupu k Internetu?**

A. Partneři, kteří zaregistrovali službu partnerského vztahu Microsoftu, spolupracují s Microsoftem a nabízejí optimalizované směrování a spolehlivé připojení ke službám Microsoftu.  

**Dotaz: jak se služba partnerského vztahu liší od ExpressRoute?**

A. Azure ExpressRoute je privátní vyhrazené připojení z jednoho nebo více zákaznických umístění. I když služba peering Service nabízí optimalizované veřejné připojení a nepodporuje žádné privátní připojení, nabízí taky optimalizované připojení pro místní Internet semináře.

## <a name="next-steps"></a>Další kroky

- Další informace o službě peering Service najdete v tématu [Přehled služby peering Service](about.md).
- Chcete-li najít poskytovatele služeb, přečtěte si téma [partneři a umístění služby partnerského vztahu](location-partners.md).
- Pokud chcete připojit připojení ke službě peering Service, přečtěte si téma připojování [služby peering](onboarding-model.md)Service.
- Pokud chcete zaregistrovat připojení ke službě peering Service, přečtěte si téma [registrace připojení ke službě peering Service – Azure Portal](azure-portal.md).
- Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).