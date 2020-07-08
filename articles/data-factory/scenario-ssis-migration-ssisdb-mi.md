---
title: SSIS migrace pomocí spravované instance Azure SQL jako cíle databázových úloh
description: SSIS migraci pomocí spravované instance Azure SQL jako cíle úloh databáze.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116316"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS migrace pomocí spravované instance Azure SQL jako cíle databázových úloh

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Při migraci databázových úloh z SQL Server instance do spravované instance Azure SQL byste měli být obeznámeni se [službou Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) a [topologiemi sítě pro migrace spravované instance SQL pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Tento článek se zaměřuje na migraci balíčků služby SQL Server Integration Service (SSIS) uložených v katalogu SSIS (SSISDB) a úlohách SQL Server agenta, které naplánují spouštění balíčků SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrace katalogu SSIS (SSISDB)

Migraci SSISDB můžete provést pomocí DMS, jak je popsáno v článku: [migrace balíčků SSIS do spravované instance SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS úlohy do agenta spravované instance SQL

Spravovaná instance SQL má nativní, první Plánovač, který je první třídou, stejně jako SQL Server agenta místně.  Vzhledem k tomu, že nástroj pro migraci pro úlohy SSIS ještě není dostupný, musí být migrován z SQL Server agenta místně do agenta spravované instance SQL prostřednictvím skriptů a ruční kopie.

## <a name="additional-resources"></a>Další zdroje

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Prostředí Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Síťové topologie pro migrace spravované instance SQL pomocí DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrace balíčků SSIS do spravované instance SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Další kroky

- [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Spouštění balíčků SSIS nasazených v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
