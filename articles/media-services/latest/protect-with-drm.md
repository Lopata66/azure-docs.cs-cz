---
title: Azure Media Services šifrování DRM a služba doručování licencí
titleSuffix: Azure Media Services
description: Naučte se používat dynamické šifrování DRM a službu doručování licencí k doručování streamů šifrovaných pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 14ba5f270138db22a76fd697b264046e22577427
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086732"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Kurz: použití dynamického šifrování DRM a služby doručování licencí

> [!NOTE]
> I když tento kurz používá příklady [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) , jsou obecné kroky stejné pro [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)nebo jiné podporované sady [SDK](media-services-apis-overview.md#sdks).

Azure Media Services můžete použít k doručování datových proudů šifrovaných pomocí licencí Microsoft PlayReady, Google Widevine nebo Apple FairPlay. Podrobné vysvětlení najdete v tématu [Ochrana obsahu s dynamickým šifrováním](content-protection-overview.md).

Media Services taky poskytuje službu pro doručování licencí PlayReady, Widevine a FairPlay DRM. Když si uživatel vyžádá obsah chráněný pomocí DRM, aplikace přehrávače si vyžádá licenci od služby Media Services License Service. Pokud je aplikace přehrávače autorizována, služba Media Services License Service vydá licenci ke službě Player. Licence obsahuje dešifrovací klíč, kterým může klientský přehrávač dešifrovat a streamovat obsah.

Tento článek vychází z ukázky [šifrování pomocí ochrany DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

Ukázka popsaná v tomto článku vede k tomuto výsledku:

![AMS s videem chráněným technologií DRM v Azure Media Player](./media/protect-with-drm/ams_player.png)

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoří transformaci kódování.
> * Nastavení podpisového klíče sloužícího k ověření vašeho tokenu.
> * Nastavte požadavky na zásady klíče obsahu.
> * Vytvořte StreamingLocator se zadaným zásadou streamování.
> * Vytvořte adresu URL používanou k přehrání souboru.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení kurzu potřebujete následující položky:

* Přečíst si článek [Přehled ochrany obsahu](content-protection-overview.md)
* Projděte si téma [návrh ochrany obsahu s více technologiemi DRM pomocí řízení přístupu](design-multi-drm-system-with-access-control.md).
* Nainstalujte Visual Studio Code nebo Visual Studio.
* Vytvořit si nový účet služby Azure Media Services podle popisu [v tomto rychlém startu](create-account-cli-quickstart.md)
* Získat přihlašovací údaje nutné k používání rozhraní API služby Media Services podle článku [Přístup k rozhraním API](access-api-cli-how-to.md)
* Nastavte příslušné hodnoty v konfiguračním souboru aplikace (appSettings. JSON).

## <a name="download-code"></a>Stažení kódu

Pomocí následujícího příkazu naklonujte do svého počítače úložiště GitHub s úplnou ukázkou streamování .NET, o které pojednává tento článek:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Ukázka „Encrypt with DRM“ se nachází ve složce [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Ukázka vytvoří jedinečné prostředky pokaždé, když aplikaci spustíte. Obvykle znovu použijete stávající prostředky, jako jsou transformace a zásady (Pokud existující prostředek má požadované konfigurace).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Začínáme používat rozhraní Media Services API se sadou .NET SDK

Chcete-li začít používat rozhraní Media Services API s rozhraním .NET, vytvořte objekt **AzureMediaServicesClient** . K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V kódu, který jste naklonovali na začátku článku, vytvoří funkce **GetCredentialsAsync** objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Vytvoření výstupního prostředku  

Výstupní [prostředek](assets-concept.md) uloží výsledek vaší úlohy kódování.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Získání nebo vytvoření transformace kódování

Když vytváříte novou instanci [Transformace](transforms-jobs-concept.md), musíte určit, co má vytvořit jako výstup. Požadovaný parametr je objekt `transformOutput`, jak je znázorněno v následujícím kódu. Každý TransformOutput obsahuje **přednastavení**. Předvolba popisuje podrobné pokyny k operacím zpracování videa nebo zvuku, které se mají použít ke generování požadovaných TransformOutput. Ukázka popsaná v tomto článku používá předdefinovanou předvolbu s názvem **AdaptiveStreaming**. Přednastavení zakóduje vstupní video do automaticky vygenerovaného žebříku (páry přenosných rychlostí) na základě rozlišení vstupu a přenosové rychlosti a vytváří soubory ISO MP4 s H. 264 a zvukem AAC odpovídajícím dvojicím přenosové rychlosti. 

Než začnete vytvářet novou **transformaci**, ověřte si nejdřív pomocí metody **Get**, jestli už neexistuje, jak vidíte v následujícím kódu.  Pokud entita v Media Services v3 neexistuje, metoda **Get** vrátí hodnotu **null** (v názvu se nerozlišují malá a velká písmena).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Spuštění úlohy

Jak je uvedeno výše, objekt **Transformace** je předpis a [Úloha](transforms-jobs-concept.md) je vlastní požadavek na službu Media Services, aby **transformaci** použila na daný vstupní videoobsah nebo zvukový obsah. **Úloha** určuje informace, jako je umístění vstupního videa a umístění výstupu.

V tomto kurzu vytvoříme vstup úlohy na základě souboru, který je ingestný přímo z [adresy URL zdroje https](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Čekání na dokončení úlohy

Dokončení úlohy trvá déle. V takovém případě chcete být upozorněni. Následující ukázka kódu je příkladem toho, jak se ve službě dotazovat na stav **úlohy**. Cyklické dotazování není doporučeným osvědčeným postupem pro produkční aplikace kvůli možné latenci. Pokud se dotazování u některého účtu používá nadměrně, je možné ho omezit. Místo dotazování by vývojáři měli používat službu Event Grid. Další informace najdete v článku [Směrování událostí na vlastní webový koncový bod](job-state-events-cli-how-to.md).

**Úloha** obvykle prochází následujícími stavy: **Naplánováno**, **Ve frontě**, **Zpracovávání** a **Dokončeno** (konečný stav). Pokud se úloha dokončí v rámci chyby, zobrazí se **chybový** stav. Pokud dojde ke zrušení úlohy, po dokončení operace se akce **zruší** a **zruší** .

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Vytvoření zásad pro klíč obsahu

Symetrický klíč poskytuje zabezpečený přístup k vašim prostředkům. Je potřeba vytvořit [zásadu klíče obsahu](content-key-policy-concept.md) při šifrování obsahu pomocí DRM. Zásady nakonfigurují způsob doručení klíče obsahu koncovým klientům. Klíč obsahu je přidružen k lokátoru streamování. Služba Media Services poskytuje taky službu doručování klíčů, která doručuje šifrovací klíče a licence autorizovaným uživatelům.

Je nutné nastavit požadavky (omezení) na **zásady klíče obsahu** , které musí být splněny pro doručení klíčů se zadanou konfigurací. V tomto příkladu jsme nastavili následující konfigurace a požadavky:

* Konfigurace

    Licence [PlayReady](playready-license-template-overview.md) a [Widevine](widevine-license-template-overview.md) jsou nakonfigurované tak, že se dají doručit pomocí služby doručení licencí Media Services. I když Tato ukázková aplikace nekonfiguruje licenci [Fairplay](fairplay-license-overview.md) , obsahuje metodu, kterou můžete použít ke konfiguraci Fairplay. Konfiguraci FairPlay můžete přidat jako jinou možnost.

* Omezení

    Aplikace nastaví u této zásady omezení typu tokenu JSON Web Token (JWT).

Když přehrávač zadá požadavek na stream, služba Media Services pomocí zadaného klíče dynamicky zašifruje váš obsah. K dešifrování streamu si přehrávač vyžádá klíč ze služby doručování klíčů. K tomu, aby služba zjistila, jestli má daný uživatel povolené získání klíče, vyhodnocuje zásadu symetrického klíče, kterou jste pro klíč určili.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Vytvoření lokátoru streamování

Po dokončení kódování a nastavení zásady symetrického klíče spočívá další krok ve vytvoření videa ve výstupním prostředku, které budou moct klienti přehrávat. Video zpřístupníte ve dvou krocích:

1. Vytvořte [Lokátor streamování](streaming-locators-concept.md).
2. Vytvoření adres URL pro streamování, které můžou používat klienti

Proces vytvoření **lokátoru streamování** se nazývá publikování. Ve výchozím nastavení je **Lokátor streamování** platný ihned po volání rozhraní API. Bude trvat až do odstranění, pokud nenastavíte volitelné počáteční a koncové časy.

Při vytváření **lokátoru streamování**je potřeba zadat požadované `StreamingPolicyName`. V tomto kurzu používáme jednu z předdefinovaných zásad streamování, která oznamuje Azure Media Services, jak publikovat obsah pro streamování. V tomto příkladu jsme nastavili název StreamingLocator.StreamingPolicyName na zásadu Predefined_MultiDrmCencStreaming. Šifry PlayReady a Widevine se aplikují a klíč se doručí klientovi pro přehrávání na základě konfigurovaných licencí DRM. Pokud zároveň chcete svůj stream zašifrovat pomocí CBCS (FairPlay), použijte zásadu Predefined_MultiDrmStreaming.

> [!IMPORTANT]
> Pokud používáte vlastní [zásady streamování](streaming-policy-concept.md), měli byste navrhnout určitou sadu takových zásad pro svůj účet Media Service a znovu je použít pro své StreamingLocators, kdykoli budete potřebovat stejné možnosti šifrování a protokoly. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet novou StreamingPolicy pro každou StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Získání testovacího tokenu

V tomto kurzu určíme, že má mít zásada symetrického klíče omezení tokenu. Zásady omezení tokenem musí být doplněny tokenem vydaným službou tokenů zabezpečení (STS). Media Services podporuje tokeny ve formátech [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) a to je to, co v ukázce nakonfigurujeme.

V zásadě ContentKeyPolicy se používá deklarace ContentKeyIdentifierClaim, což znamená, že token předaný službě doručování klíčů musí mít v sobě identifikátor symetrického klíče. V ukázce nezadáte klíč obsahu při vytváření lokátoru streamování, systém pro nás vytvoří náhodný. Pro vygenerování testovacího tokenu je potřeba získat ContentKeyId, který se vloží do deklarace ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Sestavení adresy URL streamování

Vytvořili jste streamovací lokátor [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a teď můžete vytvořit adresy URL pro streamování. Pokud chcete vytvořit adresu URL, musíte zřetězit název hostitele [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) a cestu k **lokátoru streamování** . V této ukázce se používá *výchozí* **koncový bod streamování** . Při prvním vytvoření účtu služby Media Service bude tento *výchozí* **koncový bod streamování** v zastaveném stavu, takže je potřeba zavolat **Start**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Po spuštění aplikace se zobrazí následující obrazovka:

![Ochrana pomocí DRM](./media/protect-with-drm/playready_encrypted_url.png)

Můžete otevřít prohlížeč a zadáním výsledné adresy URL spustit ukázkovou stránku Azure Media Player, na které je už předem vyplněná adresa URL a token.

## <a name="clean-up-resources-in-your-media-services-account"></a>Vyčištění prostředků v účtu služby Media Services

Obecně platí, že byste měli vyčistit vše kromě objektů, které plánujete znovu použít (obvykle budete znovu používat transformace, StreamingLocators a tak dále). Pokud chcete, aby se Váš účet vyčistil po experimentování, odstraňte prostředky, které nechcete znovu použít. Například následující kód odstraní úlohy:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtů úložiště, které jste vytvořili v tomto kurzu, pak tuto dříve vytvořenou skupinu prostředků odstraňte.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

Rezervovat

> [!div class="nextstepaction"]
> [Ochrana pomocí AES-128](protect-with-aes128.md)
