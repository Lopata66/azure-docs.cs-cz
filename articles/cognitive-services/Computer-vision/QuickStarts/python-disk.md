---
title: 'Rychlý start: Analyzujte místní image - REST, Python'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete analyzovat místní obrázek pomocí rozhraní API pro počítačové zpracování obrazu a Pythonu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: feba14f3557ac5d09c0455b02a9df64872f632c1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342081"
---
# <a name="quickstart-analyze-a-local-image-using-the-rest-api-and-python-in-computer-vision"></a>Rychlý start: Analýza místní image pomocí rozhraní REST API a Python v počítačové zpracování obrazu

V tomto rychlém startu analyzujete obrázek uložený místně za účelem extrakce vizuálních prvků pomocí rozhraní REST API počítačového zpracování obrazu. Pomocí metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální prvky na základě obsahu obrázku.

Tento rychlý start můžete spustit jako podrobný návod pomocí Jupyter Notebooku na webu [MyBinder](https://mybinder.org). Pokud chcete spustit Binder, vyberte následující tlačítko:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Pokud chcete spustit tuto ukázku v místním prostředí, musíte mít nainstalovaný jazyk [Python](https://www.python.org/downloads/).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Můžete získat bezplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k přihlášení pro počítačové zpracování obrazu a získejte klíč.
- Musíte mít nainstalované následující balíčky Pythonu. Můžete použít [pip](https://packaging.python.org/tutorials/installing-packages/) nainstalovat balíčky Pythonu.
    - požadavků
    - [matplotlib](https://matplotlib.org/)
    - [pillow](https://python-pillow.org/)

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Proveďte v kódu na příslušných místech následující změny:
    1. Hodnotu `subscription_key` nahraďte klíčem předplatného.
    1. Hodnotu `vision_base_url` nahraďte adresou URL koncového bodu prostředku počítačového zpracování obrazu z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Volitelně můžete hodnotu `image_path` nahradit cestou a názvem souboru jiného obrázku, který chcete analyzovat.
1. Uložte kód jako soubor s příponou `.py`. Například, `analyze-local-image.py`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python analyze-local-image.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

analyze_url = vision_base_url + "analyze"

# Set image_path to the local path of an image that you want to analyze.
image_path = "C:/Documents/ImageToAnalyze.jpg"

# Read the image into a byte array
image_data = open(image_path, "rb").read()
headers = {'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-Type': 'application/octet-stream'}
params = {'visualFeatures': 'Categories,Description,Color'}
response = requests.post(
    analyze_url, headers=headers, params=params, data=image_data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(image_data))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková webová stránka provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už soubor nepotřebujete, odstraňte ho.

## <a name="next-steps"></a>Další postup

Prozkoumejte aplikaci v Pythonu používající počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytváření chytře oříznutých miniatur, zjišťování, kategorizaci, označení a popis vizuálních prvků, včetně obličejů, v obrázku. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](../Tutorials/PythonTutorial.md)
