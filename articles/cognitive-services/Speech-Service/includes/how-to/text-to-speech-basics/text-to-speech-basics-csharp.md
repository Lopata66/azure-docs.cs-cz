---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 8679d6d4c8ff0a6abdf045187c284ca65f43ee7e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80986258"
---
## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Jednot<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">PODPORUJÍ<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte `using` do horní části skriptu následující příkazy.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet). Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet):

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

V tomto příkladu vytvoříte [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) pomocí klíče a oblasti předplatného. Identifikátor vaší oblasti najdete na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Také můžete vytvořit nějaký základní často používaný kód, který se použije pro zbytek tohoto článku, který můžete upravit pro různá přizpůsobení.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Vysyntetizátorování řeči v souboru

V dalším kroku vytvoříte [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) objekt, který provede převody textu na řeč a výstupy na reproduktory, soubory nebo jiné výstupní datové proudy. Parametr [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) přijímá jako je [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) objekt vytvořený v předchozím kroku a [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) objekt, který určuje, jak by měly být zpracovány výsledky výstupu.

Chcete-li začít, `AudioConfig` vytvořte pro automatický zápis výstupu do `.wav` souboru pomocí `FromWavFileOutput()` funkce a vytvoření instance pomocí `using` příkazu. `using` Příkaz v tomto kontextu automaticky odstraní nespravované prostředky a způsobí, že objekt přejde mimo obor po vyřazení.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Dále vytvořte instanci `SpeechSynthesizer` s jiným `using` příkazem. Předejte `config` objekt a `audioConfig` objekt jako Parai. Pak je provádění syntézy řeči a psaní do souboru jednoduché jako při spuštění `SpeakTextAsync()` s textovým řetězcem.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Spusťte program a v zadaném umístění se zapíše syntetizující `.wav` soubor. Toto je dobrý příklad základního využití, ale další se můžete podívat na přizpůsobení výstupu a zpracování výstupní odezvy jako proud v paměti pro práci s vlastními scénáři.

## <a name="synthesize-to-speaker-output"></a>Vysyntetizovat výstup mluvčího

V některých případech můžete chtít přímo vyprogramovat výstup syntetizované řeči přímo na mluvčí. Pokud to chcete provést, jednoduše vynechejte `AudioConfig` PARAT při `SpeechSynthesizer` vytváření v předchozím příkladu. Tento výstup vypíše aktuální aktivní výstupní zařízení.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Získat výsledek jako proud v paměti

Pro mnoho scénářů ve vývoji aplikací pro rozpoznávání řeči pravděpodobně budete potřebovat výsledná zvuková data jako proud v paměti, nikoli přímo zapisovat do souboru. To vám umožní vytvořit vlastní chování, včetně:

* Vyabstrakcte výsledné pole bajtů jako datový proud, který je možné vyhledat pro vlastní služby pro příjem dat.
* Integrujte výsledek s jinými službami nebo rozhraními API.
* Úprava zvukových dat, psaní vlastních `.wav` hlaviček atd.

Tuto změnu je jednoduché provést v předchozím příkladu. Nejprve odeberte `AudioConfig` blok, protože budete spravovat chování výstupu ručně z tohoto bodu dále pro zvýšené řízení. Pak předejte `null` `AudioConfig` v `SpeechSynthesizer` konstruktoru. 

> [!NOTE]
> Předání `null` pro místo `AudioConfig`toho, aby ho nemuseli vynechat jako v příkladu výstupu mluvčího, ve výchozím nastavení nebude přehrávat zvuk na aktuálním aktivním výstupním zařízení.

Tentokrát výsledek uložíte do [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet) proměnné. `AudioData` Vlastnost obsahuje `byte []` výstupní data. S tímto `byte []` můžete pracovat ručně nebo můžete použít [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) třídu ke správě streamu v paměti. V tomto příkladu použijete `AudioDataStream.FromResult()` statickou funkci k získání datového proudu z výsledku.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Odsud můžete implementovat jakékoli vlastní chování pomocí výsledného `stream` objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části se dozvíte, jak přizpůsobit atributy výstupů zvuku, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, použijte `SetSpeechSynthesisOutputFormat()` funkci na `SpeechConfig` objektu. Tato funkce očekává `enum` typ [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)výstupu, který použijete k výběru výstupního formátu. [Seznam zvukových formátů](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) , které jsou k dispozici, najdete v referenční dokumentaci.

