---
title: Použití vlastních aktivit v kanálu Azure Data Factory
description: Zjistěte, jak vytvořit vlastní aktivity a použít je v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8076ae81b111aa6b524b7e286ed15ca0661d748b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062113"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, který používáte:"]
> * [Verze 1](v1/data-factory-use-custom-activities.md)
> * [Aktuální verze](transform-data-using-dotnet-custom-activity.md)

Existují dva typy aktivit, které můžete použít v kanálu Azure Data Factory.

- [Aktivity přesunu dat](copy-activity-overview.md) pro přesun dat mezi [podporovanými úložišti dat zdroje a jímky](copy-activity-overview.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](transform-data.md) k transformaci dat pomocí výpočetních služeb, jako je například Azure HDInsight, Azure Batch a Azure Machine Learning.

Přesunout data do nebo z datového úložiště, že Data Factory nepodporuje, nebo pokud chcete transformovat a zpracovávat data způsobem, který není podporován službou Data Factory, můžete vytvořit **vlastní aktivity** s vlastním přesun dat nebo logiku transformace a použití aktivita v kanálu. Vlastní aktivita spouští kód vlastní logikou v **Azure Batch** fondu virtuálních počítačů.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Viz následující články, pokud jste ještě službu Azure Batch:

* [Základy Azure Batch](../batch/batch-technical-overview.md) Přehled služby Azure Batch.
* [Nové AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) rutina pro vytvoření účtu Azure Batch (nebo) [webu Azure portal](../batch/batch-account-create-portal.md) k vytvoření účtu Azure Batch pomocí webu Azure portal. Zobrazit [použití Powershellu ke správě účtu Azure Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) najdete podrobné pokyny k použití rutiny.
* [Nové AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) rutina pro vytvoření fondu služby Azure Batch.

## <a name="azure-batch-linked-service"></a>Služba Azure Batch propojené

