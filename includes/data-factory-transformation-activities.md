---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 19927fd274cbb7337248fb2ae8cf9d882612d570
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369215"
---
Azure Data Factory podporuje následující aktivity transformace, které lze přidat do kanálů jednotlivě nebo ve spojení s jinou aktivitou.

| Aktivita transformace dat | Výpočetní prostředí |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streamování Hadoop](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Azure Machine Learning Studio (klasické) aktivity: dávkové provádění a aktualizace prostředku](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Virtuální počítač Azure |
| [Uložená procedura](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure synapse Analytics nebo SQL Server |
| [U-SQL Data Lake Analytics](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] nebo Azure Batch |

> [!NOTE]
> Pomocí aktivity MapReduce můžete na svém clusteru HDInsight Spark spouštět programy Spark. Podrobnosti najdete v článku [Vyvolání programů Spark ze služby Azure Data Factory](../articles/data-factory/v1/data-factory-spark.md).
> Můžete vytvořit vlastní aktivitu pro spouštění skriptů jazyka R na svém clusteru HDInsight s nainstalovaným jazykem R. Viz [Spuštění skriptu jazyka R pomocí služby Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

