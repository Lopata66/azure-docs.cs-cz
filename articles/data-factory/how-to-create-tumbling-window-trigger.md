---
title: Vytváření aktivačních událostí pro bubny v Azure Data Factory
description: Naučte se, jak vytvořit Trigger v Azure Data Factory, který spouští kanál v bubnovém okně.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: f9e31b8f0fce1af8408b80afb1049dae8c8ecf1c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673711"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Vytvoření triggeru, který spouští kanál v bubnovém okně
Tento článek popisuje kroky pro vytvoření, spuštění a monitorování aktivační události bubnového okna. Obecné informace o aktivačních událostech a podporovaných typech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md).

Aktivační události pro přeskakující okno jsou typem aktivačních událostí, které se aktivuje v pravidelných časových intervalech od určeného počátečního okamžiku a které zachovávají stav. Přeskakující okna jsou řada nepřekrývajících se souvislých časových intervalů s pevnou velikostí. Aktivační událost bubnového okna má relaci 1:1 s kanálem a může odkazovat jenom na kanál v jednotném kanálu.

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

1. Chcete-li vytvořit aktivační událost bubnového okna v uživatelském rozhraní Data Factory, vyberte kartu **triggery** a pak vyberte možnost **Nový**. 
1. Po otevření podokna konfigurace aktivační události vyberte **okno bubnu**a pak definujte vlastnosti aktivační události pro bubnové okno. 
1. Jakmile budete mít hotovo, vyberte **Uložit**.

