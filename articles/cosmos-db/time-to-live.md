---
title: Vypršení platnosti dat v Azure Cosmos DB s využitím TTL
description: Díky TTL Microsoft Azure Cosmos DB poskytuje možnost používat dokumenty z systém automaticky vymazány po určitou dobu.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d09abfbec7ace3112de35869ccc95c56e113e6fe
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991831"
---
# <a name="time-to-live-in-azure-cosmos-db"></a>Hodnota Time to live ve službě Azure Cosmos DB 

"Time to Live" nebo TTL Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určité časové období. Ve výchozím nastavení můžete nastavit dobu za provozu na úrovni kontejneru a přepište hodnotu na základě za položku. Po nastavení interval TTL, ZÍSKÁ v kontejneru nebo na úrovni položek služby Azure Cosmos DB tyto položky automaticky odebrat po období od chvíle, kdy bylo naposledy změněno. Hodnotu Time to live je nakonfigurovaný v řádu sekund. Při konfiguraci hodnoty TTL se systém automaticky odstraní vypršela platnost položky na základě hodnoty TTL, na rozdíl od, která je explicitně vydala klientskou operaci odstranění.

## <a name="time-to-live-for-containers-and-items"></a>Hodnota Time to live pro kontejnery a položek

Během několika sekund je nastavena hodnotu time to live a je interpretován jako rozdílový od doby poslední změny položky. Můžete nastavit TTL na kontejner nebo položku v rámci kontejneru:

1. **TTL na kontejner** (sady s použitím `DefaultTimeToLive`):

   - Pokud chybí (nebo nastavený na hodnotu null), nejsou automaticky platnost položky.

   - Pokud je k dispozici a hodnota je nastavena na "-1", se rovná hodnotě nekonečné – není ve výchozím nastavení vypršení platnosti položek.

   - Pokud je k dispozici a hodnota nastavená na nějaké číslo ("n") – platnost položek "n" sekund poté, co čas jejich poslední změny.

2. **Čas TTL na položku** (sady s použitím `ttl`):

   - Tato vlastnost se vztahuje pouze tehdy, pokud `DefaultTimeToLive` je k dispozici a není nastaven na hodnotu null pro nadřazený kontejner.

   - Pokud existuje, přepíše `DefaultTimeToLive` hodnota jeho nadřazeného kontejneru.

## <a name="time-to-live-configurations"></a>Čas na živé konfigurace

* Pokud hodnota TTL je nastaven na n pro kontejner, položky v tomto kontejneru platnost vyprší po n sekund.  Pokud je položek ve stejném kontejneru, které mají své vlastní čas live, nastavte na hodnotu -1 (což znamená, že nevyprší platnost), nebo pokud některé položky mají přepsat time to live nastavení s jiným číslem, platnost těchto položek podle Použije se konfigurovaná hodnota TTL. 

* Pokud hodnota TTL není nastavena na kontejner, time to live na položky v tomto kontejneru nemá žádný vliv. 

* Pokud hodnota TTL kontejneru je nastavena na hodnotu -1, položky v tomto kontejneru, který má time to live nastavenou na n, platnost vyprší po n sekund a zbývající položky nevyprší. 

Odstranění položek podle hodnoty TTL je zdarma. Se neúčtují žádné další poplatky (to znamená, že se spotřebuje žádné další RUs) při odstranění položky v důsledku vypršení hodnoty TTL.

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci pro živé dlaždice v těchto článcích:

* [Jak nakonfigurovat TTL](how-to-time-to-live.md)
