---
title: Přesun dat v kanálech ML
titleSuffix: Azure Machine Learning
description: Přečtěte si o vstupních & výstupech dat v Azure Machine Learningch kanálech.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756958"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Přesun dat kroků kanálu ML a mezi nimi (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek poskytuje kód pro import, transformaci a přesouvání dat mezi kroky v Azure Machine Learningovém kanálu. Přehled toho, jak data fungují v Azure Machine Learning, najdete v tématu [přístup k datům ve službě Azure Storage](how-to-access-data.md). Výhody a strukturu Azure Machine Learningch kanálů najdete v tématu [co jsou Azure Machine Learning kanály?](concept-ml-pipelines.md).

Tento článek vám ukáže, jak:

- Použít `Dataset` objekty pro již existující data
- Přístup k datům v rámci svých kroků
- Rozdělit `Dataset` data na podmnožiny, jako jsou například školení a podmnožiny ověřování
- Vytvoření `OutputFileDatasetConfig` objektů pro přenos dat do dalšího kroku kanálu
- Použití `OutputFileDatasetConfig` objektů jako vstupu do postupu kanálu
- Vytvořit nové objekty, které chcete `Dataset` `OutputFileDatasetConfig` zachovat

> [!NOTE]
>`OutputFileDatasetConfig`Třídy a `OutputTabularDatasetConfig` jsou experimentální funkce ve verzi Preview a můžou se kdykoli změnit.
>
>Další informace naleznete v tématu https://aka.ms/azuremlexperimental.

## <a name="prerequisites"></a>Předpoklady

