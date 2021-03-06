---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: a27fba6e426b72d72160a9a238f68cf8cef5c73b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948051"
---
Jednou z klíčových funkcí služby Speech je možnost rozpoznávat a přepisovat lidské řeči (často se označuje jako převod řeči na text). V tomto rychlém startu se naučíte používat sadu Speech SDK ve vašich aplikacích a produktech k provádění vysoce kvalitních převodů řeči na text.

## <a name="skip-to-samples-on-github"></a>Přeskočit na ukázky na GitHubu

Pokud chcete přeskočit přímý na vzorový kód, přečtěte si [ukázky rychlý Start JavaScriptu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) na GitHubu.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu Speech SDK pro JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. V závislosti na vaší platformě postupujte podle následujících pokynů:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč <span class="docon docon-navigate-external x-hidden-focus"></span></a>

V závislosti na cílovém prostředí navíc použijte jednu z následujících možností:

# <a name="script"></a>[pravidel](#tab/script)

Stáhněte a extrahujte <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">sadu Speech SDK pro <span class="docon docon-navigate-external x-hidden-focus"></span> JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* soubor a umístěte ji do složky přístupné pro váš soubor HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Pokud cílíte na webový prohlížeč a použijete `<script>` značku; `sdk` při odkazování na třídy není nutná předpona. `sdk`Předpona je alias použitý k pojmenování `require` modulu.

# <a name="require"></a>[žádá](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Další informace o najdete v `require` tématu <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co je potřeba? <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

