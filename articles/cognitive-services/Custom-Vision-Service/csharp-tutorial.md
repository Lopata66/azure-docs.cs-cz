---
title: 'Rychlý start: Vytvoření projektu klasifikace obrázků s vlastní Vision SDK proC#'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a vytvořte předpověď pomocí sady .NET SDK a jazyka C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 9b6d01908265791a83ee311375fa50fcca995f79
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472718"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Rychlý start: Vytvoření projektu klasifikace obrázků s využitím Custom Vision .NET SDK

Tento článek obsahuje informace a vzorový kód, které vám pomůžou začít s vytvořením modelu klasifikace obrázků pomocí sady Custom Vision SDK a jazyka C#. Po jeho vytvoření můžete přidat značky, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít tento koncový bod k programovému testování obrázku. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v .NET. Pokud chcete procesem vytvoření a používání modelu klasifikace projít _bez_ kódu, přečtěte si místo toho [pokyny s využitím prohlížeče](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Získání sady Custom Vision SDK a vzorového kódu

K napsání aplikace v .NET, která využívá službu Custom Vision, budete potřebovat balíčky NuGet pro službu Custom Vision. Tyto balíčky jsou součástí ukázkového projektu, který si stáhnete, ale tady je můžete získat samostatně.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Naklonujte nebo si stáhněte projekt [Ukázky pro Cognitive Services v .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Přejděte do složky **CustomVision/ImageClassification** a otevřete soubor _ImageClassification.csproj_ v sadě Visual Studio.

Tento projekt sady Visual Studio vytvoří nový projekt služby Custom Vision s názvem __My New Project__, který bude přístupný na [webu služby Custom Vision](https://customvision.ai/). Potom nahraje obrázky k trénování a testování klasifikátoru. V tomto projektu je účelem klasifikátoru určit, jestli je strom __jedlovec__ nebo __sakura__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Vysvětlení kódu

Otevřete soubor _Program.cs_ a prozkoumejte kód. Do odpovídajících definic v metodě **Main** vložte své klíče předplatného.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Parametr koncový bod by měl odkazovat na oblasti, kde se skupina prostředků Azure obsahující prostředky Custom Vision vytvořil v. V tomto příkladu budeme předpokládat oblasti střed USA – jih a použít:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

Následující řádky kódu provádějí primární funkce projektu.

### <a name="create-a-new-custom-vision-service-project"></a>Vytvoření nového projektu služby Custom Vision

Vytvořený projekt se zobrazí na [webu služby Custom Vision](https://customvision.ai/), který jste navštívili dříve. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Nahrání a označení obrázků

Součástí tohoto projektu jsou i obrázky. Odkazuje se na ně v metodě **LoadImagesFromDisk** v souboru _Program.cs_.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Klasifikátor trénovat a publikovat

Tento kód vytvoří první iterace v projektu a ke koncovému bodu predikcí následně publikuje danou iteraci. Název zadaný pro publikované iterace lze použít k odesílání požadavků předpovědi. Iterace není k dispozici v koncovém bodě predikcí, dokud je publikována.

```csharp
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Nastavení koncového bodu předpovědi

Koncový bod předpovědi je odkaz, pomocí kterého můžete odeslat obrázek do aktuálního modelu a získat předpověď klasifikace.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Odeslání obrázku do výchozího koncového bodu předpovědi

V tomto skriptu se v metodě **LoadImagesFromDisk** načte testovací obrázek a v konzole se zobrazí výstup předpovědi modelu.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Když je aplikace spuštěná, mělo by se otevřít okno konzoly s následujícím výstupem:

```
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Pak můžete ověřit správné označení testovacího obrázku (ve složce **Images/Test/**). Aplikaci ukončete stisknutím libovolné klávesy. Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další postup

Právě jste viděli, jak se dají jednotlivé kroky procesu klasifikace obrázků provádět v kódu. Tato ukázka provede jednu iteraci trénování, ale často je potřeba model trénovat a testovat vícekrát, aby byl přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
