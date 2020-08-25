---
title: Live video Analytics o kvótách IoT Edge – Azure
description: Tento článek popisuje Live video Analytics o IoT Edge kvót a omezeních.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 32a24079b36655bfdacd25b07d419009f5012507
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750220"
---
# <a name="quotas-and-limitations"></a>Kvóty a omezení

V tomto článku se vyčíslují kvóty a omezení pro živé analýzy videí v modulu IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Maximální doba odpojeného použití

Modul Edge může tolerovat dočasnou ztrátu připojení k síti. Pokud se modul zůstane odpojený déle než 36 hodin, deaktivuje všechny spuštěné instance grafů a zablokuje se i další Přímá volání metody.

Chcete-li obnovit činnost modulu Edge do provozního stavu, bude nutné obnovit připojení k síti a modul musí být schopen úspěšně komunikovat s účtem Azure Media Service.

## <a name="maximum-number-of-graph-instances"></a>Maximální počet instancí grafu

Můžete mít maximálně 1000 instancí grafu na jeden modul (vytvořené prostřednictvím GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Maximální počet topologií grafu

Můžete mít maximálně 50 topologií grafu na jeden modul (vytvořené prostřednictvím GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Omezení pro topologie grafu ve verzi Preview

V rámci verze Preview existují omezení různých uzlů, která se dají vzájemně propojit v topologii mediálních grafů.

* Zdroj RTSP
   * Pro každou topologii grafu je povolený jenom jeden zdroj RTSP.
* Procesor filtru snímkové rychlosti
   * Musí být okamžitě podřízená se zdrojem RTSP nebo procesorem detekce pohybu.
   * Nelze použít pro procesor rozšíření HTTP nebo gRPC.
   * Z procesoru detekce pohybu nelze vytvořit nadřazený datový proud.
* Procesor rozšíření HTTP
   * U každé topologie grafu může existovat maximálně jeden takový procesor.
* procesor rozšíření gRPC
   * U každé topologie grafu může existovat maximálně jeden takový procesor.
* Procesor detekce pohybu
   * Musí být okamžitě podřízené zdroji RTSP.
   * U každé topologie grafu může existovat maximálně jeden takový procesor.
   * Nelze použít pro procesor rozšíření HTTP nebo gRPC.
* Procesor brány signálu
   * Musí být okamžitě podřízené zdroji RTSP.
* Jímka assetu 
   * Musí být okamžitě podřízená se zdrojem nebo procesorem brány signálu pro RTSP.
* Jímka souborů
   * Musí být okamžitě podřízená z procesoru brány signálu.
   * Nemůže být okamžitě podřízená procesoru rozšíření HTTP nebo gRPC nebo procesoru pro detekci pohybu.
* IoT Hub jímka
   * Nemůže být okamžitě podřízená IoT Hub zdroji.

Pokud jsou používány uzly detekce pohybu i procesorové míry filtru, měly by být ve stejném řetězu uzlů, který je vedoucí k uzlu zdroje RTSP.

## <a name="limitations-on-media-service-operations-at-preview"></a>Omezení operací Media Service ve verzi Preview

V době vydání verze Preview nepodporuje Live video Analytics na IoT Edge následující:

* Možnost migrovat účet mediální služby z jednoho předplatného na jiný bez přerušení.
* Možnost použít více než jeden účet úložiště s účtem služby Media Service.
* Možnost dynamického nastavení informací o instančních objektech v požadovaných vlastnostech modulu bez restartování.

## <a name="next-steps"></a>Další kroky

[Přehled](overview.md)
