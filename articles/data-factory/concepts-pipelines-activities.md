---
title: Kanály a aktivity v Azure Data Factory
description: Seznamte se s kanály a aktivitami v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 6e466675a9bd86693ce0ee048480712a55829ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246159"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Kanály a aktivity v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-pipelines.md)
> * [Aktuální verze](concepts-pipelines-activities.md)

Tento článek vám pomůže pochopit kanály a aktivity ve službě Azure Data Factory a naučit se je používat k sestavení kompletních pracovních postupů založených na datech pro potřeby přesunů a zpracování dat.

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. Kanál může například obsahovat sadu aktivit, které ingestují a vyčistí data protokolu a pak zahájí tok dat mapování k analýze dat protokolu. Kanál umožňuje spravovat aktivity jako sadu namísto každé z nich jednotlivě. Nasadit a naplánovat kanál namísto aktivit nezávisle.

Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke zkopírování dat z místního SQL Serveru do služby Azure Blob Storage. Potom použijte aktivitu toku dat nebo aktivitu databricks notebooku ke zpracování a transformaci dat z úložiště objektů blob do fondu Azure Synapse Analytics, na kterém jsou vytvořena řešení pro vytváření sestav business intelligence.

Data Factory má tři seskupení aktivit: [aktivity přesunu dat](copy-activity-overview.md), [aktivity transformace dat](transform-data.md)a kontrolní [aktivity](control-flow-web-activity.md). Každá aktivita může mít nula nebo více vstupních [datových sad](concepts-datasets-linked-services.md) a může generovat jednu nebo více výstupních [datových sad](concepts-datasets-linked-services.md). Následující diagram znázorňuje vztah mezi kanálem, aktivitou a datovou sadou v Data Factory:

