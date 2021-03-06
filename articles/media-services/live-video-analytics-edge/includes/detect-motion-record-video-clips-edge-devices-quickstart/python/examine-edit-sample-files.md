---
ms.openlocfilehash: 6ea9be8e7e67a8e52412e7011cfb1d33c9929191
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486707"
---
Jako součást požadavků pro tento rychlý Start jste stáhli vzorový kód do složky. Pomocí těchto kroků prověřte a upravte vzorový kód.

1. V Visual Studio Code, přejít na *Src/Edge*. Zobrazí se soubor *. env* a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení pro hraniční zařízení, kde jsou proměnné použity pro některé vlastnosti. Soubor *. env* obsahuje hodnoty pro tyto proměnné.
1. Přejít do složky *Src/Cloud-to-Device-Console-App* Tady vidíte *appsettings.jsv* souboru a několik dalších souborů:
    * ***operations.jsv** _ – seznam operací, které má program spustit.
    _ ***Main.py** _ – ukázkový kód programu. Tento kód:

        _ Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](../../../direct-methods.md). 
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně **terminálu** a prozkoumávat události vygenerované modulem v okně **výstup** .
        * Vyvolá přímé metody pro vyčištění prostředků.

1. Upravit *operations.jsv* souboru:
    * Změňte odkaz na topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * V části `GraphInstanceSet` upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Upravte adresu URL RTSP tak, aby odkazovala na videosoubor:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * V části `GraphTopologyDelete` upravte název:

        `"name": "EVRToFilesOnMotionDetection"`
