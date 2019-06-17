---
title: Transformace dat pomocí aktivita Hadoop Pig ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí aktivita Pig ve službě Azure data factory ke spouštění skriptů Pig na vyžádání/svůj vlastní clusteru služby HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 914bc37552a80886df16ed69fba4e31b3f22ac22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61399518"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivita Hadoop Pig ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
> * [Verze 1](v1/data-factory-pig-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-pig.md)

Aktivita HDInsight Pig ve službě Data Factory [kanálu](concepts-pipelines-activities.md) Pig dotazy se spustí na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](introduction.md) a proveďte [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Podrobnosti o syntaxi

| Vlastnost            | Popis                              | Požaduje se |
| ------------------- | ---------------------------------------- | -------- |
| name                | Název aktivity                     | Ano      |
| description         | Text popisující, k čemu aktivita slouží | Ne       |
| type                | Pro aktivitu Hive typ aktivity je HDinsightPig | Ano      |
| linkedServiceName   | Odkaz na clusteru HDInsight zaregistrovaný jako propojenou službu ve službě Data Factory. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| scriptLinkedService | Odkaz na propojená služba Azure Storage se využívá k uložení skript Pig, který se spustí. Pokud nezadáte tuto propojenou službu, použije se propojená služba Azure Storage, definované v propojené službě HDInsight. | Ne       |
| scriptPath          | Zadejte cestu k souboru skriptu ve službě Azure Storage odkazuje scriptLinkedService. Název souboru je velká a malá písmena. | Ne       |
| getDebugInfo        | Určuje, kdy se zkopírují soubory protokolů do služby Azure Storage používaný v clusteru HDInsight (a) zadaný ve scriptLinkedService. Povolené hodnoty: NONE, vždy, nebo selhání. Výchozí hodnota: Žádné | Ne       |
| argumenty           | Určuje pole argumentů pro úlohy Hadoopu. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| defines             | Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skript Pig. | Ne       |

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