![Vztah mezi datovou sadou, aktivitou a kanálem](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Po vytvoření datové sady můžete tuto datovou sadu používat v aktivitách v rámci kanálu. Datová sada například může být vstupní/výstupní datovou sadou aktivity kopírování nebo aktivity HDInsightHive. Další informace o datových sadách najdete v článku [Datové sady v Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Aktivity přesunu dat

Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Služba Data Factory podporuje úložiště dat uvedená v tabulce v této části. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Další informace najdete v článku [Aktivita kopírování – přehled](copy-activity-overview.md).

## <a name="data-transformation-activities"></a>Aktivity transformace dat
Azure Data Factory podporuje následující aktivity transformace, které lze přidat do kanálů jednotlivě nebo ve spojení s jinou aktivitou.

Aktivita transformace dat | Výpočetní prostředí
---------------------------- | -------------------
[Data Flow](control-flow-execute-data-flow-activity.md) | Azure Databricks spravované Azure Data Factory
[Funkce Azure Functions](control-flow-azure-function-activity.md) | Azure Functions
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Streamování Hadoop](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Jiskra](transform-data-using-spark.md) | HDInsight [Hadoop]
[Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](transform-data-using-machine-learning.md) | Virtuální počítač Azure
[Uložená procedura](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse nebo SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Vlastní aktivita](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Poznámkový blok Databricks](transform-data-databricks-notebook.md) | Azure Databricks
[Databricks Jar Aktivita](transform-data-databricks-jar.md) | Azure Databricks
[Aktivita databricks pythonu](transform-data-databricks-python.md) | Azure Databricks

Další informace najdete v článku [Aktivity transformace dat](transform-data.md).

## <a name="control-flow-activities"></a>Řízení aktivit toku
Podporují se následující aktivity toku řízení:

Aktivita řízení | Popis
---------------- | -----------
[Připojit proměnnou](control-flow-append-variable-activity.md) | Přidejte hodnotu do existující proměnné pole.
[Spustit kanál](control-flow-execute-pipeline-activity.md) | Aktivita spuštění kanálu umožňuje kanálu služby Data Factory volat jiný kanál.
[Filtr](control-flow-filter-activity.md) | Použití výrazu filtru na vstupní pole
[Pro každý](control-flow-for-each-activity.md) | Aktivita ForEach definuje ve vašem kanálu opakovaný tok řízení. Tato aktivita se používá k opakování v kolekci a spouští zadané aktivity ve smyčce. Implementace smyčky této aktivity se podobá struktuře smyčky Foreach v programovacích jazycích.
[Získat metadata](control-flow-get-metadata-activity.md) | Aktivita GetMetadata slouží k načtení metadat jakýchkoli dat ve službě Azure Data Factory.
[Aktivita podmínky If](control-flow-if-condition-activity.md) | Podmínka If se dá použít k vytvoření větve na základě podmínky, která provádí vyhodnocení na hodnotu True nebo False. Aktivita podmínky If funguje stejně jako příkaz if v programovacích jazycích. Vyhodnotí sadu aktivit, když se podmínka vyhodnotí jako `true`, a jinou sadu aktivit, když se podmínka vyhodnotí jako `false`.
[Aktivita vyhledávání](control-flow-lookup-activity.md) | Aktivita vyhledávání slouží ke čtení nebo vyhledání záznamu / názvu tabulky / hodnoty z jakéhokoli externího zdroje. Na tento výstup mohou dále odkazovat následující aktivity.
[Nastavit proměnnou](control-flow-set-variable-activity.md) | Nastavte hodnotu existující proměnné.
[Aktivita Until](control-flow-until-activity.md) | Implementuje smyčku Do-Until, která se podobá struktuře smyčky Do-Until v programovacích jazycích. Provádí ve smyčce sadu aktivit, dokud se podmínka přidružená k aktivitě nevyhodnotí jako pravdivá. Ve službě Data Factory můžete pro aktivitu Until určit hodnotu časového limitu.
[Aktivita ověřování](control-flow-validation-activity.md) | Ujistěte se, že kanál pokračuje v provádění pouze v případě, že existuje referenční datová sada, splňuje zadaná kritéria nebo bylo dosaženo časového plánu.
[Aktivita Wait](control-flow-wait-activity.md) | Pokud v kanálu použijete aktivitu Wait, kanál před pokračováním v provádění dalších aktivit počká zadanou dobu.
[Aktivita webu](control-flow-web-activity.md) | Webová aktivita slouží k volání vlastního koncového bodu REST z kanálu služby Data Factory. Můžete předávat datové sady a propojené služby, které má aktivita používat a ke kterým má mít přístup.
[Aktivita webhooku](control-flow-webhook-activity.md) | Pomocí aktivity webhooku zavolejte koncový bod a předejte adresu URL zpětného volání. Spuštění kanálu čeká na zpětné volání, které má být vyvoláno před pokračováním na další aktivitu.

## <a name="pipeline-json"></a>Zápis JSON kanálu
Tady je způsob definice kanálu ve formátu JSON:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

Značka | Popis | Typ | Požaduje se
--- | ----------- | ---- | --------
jméno | Název kanálu. Určuje název, který představuje akci prováděnou kanálem. <br/><ul><li>Maximální počet znaků: 140.</li><li>Musí začínat písmenem, číslem\_nebo podtržítkem ( ).</li><li>Nejsou povolené tyto znaky: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\“.</li></ul> | Řetězec | Ano
description | Určuje text popisující, k čemu se kanál používá. | Řetězec | Ne
activities | Část **activities** může obsahovat definici jedné nebo více aktivit. Podrobnosti o elementu activities formátu JSON najdete v části [Zápis JSON aktivity](#activity-json). | Pole | Ano
parameters | Část **parameters** může obsahovat definici jednoho nebo více parametrů v kanálu, aby byl kanál flexibilní pro opakované použití. | Seznam | Ne
souběžnost | Maximální počet souběžných spuštění kanálu může mít. Ve výchozím nastavení neexistuje žádné maximum. Pokud je dosaženo limitu souběžnosti, další spuštění kanálu budou zařazena do fronty, dokud nebudou dokončeny předchozí. | Číslo | Ne 
Poznámky | Seznam značek přidružených k kanálu | Pole | Ne

## <a name="activity-json"></a>Zápis JSON aktivity
Část **activities** může obsahovat definici jedné nebo více aktivit. Existují dva hlavní typy aktivit: aktivity spuštění a aktivity řízení.

### <a name="execution-activities"></a>Aktivity spuštění
Aktivity spuštění zahrnují aktivity [přesunu dat](#data-movement-activities) a [transformace dat](#data-transformation-activities). Mají následující strukturu nejvyšší úrovně:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

Následující tabulka obsahuje popis vlastností v definici aktivity ve formátu JSON:

Značka | Popis | Požaduje se
--- | ----------- | ---------
jméno | Název aktivity. Určuje název, který představuje akci prováděnou danou aktivitou. <br/><ul><li>Maximální počet znaků: 55.</li><li>Musí začínat písmenem nebo\_podtržítkem ( ).</li><li>Nejsou povolené tyto znaky: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\“. | Ano</li></ul>
description | Text popisující, k čemu aktivita slouží. | Ano
type | Typ aktivity. Části [Aktivity přesunu dat](#data-movement-activities), [Aktivity transformace dat](#data-transformation-activities)a [Aktivity řízení](#control-flow-activities) naleznete v různých typech aktivit. | Ano
linkedServiceName | Název propojené služby používané aktivitou.<br/><br/>Aktivita může vyžadovat zadání propojené služby, která odkazuje na požadované výpočetní prostředí. | Ano v případě aktivity HDInsight, dávkové hodnoticí aktivity služby Azure Machine Learning a aktivita uložené procedury <br/><br/>Ne ve všech ostatních případech
typeProperties | Vlastnosti v části typeProperties závisí na příslušném typu aktivity. Pokud chcete zobrazit vlastnosti typu určité aktivity, klikněte na odkaz na aktivitu v předchozí části. | Ne
policy | Zásady, které ovlivňují chování aktivity za běhu. Tato vlastnost zahrnuje chování při vypršení časového limitu a opakovaných pokusech. Pokud není zadaná, použijí se výchozí hodnoty. Další informace najdete v části [Zásada aktivity](#activity-policy). | Ne
dependsOn | Tato vlastnost slouží k určení závislostí aktivity a toho, jak následující aktivity závisejí na předchozích aktivitách. Další informace najdete v části [Závislost aktivit](#activity-dependency). | Ne

### <a name="activity-policy"></a>Zásada aktivity
Zásady ovlivňují chování aktivity za běhu a nabízejí možnosti konfigurace. Zásady aktivit jsou dostupné jenom pro aktivity spuštění.

### <a name="activity-policy-json-definition"></a>Definice zásady aktivity ve formátu JSON

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

Název JSON | Popis | Povolené hodnoty | Požaduje se
--------- | ----------- | -------------- | --------
timeout | Určuje časový limit pro spuštění aktivity. | Časový interval | Ne. Výchozí hodnota časového limitu je 7 dní.
retry | Maximální počet opakovaných pokusů. | Integer | Ne. Výchozí hodnota je 0.
retryIntervalInSeconds | Prodleva mezi pokusy o opakování v sekundách. | Integer | Ne. Výchozí hodnota je 30 sekund
secureOutput | Při nastavení vlastnosti na true se výstup aktivity považuje za bezpečný, tzn. že se nezaznamená do monitorování. | Logická hodnota | Ne. Výchozí hodnota je false.

### <a name="control-activity"></a>Aktivita řízení
Aktivity řízení mají následující strukturu nejvyšší úrovně:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Značka | Popis | Požaduje se
--- | ----------- | --------
jméno | Název aktivity. Určuje název, který představuje akci prováděnou danou aktivitou.<br/><ul><li>Maximální počet znaků: 55.</li><li>Musí začínat písmenem nebo\_podtržítkem ( ).</li><li>Nejsou povolené tyto znaky: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\“. | Ano</li><ul>
description | Text popisující, k čemu aktivita slouží. | Ano
type | Typ aktivity. Informace o různých typech aktivit najdete v částech [Aktivity přesunu dat](#data-movement-activities), [Aktivity transformace dat](#data-transformation-activities) a [Aktivity řízení](#control-flow-activities). | Ano
typeProperties | Vlastnosti v části typeProperties závisí na příslušném typu aktivity. Pokud chcete zobrazit vlastnosti typu určité aktivity, klikněte na odkaz na aktivitu v předchozí části. | Ne
dependsOn | Tato vlastnost slouží k určení závislostí aktivity a toho, jak následující aktivity závisejí na předchozích aktivitách. Další informace naleznete v tématu [závislost na aktivitě](#activity-dependency). | Ne

### <a name="activity-dependency"></a>Závislost aktivit
Závislost aktivit určuje, jak následující aktivity závisejí na předchozích aktivitách, a stanovuje podmínku určující, jestli se má pokračovat provedením další úlohy. Aktivita může záviset na jedné nebo více předchozích aktivitách s různými podmínkami závislosti.

Existují různé podmínky závislosti: Úspěch, Chyba, Vynecháno, Dokončeno.

Pokud má například kanál scénář aktivita A -> aktivita B, mohou nastat různé scénáře:

- Aktivita B má podmínku závislosti na aktivitě A s hodnotou **Úspěch**: aktivita B se spustí jenom v případě, že má aktivita A konečný stav Úspěch.
- Aktivita B má podmínku závislosti na aktivitě A s hodnotou **Chyba**: aktivita B se spustí jenom v případě, že má aktivita A konečný stav Chyba.
- Aktivita B má podmínku závislosti na aktivitě A s hodnotou **Dokončeno**: aktivita B se spustí v případě, že má aktivita A konečný stav Dokončeno.
- Aktivita B má podmínku závislosti na aktivitě A s hodnotou **Vynecháno**: aktivita B se spustí v případě, že má aktivita A konečný stav Vynecháno. Stav Přeskočeno nastává ve scénáři aktivita X -> aktivita Y -> aktivita Z, kde se každá aktivita spustí jenom v případě, že úspěšně proběhnou předchozí aktivity. Pokud v aktivitě X dojde k chybě, má aktivita Y stav „Vynecháno“, protože se nikdy neprovede. Stejně tak je ve stavu „Vynecháno“ i aktivita Z.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Příklad: Aktivita 2 závisí na předchozí aktivitě 1.

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Ukázkový kanál kopírování
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **Kopírování**. V této ukázce [aktivita kopírování](copy-activity-overview.md) kopíruje data ze služby Azure Blob Storage do databáze Azure SQL.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Je třeba počítat s následujícím:

- V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**.
- Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**. Informace o definicích datových sad ve formátu JSON najdete v článku [Datové sady](concepts-datasets-linked-services.md).
- V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. Pokud chcete zjistit více informací o přesouvání dat do úložiště dat a z něj, v části [Aktivity přesunu dat](#data-movement-activities) klikněte na úložiště dat, které chcete použít jako zdroj nebo jímku.

Kompletní postup vytváření tohoto kanálu najdete v části [Rychlý start: Vytvoření datové továrny](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Ukázkový kanál transformace
V následujícím ukázkovém kanálu je v části **activities** jedna aktivita typu **HDInsightHive**. V této ukázce [aktivita HDInsight Hive](transform-data-using-hadoop-hive.md) transformuje data ze služby Azure Blob Storage tak, že v clusteru Azure HDInsight Hadoop spustí soubor skriptu Hive.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Je třeba počítat s následujícím:

- V části activities je jenom jedna aktivita, jejíž vlastnost **type** má hodnotu **HDInsightHive**.
- Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu služby Azure Storage (který určuje služba scriptLinkedService s názvem AzureStorageLinkedService) a ve složce script v kontejneru `adfgetstarted`.
- Část `defines` určuje nastavení běhového prostředí, které se předá skriptu Hive jako konfigurační hodnoty Hive (např. $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Část **typeProperties** je u každé aktivity transformace odlišná. Další informace o vlastnostech typu podporovaných u aktivit transformace získáte kliknutím na aktivitu transformace v části [Aktivity transformace dat](#data-transformation-activities).

Kompletní postup vytváření tohoto kanálu najdete v části [Kurz: Transformace dat pomocí jazyka Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Více aktivit v kanálu
Oba předchozí ukázkové kanály obsahovaly jenom jednu aktivitu. Kanál může obsahovat víc než jednu aktivitu. Pokud máte kanál s více aktivitami a následující aktivity nejsou závislé na předchozích aktivitách, mohou se aktivity spouštět souběžně.

Dvě aktivity můžete zřetězit pomocí [závislosti aktivit](#activity-dependency), která určuje, jak následující aktivity závisejí na předchozích aktivitách, a stanovuje podmínku určující, jestli se má pokračovat provedením další úlohy. Aktivita může záviset na jedné nebo více předchozích aktivitách s různými podmínkami závislosti.

## <a name="scheduling-pipelines"></a>Plánování kanálů
Kanály se plánují pomocí aktivačních událostí. Existují různé druhy aktivačních událostí (aktivační událost plánovače, která umožňuje spouštění kanálů podle hodinového plánu, nebo ruční aktivační událost, která spouští kanály na vyžádání). Další informace o aktivačních událostech najdete v článku [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

Pokud chcete, aby aktivační událost aktivovala spuštění kanálu, musíte do definice aktivační události zahrnout odkaz na příslušný kanál. Mezi kanály a aktivačními událostmi existuje vztah n-m. Více aktivačních událostí může aktivovat jeden kanál a jedna aktivační událost může aktivovat více kanálů. Jakmile je aktivační událost definovaná, musíte ji spustit, aby mohla začít aktivovat kanál. Další informace o aktivačních událostech najdete v článku [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

Řekněme například, že máte aktivační událost plánovače, "Aktivační událost A", kterou chci zahájit můj kanál "MyCopyPipeline". Definujte aktivační událost podle následujícího příkladu:

### <a name="trigger-a-definition"></a>Definice aktivační události Trigger A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Další kroky
V následujících kurzech najdete podrobné pokyny pro vytváření kanálů s aktivitami:

- [Vytvoření kanálu s aktivitou kopírování](quickstart-create-data-factory-powershell.md)
- [Vytvoření kanálu s aktivitou transformace dat](tutorial-transform-data-spark-powershell.md)
