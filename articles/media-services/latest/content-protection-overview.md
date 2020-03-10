---
title: Chraňte svůj obsah pomocí dynamického šifrování Media Services V3
titleSuffix: Azure Media Services
description: Přečtěte si o ochraně obsahu s dynamickým šifrováním, protokoly streamování a typy šifrování v Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18e80383bfcbebc6a442663c141100faa56fd061
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393482"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Ochrana obsahu pomocí Media Services dynamického šifrování

Pomocí Azure Media Services můžete lépe zabezpečit vaše média v době, kdy počítač opustí, a to prostřednictvím úložiště, zpracování a doručování. Pomocí služby Media Services můžete doručovat na vyžádání a živého obsahu dynamicky šifrován Advanced Encryption Standard (AES-128) nebo některý z systémy tři hlavní digital rights management (DRM): Apple FairPlay, Microsoft PlayReady a Google Widevine. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům.  

V Media Services V3 je klíč obsahu přidružený k lokátoru streamování (viz [Tento příklad](protect-with-aes128.md)). Pokud používáte službu doručování klíčů Media Services, můžete nechat Azure Media Services vygenerovat klíč obsahu. Klíč obsahu by se měl vygenerovat sami, pokud používáte vlastní službu pro doručování klíčů, nebo pokud potřebujete, aby byl scénář s vysokou dostupností, kdy potřebujete stejný klíč obsahu ve dvou datových centrech.

Datový proud je žádost přehrávač, Media Services využívá se zadaným klíčem k dynamické šifrování obsahu pomocí nezašifrovaného klíče AES a DRM šifrování. Přehrávač dešifrovat datového proudu, vyžaduje klíč z doručení klíče služby Media Services nebo doručení klíče, který jste zadali. Pokud chcete zjistit, jestli má uživatel autorizaci získat klíč, služba vyhodnotí zásadu klíče obsahu, kterou jste zadali pro tento klíč.

Rozhraní REST API nebo klientskou knihovnu služby Media Services můžete použít ke konfiguraci zásad ověřování a ověřování licencí a klíčů.

Následující obrázek znázorňuje pracovní postup ochrany Media Services obsahu:

![Pracovní postup pro Media Services ochranu obsahu](./media/content-protection/content-protection.svg)
  
&#42;*Dynamické šifrování podporuje AES-128 Clear Key, CBCS a CENC. Podrobnosti najdete v tématu [matice podpory](#streaming-protocols-and-encryption-types).*

Tento článek vysvětluje koncepty a terminologii, které vám pomůžou pochopit ochranu obsahu pomocí Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Hlavní součásti systému ochrany obsahu

K úspěšnému dokončení systému ochrany obsahu je potřeba plně porozumět rozsahu úsilí. Následující části poskytují přehled tří částí, které je třeba implementovat.

> [!NOTE]
> Předtím, než přejdete k další části, důrazně doporučujeme, abyste si před přechodem na další část provedli všechny části v následujících oddílech. Chcete-li otestovat systém ochrany obsahu, použijte nástroje, které jsou uvedeny v částech.

### <a name="media-services-code"></a>Kód Media Services
  
[Ukázka DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) vám ukáže, jak implementovat systém s více DRM pomocí Media Services V3 pomocí .NET. Také ukazuje, jak používat Media Services License/Key Delivery Service.
  
