---
title: 'Rychlý Start: výuka s popisky pomocí REST API a Pythonu – rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí REST API a Pythonu využít funkci rozpoznávání formulářů s popisky, abyste mohli naučit vlastní model.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 59f969a920c30bb017e10d2aa233df02d69918e2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116910"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Výukový model pro rozpoznávání formulářů s popisky pomocí REST API a Pythonu

V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s Pythonem k učení vlastního modelu s ručně označenými daty. Další informace o této funkci najdete v části [výuka s visačkami](../overview.md#train-with-labels) v přehledu.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu musíte mít:
- Je nainstalovaný [Python](https://www.python.org/downloads/) (Pokud chcete spustit ukázku místně).
- Sada alespoň šest forem stejného typu. Tato data použijete ke školení modelu a testování formuláře. Pro tento rychlý Start můžete použít [ukázkovou datovou sadu](https://go.microsoft.com/fwlink/?linkid=2090451) . Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů BLOB v účtu Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Nastavení školicích dat

Dále budete muset nastavit požadovaná vstupní data. Funkce s popisky dat má speciální požadavky na vstup nad rámec toho, co je potřeba k učení vlastního modelu bez popisků.

Ujistěte se, že všechny školicí dokumenty mají stejný formát. Pokud máte formuláře v několika formátech, uspořádejte je do podsložek v závislosti na společném formátu. Při učení budete muset rozhraní API nasměrovat do podsložky.

Aby bylo možné vytvořit model pomocí popisků dat, budete potřebovat následující soubory jako vstupy v podsložce. Naučíte se, jak vytvořit tento soubor níže.

* **Zdrojové formuláře** – formuláře pro extrakci dat. Podporované typy jsou JPEG, PNG, PDF nebo TIFF.
* **Soubory rozložení OCR** – jedná se o soubory JSON, které popisují velikosti a pozice veškerého čitelného textu v každém zdrojovém formuláři. K vygenerování těchto dat použijete rozhraní API pro rozložení pro rozpoznávání formuláře. 
* **Soubory popisků** – jedná se o soubory JSON, které popisují popisky dat, které uživatel zadal ručně.

Všechny tyto soubory by měly zabírat stejnou podsložku a musí být v následujícím formátu:

* input_file1. PDF 
* input_file1. PDF. OCR. JSON
* input_file1. PDF. labels. JSON 
* input_file2. PDF 
* input_file2. PDF. OCR. JSON
* input_file2. PDF. labels. JSON
* ...

> [!TIP]
> Při označování formulářů pomocí [Nástroje pro označování ukázkových popisků](./label-tool.md)ve formě, nástroj vytvoří tyto soubory rozložení popisků a OCR automaticky.

### <a name="create-the-ocr-output-files"></a>Vytvoření výstupních souborů OCR

Chcete-li, aby služba mohla zvážit odpovídající vstupní soubory pro školení s označením, potřebujete soubory výsledků OCR. Chcete-li získat výsledky OCR pro daný zdrojový formulář, postupujte podle následujících kroků:

1. Zavolejte rozhraní API **[analyzovat rozložení](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** v kontejneru čtení rozložení se vstupním souborem jako součást textu žádosti. Uloží ID nalezené v hlavičce **operace – umístění** odpovědi.
1. Pomocí ID operace z předchozího kroku zavolejte rozhraní API **[výsledků získat analýzu rozložení](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** .
1. Získejte odpověď a zapište obsah do souboru. Pro každý zdrojový formulář by měl mít odpovídající soubor optického rozpoznávání znaků připojený původní název souboru `.ocr.json` . Výstup ve formátu JSON OCR by měl mít následující formát. Úplný příklad najdete v [ukázkovém souboru OCR](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) . 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Vytvoření souborů popisků

Soubory popisků obsahují přidružení klíč-hodnota, která uživatel zadal ručně. Jsou nutné pro školení s popisky dat, ale ne každý zdrojový soubor musí mít odpovídající soubor popisku. Zdrojové soubory bez popisků se budou považovat za běžné školicí dokumenty. Pro spolehlivé školení doporučujeme pět nebo více označených souborů. K vygenerování těchto souborů můžete použít nástroj uživatelského rozhraní, jako je [ukázkový nástroj pro popisky](./label-tool.md) .

Při vytváření souboru popisků můžete volitelně zadat oblasti s &mdash; přesným umístěním hodnot v dokumentu. Tím zajistíte i vyšší přesnost školení. Oblasti jsou formátovány jako sada osmi hodnot, které odpovídají čtyřm souřadnicím X, Y: vlevo nahoře, vpravo nahoře, dole vpravo a vlevo dole. Hodnoty souřadnic jsou mezi nulou a jednou škálované na rozměry stránky.

Pro každý zdrojový formulář by měl mít odpovídající soubor popisku příponu původního souboru s `.labels.json` příponou. Soubor popisku by měl mít následující formát. Úplný příklad najdete v [souboru se vzorovým popiskem](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) .

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> Můžete použít pouze jeden popisek na každý textový prvek a každý popisek lze použít pouze jednou na stránku. Popisek nelze použít na více stránkách.


## <a name="train-a-model-using-labeled-data"></a>Výuka modelu pomocí popiskových dat

Chcete-li vytvořit model s označením dat, zavolejte rozhraní API **[vlastního modelu vlaku](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** spuštěním následujícího kódu Pythonu. Před spuštěním kódu proveďte tyto změny:

1. Nahraďte `<Endpoint>` adresou URL koncového bodu pro prostředek pro rozpoznávání formulářů.
1. Nahraďte `<SAS URL>` adresou URL sdíleného přístupového podpisu (SAS) kontejneru úložiště objektů BLOB v Azure. Pokud chcete načíst adresu URL SAS, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tvar: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
1. Nahraďte `<Blob folder name>` názvem složky ve vašem kontejneru objektů blob, kde se nacházejí vstupní data. Nebo, pokud jsou vaše data v kořenovém adresáři, nechejte toto pole prázdné a odeberte `"prefix"` pole z těla požadavku HTTP.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```

## <a name="get-training-results"></a>Získat výsledky školení

Po spuštění operace vlaku použijete vrácené ID k získání stavu operace. Do dolní části skriptu Pythonu přidejte následující kód. Použije se hodnota ID z školicího hovoru v novém volání rozhraní API. Operace školení je asynchronní, takže tento skript volá rozhraní API v pravidelných intervalech, dokud není stav školení dokončeno. Doporučujeme interval jednoho sekundy nebo více.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Po dokončení procesu školení obdržíte `201 (Success)` odpověď s obsahem JSON podobným následujícímu. Odpověď byla zkrácena pro zjednodušení.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Zkopírujte `"modelId"` hodnotu pro použití v následujících krocích.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Po dokončení procesu obdržíte `202 (Success)` odpověď s obsahem JSON v následujícím formátu. Odpověď byla zkrácena pro zjednodušení. Hlavní přidružení klíč/hodnota jsou v `"documentResults"` uzlu. Výsledky rozhraní API rozložení (obsah a pozice veškerého textu v dokumentu) jsou v `"readResults"` uzlu.

```json
{ 
    "analyzeResult":{ 
      "version":"2.0.0",
      "readResults":[ 
        { 
          "page":1,
          "language":"en",
          "angle":0,
          "width":8.5,
          "height":11,
          "unit":"inch",
          "lines":[ 
            { 
              "language":"en",
              "boundingBox":[ 
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
          ]
        }
      ],
      "pageResults":[ 
        { 
          "page":1,
          "tables":[ 
            { 
              "rows":2,
              "columns":6,
              "cells":[ 
                { 
                  "rowIndex":0,
                  "columnIndex":0,
                  "text":"Invoice Number",
                  "boundingBox":[ 
                    0.5075,
                    2.8088,
                    1.9061,
                    2.8088,
                    1.9061,
                    3.3219,
                    0.5075,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/8/words/0",
                    "#/readResults/0/lines/8/words/1"
                  ]
                },
                ...        
              ]
            }
          ]
        }
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/15/words/0"
              ]
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/12/words/0"
              ]
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                "#/analyzeResult/readResults/0/lines/16/words/0"
              ]
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Zlepšení výsledků

Prověřte `"confidence"` hodnoty pro každý výsledek klíč/hodnota pod `"documentResults"` uzlem. Měli byste se také podívat na skóre spolehlivosti v `"readResults"` uzlu, který odpovídá operaci rozložení. Spolehlivost výsledků rozložení neovlivňuje spolehlivost výsledků extrakce klíč/hodnota, proto byste měli zaškrtnout obě.
* Pokud jsou skóre spolehlivosti pro operaci rozložení nízká, zkuste zlepšit kvalitu vstupních dokumentů (viz [požadavky na vstupy](../overview.md#input-requirements)).
* Pokud jsou skóre spolehlivosti pro operaci extrakce klíč/hodnota nízká, zajistěte, aby analyzované dokumenty byly stejného typu jako dokumenty používané v sadě školení. Pokud se v dokumentech v sadě školení nacházejí změny, zvažte jejich rozdělení do různých složek a školení jednoho modelu pro každou variaci.

### <a name="avoid-cluttered-labels"></a>Vyhnout se zbytečným popiskům

Když někdy použijete jiné popisky v rámci stejného řádku textu, služba může tyto popisky sloučit do jednoho pole. Například v adrese můžete označit město, stát a PSČ jako různá pole, ale během předpovědi tato pole nejsou rozpoznána samostatně.

Chápeme, že tento scénář je pro naše zákazníky nezbytný a pracujeme na jeho vylepšení v budoucnu. V současné době doporučujeme našim uživatelům označit více nezpracovaných polí jako jedno pole a pak tyto výrazy oddělit při následném zpracování výsledků extrakce.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí nástroje pro rozpoznávání formulářů REST API s Pythonem naučit model s ručně označenými daty. Dále si přečtěte referenční dokumentaci rozhraní API a prozkoumejte rozhraní API pro rozpoznávání formulářů podrobněji.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeReceiptAsync)