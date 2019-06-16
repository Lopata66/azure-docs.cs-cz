---
title: Správa stavu služby Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak definovat a spravovat služby stav služby Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870889"
---
# <a name="service-state"></a>Stav služby
**Služba stavu** odkazuje v paměti nebo na data na disku, které vyžaduje služba pro funkce. Zahrnuje, třeba datových struktur a členské proměnné, které Služba čte a zapisuje do práce. V závislosti na tom, jak je navržený službu může také obsahovat soubory nebo jiné prostředky, které jsou uložené na disku. Například soubory byste použili databázi k ukládání dat a transakční protokoly.

Jako příklad služby Pojďme se podívat kalkulačku. Základní kalkulačky služby převezme dvě čísla a vrátí jejich součet. Provádění tohoto výpočtu zahrnuje žádné proměnné členů nebo jiné informace.

Teď se podíváme stejné kalkulačky, ale s další metody pro ukládání a vrací poslední součet je vypočítán. Tato služba je nyní stavové. Stavová znamená, že obsahuje některé stavu, která zapisuje do při vypočítá nové sum a čte z když pokládáte vrátit poslední vypočítaný součet.

V Azure Service Fabric se nazývá první služby bezstavovou službu. Druhá služba se nazývá stavové služby.

## <a name="storing-service-state"></a>Ukládání stavu služby
Stav můžete externalized nebo umístěny společně s kódem, který je manipulaci se stavem. Externalizace stavu se obvykle provádí pomocí externí databáze nebo jiného úložiště dat, která běží na různých počítačích v síti nebo mimo proces ve stejném počítači. V našem příkladu kalkulačky úložiště dat může být databáze SQL nebo instance Azure Table Store. Každý požadavek pro výpočet součtu provede jeho aktualizaci těchto dat a vyžaduje, aby služba vrátí výsledek hodnotu aktuální hodnoty se načíst z úložiště. 

Stav může být také umístěny společně s kódem, který provádí úpravy stavu. Stavové služby v Service Fabric se obvykle vytvářejí pomocí tohoto modelu. Service Fabric poskytuje infrastrukturu k zajištění, že tento stav se vysoce dostupných, konzistentní a odolných a že služby vytvořené tímto způsobem můžete snadno škálovat.

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujících článcích:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Škálovatelnost služeb Service Fabric](service-fabric-concepts-scalability.md)
* [Dělení služeb Service Fabric](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
