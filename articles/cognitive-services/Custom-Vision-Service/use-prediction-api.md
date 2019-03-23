---
title: 'Příklad: Použití koncového bodu předpovědi pro programové testování imagí pomocí třídění – Custom Vision'
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít rozhraní API k programovému testování obrázků pomocí klasifikátoru služby Custom Vision Service.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: e50933ea0231b4be22c2d0f82d33fd02dd0918f5
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351605"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically"></a>Použití koncového bodu předpovědi k otestování Image prostřednictvím kódu programu

Po vytrénování modelu můžete programově testovat obrázky jejich odesláním do rozhraní API pro předpovědi.

> [!NOTE]
> Tento dokument ukazuje použití jazyka C# k odeslání obrázku do rozhraní API pro předpovědi. Další informace a příklady použití rozhraní API najdete v tématu pojednávajícím o [referencích k rozhraní API pro předpovědi](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Získání adresy URL a klíče předpovědi

Na [webové stránce služby Custom Vision](https://customvision.ai) vyberte svůj projekt a pak vyberte kartu __Výkon__. K zobrazení informací o použití rozhraní API pro předpovědi včetně __klíče předpovědi__ vyberte __URL předpovědi__. Pro projekty, které jsou připojené k prostředku Azure vaše __předpovědi klíč__ najdete také v [webu Azure portal](https://portal.azure.com) stránky pro přidružený prostředek Azure v rámci __klíče__. Zkopírujte následující informace pro použití v aplikaci:

* __URL__ pro použití se __souborem obrázku__.
* Hodnotu __Prediction-key__.

> [!TIP]
> Pokud máte více iterací, můžete určit používanou iteraci tím, že ji nastavíte jako výchozí. Vyberte iteraci v části __Iterace__ a potom v horní části stránky vyberte __Nastavit jako výchozí__.

![Karta výkonu zobrazená s červeným obdélníkem kolem adresy URL předpovědi.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Vytvoření aplikace

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci v jazyce C#.

1. Použijte následující kód jako obsah souboru __Program.cs__.

    > [!IMPORTANT]
    > Změňte následující informace:
    >
    > * Nastavte __obor názvů__ na název vašeho projektu.
    > * Nastavte hodnotu __Prediction-Key__, kterou jste získali dříve, v řádku, který začíná `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Nastavte hodnotu __URL__, kterou jste získali dříve, v řádku, který začíná `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>Použití aplikace

Při spuštění aplikace zadejte cestu k souboru obrázku. Obrázek je odeslán do rozhraní API a výsledky se vrátí jako dokument JSON. Následující kód JSON je příklad odpovědi.

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Další postup

[Export modelu pro mobilní použití](export-your-model.md)
