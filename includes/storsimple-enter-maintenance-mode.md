---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409919"
---
#### <a name="to-enter-maintenance-mode"></a>Chcete-li spustit režim údržby
1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**.
2. Zadejte heslo. Výchozí heslo je **Heslo1**.
3. Na příkazovém řádku zadejte
   
     `Enter-HcsMaintenanceMode`
4. Zobrazí se upozornění oznamující, že režimu údržby se přerušit všechny vstupně-výstupní požadavky a server připojení k portálu Azure classic a zobrazí se výzva k potvrzení. Typ **Y** do režimu údržby.
   
    Oba kontrolery se restartuje. Po dokončení restartování se zobrazí další zpráva označující, že je zařízení v režimu údržby.

