---
title: Aktualizace modelů strojového učení pomocí Azure Data Factory
description: Popisuje, jak vytvořit vytváření prediktivních kanálů pomocí Azure Data Factory a strojového učení.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: e8fb39e8762d31f00029a0eeea33f1e630fb15a6
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927390"
---
# <a name="update-ml-studio-classicv-models-by-using-update-resource-activity"></a>Aktualizace modelů ML Studio (Classic) v pomocí aktivity aktualizovat prostředek

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek doplňuje hlavní článek o integraci Azure Data Factory-ML Studio (Classic): [vytváření prediktivních kanálů pomocí Azure Machine Learning a Azure Data Factory](transform-data-using-machine-learning.md). Pokud jste to ještě neudělali, přečtěte si hlavní článek před čtením tohoto článku.

## <a name="overview"></a>Přehled
V rámci procesu zprovozňování ML Studio (klasického modelu) je váš model vyškolený a uložený. Pak ho použijete k vytvoření prediktivní webové služby. Webovou službu je pak možné spotřebovat na webech, řídicích panelech a mobilních aplikacích.

Modely, které vytvoříte pomocí Machine Learning, nejsou obvykle statické. Jakmile budou nová data k dispozici nebo když spotřebitel rozhraní API má svá vlastní data, model musí být znovu provlakované. 

Rekurze se může vyskytnout často. S aktivitou spuštění služby Batch a aktualizací aktivity prostředku můžete zprovoznění Azure Machine Learning model přeškolení a aktualizovat prediktivní webovou službu pomocí Data Factory.

Následující obrázek znázorňuje vztah mezi školicími a prediktivními webovými službami.

![Webové služby](./media/update-machine-learning-models/web-services.png)

## <a name="ml-studio-classic-update-resource-activity"></a>Aktivita aktualizace prostředku ML Studio (Classic)

Následující fragment kódu JSON definuje aktivitu spuštění dávky ML Studio (Classic).

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Vlastnost                      | Popis                              | Požaduje se |
| :---------------------------- | :--------------------------------------- | :------- |
| jméno                          | Název aktivity v kanálu     | Ano      |
| description                   | Text popisující, co aktivita dělá.  | No       |
| type                          | U Azure Machine Learning aktivita aktualizace prostředku je typ aktivity **povinná**. | Ano      |
| linkedServiceName             | Azure Machine Learning propojená služba, která obsahuje vlastnost updateResourceEndpoint. | Ano      |
| trainedModelName              | Název modulu trained model v experimentu webové služby, který se má aktualizovat | Ano      |
| trainedModelLinkedServiceName | Název propojené služby Azure Storage, která uchovává soubor ilearner, který je nahraný operací Update | Ano      |
| trainedModelFilePath          | Relativní cesta k souboru v trainedModelLinkedService, která představuje soubor ilearner, který je nahraný operací Update | Ano      |

## <a name="end-to-end-workflow"></a>Ucelený pracovní postup

Celý proces zprovozňováního přeškolení modelu a aktualizace prediktivních webových služeb zahrnuje následující kroky:

- Vyvolejte **webovou službu školení** pomocí **aktivity spuštění dávky**. Vyvolání webové služby školení je stejné jako vyvolání prediktivní webové služby popsané v tématu [vytváření prediktivních kanálů pomocí Azure Machine Learning a Data Factory aktivity spuštění dávky](transform-data-using-machine-learning.md). Výstupem webové služby školení je soubor iLearner, který můžete použít k aktualizaci prediktivní webové služby.
- Vyvolejte **koncový bod prostředku aktualizace** **prediktivní webové služby** pomocí **aktivity aktualizovat prostředek** a aktualizujte webovou službu pomocí nově vyučeného modelu.

## <a name="azure-machine-learning-linked-service"></a>Propojená služba Azure Machine Learning

Pro výše uvedený kompletní pracovní postup je potřeba vytvořit dvě propojené služby Azure Machine Learning:

