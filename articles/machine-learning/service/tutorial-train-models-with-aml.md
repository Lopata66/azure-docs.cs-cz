---
title: 'Kurz klasifikace obrázků: Trénování modelů'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak pro trénování modelu klasifikace obrázků s scikit-výuku v kurzech mooc Poznámkový blok Jupyter Pythonu pomocí služby Azure Machine Learning. Tento kurz je první částí z dvoudílné série.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: ed2b35c5a1a0a017cb6bea086601282c83956d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515552"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Kurz: Trénování modelů klasifikace obrázků s daty mnist ručně a scikit-informace pomocí Azure Machine Learning

V tomto kurzu trénování model strojového učení na vzdálené výpočetní prostředky. Budete používat pracovní postup pro trénování a nasazení pro službu Azure Machine Learning (Preview) v poznámkovém bloku Python Jupyter.  Poznámkový blok poté můžete použít jako šablonu k trénování vlastního modelu strojového učení s vlastními daty. Tento kurz je **první částí z dvoudílné série kurzů**.  

V tomto kurzu trénovat jednoduché logistickou regresi s použitím [mnist ručně](http://yann.lecun.com/exdb/mnist/) datové sady a [scikit-informace](https://scikit-learn.org) službou Azure Machine Learning. MNIST je oblíbená datová sada obsahující 70 000 obrázků ve stupních šedi. Každé image je rukou psaný číslice 28 x 28 pixelů, představující číslo od 0 do 9. Cílem je vytvořit víc tříd třídění k identifikaci číslice danou image představuje.

Zjistěte, jak provádět následující akce:

> [!div class="checklist"]
> * Nastavení vývojového prostředí.
> * Přístup a prozkoumejte data.
> * Natrénujeme jednoduchý Logistický regresní model ve vzdáleném clusteru.
> * Zkontrolujte výsledky školení a zaregistrujte tento nejlepší model.

Zjistíte, jak vybrat model a nasadit ho v [druhou částí z tohoto kurzu](tutorial-deploy-models-with-aml.md).

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

>[!NOTE]
> S využitím Azure Machine Learning SDK verze 1.0.41 testovaný kód v tomto článku.

## <a name="prerequisites"></a>Požadavky

Přejděte k [nastavení vývojového prostředí](#start) číst kroky Poznámkový blok, nebo použijte níže uvedené pokyny k získání poznámkového bloku a spustit ho v poznámkových bloků Azure nebo vašeho vlastního serveru poznámkového bloku.  Pokud chcete spustit Poznámkový blok, bude nutné:

* Server poznámkového bloku Python 3.6 s nainstalované tyto položky:
    * Azure Machine Learning sady SDK pro Python
    * `matplotlib` a `scikit-learn`
* Výukový program Poznámkový blok a soubor **utils.py**
* Pracovní prostor machine learning
* Konfigurační soubor pro pracovní prostor ve stejném adresáři jako poznámkového bloku

Získejte všechny tyto požadavky pomocí kteréhokoli z níže uvedených částech.

* Použití [server poznámkového bloku cloudu ve vašem pracovním prostoru](#azure)
* Použití [serveru poznámkového bloku](#server)

### <a name="azure"></a>Použít server poznámkového bloku cloudu ve vašem pracovním prostoru

Je snadné začít s vlastním serverem založené na cloudu poznámkového bloku. [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) již nainstalován a nakonfigurován pro vás, když vytvoříte tento prostředek v cloudu.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Po spuštění webové stránky Poznámkový blok otevřete **kurzy/img – klasifikace – část 1 – training.ipynb** poznámkového bloku.

### <a name="server"></a>Použít vlastní server poznámkového bloku Jupyter

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 Po dokončení kroků, spusťte **kurzy/img – klasifikace – část 1 – training.ipynb** Poznámkový blok z naklonovaného adresáře.

## <a name="start"></a>Nastavení vývojového prostředí

Veškeré nastavení pro vaši vývojovou práci se dá provést v poznámkovém bloku Pythonu. Instalační program obsahuje následující akce:

* Importujte balíčky Pythonu.
* Připojení k pracovnímu prostoru tak, aby váš místní počítač může komunikovat s vzdálené prostředky.
* Vytvoření experimentu ke sledování všech spuštění.
* Vytvořte vzdálené výpočetní cíl pro účely školení.

### <a name="import-packages"></a>Import balíčků

Naimportujte balíčky Pythonu, které potřebujete v této relaci. Také zobrazte verzi sady SDK Azure Machine Learning:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Připojení k pracovnímu prostoru

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. `Workspace.from_config()` přečte soubor **config.json** a načte podrobnosti do objektu s názvem `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvořte experiment pro sledování spuštění ve vašem pracovním prostoru. Pracovní prostor může mít více pokusů:

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Vytvořit nebo připojit existující výpočetní prostředek

Pomocí Azure Machine Learning Compute, je spravovaná služba, můžou odborníci přes data trénování modelů strojového učení na clusterech virtuálních počítačů Azure. Mezi příklady patří virtuální počítače s podporou GPU. V tomto kurzu vytvoříte Azure Machine Learning Compute jako prostředí pro školení. Následující kód vytvoří výpočetní clustery pro vás, pokud ještě neexistují ve vašem pracovním prostoru.

 **Vytváření výpočtů trvá přibližně během pěti minut.** Pokud výpočetní je již v pracovním prostoru, tento kód používá ho a přeskočí v procesu vytváření.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes,
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)

     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Nyní máte k dispozici potřebné balíčky a výpočetní prostředky pro trénink modelu v cloudu.

## <a name="explore-data"></a>Zkoumání dat

Než tréninku modelů, je potřeba rozumět datům, který použijete k natrénování ho. Také je nutné zkopírovat data do cloudu. Pak je přístupný ve vašem cloudovém prostředí pro školení. V této části se dozvíte, jak provést následující akce:

* Stáhněte datovou sadu mnist ručně.
* Zobrazte obrázky.
* Nahrání dat do cloudu.

### <a name="download-the-mnist-dataset"></a>Stáhnutí datové sady MNIST

Stáhněte datovou sadu MNIST a uložte soubory do místního adresáře `data`. Obrázky a popisky pro trénování a testování se stáhnou:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```

Zobrazí se výstup podobný tomuto: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Zobrazení některých ukázkových obrázků

Načtěte komprimované soubory do pole `numpy`. Pak pomocí `matplotlib` vykreslete 30 náhodných obrázků z datové sady s jejich popisky nad nimi. Tento krok vyžaduje `load_data` funkce, která je součástí `util.py` souboru. Tento soubor je umístěný ve složce s ukázkou. Ujistěte se, že je umístěný ve stejné složce jako tento poznámkový blok. `load_data` Funkce jednoduše analyzuje komprimované soubory do numpy pole:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Náhodná ukázka obrázků:

![náhodného vzorku imagí](./media/tutorial-train-models-with-aml/digits.png)

Nyní máte představu o tom, jak tyto obrázky vypadají, a o očekávaném výstupu predikce.

### <a name="upload-data-to-the-cloud"></a>Nahrání dat do cloudu

Nyní zpřístupněte data vzdáleně díky nahrávání dat z místního počítače do Azure. Pak může být přístupný pro vzdálené školení. Úložiště je vhodné konstrukce přidružené k pracovním prostoru můžete nahrávat nebo stahovat data. Můžete také pracovat s ním z vašich cílových výpočetních prostředí vzdálené. Je podporovaný službou účtu služby Azure Blob storage.

Mnist ručně soubory jsou odeslány do adresáře s názvem `mnist` v kořenovém adresáři úložiště:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
```

Teď máte všechno, co potřebujete k zahájení trénování modelu.

## <a name="train-on-a-remote-cluster"></a>Trénování na vzdáleném clusteru

Odešlete úlohu do clusteru pro vzdálené trénování, který jste nastavili dříve.  K odeslání úlohy je potřeba provést:
* Vytvoření adresáře
* Vytvoření trénovacího skriptu
* Vytvoření objektu odhad
* Odeslání úlohy

### <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte adresář, ze kterého bude dodán potřebný kód z počítače do vzdáleného prostředku.

```python
import os
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Vytvoření trénovacího skriptu

Pokud chcete odeslat úlohu do clusteru, vytvořte nejprve trénovací skript. Spuštěním následujícího kódu vytvořte trénovací skript s názvem `train.py` v adresáři, který jste právě vytvořili.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Všimněte si, jak skript získává data a ukládá modely:

+ Trénovací skript načte argument najít adresář, který obsahuje data. Když později odešlete úlohu, bude odkázána na úložiště dat pro tento argument: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Trénovací skript uloží do adresáře s názvem modelu **výstupy**. Vše, co je v tomto adresáři zapsáno, se automaticky nahraje do vašeho pracovního prostoru. Přistupujete k modelu v pozdější části kurzu z tohoto adresáře. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Trénovací skript vyžaduje soubor `utils.py` správně načíst datovou sadu. Následující kód zkopíruje `utils.py` do `script_folder` tak, aby soubor přístupný spolu s trénovací skript na vzdálený prostředek.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Vytvoření estimátoru

[Skriptu SKLearn estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) objektu se používá k odeslání příkazu run. Váš odhad vytvořte spuštěním následujícího kódu definovat tyto položky:

* Název objektu estimator `est`.
* Adresář, který obsahuje vaše skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Cílové výpočetní prostředí. V tomto případě používáte cluster pro výpočty Azure Machine Learning, který jste vytvořili.
* Název skriptu školení **train.py**.
* Parametry požadované z trénovací skript.

V tomto kurzu je tento cíl AmlCompute. Všechny soubory ve složce script nahrají do uzlů clusteru pro spuštění. **Data_folder** nastaveno pro použití úložiště dat, `ds.path('mnist').as_mount()`:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Odeslání úlohy do clusteru

Spusťte experiment odesláním estimator objektu:

```python
run = exp.submit(config=est)
run
```

Vzhledem k tomu, že asynchronní volání se vrátí **Příprava** nebo **systémem** stavu co nejdříve po spuštění úlohy.

## <a name="monitor-a-remote-run"></a>Monitorování vzdáleného spuštění

Celkem, první spusťte trvá **asi 10 minut**. Ale pro následné spuštění, dokud závislosti skript nezmění, stejnou bitovou kopii je znovu. Čas spuštění kontejneru je mnohem rychlejší.

Co se stane, zatímco čekáte:

- **Obrázek vytvoření**: Image Dockeru se vytvoří, který odpovídá zadané pomocí odhadu prostředí Pythonu. Image se nahraje do pracovního prostoru. Obrázek vytvoření a nahrání přebírá **přibližně během pěti minut**.

  Tato fáze je způsobeno jednou pro každé prostředí Python kontejneru se uloží do mezipaměti pro následné spuštění. Při vytváření image se streamují protokoly do historie spuštění. Pomocí těchto protokolů můžete sledovat průběh vytváření bitové kopie.

- **Škálování**: Pokud vzdálený cluster vyžaduje další uzly, aby spuštění než je aktuálně k dispozici, jsou přidány automaticky další uzly. Škálování obvykle trvá **přibližně během pěti minut.**

- **Spuštění**: V této fázi nezbytné skriptů a souborů odesílají do cílového výpočetního prostředí. Potom úložišť jsou připojené nebo zkopírován. A pak **entry_script** běží. Když úloha běží, **stdout** a **. / protokoly** adresáře se streamují do historie spuštění. Pomocí těchto protokolů můžete sledovat průběh spuštění.

- **Následné zpracování**: **. / Výstupy** se adresář spuštění kopíruje do historie spuštění ve vašem pracovním prostoru, takže se zobrazí tyto výsledky.

Můžete zkontrolovat průběh s probíhající úlohou několika způsoby. Tento kurz používá widgetu Jupyter a `wait_for_completion` metoda.

### <a name="jupyter-widget"></a>Widget Jupyter

Sledujte průběh spuštění pomocí widgetu Jupyter. Stejně jako spuštění odeslání widgetu je asynchronní a poskytuje živé aktualizace každých 10 až 15 sekund, dokud neskončí úloha:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Ve widgetu se na konci školení vypadat nějak takto:

![widget poznámkového bloku](./media/tutorial-train-models-with-aml/widget.png)

Pokud je potřeba zrušit běh, můžete postupovat podle [tyto pokyny](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Trénování modelu a monitorování probíhají na pozadí. Počkejte na dokončení trénování modelu, předtím, než spustíte další kód. Použití `wait_for_completion` po dokončení cvičení modelu:

```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Zobrazení výsledků spuštění

Teď je model vytrénovaný na vzdáleném clusteru. Načtěte přesnost modelu:

```python
print(run.get_metrics())
```

Výstup ukazuje, že vzdálený modelu má přesnost 0.9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

V dalším kurzu si projít tento model podrobněji.

## <a name="register-model"></a>Registrace modelu

Posledním krokem v cvičný skript napsal soubor `outputs/sklearn_mnist_model.pkl` v adresáři s názvem `outputs` ve virtuálním počítači clusteru, ve kterém se úloha spouští. `outputs` speciální adresář je v tom, že veškerý obsah v tomto adresáři je automaticky odeslána do pracovního prostoru. Tento obsah se objeví v záznamu spuštění v experimentu pod vaším pracovním prostorem. Proto se souborem modelu je nyní dostupná také v pracovního prostoru.

Můžete zobrazit soubory spojené s, která spustí:

```python
print(run.get_file_names())
```

Zaregistrujte model v pracovním prostoru tak, že vy nebo další spolupracovníci můžete později dotazování, zkoumat a nasazovat tento model:

```python
# register model
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také odstranit pouze cluster Azure Machine Learning Compute. Ale automatického škálování je zapnutý a minimální clusteru je nula. Tento konkrétní prostředek tak nebudou účtovat poplatky za další výpočetní není při použití:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Další postup

V tomto kurzu služby Azure Machine Learning používá Python pro následující úlohy:

> [!div class="checklist"]
> * Nastavení vývojového prostředí.
> * Přístup a prozkoumejte data.
> * Trénování modelů více ve vzdáleném clusteru pomocí oblíbených scikit-informace knihovna pro machine learning
> * Zkontrolujte podrobnosti o školení a zaregistrujte tento nejlepší model.

Jste připraveni nasadit tuto registrovanému modelu pomocí pokynů v další části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz 2: Nasazení modelů](tutorial-deploy-models-with-aml.md)