---

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Chcete-li volat službu Speech pomocí sady Speech SDK, je třeba vytvořit [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token. Vytvořte [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) pomocí klíče a oblasti. Podívejte se na stránku [najít klíče a oblast](../../../overview.md#find-keys-and-region) a vyhledejte pár klíč-oblast.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Existuje několik dalších způsobů, jak můžete inicializovat [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) :

* S koncovým bodem: předejte koncový bod služby řeči. Klíč nebo autorizační token jsou volitelné.
* S hostitelem: předejte adresu hostitele. Klíč nebo autorizační token jsou volitelné.
* Pomocí autorizačního tokenu: předejte autorizační token a přidruženou oblast.

> [!NOTE]
> Bez ohledu na to, jestli provádíte rozpoznávání řeči, syntézu řeči, překlad nebo rozpoznávání záměrů, vždy vytvoříte konfiguraci.

## <a name="recognize-from-microphone-browser-only"></a>Rozpoznat z mikrofonu (jenom prohlížeč)

Pokud chcete rozpoznávat řeč pomocí mikrofonu zařízení, vytvořte `AudioConfig` pomocí `fromDefaultMicrophoneInput()` . Pak inicializujte [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) a, předejte své `speechConfig` a `audioConfig` .

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

Pokud chcete použít *konkrétní* vstupní zvukové zařízení, je nutné zadat ID zařízení v `AudioConfig` . Přečtěte si, [Jak získat ID zařízení](../../../how-to-select-audio-input-devices.md) pro vstupní zvukové zařízení.

## <a name="recognize-from-file"></a>Rozpoznat ze souboru 

# <a name="browser"></a>[Prohlížeč](#tab/browser)

Pro rozpoznávání řeči ze zvukového souboru v prostředí JavaScript založeném na prohlížeči použijte `fromWavFileInput()` funkci k vytvoření [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) . Funkce `fromWavFileInput()` očekává [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) jako parametr objekt JavaScript.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

Pro rozpoznávání řeči ze zvukového souboru v Node.js musí být použit alternativní návrhový vzor pomocí datového proudu push, protože objekt jazyka JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) nelze použít v modulu Node.js runtime. Následující kód:

* Vytvoří Stream push pomocí. `createPushStream()`
* Otevře `.wav` soubor vytvořením streamu pro čtení a zapíše ho do datového proudu push.
* Vytvoří konfiguraci zvuku pomocí datového proudu push.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

Použití datového proudu push jako vstupu předpokládá, že zvuková data jsou nezpracované PCM, například přeskočení hlaviček.
Rozhraní API bude v některých případech fungovat i v případě, že hlavička nebyla vynechána, ale pro dosažení nejlepších výsledků zvažte implementaci logiky, aby bylo možné číst hlavičky, takže `fs` začíná na *začátku zvukového data*.

---

## <a name="error-handling"></a>Zpracování chyb

Předchozí příklady jednoduše získají rozpoznaný text z `result.text` , ale pro zpracování chyb a dalších odpovědí, budete muset napsat kód pro zpracování výsledku. Následující kód vyhodnocuje [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) vlastnost a:

* Vytiskne výsledek rozpoznávání: `ResultReason.RecognizedSpeech`
* Pokud se neshodují žádné rozpoznávání, informujte uživatele: `ResultReason.NoMatch`
* Pokud dojde k chybě, vytiskněte chybovou zprávu: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Průběžné rozpoznávání

V předchozích příkladech se používá rozpoznávání pomocí jediného snímku, které rozpoznává jeden utterance. Konec jednoho utterance se určuje tak, že naslouchá tichému ukončení na konci nebo dokud se nezpracovává po dobu 15 sekund zvuku.

Naopak se průběžné rozpoznávání používá, pokud chcete **určit** , kdy se má zastavit rozpoznávání. Pro získání výsledků rozpoznávání se vyžaduje přihlášení k odběru `Recognizing` `Recognized` událostí, a `Canceled` . Chcete-li zastavit rozpoznávání, je nutné zavolat [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync) . Tady je příklad toho, jak se provádí nepřetržité rozpoznávání na vstupním souboru zvuku.

Začněte definováním vstupu a inicializací [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) :

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Potom se přihlaste k odběru událostí odeslaných z [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Signál pro události obsahující mezilehlé výsledky rozpoznávání.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Signál pro události obsahující konečné výsledky rozpoznávání (indikující úspěšný pokus o uznání).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Signál pro události indikující konec relace rozpoznávání (operace).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Signál pro události obsahující zrušené výsledky rozpoznávání (indikující pokus o deaktivaci, který byl zrušen jako výsledek nebo přímý požadavek na zrušení nebo případně i přenos nebo selhání protokolu).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Když se všechno nastaví, zavolejte [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) na zahájit rozpoznávání.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Režim diktování

Při použití průběžného rozpoznávání můžete povolit zpracování diktování pomocí odpovídající funkce "Povolit diktování". V tomto režimu bude instance konfigurace řeči interpretovat popisy slov ve strukturách vět, jako je například interpunkční znaménko. Například utterance "provedete to živě ve městě otazník", který se interpretuje jako text "žijete ve městě?".

Chcete-li povolit režim diktování, použijte [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) metodu na [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Změnit jazyk zdroje

Běžným úkolem pro rozpoznávání řeči je zadání vstupu (nebo zdrojového) jazyka. Pojďme se podívat, jak byste změnili vstupní jazyk na italštinu. V kódu Najděte [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) a pak přidejte tento řádek přímo pod ním.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage)Vlastnost očekává řetězec formátu národního prostředí jazyka. Můžete zadat libovolnou hodnotu ve sloupci **locale (národní prostředí** ) v seznamu podporovaných [národních prostředí a jazyků](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Zlepšení přesnosti rozpoznávání

Seznamy frází slouží k identifikaci známých frází ve zvukových datech, jako je jméno osoby nebo konkrétní umístění. Zadáním seznamu frází Vylepšete přesnost rozpoznávání řeči.

Příklad: Pokud máte příkaz "přesunout do" a možné místo cíle "", které je možné přehlasovat, můžete přidat položku "přesunout do" dál ". Přidáním fráze se zvýší pravděpodobnost, že při rozpoznání zvuku bude místo možnosti přesunout směrem nahoru rozpoznána možnost přesunout na vyšší.

Do seznamu frází lze přidat jednotlivá slova nebo kompletní fráze. Při rozpoznávání se položka v seznamu frází používá ke zvýšení rozpoznávání slov a frází v seznamu i v případě, že se položky objeví uprostřed utterance. 

> [!IMPORTANT]
> Funkce seznamu frází je dostupná v těchto jazycích: en-US, de-DE, EN-AU, en-CA, en-GB, ES-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN

Chcete-li použít seznam frází, nejprve vytvořte [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) objekt a pak přidejte konkrétní slova a fráze pomocí [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) .

Všechny změny se projeví [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) při příštím rozpoznávání nebo po opětovném připojení ke službě Speech.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Pokud potřebujete vymazat seznam frází:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Další možnosti pro zlepšení přesnosti rozpoznávání

Seznamy frází jsou pouze jedním z možností, jak zlepšit přesnost rozpoznávání. Můžete také: 

* [Zlepšení přesnosti s využitím služby Custom Speech](../../../custom-speech-overview.md)
* [Zlepšení přesnosti s využitím modelů tenantů](../../../tutorial-tenant-model.md)
