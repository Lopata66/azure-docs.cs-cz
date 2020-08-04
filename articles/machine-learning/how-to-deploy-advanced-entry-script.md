---
title: Vytvořit skript pro pokročilé scénáře
titleSuffix: Azure Machine Learning entry script authoring
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 0499cd6885454604e89ce4cadc313b2f68c45156
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544579"
---
# <a name="advanced-entry-script-authoring"></a>Vytváření pokročilých vstupních skriptů

Tento článek ukazuje, jak psát skripty pro zadávání pro specializované případy použití.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že už máte školený model strojového učení, který máte v úmyslu nasadit pomocí Azure Machine Learning. Další informace o nasazení modelu najdete v [tomto kurzu](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Automatické generování schématu Swagger

Chcete-li automaticky vygenerovat schéma pro webovou službu, poskytněte vzorek vstupu a/nebo výstupu v konstruktoru pro jeden z definovaných objektů typu. Typ a ukázka slouží k automatickému vytvoření schématu. Azure Machine Learning pak vytvoří specifikace [openapi](https://swagger.io/docs/specification/about/) (Swagger) pro webovou službu během nasazování.

Tyto typy jsou aktuálně podporovány:

* `pandas`
* `numpy`
* `pyspark`
* Standardní objekt Pythonu

Chcete-li použít generování schématu, zahrňte do `inference-schema` souboru závislostí Open Source balíček. Další informace o tomto balíčku najdete v tématu [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Definujte vstupní a výstupní ukázkové formáty v `input_sample` `output_sample` proměnných a, které reprezentují formáty požadavků a odpovědí webové služby. Tyto ukázky použijte ve funkci vstupu a výstupu dekoratéry na `run()` funkci. Následující scikit příklad používá generování schématu.


## <a name="power-bi-compatible-endpoint"></a>Power BI kompatibilní koncový bod 

Následující příklad ukazuje, jak definovat vstupní data jako `<key: value>` slovník pomocí datového rámce. Tato metoda je podporována pro využívání nasazené webové služby od Power BI. (Další[informace o tom, jak využívat webovou službu z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a>Binární data (tj. obrázky)

Pokud váš model akceptuje binární data, jako je obrázek, musíte upravit soubor, který se `score.py` používá pro vaše nasazení, aby přijímal nezpracované požadavky HTTP. K přijetí nezpracovaných dat použijte `AMLRequest` třídu ve vašem vstupním skriptu a přidejte `@rawhttp` dekoratér do `run()` funkce.

Tady je příklad `score.py` , který přijímá binární data:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest`Třída je v `azureml.contrib` oboru názvů. Entity v tomto oboru názvů se často mění při práci za účelem vylepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za verzi Preview, která není plně podporovaná společností Microsoft.
>
> Pokud potřebujete tuto možnost otestovat ve vašem místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`Třída umožňuje přístup k nezpracovaným publikovaným datům v Score.py, neexistuje žádná součást na straně klienta. Z klienta můžete data publikovat jako normální. Například následující kód Python přečte soubor obrázku a odešle data:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Sdílení prostředků mezi zdroji je způsob, jak u prostředků na webové stránce požádat z jiné domény. CORS funguje pomocí hlaviček HTTP odeslaných s požadavkem klienta a vrácených s odpovědí služby. Další informace o CORS a platných hlavičkách najdete v tématu [sdílení prostředků mezi zdroji](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) v Wikipedii.

Pokud chcete nakonfigurovat nasazení modelu tak, aby podporovalo CORS, použijte `AMLResponse` ve svém vstupním skriptu třídu. Tato třída umožňuje nastavit záhlaví objektu Response.

Následující příklad nastaví `Access-Control-Allow-Origin` hlavičku odpovědi z vstupního skriptu:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse`Třída je v `azureml.contrib` oboru názvů. Entity v tomto oboru názvů se často mění při práci za účelem vylepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za verzi Preview, která není plně podporovaná společností Microsoft.
>
> Pokud potřebujete tuto možnost otestovat ve vašem místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning bude směrovat pouze požadavky POST a GET do kontejnerů, ve kterých je spuštěna služba bodování. To může způsobit chyby způsobené prohlížeči pomocí požadavků na požadavky na požadavky CORS před lety.
> 


## <a name="load-registered-models"></a>Načíst modely registrované

Existují dva způsoby, jak ve skriptu pro vložení najít modely:
* `AZUREML_MODEL_DIR`: Proměnná prostředí obsahující cestu k umístění modelu.
* `Model.get_model_path`: Rozhraní API, které vrací cestu k souboru modelu pomocí názvu registrovaného modelu.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR je proměnná prostředí vytvořená během nasazování služby. Tuto proměnnou prostředí lze použít k vyhledání umístění nasazených modelů.

V následující tabulce je popsána hodnota AZUREML_MODEL_DIR v závislosti na počtu nasazených modelů:

| Nasazení | Hodnota proměnné prostředí |
| ----- | ----- |
| Jeden model | Cesta ke složce obsahující model |
| Více modelů | Cesta ke složce obsahující všechny modely Modely jsou umístěné podle názvu a verze v této složce ( `$MODEL_NAME/$VERSION` ). |

Během registrace a nasazení modelu jsou modely umístěny v AZUREML_MODEL_DIR cestě a jejich původní názvy souborů jsou zachovány.

Pokud chcete získat cestu k souboru modelu ve vašem vstupním skriptu, slučte proměnnou prostředí s cestou k souboru, který hledáte.

**Příklad jednoduchého modelu**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Příklad více modelů**

V tomto scénáři jsou v pracovním prostoru registrovány dva modely:

* `my_first_model`: Obsahuje jeden soubor ( `my_first_model.pkl` ) a existuje pouze jedna verze ( `1` ).
* `my_second_model`: Obsahuje jeden soubor ( `my_second_model.pkl` ) a existují dvě verze, `1` a `2` .

Po nasazení služby jsou v operaci nasazení k dispozici oba modely:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

V imagi Docker, který hostuje službu, `AZUREML_MODEL_DIR` obsahuje proměnná prostředí adresář, ve kterém se modely nacházejí.
V tomto adresáři se všechny modely nacházejí v cestě k adresáři `MODEL_NAME/VERSION` . Kde `MODEL_NAME` je název registrovaného modelu a `VERSION` je verze modelu. Soubory, které tvoří registrovaný model, jsou uloženy v těchto adresářích.

V tomto příkladu by byly cesty `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` a `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Při registraci modelu zadáte název modelu, který se používá pro správu modelu v registru. Pomocí tohoto názvu s metodou [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) můžete načíst cestu souboru modelu nebo souborů v místním systému souborů. Pokud zaregistrujete složku nebo kolekci souborů, toto rozhraní API vrátí cestu k adresáři, který obsahuje tyto soubory.

Když zaregistrujete model, přiřadíte mu název. Název odpovídá umístění modelu, a to buď místně, nebo během nasazování služby.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizovat webovou službu](how-to-deploy-update-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)