Budete potřebovat:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte existující sadu pomocí sady Python SDK. Importujte `Workspace` třídu a a `Datastore` načtěte ze souboru informace o svém předplatném `config.json` pomocí funkce `from_config()` . Tato funkce ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete také zadat parametr cesty, který bude odkazovat na soubor pomocí `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Některá již existující data. V tomto článku se krátce zobrazuje použití [kontejneru objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Volitelné: existující kanál strojového učení, který je popsaný v tématu [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Použít `Dataset` objekty pro již existující data 

Upřednostňovaným způsobem, jak ingestovat data do kanálu, je použít objekt [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) . `Dataset` objekty reprezentují trvalá data dostupná v celém pracovním prostoru.

Existuje mnoho způsobů, jak vytvářet a registrovat `Dataset` objekty. Tabulkové datové sady jsou pro data s oddělovači k dispozici v jednom nebo více souborech. Datové sady souborů jsou pro binární data (například obrázky) nebo pro data, která budete analyzovat. Nejjednodušším programovým způsobem, jak vytvářet `Dataset` objekty, je použít existující objekty BLOB v úložišti pracovních prostorů nebo veřejných adresách URL:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Další možnosti vytváření datových sad s různými možnostmi a z různých zdrojů, jejich registrace a kontrola v uživatelském rozhraní Azure Machine Learning, porozumět způsobu, jakým velikost dat komunikuje s výpočetní kapacitou a jejich verze, najdete v tématu [vytvoření Azure Machine Learning datových sad](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Předání datových sad vašemu skriptu

Chcete-li předat cestu datové sady ke skriptu, použijte `Dataset` `as_named_input()` metodu objektu. Výsledný objekt můžete buď předat `DatasetConsumptionConfig` vašemu skriptu jako argument nebo pomocí `inputs` argumentu pro skript kanálu, můžete načíst datovou sadu pomocí `Run.get_context().input_datasets[]` .

Po vytvoření pojmenovaného vstupu můžete zvolit režim přístupu: `as_mount()` nebo `as_download()` . Pokud váš skript zpracovává všechny soubory v datové sadě a disk na výpočetním prostředku je pro datovou sadu dostatečně velký, je režim přístupu ke stažení lepší volbou. Režim přístupu ke stažení se vyhne režie streamování dat za běhu. Pokud váš skript přistupuje k podmnožině datové sady nebo je pro výpočetní prostředky příliš velký, použijte režim přístupu k připojení. Další informace najdete v tématu [připojení pro čtení a stahování.](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Předání datové sady do vašeho kroku kanálu:

1. `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` Pro vytvoření objektu použijte nebo (ne ' na konci). `DatasetConsumptionConfig`
1. Použijte `as_mount()` nebo `as_download()` k nastavení režimu přístupu
1. Předání datových sad do kroků kanálu pomocí `arguments` `inputs` argumentů nebo

Následující fragment kódu ukazuje běžný vzor kombinace těchto kroků v rámci `PythonScriptStep` konstruktoru: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Můžete také použít metody, jako jsou `random_split()` a, a `take_sample()` vytvořit několik vstupů nebo snížit množství dat předávaných do vašeho kroku kanálu:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Přístup k datovým sadám v rámci skriptu

Pojmenované vstupy skriptu kroku kanálu jsou k dispozici jako slovník v rámci `Run` objektu. Načtěte aktivní `Run` objekt pomocí `Run.get_context()` a pak načtěte slovník pojmenovaných vstupů pomocí `input_datasets` . Pokud jste předali `DatasetConsumptionConfig` objekt pomocí `arguments` argumentu místo `inputs` argumentu, přístup k datům pomocí `ArgParser` kódu. Obě metody jsou znázorněny v následujícím fragmentu kódu.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Předaná hodnota bude cesta k souborům DataSet.

Je také možné získat přímý přístup k zaregistrovanému `Dataset` . Vzhledem k tomu, že registrované datové sady jsou trvalé a sdílené v pracovním prostoru, můžete je načíst přímo:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Použít `OutputFileDatasetConfig` pro mezilehlé data

Přestože `Dataset` objekty reprezentují pouze trvalá data, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) lze objekty použít pro dočasný výstup dat z kroků kanálu **a** trvalých výstupních dat. 

 `OutputFileDatasetConfig` výchozí chování objektu je zapisovat do výchozího úložiště dat pracovního prostoru. Předejte své `OutputFileDatasetConfig` objekty do objektu `PythonScriptStep` pomocí `arguments` parametru.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Můžete se rozhodnout nahrát obsah svého `OutputFileDatasetConfig` objektu na konci běhu. V takovém případě použijte `as_upload()` funkci spolu s `OutputFileDatasetConfig` objektem a určete, zda přepsat existující soubory v cíli. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Použít `OutputFileDatasetConfig` jako výstupy krok školení

V rámci vašeho kanálu `PythonScriptStep` můžete načíst dostupné výstupní cesty pomocí argumentů programu. Pokud je tento krok první a Inicializuje výstupní data, je nutné vytvořit adresář v zadané cestě. Pak můžete napsat libovolné soubory, které chcete zahrnout do `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Číst `OutputFileDatasetConfig` jako vstupy do jiných než počátečních kroků

Jakmile krok počátečního kanálu zapíše do cesty nějaká data `OutputFileDatasetConfig` a bude se jednat o výstup tohoto počátečního kroku, dá se použít jako vstup do pozdějšího kroku. 

V následujícím kódu 

* `step1_output_data` označuje, že výstup PythonScriptStep se `step1` zapisuje do úložiště dat adls Gen 2 `my_adlsgen2` v režimu přístupu upload. Přečtěte si další informace o tom, jak [nastavit oprávnění role](how-to-access-data.md#azure-data-lake-storage-generation-2) , aby bylo možné zapisovat data zpátky do úložiště adls Gen 2. 

* Až se `step1` dokončí a výstup se zapíše do cíle uvedeného v `step1_output_data` , STEP2 je připravený k použití `step1_output_data` jako vstup. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Registrovat `OutputFileDatasetConfig` objekty pro opakované použití

Pokud byste chtěli mít `OutputFileDatasetConfig` k dispozici déle než po dobu trvání experimentu, zaregistrujte ho do svého pracovního prostoru, abyste mohli sdílet a opakovaně používat v experimentech.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady služby Azure Machine Learning](how-to-create-register-datasets.md)
* [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
