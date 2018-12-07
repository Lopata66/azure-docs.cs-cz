---
title: Škálování ASP.NET Core SignalR pomocí Azure SignalR | Microsoft Docs
description: Přehled používání služby Azure SignalR ke škálování aplikací ASP.NET Core SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 1492b2145187f7334d1e7d9df91adc109ca826ee
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016925"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Škálování aplikací ASP.NET Core SignalR pomocí služby Azure SignalR

## <a name="developing-signalr-apps"></a>Vývoj aplikací SignalR

V současnosti existují [dvě verze](https://docs.microsoft.com/aspnet/core/signalr/version-differences) SignalR, které můžete ve webových aplikacích použít: SignalR pro ASP.NET a novější verze SignalR pro ASP.NET Core. Služba Azure SignalR je služba spravovaná v Azure a založená na SignalR pro ASP.NET Core. 

SignalR pro ASP.NET Core je přepsaná předchozí verze. To znamená, že SignalR pro ASP.NET Core není zpětně kompatibilní se starší verzí SignalR. Rozhraní API a chování se liší. Sada SDK služby SignalR pro ASP.NET Core cílí na .NET Standard, takže ji stále můžete používat s rozhraním .NET Framework. Místo starých rozhraní API však musíte použít nová rozhraní API. Pokud používáte SignalR a chcete přejít na SignalR pro ASP.NET Core neboli službu Azure SignalR, budete muset změnit svůj kód s ohledem na rozdíly v rozhraních API.

V případě služby Azure SignalR je serverová komponenta SignalR pro ASP.NET Core hostovaná v Azure. Vzhledem k tomu, že tato technologie využívá ASP.NET Core, však budete moct spouštět vlastní webové aplikace na několika platformách (Windows, Linux a MacOS), zatímco k hostování můžete použít [Azure App Service](../app-service/app-service-web-overview.md), [službu IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [NGINX](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) nebo [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Ve vlastním procesu můžete využít také samoobslužné hostování.

Pokud mezi cíle vaší aplikace patří podpora nejnovějších funkcí pro aktualizace obsahu webových klientů v reálném čase, provoz na několika platformách (Azure, Windows, Linux a macOS) a hostování v různých prostředích, může být nejlepší volbou využít službu Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Proč nepoužít vlastní nasazení SignalR?

Stále můžete nasadit vlastní webovou aplikaci podporující SignalR pro ASP.NET Core jako back-endovou součást celkové webové aplikace.

Jedním z hlavních důvodů, proč použít službu Azure SignalR, je jednoduchost. Díky službě Azure SignalR nemusíte řešit problémy s výkonem, škálovatelností ani dostupností. Tyto problémy za vás řeší smlouva o úrovni služeb (SLA) zajišťující 99,9% dostupnost.

Kromě toho se pro zajištění podpory aktualizací obsahu v reálném čase obvykle upřednostňují protokoly WebSocket. Při škálování se však může z vyrovnávání zatížení velkého počtu trvalých připojení WebSocket stát složitý problém, který budete muset řešit. Běžná řešení využívají vyrovnávání zatížení DNS, hardwarové nástroje pro vyrovnávání zatížení a softwarové vyrovnávání zatížení. Služba Azure SignalR řeší tento problém za vás.

Dalším důvodem může být, že webovou aplikaci vlastně vůbec nepotřebujete hostovat. Logika vaší webové aplikace může využívat [architekturu bez serverů](https://azure.microsoft.com/overview/serverless-computing/). Váš kód například může být hostovaný a spouštěný pouze na vyžádání pomocí triggerů [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Tento scénář může být složitý, protože se váš kód spouští pouze na vyžádání a neudržuje dlouhé spojení s klienty. Služba Azure SignalR dokáže tuto situaci řešit díky tomu, že už za vás spravuje připojení. Podrobnosti najdete v [přehledu, jak používat službu SignalR s Azure Functions](signalr-overview-azure-functions.md). 

## <a name="how-does-it-scale"></a>Jak se škáluje?

Je běžné škálování SignalR s SQL serverem, Azure Service Bus nebo mezipaměti Azure Redis. Služba Azure SignalR se stará o postup škálování za vás. Výkon a náklady jsou srovnatelné s těmito postupy, ale bez složité správy těchto dalších služeb. Stačí pouze aktualizovat počet jednotek pro vaši službu. Každá jednotka podporuje až 1000 klientských připojení.

## <a name="next-steps"></a>Další postup
* [Rychlý start: Vytvoření chatovací místnosti s využitím služby Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

