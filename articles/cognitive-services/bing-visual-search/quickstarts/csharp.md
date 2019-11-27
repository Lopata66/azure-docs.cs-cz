---
title: 'Rychlý Start: Získání přehledů obrázků pomocí REST API C# a-vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat přehled o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: scottwhi
ms.openlocfilehash: 82c1159aca51bc30839f5380a414bd2b3b488bb8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383633"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Rychlý Start: Získání přehledů obrázků pomocí Vizuální vyhledávání Bingu REST API aC#

Tento rychlý Start ukazuje, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a zobrazit přehledy, které vrátí.

## <a name="prerequisites"></a>Požadavky

* Všechny edice sady [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* [Rozhraní JSON.NET](https://www.newtonsoft.com/json), které je k dispozici jako balíček NuGet.
* Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. V aplikaci Visual Studio vytvořte nové řešení konzoly s názvem BingSearchApisQuickStart. Do hlavního souboru kódu přidejte následující obory názvů:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Přidejte proměnné pro klíč předplatného, koncový bod a cestu k imagi, kterou chcete nahrát:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Vytvořte metodu s názvem `GetImageFileName()`, abyste získali cestu k imagi:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Vytvořte metodu pro získání binárních dat obrázku:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Sestavení dat formuláře

Chcete-li nahrát místní obrázek, nejprve Sestavte data formuláře k odeslání do rozhraní API. Data formuláře musí zahrnovat hlavičku `Content-Disposition`, její parametr `name` musí být nastaven na hodnotu "image" a parametr `filename` lze nastavit na libovolný řetězec. Obsah formuláře obsahuje binární data obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Přidejte řetězce hranice pro formátování dat formuláře POST. Řetězce hranice určují znaky začátku, konce a nového řádku pro data:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. K přidání parametrů do dat formuláře použijte následující proměnné:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Vytvořte funkci s názvem `BuildFormDataStart()` pro vytvoření začátku dat formuláře pomocí řetězců hranic a cesty k obrázku:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Vytvořte funkci s názvem `BuildFormDataEnd()` pro vytvoření konce dat formuláře pomocí řetězců hranic:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Volání rozhraní API pro vizuální vyhledávání Bingu

1. Vytvořte funkci pro volání koncového bodu Vizuální vyhledávání Bingu a vraťte odpověď JSON. Funkce bere na začátku a na konci data formuláře, bajtové pole obsahující data obrázku a hodnotu `contentType`.

2. K uložení identifikátoru URI, hodnoty contentType a hlaviček použijte `WebRequest`.  

3. Pomocí `request.GetRequestStream()` Zapište data formuláře a obrázku a pak Získejte odpověď. Vaše funkce by měla být podobná té následující:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Vytvoření metody Main

1. V metodě `Main` vaší aplikace Získejte název souboru a binární data vaší image:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Nastavte tělo příspěvku tak, že naformátujete jeho hranici. Potom zavolejte `startFormData()` a `endFormData` vytvořte data formuláře:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Vytvoření `ContentType` hodnoty formátováním `CONTENT_TYPE_HEADER_PARAMS` a hranicí dat formuláře:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Získejte odpověď rozhraní API voláním `BingImageSearch()` a vytiskněte odpověď:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Jak použít HTTPClient

Použijete-li `HttpClient`, můžete použít třídu `MultipartFormDataContent` k sestavení dat formuláře. Pouze pomocí následujících sekcí kódu nahraďte odpovídající metody v předchozím příkladu.

Nahraďte metodu `Main` tímto kódem:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Nahraďte metodu `BingImageSearch` tímto kódem:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování](../tutorial-bing-visual-search-single-page-app.md)
