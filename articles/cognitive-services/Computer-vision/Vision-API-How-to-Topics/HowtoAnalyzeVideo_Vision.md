---
title: 'Příklad: Analýza videa v reálném čase – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí rozhraní API pro počítačové zpracování obrazu provádět analýzu snímků z živého video streamu v téměř reálném čase.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859070"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Jak analyzovat videa v reálném čase

Tato příručka ukazuje, jak provádět analýzu snímků z živého video streamu téměř v reálném čase. Takový systém funguje následovně:

- Pořídí snímky ze zdroje videa.
- Vybere snímky, které se mají analyzovat.
- Odešle tyto snímky do rozhraní API.
- Přijme jednotlivé výsledky analýzy vrácené z volání rozhraní API.

Tyto ukázky jsou napsané v jazyce C# a kód najdete na GitHubu tady: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Přístup

Problém s analýzou video streamů téměř v reálném čase se dá vyřešit několika způsoby. Začneme nastíněním tří přístupů s narůstající úrovní složitosti.

### <a name="a-simple-approach"></a>Jednoduchý přístup

Nejjednodušším návrhem systému pro analýzu téměř v reálném čase je nekonečná smyčka, kdy se v každé iteraci vezme snímek, analyzuje se a výsledek se následně přijme:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Pokud analýza spočívá v jednoduchém algoritmu na straně klienta, bude tento přístup vhodný. Pokud ale analýza probíhá v cloudu, může kvůli latenci trvat volání rozhraní API několik sekund, během kterých se nebudou zachytávat snímky a vlákno nebude dělat v podstatě nic. Maximální frekvence snímků je omezená latencí volání rozhraní API.

### <a name="parallelizing-api-calls"></a>Paralelizace volání rozhraní API

I když prostá smyčka s jedním vláknem dává smysl u jednoduchého algoritmu na straně klienta, nevyhovuje latenci cloudových volání rozhraní API. Tento problém je možné vyřešit tak, že umožníte, aby se dlouhá volání rozhraní API prováděla souběžně se zachytáváním snímků. V jazyce C# toho můžeme dosáhnout pomocí paralelismu na základě úloh, například:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Tento přístup spustí každou analýzu v samostatné úloze, která běží na pozadí, zatímco se nadále zachytávají nové snímky. Zabrání to sice blokování hlavního vlákna při čekání na návrat volání rozhraní API, ale přijdeme o určité záruky, které poskytovala jednoduchá verze – k několika voláním rozhraní API může dojít souběžně a výsledky se nemusejí vracet ve správném pořadí. Tento přístup může navíc znamenat, že několik vláken použije funkci ConsumeResult() souběžně, což může být nebezpečné, pokud tato funkce není bezpečná pro přístup z více vláken. Tento jednoduchý kód navíc nesleduje vytvářené úlohy, takže výjimky bez povšimnutí zmizí. Poslední ingrediencí, kterou potřebujeme přidat, je vlákno „příjemce“, které bude sledovat úlohy analýzy, vyvolávat výjimky, ukončovat dlouho běžící úlohy a zajišťovat, aby se výsledky přijímaly postupně a ve správném pořadí.

### <a name="a-producer-consumer-design"></a>Návrh typu producent-příjemce

Ve finálním systému „producent-příjemce“ je vlákno producenta, které se podobá předchozí nekonečné smyčce. Místo toho, aby příjemce přijal výsledky analýzy hned, jak jsou dostupné, jednoduše zařazuje úlohy do fronty, aby o nich měl přehled.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Je zde rovněž vlákno příjemce, které přebírá úlohy z fronty, čeká na jejich dokončení a pak buď zobrazí výsledek, nebo vyvolá vzniklou výjimku. Díky frontě můžeme zaručit, že se výsledky přijímají postupně a ve správném pořadí, a to bez omezení maximální frekvence snímků systému.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementace řešení

### <a name="getting-started"></a>Začínáme

