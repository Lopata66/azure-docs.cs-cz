---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 0463dbd67f27f6664ab246ad56af9c6fb95c52ee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682106"
---
Položky v dokumentu `deploymentconfig.json` namapovány na parametry pro [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

| Entita JSON | Parametr metody | Popis |
| ----- | ----- | ----- |
| `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro AKS musí být hodnota `aks`. |
| `autoScaler` | Není k dispozici | Obsahuje prvky konfigurace pro automatické škálování. Podívejte se na tabulku automatického škálování. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Určuje, zda má být povoleno automatické škálování webové služby. Pokud `numReplicas` = `0`, `True`; v opačném případě `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Minimální počet kontejnerů, které se mají použít při automatickém škálování této webové služby. Výchozí `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Maximální počet kontejnerů, které se mají použít při automatickém škálování této webové služby. Výchozí `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Jak často se automatické škálování pokusí škálovat tuto webovou službu. Výchozí `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Cílové využití (v procentech z 100), které by měl modul automatického škálování zkusit zachovat pro tuto webovou službu. Výchozí `70`. |
| `dataCollection` | Není k dispozici | Obsahuje prvky konfigurace pro shromažďování dat. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Zda se má pro webovou službu povolit shromažďování dat modelu. Výchozí `False`. |
| `authEnabled` | `auth_enabled` | Určuje, zda má být pro webovou službu povoleno ověřování klíčů. `tokenAuthEnabled` i `authEnabled` nemohou být `True`. Výchozí `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Určuje, jestli se má pro webovou službu povolit ověřování tokenu. `tokenAuthEnabled` i `authEnabled` nemohou být `True`. Výchozí `False`. |
| `containerResourceRequirements` | Není k dispozici | Kontejner pro entity CPU a paměti |
| &emsp;&emsp;`cpu` | `cpu_cores` | Počet jader procesoru, které se mají přidělit této webové službě. Výchozí `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Velikost paměti (v GB), která má být přidělena této webové službě. Výchozí `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Určuje, zda má být povoleno protokolování Application Insights pro webovou službu. Výchozí `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Časový limit pro vykonání volání bodování webové službě. Výchozí `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maximální počet souběžných požadavků na uzel pro tuto webovou službu. Výchozí `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Maximální doba, po kterou požadavek zůstane ve frontě Thee (v milisekundách), než se vrátí chyba 503. Výchozí `500`. |
| `numReplicas` | `num_replicas` | Počet kontejnerů, které se mají přidělit této webové službě. Žádná výchozí hodnota. Pokud tento parametr není nastaven, je automatické škálování ve výchozím nastavení povolené. |
| `keys` | Není k dispozici | Obsahuje prvky konfigurace pro klíče. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Primární ověřovací klíč, který se má použít pro tuto webovou službu |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Sekundární ověřovací klíč, který se má použít pro tuto webovou službu |
| `gpuCores` | `gpu_cores` | Počet jader GPU, které se mají přidělit pro tuto webovou službu. Výchozí hodnota je 1. Podporuje jenom celé číselné hodnoty. |
| `livenessProbeRequirements` | Není k dispozici | Obsahuje prvky konfigurace pro požadavky sondy pro živý provoz. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Jak často (v sekundách) provést test živého provozu Výchozí hodnota je 10 sekund. Minimální hodnota je 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Počet sekund po zahájení kontejneru, než se iniciují sondy pro živou práci. Výchozí hodnota je 310. |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Počet sekund, po jejichž uplynutí vyprší platnost testu živého provozu. Výchozí hodnota je 2 sekundy. Minimální hodnota je 1. |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimální po sobě jdoucí úspěšnost testu za provozu, aby bylo možné považovat za úspěšné po selhání. Výchozí hodnota je 1. Minimální hodnota je 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Když se spustí pod a sonda živého vysílání selže, Kubernetes se pokusí failureThreshold časy před tím, než zadáte. Výchozí hodnota je 3. Minimální hodnota je 1. |
| `namespace` | `namespace` | Obor názvů Kubernetes, do kterého je webová služba nasazena. Až 63 malých alfanumerických znaků (' a-z ', ' 0 '-' 9 ') a spojovníky ('-'). První a poslední znak nesmí být spojovníky. |

Následující kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
