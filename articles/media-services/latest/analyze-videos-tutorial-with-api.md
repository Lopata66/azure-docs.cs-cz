---
title: Analýza videa pomocí Media Services pomocí rozhraní .NET – Azure | Dokumentace Microsoftu
description: Pokud chcete analyzovat video pomocí služby Azure Media Services, postupujte podle kroků v tomto kurzu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 48d08af1c33eaddd8d963d9e1ab0322c3972997e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103529"
---
# <a name="tutorial-analyze-videos-with-media-services-v3-using-net"></a>Kurz: Analýza videa pomocí Media Services v3 pomocí .NET

Tento kurz vám ukáže, jak analyzovat video pomocí služby Azure Media Services. Existuje mnoho případů, ve kterých můžete potřebovat získat podrobnější přehled o zaznamenaném video nebo audio obsahu. Pokud chtějí organizace například dosáhnout vyšší spokojenosti zákazníků, mohou převést záznamy řeči zákaznické podpory na text a vytvořit prohledávatelný katalog s rejstříky a řídicími panely. Pak může získat přehled o jejich firmy, jako je například seznam častých stížností, zdrojům stížnosti a další užitečné informace.

V tomto kurzu získáte informace o následujících postupech:    

> [!div class="checklist"]
> * Stažení ukázkové aplikace popsané v tématu
> * Kontrola kódu, který analyzuje dané video
> * Spuštění aplikace
> * Prozkoumání výstupu
> * Vyčištění prostředků

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte nainstalovanou sadu Visual Studio, můžete získat sadu [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Vytvoření účtu Media Services](create-account-cli-how-to.md).<br/>Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti.
- Postupujte podle kroků v [rozhraní API k přístupu k Azure Media Services pomocí Azure CLI](access-api-cli-how-to.md) a uložte přihlašovací údaje. Je potřeba použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Do svého počítače naklonujte pomocí následujícího příkazu úložiště GitHub obsahující ukázku .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Tato ukázka se nachází ve složce [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Otevřít [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) stažený projekt. Nahraďte hodnoty s přihlašovacími údaji, které jste získali z [přístup k rozhraní API](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Kontrola kódu, který analyzuje dané video

Tato část prozkoumává funkce definované v souboru [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) z projektu *AnalyzeVideos*.

Tato ukázka provede následující akce:

1. Vytvoří **transformace** a **úlohy** , která analyzuje vaše video.
2. Vytvoří vstup **Asset** a nahraje video do něj. Prostředek se použije jako vstup úlohy.
3. Vytvoří výstupní prostředek, který ukládá výstup úlohy. 
4. Odešle úlohu.
5. Zkontroluje stav úlohy.
6. Stáhne soubory, které jsou výsledkem spuštění úlohy. 

> [!NOTE]
> Pokud použijete předvolby analyzátoru videa nebo zvuku, pomocí webu Azure Portal nastavte pro svůj účet 10 rezervovaných jednotek médií S3. Další informace najdete v tématu [Škálování zpracování médií](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme s rozhraním API služby Media Services se sadou .NET SDK

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Vytvoření vstupního prostředku a nahrání místního souboru do tohoto prostředku 

Funkce **CreateInputAsset** vytvoří nový vstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) a nahraje do něj zadaný místní videosoubor. Tento prostředek se použije jako vstup ve vaší úloze kódování. Ve službě Media Services v3 můžete jako vstup úlohy použít buď prostředek, nebo obsah, který jste přes adresu URL protokolu HTTPS zpřístupnili ve svém účtu služby Media Services. Postup zakódování obsahu přes adresu URL protokolu HTTPS najdete v [tomto](job-input-from-http-how-to.md) článku.  

Ve službě Media Services v3 slouží k nahrání souborů rozhraní API služby Azure Storage. Následující fragment kódu .NET vám ukáže, jak na to.

Uvedená funkce provede následující akce:

* Vytvoří prostředek. 
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Vytvoření výstupního prostředku k uložení výsledku úlohy 

Výstupní [prostředek](https://docs.microsoft.com/rest/api/media/assets) uloží výsledek vaší úlohy. Projekt definuje funkci **DownloadResults**, která stáhne výsledky z tohoto výstupního prostředku do výstupní složky, kde si je můžete zkontrolovat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Vytvoření transformace a úlohy, která analyzuje videa

Když kódujete nebo zpracováváte obsah v Media Services, kódování se obvykle nastaví jako předpis. Potom stačí odeslat **Úlohu**, která tento předpis použije pro video. Když odešlete novou Úlohu pro každé nové video, použije se tento předpis pro všechna videa ve vaší knihovně. V Media Services se pro předpis používá označení **transformace**. Další informace najdete v tématu [Transformace a úlohy](transform-concept.md). Ukázka popsaná v tomto kurzu definuje předpis, který analyzuje zadané video. 

#### <a name="transform"></a>Transformace

Když vytváříte novou instanci [Transformace](https://docs.microsoft.com/rest/api/media/transforms), musíte určit, co má vytvořit jako výstup. Jak je vidět ve výše uvedeném kódu, povinným parametrem je objekt **TransformOutput**. Každý objekt **TransformOutput** obsahuje **Předvolbu**. **Předvolba** popisuje podrobné pokyny operací zpracování videa a/nebo audia, které se používají ke generování požadovaného objektu **TransformOutput**. V tomto příkladu se používá předvolba **VideoAnalyzerPreset** a jazyk („en-US“) se předává jejímu konstruktoru. Tato předvolba vám umožňuje z videa extrahovat několik audio a video přehledů. Pokud potřebujete z videa extrahovat pouze několik audio přehledů, můžete použít předvolbu **AudioAnalyzerPreset**. 

Než začnete vytvářet **transformaci**, ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje (viz kód níže).  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Úloha

Jak je uvedeno výše, objekt [Transformace](https://docs.microsoft.com/rest/api/media/transforms) je předpis a [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění pro výstup. Můžete zadat umístění vašeho videa pomocí: Adresy URL HTTPS, adresy URL SAS nebo prostředky, které jsou ve vašem účtu služby Media Service. 

V tomto příkladu je vstupem úlohy místní video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Úloze chvíli trvá, než se dokončí, a když k tomu dojde, budete na to pravděpodobně chtít upozornit. Existuje několik možností, jak se nechat na dokončení [úlohy](https://docs.microsoft.com/rest/api/media/jobs) upozornit. Nejjednodušší možností (kterou zde ukazujeme) je použití dotazování. 

Dotazování nepatří mezi doporučené postupy, jak sestavovat aplikace, protože může mít prodlevu. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid.

Služba Event Grid je navržená pro vysokou dostupnost, konzistentní výkon a dynamické škálování. Díky službě Event Grid můžou vaše aplikace naslouchat událostem a reagovat na ně, ať už pocházejí z kterékoli služby Azure. Události můžou pocházet i z vlastních zdrojů. Jednoduché, reaktivní zpracování událostí založené na protokolu HTTP pomáhá sestavovat efektivní řešení prostřednictvím inteligentního filtrování a směrování událostí. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úlohy** obvykle prochází následujících stavů: **Naplánované**, **ve frontě**, **zpracování**, **dokončeno** (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu **Chyba**. Když úlohu zrušíte, změní se její stav na **Rušení** a potom na **Zrušeno**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]


### <a name="job-error-codes"></a>Kódy chyb úlohy

Zobrazit [kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Stažení výsledku úlohy

Následující funkce stáhne výsledky z výstupního [prostředku](https://docs.microsoft.com/rest/api/media/assets) do „výstupní“ složky, abyste si mohli výsledky úlohy prohlédnout. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit všechno kromě objektů, které máte v plánu použít znovu (obvykle budete chtít znovu použít transformace a zachovat streamovací lokátory). Pokud chcete účet po experimentování vyčistit, měli byste odstranit prostředky, které nemáte v plánu znovu použít. Následující kód například odstraní Úlohy.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Stisknutím kombinace kláves CTRL+F5 spusťte aplikaci *AnalyzeVideos*.

Když program spustíte, úloha vytvoří miniatury každého obličeje, který ve videu najde. Zároveň vytvoří soubor insights.json.

## <a name="examine-the-output"></a>Prozkoumání výstupu

Výstupní soubor analyzování videa se nazývá insights.json. Tento soubor obsahuje přehledy o vašem videu. Popis jednotlivých elementů nalezených v souboru JSON získáte v článku [Inteligentní funkce médií](intelligence-concept.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtu úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte. 

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Sady SDK služby Azure Media Services v3 nejsou bezpečné pro přístup z více vláken. Při práci s vícevláknovou aplikací byste měli pro každé vlákno vygenerovat nový objekt AzureMediaServicesClient.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Nahrávání, kódování a streamování souborů](stream-files-tutorial-with-api.md)
