---
title: Transformace dat pomocí Pythonu datacihlů
description: Naučte se zpracovávat nebo transformovat data spuštěním Pythonu datacihly.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.custom: tracking-python
ms.openlocfilehash: 6ae42c9cb68b28e5d2f0b5a2ba3cf7eab74a74b4
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561128"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformujte data spuštěním aktivity Pythonu v Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Aktivita Azure Databricks Pythonu v [kanálu Data Factory](concepts-pipelines-activities.md) spustí v clusteru Azure Databricks soubor Pythonu. Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md)   , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.Azure Databricks je spravovaná platforma pro spouštění Apache Spark.

Jedenáctiminutové představení a ukázku této funkce najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definice aktivity v Pythonu datacihly

Tady je ukázková definice JSON aktivity datacihly v Pythonu:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Vlastnosti aktivity Pythonu datacihly

Následující tabulka obsahuje popis vlastností JSON použitých v definici JSON:

|Vlastnost|Popis|Vyžadováno|
|---|---|---|
|name|Název aktivity v kanálu.|Ano|
|description|Text popisující, co aktivita dělá.|Ne|
|typ|Pro aktivitu Python datacihly je typ aktivity DatabricksSparkPython.|Ano|
|linkedServiceName|Název propojené služby datacihly, na které běží aktivita Pythonu Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md)   .|Ano|
|pythonFile|Identifikátor URI souboru Pythonu, který má být spuštěn. Jsou podporovány pouze DBFS cesty.|Ano|
|parameters|Parametry příkazového řádku, které budou předány souboru Python. Toto je pole řetězců.|Ne|
|Knihovna|Seznam knihoven, které se mají nainstalovat na cluster, který spustí úlohu. Může to být pole <řetězec, objekt>|Ne|

## <a name="supported-libraries-for-databricks-activities"></a>Podporované knihovny pro aktivity datacihly

Ve výše uvedené definici aktivity datacihly zadáte tyto typy knihoven: *jar*, *vejce*, *Maven*, *PyPI*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Další podrobnosti najdete v [dokumentaci k datacihlám](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) pro typy knihoven.

## <a name="how-to-upload-a-library-in-databricks"></a>Postup nahrání knihovny v datacihlách

#### <a name="using-databricks-workspace-ui"></a>[Použití uživatelského rozhraní pracovního prostoru datacihly](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Chcete-li získat cestu dBFS knihovny přidané pomocí uživatelského rozhraní, můžete použít rozhraní příkazového [řádku datacihly (instalace)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Knihovny jar jsou obvykle uloženy v dBFS:/úložiště/jar při používání uživatelského rozhraní. Můžete zobrazit seznam všech prostřednictvím rozhraní příkazového řádku: *datacihly FS LS dBFS:/úložiště/jar* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopírování knihovny pomocí rozhraní příkazového řádku datacihly](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Příklad: *datacihly FS CP sparkpi-Assembly-0,1. jar dBFS:/úložiště/jar*
