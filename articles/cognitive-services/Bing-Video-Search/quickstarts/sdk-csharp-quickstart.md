---
title: 'Rychlý start: Hledat pomocí sady SDK pro vyhledávání Bingu videa pro videaC#'
titleSuffix: Azure Cognitive Services
description: Použít tento rychlý start k odesílání požadavků pro vyhledávání videí pomocí Bingu videa hledání sady SDK pro C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 3673f18ff38b2ae98180f470b9f76f1fc57ee8b6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442539"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Rychlý start: Provést hledání videí pomocí Bingu videa hledání sady SDK proC#

V tomto rychlém startu můžete zahájit hledání zpráv pomocí sady SDK pro vyhledávání Bingu videa pro C#. Při vyhledávání videí Bingu je kompatibilní s Většina programovacích jazyků rozhraní REST API, sada SDK poskytuje snadný způsob, jak do svých aplikací integrovat službu. Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) pomocí dalších poznámek a funkcí.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice [sady Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/).
* Rozhraní Json.NET, k dispozici [jako balíček NuGet](https://www.nuget.org/packages/Newtonsoft.Json/).

Chcete-li přidat do projektu sady SDK Video vyhledávání Bingu, **spravovat balíčky NuGet** z **Průzkumníka řešení** v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

Instalace [[balíčku NuGet Video Search SDK]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) nainstaluje taky následující závislosti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nový C# konzole řešení v sadě Visual Studio. Pak přidejte následující kód do souboru hlavní kód.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Vytvořit instanci klienta tak, že vytvoříte nový `ApiKeyServiceClientCredentials` objektu s klíči předplatného a volání konstruktoru.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Odeslat žádost o vyhledávání a zpracování výsledků

1. Použití klienta odeslat žádost o vyhledávání. "SwiftKey" pomocí vyhledávacího dotazu.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Pokud nebyly vráceny žádné výsledky, získat první z nich s `videoResults.Value[0]`. Poté vytiskněte ID, název a adresu url videa.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace v jediné stránce](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Další informace najdete v tématech 

* [Co je API pro vyhledávání videí Bingu?](../overview.md)
* [Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
