---
title: 'Spustit skript jazyka Python: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul spuštění skriptu Pythonu v Azure Machine Learning ke spouštění kódu Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: 9b2114672db755efba1818505c8f399ac01aea71
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983597"
---
# <a name="execute-python-script-module"></a>Spustit modul Python Script

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul použijte ke spuštění kódu Pythonu. Další informace o architektuře a zásadách návrhu Pythonu najdete v [následujícím článku](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Pomocí Pythonu můžete provádět úlohy, které aktuálně nejsou podporované existujícími moduly, například:

+ Vizualizace dat pomocí`matplotlib`
+ Vytvoření výčtu datových sad a modelů v pracovním prostoru pomocí knihoven Pythonu
+ Čtení, načítání a manipulace s daty ze zdrojů, které nepodporují modul [Import dat](./import-data.md)
+ Spusťte si vlastní kód hloubkového učení. 


Azure Machine Learning používá Anaconda distribuci Pythonu, která zahrnuje mnoho běžných nástrojů pro zpracování dat. Verze Anaconda se aktualizuje automaticky. Aktuální verze je:
 -  Anaconda 4.5 + distribuce pro Python 3,6 

Předem nainstalované balíčky jsou:
-    ADAL = = 1.2.2
-    ApplicationInsights = = 0.11.9
-    attrs = = 19.3.0
-    Azure – běžné = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure – graphrbac = = 0.61.1
-    Azure-identity = = 1.3.0
-    Azure-Správa-autorizace = = 0.60.0
-    Azure-Správa – containerregistry = = 2.8.0
-    Azure-Správa – Trezor klíčů = = 2.2.0
-    Azure-Správa-prostředek = = 8.0.1
-    Azure-Správa-Storage = = 8.0.0
-    Azure-Storage-BLOB = = 1.5.0
-    Azure-Storage-Common = = 1.4.2
-    AzureML-Core = = 1.1.5.5
-    AzureML-dataprep-Native = = 14.1.0
-    AzureML-dataprep = = 1.3.5
-    AzureML – výchozí = = 1.1.5.1
-    AzureML-Designer-Classic-modules = = 0.0.118
-    AzureML-Designer-Core = = 0.0.31
-    AzureML-Designer-Internal = = 0.0.18
-    AzureML-Model-Management-SDK = = 1.0.1 B6. post1
-    AzureML-Pipeline-Core = = 1.1.5
-    AzureML-telemetrie = = 1.1.5.3
-    neporty. tempFile = = 1.0
-    neports. WeakRef = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    certifi = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    klikněte na = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    kryptografie = = 2.8
-    koloběh = = 0.10.0
-    Dill = = 0.3.1.1
-    distribuce = = 1.4.0
-    Docker = = 4.2.0
-    Docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    Baňka = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google – obnovitelné – médium = = 0.5.0
-    googleapis – společný – proto = = 1.51.0
-    Gunicorn = = 19.9.0
-    IDNA = = 2.9
-    nevyvážené – Další informace = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON – protokolování-py = = 0,2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    liac-arff = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    more – itertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    PANDAS = = 0.25.3
-    pathSpec = = 0.7.0
-    PIP = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python – dateutil = = 2.8.1
-    pytz = = 2019.3
-    požadavky – oauthlib = = 1.3.0
-    požadavky = = 2.23.0
-    RSA = = 4.0
-    ruamel. yaml = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit-učení = = 0.22.2
-    scipy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    šest = = 1.14.0
-    Smart-Open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket – klient = = 0.57.0
-    Werkzeug = = 0.16.1
-    kolo = = 0.34.2

 Pokud chcete nainstalovat další balíčky, které nejsou v seznamu předem nainstalovaných, například *scikit-misc*, přidejte do skriptu následující kód: 

 ```python
import os
os.system(f"pip install scikit-misc")
```
> [!NOTE]
> Pokud váš kanál obsahuje více modulů skriptu Pythonu pro spouštění a potřebujete stejné balíčky, které nejsou v seznamu předem nainstalované, nainstalujte balíčky do každého modulu. 

## <a name="upload-files"></a>Nahrání souborů
**Skript spouštějící Python** podporuje nahrávání souborů pomocí [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

Následující příklad ukazuje, jak nahrát soubor obrázku do modulu **spouštění skriptu Pythonu** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Po dokončení spuštění kanálu můžete zobrazit náhled obrázku na pravém panelu modulu.

> [!div class="mx-imgBorder"]
> ![Nahráno – obrázek](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Jak nakonfigurovat skript spustit v Pythonu

Modul **spuštění skriptu Pythonu** obsahuje ukázkový kód Pythonu, který můžete použít jako výchozí bod. Pokud chcete nakonfigurovat modul **spouštění skriptu Pythonu** , zadáte sadu vstupů a kódů Pythonu, které se spustí v textovém poli **skript Pythonu** .

1. Přidejte modul **spuštění skriptu Pythonu** do vašeho kanálu.

2. Přidejte a připojte se k **DataSet1.** datových sad z návrháře, který chcete použít pro vstup. Odkazujte tuto datovou sadu ve skriptu Pythonu jako **DataFrame1**.

    Použití datové sady je volitelné, pokud chcete generovat data pomocí Pythonu, nebo použít kód Pythonu k importu dat přímo do modulu.

    Tento modul podporuje přidání druhé datové sady na **Dataset2**. Odkaz na druhou datovou sadu ve skriptu Pythonu jako DataFrame2.

    Datové sady, které jsou uložené v Azure Machine Learning, se automaticky převedou na data **PANDAS** . po načtení do tohoto modulu se snímky načítají.

    ![Spustit vstupní mapu Pythonu](media/module/python-module.png)

4. Pokud chcete zahrnout nové balíčky nebo kód v Pythonu, přidejte do **svazku skriptu**soubor zip, který obsahuje tyto vlastní prostředky. **Sada prostředků skriptu** musí být soubor zip odeslaný do vašeho pracovního prostoru jako datová sada typu souboru. Datovou sadu můžete nahrát na stránce Asset **sady** dat a můžete ji přetáhnout ze seznamu **Moje datové sady** na levém stromu modulu na stránce vytváření návrháře. 

    Během provádění kanálu lze použít jakýkoli soubor obsažený v odeslaném archivu zip. Pokud archiv obsahuje adresářovou strukturu, struktura se zachová, ale musíte do cesty předřadit adresář **Src** .

5. Do textového pole **skript Pythonu** zadejte nebo vložte platný skript Pythonu.

    > [!NOTE]
    > Při psaní skriptu Buďte velmi opatrní a ujistěte se, že nedochází k žádné chybě syntaxe, jako je například použití nedeklarovaných objektů nebo neimportovaných modulů. Také věnujte zvláštní pozornost seznamu předem nainstalovaných modulů. Pokud chcete importovat moduly, které nejsou v seznamu, nainstalujte odpovídající balíčky do skriptu, například
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    Textové pole **skriptu Pythonu** se předem vyplní některými pokyny v komentářích a vzorový kód pro přístup k datům a výstup. Tento kód musíte upravit nebo nahradit. Nezapomeňte dodržovat konvence Pythonu týkající se odsazení a používání velkých a malých písmen.

    + Skript musí obsahovat funkci s názvem `azureml_main` jako vstupní bod pro tento modul.
    + Funkce vstupního bodu musí mít dva vstupní argumenty: `Param<dataframe1>` a, `Param<dataframe2>`a to i v případě, že se tyto argumenty ve skriptu nepoužívají.
    + Soubory zip připojené k třetímu vstupnímu portu jsou komprimovány a uloženy v adresáři, `.\Script Bundle`který je také přidán do jazyka Python `sys.path`. 

    Proto pokud váš soubor zip obsahuje `mymodule.py`, importujte ho pomocí. `import mymodule`

    + Do návrháře lze vrátit dvě datové sady, které musí být sekvence typu `pandas.DataFrame`. V kódu Pythonu můžete vytvořit další výstupy a zapsat je přímo do služby Azure Storage.

6. Odešlete kanál, nebo vyberte modul a klikněte na **Spustit vybraný** , aby se spouštěl jenom skript Pythonu.

    Všechna data a kód se načtou do virtuálního počítače a spustí se pomocí zadaného prostředí Pythonu.

## <a name="results"></a>Výsledky

Výsledky jakýchkoli výpočtů provedených integrovaným kódem Pythonu musí být poskytnuty jako PANDAS. Datový rámec, který je automaticky převeden do formátu datové sady Azure Machine Learning, abyste mohli výsledky použít s jinými moduly v kanálu.

Modul vrací dvě datové sady:  
  
+ **Datová sada výsledků 1**definovaná prvním vráceným pandasm datovým rámcem ve skriptu Pythonu

+ **Výsledná datová sada 2**definovaná druhým vráceným pandasm datovým snímkem ve skriptu Pythonu


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 