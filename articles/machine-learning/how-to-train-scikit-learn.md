---
title: Výukové scikity – Naučte se modely strojového učení
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spouštět scikit skripty pro školení na Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4221ed6a927d0c589407dc38b5371ad8a65d2174
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054387"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Sestavujte modely scikit s využitím škálování pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spouštět scikit skripty s učením pomocí Azure Machine Learning.

V ukázkových skriptech v tomto článku se používají ke klasifikaci imagí květů Iris k sestavení modelu machine learningu založeného na [datové sadě Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit-učení.

Bez ohledu na to, jestli provedete výukový model Machine Learning scikit z provozu nebo do cloudu převedete existující model, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pomocí elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat a monitorovat modely produkčního prostředí.

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:
 - Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

    - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce školení ukázek na serveru pro Poznámkový blok Najděte dokončený a rozbalený Poznámkový blok tak, že přejdete na tento adresář: **How-to->-azureml ml-framework > scikit-Projděte si > školení > výukových--skriptu sklearn-Intune-with-** .

 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experiment pro školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním školicích dat a školicích skriptů.

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Příprava skriptů

V tomto kurzu se školicí skript **train_iris. py** [pro vás už](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)poskytuje. V praxi byste měli být schopni vzít libovolný vlastní školicí skript a spustit ho s Azure ML bez nutnosti upravovat kód.

Poznámky:
- Zadaný školicí skript ukazuje, jak protokolovat některé metriky do běhu Azure ML pomocí `Run` objektu v rámci skriptu.
- Zadaný školicí skript používá ukázková data z `iris = datasets.load_iris()` funkce.  Pro vlastní data možná budete muset použít kroky, jako je například [nahrát datovou sadu a skripty](how-to-train-keras.md#data-upload) k zpřístupnění dat během školení.

### <a name="define-your-environment"></a>Definujte své prostředí.

#### <a name="create-a-custom-environment"></a>Vytvořte vlastní prostředí.

Vytvořte conda prostředí (sklearn-env. yml).
Chcete-li zapsat prostředí conda z poznámkového bloku, můžete přidat čáru ```%%writefile sklearn-env.yml``` v horní části buňky.

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Vytvořte prostředí Azure ML z této specifikace prostředí conda. Prostředí se zabalí do kontejneru Docker za běhu.
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
myenv.docker.enabled = True
```

#### <a name="use-a-curated-environment"></a>Použití spravovaného prostředí
Pokud nechcete vytvářet vlastní image, Azure ML poskytuje předem vytvořená a podbudovaná kontejnerová prostředí. Další informace najdete [tady](resource-curated-environments.md).
Pokud chcete použít konkrétní prostředí, můžete místo toho spustit následující příkaz:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>Vytvoření ScriptRunConfig

Tento ScriptRunConfig odešle vaši úlohu k provedení na místním výpočetním cíli.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
sklearnconfig.run_config.environment = myenv
```

Pokud chcete odesílat služby proti vzdálenému clusteru, můžete změnit run_config. Target na požadovaný výpočetní cíl.

### <a name="submit-your-run"></a>Odeslat běh
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho přístup k datům pomocí [úložiště](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)dat.

Další informace o přizpůsobení prostředí Pythonu najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Co se stane při spuštění
Po spuštění se spustí v následujících fázích:

- **Příprava**: obrázek Docker se vytvoří podle TensorFlow Estimator. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka./Outputs se v běhu kopíruje do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložení a registrace modelu

Po proškolení modelu ho můžete uložit a zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

Do skriptu pro školení přidejte následující kód train_iris. py pro uložení modelu. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zaregistrujte model do svého pracovního prostoru pomocí následujícího kódu. Zadáním parametrů `model_framework` , `model_framework_version` a `resource_configuration` nebudete mít k dispozici nasazení modelu bez kódu. Nasazení modelu bez kódu vám umožňuje přímo nasadit model jako webovou službu z registrovaného modelu a [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) objekt definuje výpočetní prostředek pro webovou službu.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Nasazení

Model, který jste právě zaregistrovali, lze nasadit stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který Estimator jste použili pro školení. Postup nasazení obsahuje část týkající se registrace modelů, ale můžete přeskočit přímo na [Vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

### <a name="preview-no-code-model-deployment"></a>Tisk Nasazení modelu bez kódu

Místo tradičního postupu nasazení můžete také použít funkci nasazení bez kódu (Preview) pro scikit-učení. Nasazení modelu bez kódu se nepodporuje pro všechny integrované typy modelů scikit-učení. Registrací modelu, jak je uvedeno výše, `model_framework` pomocí `model_framework_version` parametrů, a `resource_configuration` můžete jednoduše použít [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statickou funkci pro nasazení modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Poznámka: tyto závislosti jsou součástí předem připraveného kontejneru scikit-učení.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Úplný [postup](how-to-deploy-and-where.md) pokrývá nasazení v Azure Machine Learning s větší hloubkou.


## <a name="next-steps"></a>Další kroky

V tomto článku jste si naučili a zaregistrovali model scikit-učení a seznámili jste se s možnostmi nasazení. Další informace o Azure Machine Learning najdete v těchto článcích.

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