Kvůli co nejrychlejšímu zprovoznění aplikace jsme implementovali výše popsaný systém, který je dostatečně flexibilní, aby vyhovoval různým situacím, ale zároveň se snadno používal. Tento kód můžete získat na adrese [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Tato knihovna obsahuje třídu FrameGrabber, která implementuje výše popsaný systém producent-příjemce ke zpracování snímků videa z webkamery. Uživatel může určit přesnou formu volání rozhraní API, přičemž tato třída informuje volající kód pomocí událostí o pořízení nového snímku nebo dostupnosti výsledku analýzy.

Některé možnosti ilustrují dvě ukázkové aplikace, které tuto knihovnu používají. První je jednoduchá konzolová aplikace, jejíž zjednodušená verze je uvedená níže. Zachytává snímky z výchozí webkamery a odesílá je do rozhraní API pro rozpoznávání tváře za účelem detekce obličeje.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

Druhá ukázková aplikace je o něco zajímavější a umožňuje zvolit, které rozhraní API se má na snímky videa zavolat. Tato aplikace zobrazuje na levé straně náhled živého videa a na pravé straně ukazuje poslední výsledek rozhraní API překrývající odpovídající snímek.

Ve většině režimů bude mezi živým videem vlevo a vizualizovanou analýzou vpravo viditelné zpoždění. Toto zpoždění představuje dobu, jakou trvá volání rozhraní API. Výjimkou je režim EmotionsWithClientFaceDetect, který provádí detekci obličeje místně na klientském počítači pomocí OpenCV před odesláním obrázků do služeb Cognitive Services. Díky tomu můžeme detekovanou tvář vizualizovat okamžitě a emoce aktualizovat později, jakmile se vrátí volání rozhraní API. To demonstruje možnost „hybridního“ přístupu, kdy je možné určité jednoduché zpracování provést na straně klienta, a v případě potřeby je rozšířit o pokročilejší analýzu pomocí rozhraní API služeb Cognitive Services.

![Snímek obrazovky aplikace LiveCameraSample zobrazující obrázek se zobrazenými značkami](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do základu kódu

Pokud chcete s touto ukázkou začít, postupujte takto:

1. Získejte klíče rozhraní API pro zpracování obrazu v oblasti [Předplatná](https://azure.microsoft.com/try/cognitive-services/). U analýzy snímků videa jde o tato rozhraní API:
    - [Rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Naklonujte úložiště [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

3. Otevřete ukázku v aplikaci Visual Studio 2015 nebo novější, sestavte a spusťte ukázkové aplikace:
    - Klíč rozhraní API pro rozpoznávání tváře v aplikaci BasicConsoleSample je pevně zakódovaný v souboru [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - U aplikace LiveCameraSample se klíče zadávají do panelu s nastavením aplikace. Uloží se pro následné relace jako uživatelská data.

Až budete připravení integrovat, **stačí do vlastních projektů přidat odkaz na knihovnu VideoFrameAnalyzer**.

Funkce knihovny VideoFrameAnalyzer pro rozpoznávání obrazu, hlasu, videa nebo textu využívají služby Azure Cognitive Services. Microsoft bude přijímat obrázky, zvuk, video a další data, která (přes tuto aplikaci) nahrajete, a může je používat pro účely vylepšování služeb. Žádáme vás o pomoc při ochraně uživatelů, jejichž data vaše aplikace odesílá do služeb Azure Cognitive Services.

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, jak spustit analýzu v reálném čase s využitím obličeje a Počítačové zpracování obrazu rozhraní API a jak můžete začít používat náš vzorový kód. Svou vlastní aplikaci můžete začít sestavovat s využitím bezplatných klíčů rozhraní API na [registrační stránce služby Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Nestyďte se podělit o své názory a návrhy v [úložišti GitHubu](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), případně o obecnější názory na rozhraní API na našem[webu UserVoice](https://cognitive.uservoice.com/).

