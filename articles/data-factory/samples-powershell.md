---
title: Ukázky Azure PowerShell pro Azure Data Factory
description: Ukázky Azure PowerShell – skripty, které vám pomůžou vytvářet a spravovat datové továrny.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 9abf4b46896a3c067cfb189da7921dfe318aedab
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229376"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Ukázky Azure PowerShell pro Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure Data Factory.

| Skript | Popis  |
|---|---|
|**Kopírovat data**||
|[Kopírování objektů BLOB ze složky do jiné složky v Azure Blob Storage](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu kopíruje objekty blob ze složky v Azure Blob Storage do jiné složky ve stejném Blob Storage. |
|[Kopírování dat z SQL Server do Azure Blob Storage](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure. |
|[Hromadná kopie](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento ukázkový skript PowerShellu kopíruje data z několika tabulek v databázi v Azure SQL Database do služby Azure SQL Data Warehouse. |
|[Přírůstková kopie](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento ukázkový skript PowerShellu načte jenom nové nebo aktualizované záznamy ze zdrojového úložiště dat do úložiště dat jímky po počáteční úplné kopii dat ze zdroje do jímky. |
|**Transformace dat**||
|[Transformace dat pomocí clusteru Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu transformuje data spuštěním programu v clusteru Spark. |
|**Přezvednutí a posunutí balíčků SSIS do Azure**||
|[Vytvoření prostředí Azure-SSIS Integration Runtime](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu zřídí prostředí Azure-SSIS Integration runtime, které spouští balíčky služba SSIS (SQL Server Integration Services) (SSIS) v Azure. |