Následující kód JSON určuje ukázku služby Azure Batch a propojený. Podrobnosti najdete v tématu [výpočetní prostředí podporovaných službou Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Další informace o službě propojené služby Azure Batch najdete v tématu [propojené služby Compute](compute-linked-services.md) článku.

## <a name="custom-activity"></a>Vlastní aktivita

Následující fragment kódu JSON definuje kanál s aktivitou jednoduchý vlastní. Definice aktivity obsahuje odkaz na službu Azure Batch a propojený.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

V této ukázce helloworld.exe je uložen ve složce customactv2/helloworld účtu služby Azure Storage, který je používán resourceLinkedService vlastní aplikaci. Vlastní aktivita odešle tato vlastní aplikace má být proveden v Azure Batch. Příkaz pro všechny preferované aplikace, které mohou být na cíli prováděly o operační systém z uzlů fondu Azure Batch můžete nahradit.

Následující tabulka popisuje názvy a popisy vlastností, které jsou specifické pro tuto aktivitu.

| Vlastnost              | Popis                              | Požaduje se |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Název aktivity v kanálu     | Ano      |
| description           | Text popisující, jakým způsobem aktivita naloží.  | Ne       |
| type                  | Pro vlastní aktivitu, typ aktivity je **vlastní**. | Ano      |
| linkedServiceName     | Propojená služba se službou Azure Batch. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku.  | Ano      |
| Příkaz               | Příkaz vlastní aplikace, který se spustí. Pokud aplikace je již k dispozici na uzlech fondu Azure Batch, můžete přeskočit resourceLinkedService a folderPath. Například můžete zadat příkaz, který má být `cmd /c dir`, která je nativně podporuje uzlu Windows fondu služby Batch. | Ano      |
| resourceLinkedService | Propojená služba Azure Storage do účtu úložiště, kde je uložený vlastní aplikace | Ne&#42;       |
| folderPath            | Cesta ke složce vlastní aplikace a všechny její závislosti<br/><br/>Pokud máte závislosti uložena v podsložkách – to znamená, že v hierarchickou strukturu složek v části *folderPath* – struktura složek se sloučí aktuálně, když soubory se zkopírují do služby Azure Batch. To znamená všechny soubory se zkopírují do jediné složky žádné podsložky. Chcete-li tento problém vyřešit, zvažte komprese souborů, kopírování komprimovaného souboru a pak rozzipovávání vlastního kódu do požadovaného umístění. | Ne&#42;       |
| referenceObjects      | Pole z existujících propojených služeb a datových sad. Odkazované propojené služby a datové sady jsou předány do vlastní aplikace ve formátu JSON tak váš vlastní kód může odkazovat na prostředky služby Data Factory | Ne       |
| extendedProperties    | Uživatelem definované vlastnosti, které mohou být předány vlastní aplikaci ve formátu JSON, tak váš vlastní kód mohou odkazovat další vlastnosti | Ne       |
| retentionTimeInDays | Doba uchování pro soubory odeslané pro vlastní aktivity. Výchozí hodnota je 30 dní. | Ne |

&#42;Vlastnosti `resourceLinkedService` a `folderPath` buď musí být zadány oba, nebo obojí vynechat.

> [!NOTE]
> Pokud jako referenceObjects ve vlastní aktivitě předáváte propojené služby, je, že osvědčeným postupem zabezpečení služby Azure Key Vault předat povolená propojené služby, (protože neobsahuje žádné zabezpečené řetězce) a načítání přihlašovacích údajů pomocí název tajného kódu přímo z klíče Trezoru z kódu. Příklad můžete nalézt [tady](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) , že odkazy AKV povolené propojené služby, načte přihlašovací údaje ze služby Key Vault a pak přistupuje k úložišti v kódu.

## <a name="custom-activity-permissions"></a>Vlastní aktivita oprávnění

Vlastní aktivita nastaví automaticky uživatelský účet Azure Batch na *přístup bez oprávnění správce s oborem úloh* (specifikace výchozí uživatele automaticky). Nelze změnit úroveň oprávnění automaticky uživatelského účtu. Další informace najdete v tématu [spouštění úloh v rámci uživatelských účtů ve službě Batch | Automatické uživatelské účty](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Provádění příkazů

Můžete přímo spustit příkaz použití vlastní aktivity. V následujícím příkladu spustí příkaz "odezvu hello world" v cílové uzly fondu Azure Batch a vypíše výstup stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Předávání objektů a vlastností

Tato ukázka předvádí, jak vám pomůže referenceObjects a extendedProperties předat objekty služby Data Factory a uživatelem definované vlastnosti vaší vlastní aplikace.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Při spuštění aktivity referenceObjects a extendedProperties jsou uloženy v následující soubory, které jsou nasazené do stejné složky provádění SampleApp.exe:

- `activity.json`

  Ukládá extendedProperties a vlastnosti vlastní aktivity.

- `linkedServices.json`

  Úložiště pole propojené služby definované ve vlastnosti referenceObjects.

- `datasets.json`

  Úložiště v vlastnost referenceObjects definovaný pole datové sady.

Následující ukázka kódu ukazují, jak SampleApp.exe můžete získat přístup k požadované informace ze souborů JSON:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Načítání výstupů provedení

Můžete začít spuštění kanálu pomocí následujícího příkazu Powershellu:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Při spuštění kanálu můžete zkontrolovat výstupu spuštění pomocí následujících příkazů:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

**Stdout** a **stderr** vaší vlastní aplikace, které jsou uloženy do **adfjobs** kontejneru v propojená služba Azure Storage, můžete definovat při vytváření propojených Azure Batch Služba s identifikátorem GUID úlohy. Z výstupu spuštění aktivit můžete získat podrobné cestu, jak je znázorněno v následujícím fragmentu kódu:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Pokud chcete využívat obsah stdout.txt v podřízené aktivity, můžete získat cestu k souboru stdout.txt ve výrazu "\@activity('MyCustomActivity').output.outputs [0]".

> [!IMPORTANT]
> - Activity.json, linkedServices.json a datasets.json jsou uloženy ve složce modulu runtime úlohy služby Batch. V tomto příkladu activity.json, linkedServices.json a datasets.json jsou uloženy v `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` cestu. V případě potřeby, musíte ho PROČISTIT samostatně.
> - Pro propojené služby, která používá modul Integration Runtime citlivé informace, jako jsou klíče nebo hesla je jím zašifrovaná modul Integration Runtime k zajištění přihlašovacích údajů zůstávají v zákazník definovaný privátním síťovém prostředí. Při odkazu kód vlastní aplikace tímto způsobem, může být některá citlivá pole chybí. V extendedProperties namísto používání odkaz na propojenou službu, v případě potřeby použijte SecureString.

## <a name="pass-outputs-to-another-activity"></a>Předejte výstup do jiné

Můžete odeslat vlastní hodnoty z kódu ve vlastní aktivitě zpátky do služby Azure Data Factory. To lze provést napsáním do `outputs.json` z vaší aplikace. Data Factory kopíruje obsah `outputs.json` a připojí ji jako hodnotu do výstup aktivity `customOutput` vlastnost. (Maximální velikost je 2MB.) Pokud chcete využívat obsah `outputs.json` v podřízené aktivity, můžete získat hodnotu s použitím výrazu `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Načíst SecureString výstupy

Určený jako typ hodnoty vlastností důvěrné *SecureString*, jak je znázorněno v některé z příkladů v tomto článku jsou maskována na kartě monitorování v uživatelském rozhraní služby Data Factory.  Při provádění skutečné kanálu, ale *SecureString* vlastnost serializuje jako JSON v rámci `activity.json` souboru jako prostý text. Příklad:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Serializace není skutečně zabezpečené a není určena pro zabezpečení. Cílem je pomocný parametr do služby Data Factory k maskování hodnotu na kartě monitorování.

Pro přístup k vlastnostem typu *SecureString* z vlastní aktivitu, přečtěte si `activity.json` soubor, který je umístěn ve stejné složce jako vaše. Soubor EXE, deserializovat JSON a pak přístup k vlastnosti JSON (extendedProperties = > [propertyName] = > hodnota).

## <a name="compare-v2-v1"></a> Porovnání v2 pro vlastní aktivity a verze 1 (vlastní) aktivity DotNet

Ve službě Azure Data Factory verze 1 implementujete aktivity DotNet (vlastní) tak, že vytvoříte projekt knihovny tříd .NET vytvoříte třídou, která implementuje `Execute` metodu `IDotNetActivity` rozhraní. Metoda spuštění se předá propojené služby, datové sady a rozšířené vlastnosti v datové části JSON aktivity DotNet (vlastní) jako silně typované objekty. Podrobnosti o chování verzi 1 najdete v tématu [DotNet (vlastní) ve verzi 1](v1/data-factory-use-custom-activities.md). Z důvodu této implementaci váš kód aktivity DotNet verze 1 má cílit na rozhraní .NET Framework 4.5.2. Verze 1 aktivity DotNet také musí být spuštěn na uzlech Azure Batch Pool se systémem Windows.

Ve vlastních aktivit Azure Data Factory V2 nemusíte implementovat rozhraní .NET. Můžete nyní přímo spustit příkazy, skripty a vlastní kód, kompilovány jako spustitelný soubor. Pokud chcete nakonfigurovat tuto implementaci, zadejte `Command` vlastnost spolu s `folderPath` vlastnost. Vlastní aktivita nahraje spustitelného souboru a jeho závislosti do `folderpath` a provede příkaz za vás.

Propojené služby, datové sady (definováno v referenceObjects) a rozšířené vlastnosti definované v datové části JSON služby Data Factory v2, který vlastní aktivita je přístupný spustitelný soubor jako soubory JSON. Můžete přistupovat pomocí serializátor JSON, jak je znázorněno v předchozí ukázce kódu SampleApp.exe požadované vlastnosti.

Změny zavedené v Data Factory V2 vlastní aktivitu můžete napsat kód vlastní logikou ve vašem preferovaném jazyce a spustit na Windows a operační systémy Linux podporované službou Azure Batch.

Následující tabulka popisuje rozdíly mezi Data Factory V2 vlastní aktivity a Data Factory verze 1 (vlastní) aktivity DotNet:

|Rozdíly      | Vlastní aktivity      | verze 1 (vlastní) aktivity DotNet      |
| ---- | ---- | ---- |
|Jak je definován vlastní logiku      |Tím, že poskytuje spustitelný soubor      |Implementací knihovny DLL .NET      |
|Prostředí pro spouštění vlastní logiky      |Windows nebo Linux      |Windows (.NET Framework 4.5.2)      |
|Spouštění skriptů      |Podporuje spouštění skriptů přímo (například "cmd /c odezvu hello world" na virtuálním počítači Windows)      |Vyžaduje implementaci v knihovně DLL .NET      |
|Datová sada, povinné      |Nepovinné      |Požadované aktivity zřetězit a předávají informace      |
|Předávání informací z aktivity do vlastní logiku      |Prostřednictvím ReferenceObjects (LinkedServices a datové sady) a ExtendedProperties (Vlastnosti)      |Prostřednictvím ExtendedProperties (Vlastnosti), vstupní a výstupní datové sady      |
|Načtení informací vlastní logiku      |Analyzuje activity.json linkedServices.json a datasets.json uloženy ve stejné složce spustitelného souboru      |Pomocí sady .NET SDK (rámce .NET 4.5.2)      |
|Protokolování      |Zapisuje přímo do STDOUT      |Implementace protokolovací nástroj v knihovně DLL .NET      |

Pokud máte stávající kód technologie .NET, které jsou vytvořené pro verze 1 aktivity DotNet (vlastní), budete muset upravit kód pro práci s aktuální verzí pro vlastní aktivity. Aktualizujte svůj kód pomocí následujících tyto podrobné pokyny:

  - Změňte projekt z knihovny tříd .NET do konzolové aplikace.
  - Spustit aplikaci `Main` metody. `Execute` Metodu `IDotNetActivity` rozhraní se už nevyžaduje.
  - Přečíst a parsovat propojené služby, datové sady a aktivitu s serializátor JSON a ne jako silně typované objekty. Předání hodnot požadovaných vlastností hlavní kód vlastní logikou. Předchozí kód SampleApp.exe označujeme jako příklad.
  - Objekt protokolovací nástroj se už nepodporuje. Výstup z spustitelný soubor lze vytisknout na konzole a uložení do umístění stdout.txt.
  - Balíček Microsoft.Azure.Management.DataFactories NuGet se už nevyžaduje.
  - Kompilaci kódu, ukládání spustitelného souboru a jeho závislosti do služby Azure Storage a definování cestu `folderPath` vlastnost.

Úplnou ukázku začátku do konce vzorku knihovny DLL a kanál popisu v Data Factory verze 1 článku [použití vlastních aktivit v kanálu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) může být přepsán ve formátu Data Factory vlastní aktivity najdete v tématu [ Ukázková data Factory pro vlastní aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatické škálování služby Azure Batch

Můžete také vytvořit fond služby Azure Batch s **automatického škálování** funkce. Můžete například vytvořit fond služby azure batch s 0 vyhrazených virtuálních počítačích a se vzorec automatického škálování na základě počtu úkolů čekajících na vyřízení.

Ukázkové vzorce zde dosáhne následující chování: Při počátečním vytvoření fondu začíná 1 virtuální počítač. Metrika $PendingTasks definuje počet úloh ve spuštění + aktivní (ve frontě) stavu. Vzorec najde průměrný počet čekající úlohy za posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy nedostane mimo 25 virtuálních počítačů. Tak jako jsou odeslány nové úkoly, fondu automaticky rozšíří a jako dokončení úkolů, budou virtuální počítače zdarma jednu po druhé a automatickým Škálováním zmenšuje těchto virtuálních počítačů. startingNumberOfVMs a maxNumberofVMs lze upravit podle vašich potřeb.

Vzorec automatického škálování:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zobrazit [automatické škálování výpočetních uzlů ve fondu služby Azure Batch](../batch/batch-automatic-scaling.md) podrobnosti.

Pokud fond používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může trvat 15 – 30 minut přípravy virtuálního počítače před spuštěním vlastní aktivity. Pokud se fond používá různé autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval + 10 minut.

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
