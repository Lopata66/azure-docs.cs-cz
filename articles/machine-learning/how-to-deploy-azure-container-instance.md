---
title: Postup nasazení modelů pro Azure Container Instances
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: cbba0dd5341ad148831ac3b1f94685bf2beddd5a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855261"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Nasazení modelu do služby Azure Container Instances
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby v Azure Container Instances (ACI). Použijte Azure Container Instances, pokud je splněna jedna z následujících podmínek:

- Potřebujete rychle nasadit a ověřit model. Nemusíte vytvářet kontejnery ACI předem. Jsou vytvořeny v rámci procesu nasazení.
- Testujete model, který je ve vývoji. 

Informace o dostupnosti kvót a oblastí pro ACI najdete v článku [kvóty a dostupnost oblastí pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) článek.

> [!IMPORTANT]
> Důrazně doporučujeme ladit místně před nasazením do webové služby, další informace najdete v tématu [ladění místně](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally) .
>
> Můžete využít také [poznámkový blok služby Azure Machine Learning pro místní nasazení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local).

## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model služby Machine Learning, který je zaregistrován ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si téma [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastavené následující proměnné:

    * `ws` – Nastavte na svůj pracovní prostor.
    * `model` – Nastavte na registrovaný model.
    * `inference_config` – Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty rozhraní příkazového __řádku__ v tomto článku předpokládají, že jste vytvořili `inferenceconfig.json` dokument. Další informace o vytváření tohoto dokumentu najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Nasazení do ACI

Pokud chcete nasadit model, který se Azure Container Instances, vytvořte __konfiguraci nasazení__ , která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné pro hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI je vhodný jenom pro malé modely, které jsou menší než 1 GB. 
> * Pro vývoj a testování větších modelů doporučujeme použít AKS s jedním uzlem.
> * Počet modelů, které se mají nasadit, je omezený na 1 000 modelů na jedno nasazení (na kontejner). 

### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Použití VS Code

Viz [nasazení modelů pomocí vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nemusíte vytvářet kontejner ACI k testování předem. Kontejnery ACI se vytvářejí podle potřeby.


## <a name="next-steps"></a>Další kroky

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
