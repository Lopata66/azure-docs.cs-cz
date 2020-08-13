---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning
description: Naučte se, jak a kde nasadit modely Azure Machine Learning, včetně Azure Container Instances, služby Azure Kubernetes, Azure IoT Edge a programovatelné pole brány.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 67a66ae94508a8d7f54d6112de95fa65a8fd5f09
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185413"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Nasazování modelů pomocí služby Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo Azure IoT Edge zařízení.

Pracovní postup je podobný bez ohledu na to, kam model nasadíte:

1. Zaregistrujte model.
1. Příprava odvození konfigurace
1. Příprava vstupního skriptu (Pokud nepoužíváte [nasazení bez kódu](how-to-deploy-no-code-deployment.md))
1. Nasaďte model do cílového výpočetního objektu.
1. Otestujte nasazený model, označovaný také jako webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v tématu [Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Předpoklady

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
- Model. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).
- [Rozšíření rozhraní příkazového řádku Azure (CLI) pro službu Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
- Model. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).
- [Sada SDK (Azure Machine Learning Software Development Kit) pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Podle pokynů v dokumentaci k Azure CLI [nastavte kontext předplatného](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Pak postupujte takto:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

k zobrazení pracovních prostorů, ke kterým máte přístup.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Další informace o použití sady SDK pro připojení k pracovnímu prostoru naleznete v dokumentaci [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .


---


## <a name="register-your-model"></a><a id="registermodel"></a>Registrace modelu

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model uložený ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů si pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu zadáte cestu buď umístění v cloudu (z školicího běhu), nebo místního adresáře. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace. Nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [vyhledání souborů modelu ve vstupním skriptu](how-to-deploy-advanced-entry-script.md#load-registered-models).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo z někde jinde. Při registraci modelu můžete volitelně zadat metadata o modelu. `tags` `properties` Slovníky a, které použijete pro registraci modelu, lze použít k filtrování modelů.

Následující příklady ukazují, jak registrovat model.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrace modelu z školicího běhu Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `--asset-path` na cestu ke složce, která obsahuje soubory.

### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Pokud chcete do registrace modelu zahrnout více souborů, nastavte `-p` na cestu ke složce, která obsahuje soubory.

Další informace o nástroji `az ml model register` najdete v [referenční dokumentaci](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model uložený ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů si pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu zadáte cestu buď umístění v cloudu (z školicího běhu), nebo místního adresáře. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace. Nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [vyhledání souborů modelu ve vstupním skriptu](how-to-deploy-advanced-entry-script.md#load-registered-models).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo z někde jinde. Při registraci modelu můžete volitelně zadat metadata o modelu. `tags` `properties` Slovníky a, které použijete pro registraci modelu, lze použít k filtrování modelů.

Následující příklady ukazují, jak registrovat model.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrace modelu z školicího běhu Azure ML

  Když použijete sadu SDK k vytvoření výukového modelu, můžete získat buď objekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) , nebo objekt [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) v závislosti na tom, jak jste model využívali. Každý objekt lze použít k registraci modelu vytvořeného spuštěním experimentu.

  + Registrace modelu z `azureml.core.Run` objektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory. Další informace najdete v dokumentaci ke [spuštění. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrace modelu z `azureml.train.automl.run.AutoMLRun` objektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    V tomto příkladu `metric` `iteration` nejsou zadány parametry a, takže iterace s nejlepší primární metrikou bude zaregistrována. `model_id`Hodnota vrácená z běhu se používá místo názvu modelu.

    Další informace najdete v dokumentaci k [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .


### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

Model můžete zaregistrovat zadáním místní cesty k modelu. Můžete zadat cestu buď složky, nebo jednoho souboru. Tuto metodu můžete použít k registraci modelů vyškolených pomocí Azure Machine Learning a potom ke stažení. Tuto metodu můžete použít také k registraci modelů, které jsou vyškolené mimo Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Používání sady SDK a ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory.

Další informace naleznete v dokumentaci k [třídě modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely poučenými mimo Azure Machine Learning najdete v tématu [Jak nasadit existující model](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definování skriptu pro zadávání

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definovat odvozenou konfiguraci

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Následující příkaz ukazuje, jak nasadit model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace určuje následující nastavení:

* Model vyžaduje Python.
* [Skript vstupu](#define-an-entry-script), který se používá ke zpracování webových požadavků odeslaných do nasazené služby
* Soubor Conda, který popisuje balíčky Pythonu potřebné pro odvození

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

# <a name="python"></a>[Python](#tab/python)

Konfigurace odvození popisuje, jak nastavit webovou službu, která obsahuje váš model. Používá se později při nasazení modelu.

Odvozená konfigurace používá Azure Machine Learning prostředí k definování závislostí softwaru potřebných pro vaše nasazení. Prostředí umožňují vytvářet, spravovat a opakovaně používat závislosti softwaru vyžadované pro školení a nasazení. Můžete vytvořit prostředí z vlastních souborů závislosti nebo použít jedno z Azure Machine Learning prostředí. Následující YAML je příkladem souboru závislostí conda pro odvození. Všimněte si, že je třeba určit, že se ve službě AzureML-Defaults verze >= 1.0.45 jako závislost PIP, protože obsahuje funkce potřebné k hostování modelu jako webové služby. Pokud chcete použít automatické generování schématu, váš vstupní skript musí také naimportovat `inference-schema` balíčky.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Pokud je vaše závislost k dispozici prostřednictvím conda i PIP (z PyPi), společnost Microsoft doporučuje používat verzi Conda, protože balíčky conda obvykle obsahují předem připravené binární soubory, které zajistí spolehlivější instalaci.
>
> Další informace najdete v tématu [Principy conda a PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Pokud chcete zjistit, jestli je vaše závislost k dispozici prostřednictvím Conda, použijte `conda search <package-name>` příkaz nebo použijte indexy balíčku v [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) a [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Pomocí souboru závislostí můžete vytvořit objekt prostředí a uložit ho do svého pracovního prostoru pro budoucí použití:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Důkladnou diskuzi o používání a přizpůsobení prostředí Pythonu s Azure Machine Learning najdete v tématu [vytvoření & použití softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).


Následující příklad ukazuje načtení prostředí z pracovního prostoru a jeho následné použití s konfigurací odvození:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození naleznete v dokumentaci třídy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

---

## <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definování konfigurace nasazení

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Možnosti, které jsou k dispozici pro konfiguraci nasazení, se liší v závislosti na zvoleném cílovém výpočetním prostředí.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Další informace najdete v dokumentaci [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="python"></a>[Python](#tab/python)

Před nasazením modelu je nutné definovat konfiguraci nasazení. *Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby.* Pokud například nasadíte model místně, je nutné zadat port, kam služba přijímá požadavky. Konfigurace nasazení není součástí vašeho skriptu pro vložení. Slouží k definování charakteristik výpočetního cíle, který bude hostovat model a vstupní skript.

Je také možné, že budete muset vytvořit výpočetní prostředek, pokud například ještě nemáte k vašemu pracovnímu prostoru přidruženou instanci služby Azure Kubernetes (AKS).

Následující tabulka uvádí příklad vytvoření konfigurace nasazení pro každý cíl služby Compute:

| Cílový výpočetní objekt | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Třídy pro místní, Azure Container Instances a webové služby AKS lze importovat z `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---



## <a name="deploy-your-model"></a>Nasazení modelu

Teď jste připraveni nasadit svůj model. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Použití registrovaného modelu

Pokud jste model zaregistrovali v pracovním prostoru Azure Machine Learning, nahraďte "MyModel: 1" názvem vašeho modelu a číslem verze.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Použití místního modelu

Pokud byste nechtěli zaregistrovat model, můžete předat parametr "sourceDirectory" v inferenceconfig.jsna, abyste určili místní adresář, ze kterého bude váš model sloužit.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje místní nasazení. Syntaxe se bude lišit v závislosti na cílovém výpočetním cíli, který jste zvolili v předchozím kroku.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace najdete v dokumentaci pro [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)a [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

---

### <a name="understanding-service-state"></a>Princip stavu služby

Během nasazování modelu se může zobrazit Změna stavu služby během plného nasazení.

Následující tabulka popisuje různé stavy služby:

| Stav WebService | Popis | Konečný stav?
| ----- | ----- | ----- |
| Přechod | Služba je v procesu nasazení. | Ne |
| Není v pořádku | Služba je nasazená, ale v tuto chvíli není dostupná.  | Ne |
| Unschedulable | Službu nyní nelze nasadit z důvodu nedostatku prostředků. | Ne |
| Neúspěšný | Nasazení služby se nezdařilo z důvodu chyby nebo selhání. | Ano |
| V pořádku | Služba je v pořádku a koncový bod je k dispozici. | Ano |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Odvození dávky
Azure Machine Learning výpočetní cíle jsou vytvářeny a spravovány pomocí Azure Machine Learning. Je možné je použít ke dávkové předpovědi z Azure Machine Learningch kanálů.

Návod pro odvození dávky s Azure Machine Learning COMPUTE najdete v tématu [How to run Batch předpovědi](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Odvození IoT Edge
Podpora pro nasazení na Edge je ve verzi Preview. Další informace najdete v tématu [nasazení Azure Machine Learning jako modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="delete-resources"></a>Odstranění prostředků

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Pokud chcete odstranit nasazenou webovou službu, použijte `az ml service <name of webservice>` .

Pokud chcete z pracovního prostoru odstranit registrovaný model, použijte`az ml model delete <model id>`

Přečtěte si další informace o [odstranění webové](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) služby a [odstranění modelu](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()` .
Pokud chcete odstranit registrovaný model, použijte `model.delete()` .

Další informace najdete v dokumentaci pro [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) a [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

---


## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)

