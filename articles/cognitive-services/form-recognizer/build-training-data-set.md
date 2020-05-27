---
title: Postup sestavení sady školicích dat pro vlastní model – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Naučte se, jak zajistit, aby byla sada dat pro školení optimalizovaná pro školení modelu pro rozpoznávání formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 9342d87318eb6a5248c75d2333fb5e2a4cbef8f4
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873297"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Sestavení sady školicích dat pro vlastní model

Když použijete vlastní model pro rozpoznávání formulářů, budete mít k dispozici vlastní školicí data, aby model mohl vytvořit výukové formuláře pro konkrétní obor. 

Pokud provádíte školení bez ručních popisků, můžete použít 5 vyplněné formuláře nebo prázdnou formu (v názvu souboru musíte uvést slovo "Empty") a dva vyplněné formuláře. I v případě, že máte dost vyplněné formuláře, může přidání prázdného formuláře do sady školicích dat zlepšit přesnost modelu.

Pokud chcete použít manuálně popsání školicích dat, musíte začínat aspoň s 5 vyplněnými formuláři stejného typu. Kromě požadované datové sady můžete stále používat neoznačené formuláře a prázdný formulář.

## <a name="training-data-tips"></a>Tipy k datům školení

Je důležité použít datovou sadu optimalizovanou pro školení. Následující tipy vám pomohou zajistit, abyste získali nejlepší výsledky z operace [vlastního modelu vlaku](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) :

* Pokud je to možné, používejte textové dokumenty PDF namísto dokumentů na základě obrázků. Naskenované soubory PDF jsou zpracovávány jako obrázky.
* Pro vyplněné formuláře použijte příklady, které mají všechna jejich pole vyplněna.
* Používejte formuláře s různými hodnotami v každém poli.
* Pokud jsou obrázky z formuláře nižší kvality, použijte větší sadu dat (například obrázek 10-15).
* Celková velikost sady školicích dat může být až 500 stránek.

## <a name="general-input-requirements"></a>Obecné požadavky na vstupy

Ujistěte se, že vaše školicí data budou také postupovat podle požadavků na vstup pro veškerý obsah nástroje pro rozpoznávání formulářů. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Nahrajte školicí data.

Když se spojíte se sadou dokumentů formuláře, které budete používat pro školení, je nutné ji nahrát do kontejneru úložiště objektů BLOB v Azure. Pokud si nejste jisti, jak vytvořit účet služby Azure Storage pomocí kontejneru, postupujte podle pokynů [pro rychlý start Azure Storage pro Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Pokud chcete použít ručně označené údaje, budete také muset nahrát soubory *. labels. JSON* a *. OCR. JSON* , které odpovídají vašim školicím dokumentům. K vygenerování těchto souborů můžete použít [Nástroj pro označování ukázkových popisků](./quickstarts/label-tool.md) (nebo vlastní uživatelské rozhraní).

### <a name="organize-your-data-in-subfolders-optional"></a>Uspořádání dat v podsložkách (volitelné)

Ve výchozím nastavení budou rozhraní API pro [vlastní model výuky](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) používat jenom dokumenty formuláře, které jsou umístěné v kořenovém adresáři kontejneru úložiště. Můžete však s daty v podsložkách vlaky, pokud ji zadáte v volání rozhraní API. Obvykle tělo volání [vlastního modelu vlaku](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) má následující formát, kde `<SAS URL>` je adresa URL sdíleného přístupového podpisu vašeho kontejneru:

```json
{
  "source":"<SAS URL>"
}
```

Pokud do textu žádosti přidáte následující obsah, rozhraní API bude zaškolit dokumenty umístěné v podsložkách. `"prefix"`Pole je volitelné a omezí data školicí sady na soubory, jejichž cesty začínají daným řetězcem. Hodnota, například, `"Test"` způsobí, že rozhraní API bude zobrazovat pouze soubory nebo složky, které začínají slovem "test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit školicí sadu dat, postupujte podle rychlého startu a Naučte se vlastní model pro rozpoznávání formulářů a začněte ho používat na formulářích.

* [Výuka modelu a extrakce dat z formuláře pomocí kudrlinkou](./quickstarts/curl-train-extract.md)
* [Výuka modelu a extrakce dat formuláře pomocí REST API a Pythonu](./quickstarts/python-train-extract.md)
* [Výuka pomocí popisků pomocí ukázkového nástroje pro označování](./quickstarts/label-tool.md)
* [Trénování s popisky s využitím REST API a Pythonu](./quickstarts/python-labeled-data.md)
