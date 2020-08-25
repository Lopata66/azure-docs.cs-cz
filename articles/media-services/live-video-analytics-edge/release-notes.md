---
title: Live video Analytics na IoT Edge – poznámky k verzi – Azure
description: Toto téma obsahuje poznámky k verzi Live video Analytics o IoT Edge vydáních, vylepšeních, opravách chyb a známých problémech.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: bad21297c5174c107a8eb03da0e2ed57ee7355d3
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750219"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Poznámky k verzi pro Live video Analytics na IoT Edge

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` do čtečky kanálů RSS.

V tomto článku najdete informace o:

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="august-19-2020"></a>19. srpna 2020

Tato značka vydání pro aktualizaci modulu v 2020. srpna je:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 1 (Live-video-Analytics: 1). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.

## <a name="new-features"></a>Nové funkce 

* Nyní můžete dosáhnout vysokého výkonu přenosu dat mezi živým analýzou videa na IoT Edge a vlastním rozšířením pomocí rozhraní gRPC Framework. Začněte [tím](analyze-live-video-use-your-grpc-model-quickstart.md) , že se zobrazí.
* Širší regionální nasazení živé analýzy videí a aktualizace jenom cloudové služby.  
* Live video Analytics je teď k dispozici v 25 dalších oblastech po celém světě. Tady je [seznam](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) všech oblastí, které jsou k dispozici.  
* [Nastavení](https://aka.ms/lva-edge/setup-resources-for-samples) pro rychlé spuštění se aktualizovalo i v případě podpory nových oblastí.
    * Neexistuje žádné volání akce pro kohokoli, kdo již nastavil prostředky.

## <a name="bug-fixes"></a>Opravy chyb 

* Odebrání použití zastaralého rozšíření Azure ve skriptu pro nastavení

## <a name="july-13-2020"></a>13. července 2020

Tato značka vydání v červenci 2020 aktualizace modulu je:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> V rychlých startech a kurzech manifesty nasazení používají značku 1 (Live-video-Analytics: 1). Takže jednoduše znovu nasadíte takové manifesty, které by měly aktualizovat modul na hraničních > zařízeních.

### <a name="new-features"></a>Nové funkce
* Nyní můžete vytvářet topologie grafů, které mají uzel jímky assetu a také uzel jímky souborů podřízeného uzlu procesoru brány signálu. Příklad najdete v [tomto](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) příkladu.

### <a name="bug-fixes"></a>Opravy chyb
* Vylepšení ověřování požadovaných vlastností

## <a name="june-1-2020"></a>1\. června 2020

Tato verze je první verze Public Preview pro Live video Analytics na IoT Edge. Značka verze je

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Podporované funkce
* Analyzovat živé streamy videa pomocí modulů AI podle vašeho výběru a volitelně nahrávat video na hraničním zařízení nebo v cloudu
* Použití v operačních systémech Linux AMD64 [podporovaných](../../iot-edge/support.md) nástrojem IoT Edge
* Nasazení a konfigurace modulu prostřednictvím IoT Hub pomocí Azure Portal nebo Visual Studio Code
* Spravujte [topologie grafů a instance grafů](media-graph-concept.md#media-graph-topologies-and-instances) vzdáleně nebo místně prostřednictvím následujících přímých volání metody.

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>Další kroky

[Přehled](overview.md)
