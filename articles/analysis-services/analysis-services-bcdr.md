---
title: Vysoká dostupnost služby Azure Analysis Services | Dokumentace Microsoftu
description: Zajišťuje vysokou dostupnost služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 657800c6f96560c68e690ccbd1dfb166c5034812
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430132"
---
# <a name="analysis-services-high-availability"></a>Vysoká dostupnost služby analýzy
Tento článek popisuje, je ujištěním, vysoká dostupnost pro servery služby Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Zajišťuje vysokou dostupnost během přerušení služby
Když taková situace vzácná, datové centrum Azure může dojít k výpadku. Pokud dojde k výpadku, dojde k narušení provozu, které může trvat několik minut nebo hodin trvat. Vysoké dostupnosti se dosahuje nejčastěji s redundancí serveru. Pomocí služby Azure Analysis Services můžete dosáhnout redundance tím, že vytvoříte další, sekundární servery v jedné nebo několika oblastech. Při vytváření redundantních serverech, aby bylo zaručeno dat a metadat na těchto serverech je v synchronizaci se serverem v oblasti, který přešel do stavu offline, můžete:

* Nasazování modelů na redundantních serverech v jiných oblastech. Tato metoda vyžaduje, zpracování dat na primárním serveru i redundantních serverech v paralelní, je ujištěním, všechny servery jsou synchronizovaná.

* [Zálohování](analysis-services-backup.md) databází z primárního serveru a obnovení na redundantních serverech. Například můžete automatizovat nočních záloh do služby Azure storage a obnovit na jiné servery redundantní v jiných oblastech. 

V obou případech se primární server dojde k výpadku, musíte změnit připojovací řetězce v generování sestav klienty pro připojení k serveru v jiného místního datového centra. Tato změna by měl být poslední možnost a dochází pouze v případě výpadku katastrofickými místního datového centra. Je větší pravděpodobnost výpadku datového centra hostujícího vaše primární server by návratu do režimu online předtím, než může aktualizovat připojení ve všech klientech. 

Aby se nemusela změnit připojovací řetězce na klienti oznamující, vytvoříte server [alias](analysis-services-server-alias.md) pro primární server. Pokud primární server přestane fungovat, můžete změnit aliasu, aby odkazoval na záložní server v jiné oblasti. Můžete automatizovat alias pro název serveru kódu pro kontrolu stavu koncových bodů na primárním serveru. Pokud se nezdaří Kontrola stavu stejný koncový bod může směrovat na záložní server v jiné oblasti. 

## <a name="related-information"></a>Související informace
[Zálohování a obnovení](analysis-services-backup.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md)   
[Názvy serveru aliasů](analysis-services-server-alias.md) 