1. Propojená služba Azure Machine Learning k webové službě školení. Tato propojená služba se používá v aktivitě provádění dávky stejným způsobem jako v případě, že jsou uvedena v části [vytváření prediktivních kanálů pomocí Azure Machine Learning a Data Factory aktivity spuštění dávky](transform-data-using-machine-learning.md). Rozdíl je výstupem webové služby školení je soubor iLearner, který se pak používá k aktualizaci prediktivní webové služby pomocí aktivity aktualizovat prostředek.
2. Propojená služba Azure Machine Learning k koncovému bodu prostředku aktualizace prediktivní webové služby. Tato propojená služba se používá při aktualizaci aktivity prostředku k aktualizaci prediktivní webové služby pomocí souboru iLearner vráceného z výše uvedeného kroku.

Pro druhou Azure Machine Learning propojenou službu se konfigurace liší v případě, že webová služba Azure Machine Learning je klasický webový servis nebo nová webová služba. Rozdíly jsou popsány samostatně v následujících oddílech.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webová služba je nová Azure Resource Manager webové služby.

Pokud je webová služba novým typem webové služby, který zveřejňuje Azure Resource Manager koncový bod, nemusíte přidávat druhý **jiný než výchozí** koncový bod. **UpdateResourceEndpoint** v propojené službě má formát:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Můžete získat hodnoty pro držitele v adrese URL při dotazování webové služby na [portálu Azure Machine Learning Web Services](https://services.azureml.net/).

Nový typ koncového bodu prostředku aktualizace vyžaduje ověření objektu služby. Pokud chcete použít ověřování instančního objektu, zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) a udělte jí roli **Přispěvatel** nebo **vlastník** předplatného nebo skupiny prostředků, do které patří webová služba. Přečtěte si téma [Vytvoření instančního objektu a přiřazení oprávnění ke správě prostředků Azure](../active-directory/develop/howto-create-service-principal-portal.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

- ID aplikace
- Klíč aplikace
- ID tenanta

Tady je ukázka definice propojené služby:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Další podrobnosti najdete v následujícím scénáři. Obsahuje příklad pro přeškolení a aktualizaci Azure Machine Learningch modelů studia z kanálu Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Ukázka: přeškolení a aktualizace Azure Machine Learningho modelu

V této části najdete ukázkový kanál, který používá **aktivitu spuštění Azure Machine Learning studia dávky** k přeškolování modelu. Kanál také používá **aktivitu prostředku Azure Machine Learning Studio Update** k aktualizaci modelu ve webové službě bodování. Oddíl také poskytuje fragmenty JSON pro všechny propojené služby, datové sady a kanály v příkladu.

### <a name="azure-blob-storage-linked-service"></a>Propojená služba úložiště objektů BLOB v Azure:
Azure Storage obsahuje následující data:

* školicí data. Vstupní data pro webovou službu Azure Machine Learning studia školení.
* soubor iLearner Výstup z webové služby Azure Machine Learning Studio Training Tento soubor je také vstupem aktivity aktualizovat prostředek.

Tady je ukázka definice JSON propojené služby:

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Propojená služba pro školicí koncový bod služby Azure Machine Learning Studio
Následující fragment kódu JSON definuje propojenou službu Azure Machine Learning, která odkazuje na výchozí koncový bod webové služby školení.

```JSON
{
    "name": "trainingEndpoint",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
            "apiKey": "myKey"
        }
    }
}
```

V **Azure Machine Learning Studiu**proveďte následující kroky a získejte hodnoty pro **mlEndpoint** a **apiKey**:

1. V nabídce vlevo klikněte na položku **webové služby** .
2. Klikněte na **webovou službu školení** v seznamu webových služeb.
3. Klikněte na Kopírovat vedle textového pole **klíč rozhraní API** . Vložte klíč do schránky do editoru JSON Data Factory.
4. V **Azure Machine Learning Studiu**klikněte na odkaz **spuštění dávky** .
5. Zkopírujte **identifikátor URI žádosti** z oddílu **žádosti** a vložte ho do Data Factory Editor JSON.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Propojená služba pro vyhodnocování koncového bodu služby Azure Machine Learning Studio:
Následující fragment kódu JSON definuje propojenou službu Azure Machine Learning, která odkazuje na aktualizovatelný koncový bod webové služby bodování.

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>Kanál
Kanál má dvě aktivity: **AzureMLBatchExecution** a **povinná**. Aktivita spuštění dávky převezme data školení jako vstup a vytvoří soubor iLearner jako výstup. Aktivita aktualizovat prostředek pak převezme tento soubor iLearner a použije ho k aktualizaci prediktivní webové služby.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