![Vytvoření aktivační události bubnového okna v Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Vlastnosti typu triggeru bubnového okna

Okno bubnu má následující vlastnosti typu triggeru:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Následující tabulka poskytuje podrobný přehled hlavních elementů JSON, které souvisejí s opakováním a plánováním aktivační události bubnového okna:

| Element JSON | Popis | Typ | Povolené hodnoty | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Typ triggeru Typ je pevná hodnota "TumblingWindowTrigger". | Řetězec | "TumblingWindowTrigger" | Ano |
| **runtimeState** | Aktuální stav doby spuštění triggeru.<br/>**Poznámka**: Tento element je \<jen pro čtení >. | Řetězec | "Spuštěno, zastaveno", "zakázáno" | Ano |
| **frequency** | Řetězec, který představuje jednotku frekvence (minuty nebo hodiny), ve které se aktivační událost opakuje. Pokud jsou hodnoty data **čas_spuštění** lépe podrobnější než hodnota **frekvence** , jsou při výpočtu hranic okna zvážena data **StartTime** . Pokud má například hodnota **frekvence** hodinu a hodnota **StartTime** je 2017-09-01T10:10:10z, první okno je (2017-09-01T10:10:10z, 2017-09-01T11:10:10z). | Řetězec | "Minute", "hodina"  | Ano |
| **interval** | Kladné celé číslo označující interval pro hodnotu **frequency**, která určuje, jak často se má aktivační událost spouštět. Pokud má například **interval** hodnotu 3 a **frekvence** je "hodina", aktivační událost se opakuje každé 3 hodiny. <br/>**Poznámka**: minimální interval okna je 15 minut. | Integer | Kladné celé číslo. | Ano |
| **startTime**| První výskyt, který může být v minulosti. První interval triggeru je (**čas_spuštění**, **čas_spuštění** + **interval**). | DateTime | Hodnota DateTime | Ano |
| **endTime**| Poslední výskyt, který může být v minulosti. | DateTime | Hodnota DateTime | Ano |
| **způsobené** | Doba, po kterou se má zpozdit začátek zpracování dat okna. Spuštění kanálu se spustí po očekávaném čase spuštění a **prodlevě**. **Prodleva** definuje, jak dlouho bude aktivační událost před aktivací nového běhu čekat po uplynutí doby platnosti. **Zpoždění** nezmění okno **čas_spuštění**. Například hodnota **zpoždění** 00:10:00 implikuje zpoždění 10 minut. | Časový interval<br/>(hh: mm: SS)  | Hodnota TimeSpan, kde výchozí hodnota je 00:00:00. | Ne |
| **maxConcurrency** | Počet souběžných spuštění triggerů, které jsou aktivovány pro Windows, která jsou připravena. Například pro zálohování na celou hodinu běží u včerejších výsledků v 24 oknech. Pokud **maxConcurrency** = 10, aktivační události se aktivují jenom pro prvních 10 oken (00:00-01:00-09:00-10:00). Po dokončení prvních 10 aktivovaných spuštění kanálu se triggery spustí pro následující 10 Windows (10:00-11:00-19:00-20:00). Pokud budete pokračovat v tomto příkladu **maxConcurrency** = 10, pokud je k dispozici 10 Windows, je k dispozici 10 celkových spuštění kanálu. Pokud je k dispozici pouze 1 okno, je k dispozici pouze 1 spuštění kanálu. | Integer | Celé číslo od 1 do 50. | Ano |
| **retryPolicy: počet** | Počet opakování před spuštěním kanálu je označený jako "neúspěšné".  | Integer | Celé číslo, kde výchozí hodnota je 0 (žádné opakování). | Ne |
| **retryPolicy: intervalInSeconds** | Prodleva mezi pokusy o opakování zadané v sekundách. | Integer | Počet sekund, kde výchozí hodnota je 30. | Ne |
| **dependsOn: typ** | Typ TumblingWindowTriggerReference. Vyžaduje se, pokud je nastavená závislost. | Řetězec |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Ne |
| **dependsOn: velikost** | Velikost bubnového okna závislosti. | Časový interval<br/>(hh: mm: SS)  | Kladná hodnota TimeSpan, kde výchozí je velikost okna podřízené triggeru  | Ne |
| **dependsOn: posun** | Posun triggeru závislosti. | Časový interval<br/>(hh: mm: SS) |  Hodnota TimeSpan, která musí být záporná v závislosti na sobě. Pokud není zadána žádná hodnota, bude okno stejné jako Trigger sám. | Samostatná závislost: Ano<br/>Jiné: ne  |

### <a name="windowstart-and-windowend-system-variables"></a>Systémové proměnné WindowStart a WindowEnd

V definici **kanálu** (to znamená pro část dotazu) můžete použít systémové proměnné **WindowStart** a **WindowEnd** triggeru bubnového okna (tj.). Předejte systémové proměnné jako parametry do kanálu v definici **triggeru** . Následující příklad ukazuje, jak předat tyto proměnné jako parametry:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Chcete-li v definici kanálu použít hodnoty systémové proměnné **WindowStart** a **WindowEnd** , použijte odpovídajícím způsobem parametry "MyWindowStart" a "MyWindowEnd".

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Pořadí spouštění Windows ve scénáři obnovení
Pokud existuje více oken ke spuštění (zejména ve scénáři opakovaného naplnění), pořadí spouštění pro systém Windows je deterministické od nejstarších po nejnovější intervaly. Toto chování v současné době není možné změnit.

### <a name="existing-triggerresource-elements"></a>Existující prvky TriggerResource
Následující body platí pro existující prvky **TriggerResource** :

* Pokud se změní hodnota elementu **frekvence** (nebo velikosti okna) triggeru, stav systému Windows, který je již zpracován, *není* resetován. Aktivační událost se nadále spustí pro okna z posledního okna, které bylo spuštěno, pomocí nové velikosti okna.
* Pokud se hodnota elementu **čas_ukončení** triggeru změní (přidáno nebo aktualizováno), stav systému Windows, který je již zpracován, *není* resetován. Aktivační událost respektuje novou hodnotu pro **čas** ukončení. Pokud je nová hodnota pro **čas** ukončení před systémem Windows, která již byla spuštěna, aktivační událost se zastaví. V opačném případě se aktivační událost zastaví, když dojde k nové hodnotě pro **čas** ukončení.

### <a name="tumbling-window-trigger-dependency"></a>Závislost triggeru zabubného okna

Pokud chcete zajistit, aby se aktivační událost bubnového okna spustila až po úspěšném spuštění jiného zabubnového okna v objektu pro vytváření dat, [vytvořte závislost triggeru bubnového](tumbling-window-trigger-dependency.md)okna. 

## <a name="sample-for-azure-powershell"></a>Ukázka pro Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V této části se dozvíte, jak pomocí Azure PowerShell vytvořit, spustit a monitorovat Trigger.

1. Ve složce C:\ADFv2QuickStartPSH\ vytvořte soubor JSON s názvem **MyTrigger. JSON** s následujícím obsahem:

    > [!IMPORTANT]
    > Před uložením souboru JSON nastavte hodnotu elementu **StartTime** na aktuální čas UTC. Nastavte hodnotu elementu **čas_ukončení** na jednu hodinu po aktuálním čase UTC.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Vytvořte Trigger pomocí rutiny **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Pomocí rutiny **Get-AzDataFactoryV2Trigger** ověřte, že se stav triggeru **zastavil** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Spusťte Trigger pomocí rutiny **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Pomocí rutiny **Get-AzDataFactoryV2Trigger** ověřte, že je stav triggeru **spuštěný** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Aktivační událost se spouští v Azure PowerShell pomocí rutiny **Get-AzDataFactoryV2TriggerRun** . Chcete-li získat informace o spuštění triggeru, spusťte následující příkaz pravidelně. Aktualizujte hodnoty **TriggerRunStartedAfter** a **TriggerRunStartedBefore** tak, aby odpovídaly hodnotám v definici triggeru:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Pokud chcete monitorovat spuštění aktivačních událostí a spuštění kanálů v Azure Portal, přečtěte si téma [monitorování spuštění kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Další kroky

* Podrobné informace o aktivačních událostech najdete v tématu [spuštění kanálu a triggery](concepts-pipeline-execution-triggers.md#triggers).
* [Vytvořit závislost triggeru bubnového okna](tumbling-window-trigger-dependency.md)
