---
title: Možnosti úložiště stavu na síti Azure Service Fabric sítě
description: Přečtěte si informace o spolehlivém ukládání stavu v Service Fabricch aplikacích sítě, které běží na Azure Service Fabric sítě.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397259"
---
# <a name="state-management-with-service-fabric"></a>Správa stavu pomocí Service Fabric

Service Fabric podporuje mnoho různých možností pro úložiště stavů. Koncepční Přehled vzorů správy stavů a Service Fabric najdete v tématu [Service Fabric koncepty: State](/azure/service-fabric/service-fabric-concepts-state). Všechny tyto stejné koncepty platí bez ohledu na to, jestli se vaše služby spouštějí uvnitř nebo vně Service Fabric sítě. 

Pomocí Service Fabric sítě můžete snadno nasadit novou aplikaci a připojit ji k existujícímu úložišti dat hostovanému v Azure. Kromě použití jakékoli vzdálené databáze existuje několik možností pro ukládání dat v závislosti na tom, jestli služba používá místní nebo vzdálené úložiště. 

## <a name="volumes"></a>Svazky

Kontejnery často využívají dočasné disky. Dočasné disky jsou ale dočasné, takže získáte nový dočasný disk a ztratíte informace, když dojde k selhání kontejneru. Je také obtížné sdílet informace o dočasných discích s jinými kontejnery. Svazky jsou adresáře, které se připevní do instancí kontejnerů, které můžete použít k trvalému stavu. Svazky poskytují úložiště souborů pro obecné účely a umožňují čtení a zápis souborů pomocí normálních rozhraní API v/v souborů na disku. Prostředek Volume popisuje, jak připojit adresář a které záložní úložiště použít. K ukládání dat můžete vybrat buď službu Azure File Storage, nebo Service Fabric diskové jednotky.

![Svazky][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric spolehlivý svazek

Service Fabric Reliable Volume je ovladač svazku Docker, který slouží k připojení místního svazku ke kontejneru. Čtení a zápisy jsou místní operace a rychlé. Data se replikují do sekundárních uzlů, takže budou vysoce dostupná. Převzetí služeb při selhání je také rychlé. Když dojde k selhání kontejneru, převezme služby při selhání uzel, který již obsahuje kopii vašich dat. Příklad najdete v tématu [nasazení aplikace s Service Fabric spolehlivým svazkem](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Svazek souborů Azure

Svazek služby soubory Azure je ovladač svazku Docker, který se používá k připojení sdílené složky Azure Files do kontejneru. Úložiště souborů Azure používá síťové úložiště, takže čtení a zápisy probíhají přes síť. V porovnání s Service Fabric spolehlivým svazkem je úložiště souborů Azure méně výkonné, ale poskytuje možnost levnější a plně spolehlivého data. Příklad najdete v tématu [nasazení aplikace se svazkem služby soubory Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Další kroky

Informace o aplikačním modelu najdete v tématu [Service Fabric prostředky](service-fabric-mesh-service-fabric-resources.md) .

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
