---
title: Obecné úrovni služby – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o úrovni general purpose Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: dc379f1ee67174cd806840e4244054701d18f0d4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784018"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Obecné úrovni služby – Azure SQL Database

> [!NOTE]
> Služba úrovni general purpose v nákupní model založený na virtuálních jádrech je volána v nákupní model založený na DTU úrovně služeb standard. Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-purchase-models.md).

Azure SQL Database je založené na architektuře modul databáze serveru SQL Server upravené pro cloudovém prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují tři úrovně služby, které se používají ve službě Azure SQL Database, každý s různými modely architektury. Tyto úrovně služby jsou:

- Obecné účely
- Pro důležité obchodní informace
- Hyperškálování

Oddělení výpočetního výkonu a úložiště podle architektury modelu pro danou vrstvu služeb pro obecné účely. Tento Architektonický model spoléhá na vysokou dostupnost a spolehlivost služby Azure Blob storage, který transparentně replikuje soubory databáze a pokud základní infrastruktury selhání zaručuje ztrátě dojde.

Následující obrázek znázorňuje čtyři uzly ve standardní Architektonický model s oddělenými výpočetní a úložnou vrstvu.

![Oddělení výpočetního výkonu a úložiště](media/sql-database-managed-instance/general-purpose-service-tier.png)

V architektuře modelu pro danou vrstvu služeb pro obecné účely existují dvě vrstvy:

- Bezstavové výpočetní vrstvě, na kterém běží `sqlserver.exe` zpracování a obsahuje pouze přechodné a uložená v mezipaměti dat (například – mezipaměti plánu, fondu vyrovnávací paměti, fondu úložiště sloupce). Azure Service Fabric, který inicializuje procesu, řídí stav uzlu a provede převzetí služeb při selhání na jiné místo v případě potřeby je provozována této bezstavové uzlu serveru SQL Server.
- Stavová data vrstvy se soubory databáze (.mdf/.ldf), které jsou uloženy v úložišti objektů Blob v Azure. Azure Blob storage zaručuje, že bude bez ztráty dat u všech záznamů, který je umístěn v žádném souboru databáze. Azure Storage má předdefinované datové dostupnost a redundance, který zajistí, že každý záznam v souboru protokolu nebo stránky v datovém souboru budou zachovány, i když dojde k chybě procesu serveru SQL Server.

Pokaždé, když se upgraduje operační systém nebo databázový stroj, některá část základní infrastruktury selže nebo pokud se zjistí některé kritický problém v procesu serveru SQL Server, Azure Service Fabric se přesune bezstavové procesu serveru SQL Server na jiný bezstavové výpočetní uzel. Je sada uzlů za chodu, který čeká na spuštění nové výpočetní služby, pokud převzetí služeb při selhání z primárního uzlu se stane, aby se minimalizoval čas převzetí služeb při selhání. Data ve vrstvě úložiště Azure nebudou ovlivněna a data/log soubory jsou připojeny k nově inicializované procesu serveru SQL Server. Tento postup zaručuje 99,99 % dostupnost, ale může mít některé vliv na funkčnost v případě velkého zatížení, které běží kvůli čas přechodu a fakt nový uzel SQL serveru začíná studenou mezipaměti.

## <a name="when-to-choose-this-service-tier"></a>Kdy zvolit této vrstvy služeb

Obecné účely úrovně služeb je výchozí úroveň služby ve službě Azure SQL Database, která je navržená pro většinu obecných úloh. Pokud potřebujete modul je plně spravovaná databáze s 99,99 % smlouva SLA s latencí úložiště mezi 5 až 10 ms, které odpovídá Azure SQL IaaS ve většině případů, úrovni General Purpose je možnost za vás.

## <a name="next-steps"></a>Další postup

- Další informace o [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) a [Hyperškálovatelného](sql-database-service-tier-hyperscale.md) úrovně.
- Další informace o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [kontinuita podnikových procesů](sql-database-business-continuity.md).