Každý prostředek můžete zašifrovat i pomocí několika typů šifrování (AES-128, PlayReady, Widevine, FairPlay). Informace o tom, co je vhodné kombinovat, najdete v tématu [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

Příklad ukazuje postup:

1. Vytvořte a nakonfigurujte [zásady klíče obsahu](content-key-policy-concept.md).

   Vytvoříte zásadu pro klíč obsahu ke konfiguraci způsobu doručení klíče obsahu (který poskytuje zabezpečený přístup k vašim prostředkům) koncovým klientům:  

   * Definujte autorizaci pro doručování licencí. Zadejte logiku kontroly autorizace na základě deklarací identity v JSON Web Token (JWT).
   * Nakonfigurujte licence [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md)a [Fairplay](fairplay-license-overview.md) . Šablony vám umožní nakonfigurovat práva a oprávnění pro jednotlivé několikanásobnou.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Vytvořte [Lokátor streamování](streaming-locators-concept.md) , který je nakonfigurovaný pro streamování šifrovaného prostředku.
  
   Lokátor streamování musí být přidružený k [zásadě streamování](streaming-policy-concept.md). V tomto příkladu nastavíme `StreamingLocator.StreamingPolicyName` na zásadu "Predefined_MultiDrmCencStreaming".

   Šifry PlayReady a Widevine se aplikují a klíč se doručí klientovi pro přehrávání na základě konfigurovaných licencí DRM. Pokud chcete datový proud zašifrovat pomocí CBCS (FairPlay), použijte zásadu "Predefined_MultiDrmStreaming".

   Lokátor streamování je také přidružen k zásadě klíče obsahu, kterou jste definovali.

3. Vytvořte token testu.

   Metoda `GetTokenAsync` ukazuje, jak vytvořit testovací token.
4. Sestavte adresu URL streamování.

   Metoda `GetDASHStreamingUrlAsync` ukazuje, jak vytvořit adresu URL streamování. V tomto případě adresa URL streamuje obsah POMLČKy.

### <a name="player-with-an-aes-or-drm-client"></a>Přehrávač s klientem AES nebo DRM

Přehrávač videa aplikaci založenou na přehrávač SDK (nativní nebo založené na prohlížeči) musí splňovat následující požadavky:

* Sada Player SDK podporuje potřebné klienty DRM.
* Sada Player SDK podporuje požadované protokoly streamování: hladké, PŘERUŠOVANé a/nebo HTTP Live Streaming (HLS).
* Sada Player SDK dokáže zvládnout předávání tokenu JWT v žádosti o získání licence.

Přehrávač můžete vytvořit pomocí [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Pomocí [rozhraní Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) určete, která technologie DRM se má používat na různých platformách DRM.

Pro testování šifrování AES nebo CENC (Widevine nebo PlayReady) můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Ujistěte se, že jste vybrali možnost **Pokročilé možnosti** a zkontrolujete možnosti šifrování.

Pokud chcete testovat FairPlay zašifrovaný obsah, použijte [Tento testovací přehrávač](https://aka.ms/amtest). Přehrávač podporuje Widevine, PlayReady a FairPlay několikanásobnou společně s šifrováním pomocí šifrovaného klíče AES-128.

Vyberte si správný prohlížeč pro otestování různých několikanásobnou:

* Chrome, Opera nebo Firefox pro Widevine.
* Microsoft Edge nebo Internet Explorer 11 pro PlayReady
* Safari v macOS pro FairPlay.

### <a name="security-token-service"></a>Služba tokenů zabezpečení

Služba tokenů zabezpečení (STS) vydává token JWT jako přístupový token pro přístup k prostředkům back-endu. Jako prostředek back-endu můžete použít službu Azure Media Services License/Key Delivery Service. Služba STS musí definovat následující věci:

* Vystavitel a cílová skupina (nebo obor).
* Deklarace identity, které jsou závislé na obchodních požadavcích v ochraně obsahu.
* Symetrický nebo asymetrický ověření pro ověření podpisu.
* Podpora pro výměnu klíčů (v případě potřeby).

[Tento nástroj STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) můžete použít k otestování služby STS. Podporuje všechny tři typy ověřovacích klíčů: symetrický, asymetrický nebo Azure Active Directory (Azure AD) s použitím klíčového přechodu.

## <a name="streaming-protocols-and-encryption-types"></a>Typy šifrování a protokoly streamování

Media Services můžete doručovat obsah zašifrovaný dynamicky pomocí nezašifrovaného klíče AES a DRM šifrování pomocí PlayReady, Widevine a FairPlay. V současné době můžete šifrovat formáty HLS, MPEG POMLČKy a Smooth Streaming. Každý protokol podporuje následující metody šifrování.

### <a name="hls"></a>HLS

Protokol HLS podporuje následující formáty kontejnerů a schémata šifrování:

|Formát kontejneru|Schéma šifrování|Příklad adresy URL|
|---|---|---|
|Všechny|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 TS |Šifrování CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |Šifrování CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (včetně HEVC/H. 265) se podporuje na následujících zařízeních:

* iOS 11 nebo novější.
* iPhone 8 nebo novější.
* MacOS vysoký Sierra s PROCESORem Intel 7 pro generace.

### <a name="mpeg-dash"></a>MPEG-DASH

Protokol MPEG-SPOJOVNÍK podporuje následující formáty kontejneru a schémata šifrování:

|Formát kontejneru|Schéma šifrování|Příklady adres URL
|---|---|---|
|Všechny|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |Šifrování CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|Šifrování CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Technologie Smooth Streaming

Protokol Smooth Streaming podporuje následující formáty kontejneru a schémata šifrování.

|Protokol|Formát kontejneru|Schéma šifrování|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | Šifrování CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Prohlížeče

Běžné prohlížeče podporují následující klienty DRM:

|Prohlížeč|Šifrování|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Řízení přístupu k obsahu

Můžete řídit, kdo má přístup k vašemu obsahu tím, že nakonfigurujete zásady obsahu klíčů. Služba Media Services podporuje více způsobů autorizace uživatelů, kteří žádají o klíč. Klient (přehrávač) musí zásady splňovat, než tento klíč se dá doručit do klienta. Zásady klíčů obsahu můžou mít omezení *Open* nebo *token* .

Zásada pro klíč obsahu s omezeným přístupem se dá použít, když chcete licenci vydávat nikomu bez autorizace. Například pokud vaše tržby jsou založené na službě AD a nikoli na základě předplatného.  

Pomocí zásad klíčového obsahu omezeného tokenu se klíč obsahu pošle jenom klientovi, který prezentuje platný token JWT, nebo jednoduchý webový token (SWT) v žádosti o licenci nebo klíč. Tento token musí být vydán službou STS.

Můžete použít Azure AD jako STS nebo nasadit [vlastní STS](#using-a-custom-sts). Služba tokenů zabezpečení musí být nakonfigurovaný k vytvoření tokenu podepsán zadaný klíč a vydávání deklarací identity, které jste zadali v konfiguraci omezení s tokenem. Služba Media Services License/Key Delivery Services vrátí požadovanou licenci nebo klíč klientovi, pokud existují obě tyto podmínky:

* Token je platný.
* Deklarace identity v tokenu se shodují s požadavky nakonfigurovanými pro licenci nebo klíč.

Když konfigurujete zásady s omezením tokenu, musíte zadat primární ověřovací klíč, Vystavitel a parametry cílové skupiny. Primární ověřovací klíč obsahuje klíč, který byl token podepsán pomocí. Vystavitel je STS, který vydává token. Cílová skupina, někdy označovaná jako Scope, popisuje účel tokenu nebo prostředku, ke kterému token opravňuje přístup. Služba Media Services License/Key Delivery Services ověřuje, že se tyto hodnoty v tokenu shodují s hodnotami v šabloně.

### <a name="token-replay-prevention"></a>Prevence opětovného přehrání tokenu

Funkce *Prevence opětovného přehrání tokenu* umožňuje Media Services zákazníkům nastavit limit, kolikrát se dá stejný token použít k vyžádání klíče nebo licence. Zákazník může do tokenu přidat deklaraci identity typu `urn:microsoft:azure:mediaservices:maxuses`, kde hodnota je počet, kolikrát se token dá použít k získání licence nebo klíče. Všechny následné požadavky se stejným tokenem na doručení klíče vrátí neautorizovanou odpověď. Podívejte se, jak přidat deklaraci identity v [ukázce DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Požadavky

* Zákazníci musí mít kontrolu nad generováním tokenu. Deklarace identity musí být umístěna do samotného tokenu.
* Při použití této funkce jsou požadavky s tokeny, jejichž čas vypršení platnosti je více než jedna hodina od doby přijetí žádosti, odmítnuty s neoprávněnou odpovědí.
* Tokeny se jednoznačně identifikují podle jejich signatury. Jakékoli změny v datové části (například aktualizace na čas vypršení platnosti nebo deklarace identity) mění signaturu tokenu a počítají se jako nový token, který nepřijde do výše uvedeného klíče.
* Přehrávání se nezdařilo, pokud token překročil `maxuses` hodnotu nastavenou zákazníkem.
* Tato funkce se dá použít pro veškerý stávající chráněný obsah (musí se změnit jenom vydaný token).
* Tato funkce funguje s tokenem JWT i SWT.

## <a name="using-a-custom-sts"></a>Použití vlastní služby STS

Zákazník se může rozhodnout použít vlastní STS k poskytování tokenů. Mezi důvody patří:

* Zprostředkovatel identity (IDP) používaný zákazníkem nepodporuje službu STS. V takovém případě vlastních služeb STS, může být možnost.
* Zákazník může být nutné flexibilní nebo užší ovládací prvek integrovat službu tokenů zabezpečení zákazníka odběratele fakturačním systémem.

   Například operátor služby [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) může nabízet několik balíčků předplatitelů, jako jsou Premium, Basic a sport. Operátor může být vhodné tak, aby odpovídaly deklarací identity v tokenu s balíčkem předplatitele, tak, že jsou k dispozici pouze obsah v určitém balíčku. V takovém případě vlastních služeb STS nabízí potřebné flexibility a kontroly.

* Pokud chcete do tokenu zahrnout vlastní deklarace identity, které se mají vybrat mezi různými ContentKeyPolicyOptions s různými licenčními parametry DRM (licence k předplatnému a licence k pronájmu).
* Chcete-li zahrnout deklaraci identity představující identifikátor klíče obsahu klíče, ke kterému token uděluje přístup.

Při použití vlastních služeb STS musí udělali dvě změny:

* Když nakonfigurujete službu doručování licencí pro určitý prostředek, musíte zadat klíč zabezpečení použijí k ověření pomocí vlastních služeb STS místo platnost aktuálního klíče ze služby Azure AD.
* Při vygenerování tokenu JTW klíč zabezpečení je zadán místo aktuální X509 privátní klíč certifikátu ve službě Azure AD.

Existují dva typy bezpečnostních klíčů:

* Symetrický klíč: stejný klíč se používá ke generování a ověřit token JWT.
* Asymetrický klíč: pár veřejného a privátního klíče v x X509 certifikát se používá s privátním klíčem k šifrování a generovat token JWT a s veřejným klíčem k ověření tokenu.

Pokud používáte rozhraní .NET Framework a jazyka C# jako svou vývojovou platformu, X509 certifikát používaný pro asymetrické bezpečnostní klíč musí mít klíč délku aspoň 2048. Tato délka klíče je požadavkem třídy System. IdentityModel. Tokens. X509AsymmetricSecurityKey v .NET Framework. V opačném případě je vyvolána následující výjimka: IDX10630: ' System. IdentityModel. Tokens. X509AsymmetricSecurityKey ' pro Signing nemůže být menší než ' 2048 ' bitů.

## <a name="custom-key-and-license-acquisition-url"></a>Adresa URL vlastního klíče a získání licence

Následující šablony použijte, pokud chcete zadat jinou službu pro doručování licencí nebo klíčů (ne Media Services). Dvě umístěná pole v šablonách jsou zde, takže můžete sdílet zásady streamování napříč mnoha prostředky namísto vytváření zásad streamování na jeden prostředek. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Šablona pro adresu URL vlastní služby, která poskytuje klíče přehrávačům koncových uživatelů. Tento požadavek není nutný, pokud používáte Azure Media Services pro vydávání klíčů. 

   Šablona podporuje nahraditelné tokeny, které služba aktualizuje za běhu s hodnotou specifickou pro požadavek.  Aktuálně podporované hodnoty tokenu jsou:
   * `{AlternativeMediaId}`, která je nahrazena hodnotou StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, která je nahrazena hodnotou identifikátoru požadovaného klíče.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Šablona pro adresu URL vlastní služby, která poskytuje licence přehrávačům koncových uživatelů. Tento požadavek není nutný, pokud používáte Azure Media Services pro vystavování licencí.

   Šablona podporuje nahraditelné tokeny, které služba aktualizuje za běhu s hodnotou specifickou pro požadavek. Aktuálně podporované hodnoty tokenu jsou:  
   * `{AlternativeMediaId}`, která je nahrazena hodnotou StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, která je nahrazena hodnotou identifikátoru požadovaného klíče. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: stejné jako předchozí šablona, pouze pro Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: stejné jako předchozí šablona, pouze pro FairPlay.  

Příklad:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` má hodnotu požadovaného klíče. `AlternativeMediaId` můžete použít, pokud chcete mapovat požadavek na entitu na vaší straně. Můžete například použít `AlternativeMediaId`, které vám pomůžou vyhledat oprávnění.

Příklady REST, které používají vlastní licence nebo adresy URL pro získání klíčů, najdete v tématu [zásady streamování – vytvořit](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="troubleshoot"></a>Řešení problémů

Pokud se zobrazí chyba `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`, ujistěte se, že zadáváte vhodné zásady streamování.

Pokud se zobrazí chyby, které končí `_NOT_SPECIFIED_IN_URL`, ujistěte se, že jste v adrese URL zadali formát šifrování. Příklad: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Viz [protokoly streamování a typy šifrování](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

* [Ochrana pomocí šifrování AES](protect-with-aes128.md)
* [Ochrana pomocí DRM](protect-with-drm.md)
* [Návrh sady multi-DRM pro ochranu obsahu pomocí řízení přístupu](design-multi-drm-system-with-access-control.md)
* [Šifrování na straně úložiště](storage-account-concept.md#storage-side-encryption)
* [Nejčastější dotazy](frequently-asked-questions.md)
* [Obslužná rutina JSON Web Token](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
