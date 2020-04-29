---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: e0c2554162d54af6c0a483e26f708838c3045a03
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80986244"
---
## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat sadu Speech SDK. V závislosti na vaší platformě postupujte podle následujících pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Svém<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto článku, zahrňte do horní části skriptu následující příkazy importu.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable):

* S předplatným: předejte klíč a přidruženou oblast.
* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

V tomto příkladu vytvoříte [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) pomocí klíče a oblasti předplatného. Identifikátor vaší oblasti najdete na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Také můžete vytvořit nějaký základní často používaný kód, který se použije pro zbytek tohoto článku, který můžete upravit pro různá přizpůsobení.

```java
public class Program 
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Vysyntetizátorování řeči v souboru

V dalším kroku vytvoříte [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) objekt, který provede převody textu na řeč a výstupy na reproduktory, soubory nebo jiné výstupní datové proudy. Parametr [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) přijímá jako je [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) objekt vytvořený v předchozím kroku a [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) objekt, který určuje, jak by měly být zpracovány výsledky výstupu.

Začněte tím, že vytvoříte `AudioConfig` a automaticky zapíšete výstup do `.wav` souboru pomocí `fromWavFileOutput()` statické funkce.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Dále vytvořte instanci `SpeechSynthesizer` předání `speechConfig` objektu a `audioConfig` objektu jako Paras. Pak je provádění syntézy řeči a psaní do souboru jednoduché jako při spuštění `SpeakText()` s textovým řetězcem.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Spusťte program a v zadaném umístění se zapíše syntetizující `.wav` soubor. Toto je dobrý příklad základního využití, ale další se můžete podívat na přizpůsobení výstupu a zpracování výstupní odezvy jako proud v paměti pro práci s vlastními scénáři.

## <a name="synthesize-to-speaker-output"></a>Vysyntetizovat výstup mluvčího

V některých případech můžete chtít přímo vyprogramovat výstup syntetizované řeči přímo na mluvčí. Chcete-li to provést, vytvořte `AudioConfig` instanci pomocí `fromDefaultSpeakerOutput()` funkce static. Tento výstup vypíše aktuální aktivní výstupní zařízení.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
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

Tentokrát výsledek uložíte do [`SpeechSynthesisResult`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-java-stable) proměnné. `SpeechSynthesisResult.getAudioData()` Funkce vrátí `byte []` výstupní data. S tímto `byte []` můžete pracovat ručně nebo můžete použít [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) třídu ke správě streamu v paměti. V tomto příkladu použijete `AudioDataStream.fromResult()` statickou funkci k získání datového proudu z výsledku.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    
    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Odsud můžete implementovat jakékoli vlastní chování pomocí výsledného `stream` objektu.

## <a name="customize-audio-format"></a>Přizpůsobení formátu zvuku

V následující části se dozvíte, jak přizpůsobit atributy výstupů zvuku, včetně:

* Typ zvukového souboru
* Vzorkovací frekvence
* Bitová hloubka

Chcete-li změnit formát zvuku, použijte `setSpeechSynthesisOutputFormat()` funkci na `SpeechConfig` objektu. Tato funkce očekává `enum` typ [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable)výstupu, který použijete k výběru výstupního formátu. [Seznam zvukových formátů](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) , které jsou k dispozici, najdete v referenční dokumentaci.

V závislosti na vašich požadavcích máte k dispozici různé možnosti pro různé typy souborů. Všimněte si, že podle definice nezpracované `Raw24Khz16BitMonoPcm` formáty jako neobsahují zvukové hlavičky. Nezpracované formáty použijte jenom v případě, že vaše implementace pro příjem dat může dekódovat nezpracovaný Bitstream, nebo pokud plánujete ruční vytváření hlaviček na základě bitové hloubky, vzorkovací frekvence, počtu kanálů atd.

V tomto příkladu zadáte RIFF formát `Riff24Khz16BitMonoPcm` s vysokou přesností nastavením `SpeechSynthesisOutputFormat` `SpeechConfig` objektu na. Podobně jako v předchozím oddílu můžete použít [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) k získání streamu v paměti výsledku a pak ho zapsat do souboru.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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

Dál je potřeba změnit požadavek na Shrnutí řeči, aby odkazoval na váš soubor XML. Požadavek je většinou stejný, ale namísto použití `SpeakText()` funkce použijte. `SpeakSsml()` Tato funkce očekává řetězec XML, takže nejprve vytvoříte funkci pro načtení souboru XML a vrátíte ji jako řetězec.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Z tohoto místa je objekt výsledku přesně stejný jako předchozí příklady.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
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