V závislosti na vašich požadavcích máte k dispozici různé možnosti pro různé typy souborů. Všimněte si, že podle definice nezpracované `Raw24Khz16BitMonoPcm` formáty jako neobsahují zvukové hlavičky. Nezpracované formáty použijte jenom v případě, že vaše implementace pro příjem dat může dekódovat nezpracovaný Bitstream, nebo pokud plánujete ruční vytváření hlaviček na základě bitové hloubky, vzorkovací frekvence, počtu kanálů atd.

V tomto příkladu zadáte RIFF formát `Riff24Khz16BitMonoPcm` s vysokou přesností nastavením `SpeechSynthesisOutputFormat` `SpeechConfig` objektu na. Podobně jako v předchozím oddílu můžete použít [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) k získání streamu v paměti výsledku a pak ho zapsat do souboru.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Po opětovném spuštění programu se zapíše `.wav` soubor do zadané cesty.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Přizpůsobení vlastností řeči pomocí SSML

SSML (Speech syntézy Language) umožňuje vyladit rozteč, výslovnost, míru řeči, objem a další výstup textu do mluvené řeči odesláním požadavků ze schématu XML. Tato část obsahuje několik praktických příkladů použití, ale pro podrobnějšího průvodce si přečtěte [článek SSML postupy](../../../speech-synthesis-markup.md).

Chcete-li začít používat SSML k přizpůsobení, provedete jednoduchou změnu, která přepne hlas.
Nejprve vytvořte nový soubor XML pro SSML config v kořenovém adresáři projektu, v tomto příkladu `ssml.xml`. Kořenový element je vždy `<speak>`a zalamování textu v `<voice>` prvku umožňuje změnit hlas pomocí `name` param. Tento příklad změní hlas na hlas v češtině (UK). Všimněte si, že tento hlas je **standardní** hlas, který má různé ceny a dostupnost než **neuronové** hlasy. Podívejte se na [úplný seznam](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) podporovaných **standardních** hlasů.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Dál je potřeba změnit požadavek na Shrnutí řeči, aby odkazoval na váš soubor XML. Požadavek je většinou stejný, ale namísto použití `SpeakTextAsync()` funkce použijte. `SpeakSsmlAsync()` Tato funkce očekává řetězec XML, takže nejdřív načtěte konfiguraci SSML jako řetězec pomocí `File.ReadAllText()`. Z tohoto místa je objekt výsledku přesně stejný jako předchozí příklady.

> [!NOTE]
> Pokud používáte aplikaci Visual Studio, konfigurace sestavení pravděpodobně nenalezne soubor XML ve výchozím nastavení. Pokud to chcete opravit, klikněte pravým tlačítkem na soubor XML a vyberte **vlastnosti**. Změňte **akci sestavení** na *obsah*a změňte **Kopírovat do výstupního adresáře** na *vždycky kopírovat*.

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Výstup funguje, ale existuje několik jednoduchých změn, které vám pomůžou s tím, aby se lépe staly přirozenější. Celková rychlost speakace je trochu příliš rychlá, takže přidáme `<prosody>` značku a omezíme rychlost na **90%** výchozí sazby. Kromě toho je pozastavení po čárkě ve větě trochu krátké a nepřirozený zvuk. Chcete-li tento problém vyřešit, `<break>` přidejte značku pro zpoždění řeči a nastavte parametr Time na **200ms**. Znovu spusťte syntézu, abyste viděli, jak tato přizpůsobení ovlivnila výstup.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Hlasy neuronové

Hlasy neuronové jsou algoritmy pro syntézu řeči založené na hluboce neuronové sítích. Při použití hlasu neuronové je syntetizované rozpoznávání řeči skoro neodlišitelné od lidských nahrávek. V případě přirozeného Prosody jako přirozeného a jasného kloubování slov, neuronové hlasy významně omezují naslouchat únavu při interakci uživatelů se systémy AI.

Pokud chcete přepnout na neuronové hlas, změňte `name` na jednu z [možností hlasu neuronové](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Pak přidejte obor názvů XML pro `mstts`a zabalte text do `<mstts:express-as>` značky. Použijte `style` parametr pro přizpůsobení stylu speaking. Tento příklad používá `cheerful`, ale zkuste ho nastavit na `customerservice` nebo `chat` pro zobrazení rozdílu ve stylu speaking.

> [!IMPORTANT]
> Hlasy neuronové se podporují **jenom** u zdrojů řeči vytvořených v oblastech *Východní USA*, *Jižní východní Asie*a *západní Evropa* .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
