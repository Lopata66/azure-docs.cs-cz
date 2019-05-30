---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238275"
---
| Resource | Free | Sdílené | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webové, mobilní telefon nebo API apps](https://azure.microsoft.com/services/app-service/) za [plán služby App Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Neomezený počet<sup>2</sup> |Neomezený počet<sup>2</sup> |Neomezený počet<sup>2</sup> |Neomezený počet<sup>2</sup>|
| [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) |10 na oblast |10 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků|
| Výpočetní instance typu |Sdílené |Sdílené |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup></p> |Vyhrazené<sup>3</sup>|
| [Horizontální navýšení kapacity](../articles/app-service/web-sites-scale.md) (maximální počet instancí) |sdílené 1 |sdílené 1 |3 vyhrazené<sup>3</sup> |10 vyhrazené<sup>3</sup> |20 vyhrazených<sup>3</sup>|100 vyhrazených<sup>4</sup>|
| Úložiště<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Čas procesoru (5 minut)<sup>6</sup> |3 minuty |3 minuty |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Čas procesoru (den)<sup>6</sup> |60 minut |240 minut |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Paměť (1 hodina) |1 024 MB za plán služby App Service |1 024 MB na aplikaci |neuvedeno |neuvedeno |neuvedeno |neuvedeno |
| Šířka pásma |165 MB |Neomezený počet, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |Neomezený počet, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |Neomezený počet, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |Neomezený počet, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |Neomezený počet, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |
| Architektura aplikace |32-bit |32-bit |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |
| Webové sokety každou instanci<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| Souběžné [připojení ladicího programu](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikaci. |1 |1 |1 |5 |5 |5 |
| Certifikáty App Service na jedno předplatné<sup>10</sup>| Nepodporuje se | Nepodporuje se |10 |10 |10 |10 |
| Vlastní domény na aplikaci</a> |0 (azurewebsites.net subdomain only)|500 |500 |500 |500 |500 |
| Vlastní domény [podpora protokolu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Není podporováno, pro certifikát se zástupným znakem *. azurewebsites.net k dispozici ve výchozím nastavení|Není podporováno, pro certifikát se zástupným znakem *. azurewebsites.net k dispozici ve výchozím nastavení|Neomezené možnosti připojení SNI SSL |Neomezený počet SNI SSL a 1 IP SSL. připojení zahrnuté |Neomezený počet SNI SSL a 1 IP SSL. připojení zahrnuté | Neomezený počet SNI SSL a 1 IP SSL. připojení zahrnuté|
| Integrovaný nástroj load balancer | |X |X |X |X |X<sup>9</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Naplánované zálohování](../articles/app-service/manage-backup.md) | | | | Naplánované zálohy každé dvě hodiny, maximálně 12 zálohy za den (ruční a plánovaná) | Naplánované zálohování každou hodinu, maximálně 50 zálohy za den (ruční a plánovaná) | Naplánované zálohování každou hodinu, maximálně 50 zálohy za den (ruční a plánovaná) |
| [Automatické škálování](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) podpory | |X |X |X |X |X |
| [Monitorování koncových bodů](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [pracovní sloty](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,9 % |99,95 %|99,95 %|99,95 %|  

<sup>1</sup>aplikací a kvót úložiště jsou za plán služby App Service, pokud není uvedeno jinak.  
<sup>2</sup>skutečný počet aplikací, které můžete hostovat na těchto počítačích závisí na aktivity aplikací, velikosti instance počítačů a odpovídajících využití prostředků.  
<sup>3</sup>vyhrazené instance může být různě velká. Další informace najdete v tématu [ceny služeb App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>povoleno více aktivací na vyžádání.  
<sup>5</sup>limitu úložiště je celková velikost obsahu mezi všemi aplikacemi v rámci stejného plánu služby App Service.  
<sup>6</sup>tyto prostředky jsou omezeny fyzické prostředky na vyhrazených instancích (velikost instance a počtu instancí).  
<sup>7</sup>škálovat aplikace na úrovni Basic na dvě instance, kdy máte 350 souběžných připojení pro každou z obou instancí.  
<sup>8</sup>spouštět vlastní spustitelné soubory nebo skripty na požádání, podle plánu nebo nepřetržitě jako úlohu na pozadí v rámci vaší služby App Service instance. U nepřetržitého provádění úloh WebJobs se vyžaduje stálé připojení. Pro plánované úlohy WebJobs se vyžaduje Azure Scheduler na úrovni Free nebo Standard. Neexistuje žádné předdefinované omezení počtu webových úloh, které můžete spustit v instanci služby App Service. Existují praktické omezení, které jsou závislé na co se kód aplikace snaží udělat.  
<sup>9</sup>SKU izolované aplikace služby se budou moct být interně Vyrovnávání zatížení (ILB) pomocí Azure Load Balancer, takže není bez veřejného připojení z Internetu. Následkem toho se některé služby ILB App Service v izolovaném prostředí musí používat v počítačích, které mají přímé připojení ke koncovému bodu sítě ILB.  
<sup>10</sup>prostřednictvím žádosti o podporu do maximálního počtu 200 je možné zvýšit kvótu App Service Certificate na jedno předplatné.  