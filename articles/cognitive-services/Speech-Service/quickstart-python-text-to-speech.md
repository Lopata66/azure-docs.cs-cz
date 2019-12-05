---
title: 'Rychlý Start: převod textu na řeč, Python-Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí Pythonu a rozhraní REST API pro převod textu na řeč. Ukázkový text v tomto průvodci strukturovaná jako jazyk pro značky syntézu řeči (SSML). To umožňuje zvolit hlasu a jazyk odpovědi řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 23f3a6b030b477d3dcc06317a545064da95100ff
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816212"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Rychlý start: Převod převod textu na řeč pomocí Pythonu

V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí Pythonu a převod textu na řeč rozhraní REST API. Text požadavku v této příručce má strukturu [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md), která umožňuje výběr hlasu a jazyk odpovědi.

Tento rychlý Start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč předplatného Azure pro službu pro rozpoznávání řeči

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `tts.py`, zkopírujte tento fragment kódu.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Tyto moduly se používají k zápisu odpovědi řeči do souboru s časovým razítkem, vytvořit požadavek HTTP a volání rozhraní API pro převod textu na řeč.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavte klíč předplatného a vytvoření výzvu pro převod textu na ŘEČ

V následujících částech vytvoříte metody pro autorizaci, volání rozhraní API pro převod textu na řeč a ověřit odpověď. Začněme přidáním kódu, který zajišťuje, že tento příklad bude fungovat s Python 2.7.x a 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

V dalším kroku vytvoříme třídy. Je to, kde dáme naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na řeč.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` Je jedinečný klíč z portálu Azure portal. `tts` se zobrazí výzva k zadání textu, který bude převeden do mluvené řeči. Tento vstup je řetězcový literál, znaky se nemusí být uvozeny řídicími znaky. Nakonec `timestr` získá aktuální čas, který použijeme název souboru.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Převod textu na řeč rozhraní REST API vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken`ho koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `fetch_token_url`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte tento kód do `TextToSpeech` třídy:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Další informace o ověřování najdete v tématu [ověřování pomocí přístupového tokenu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady budete vytvářet žádosti a uložení odpovědi řeči. Nejprve je nutné nastavit `base_url` a `path`. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `base_url`. Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je třeba přidat povinné hlavičky pro dané žádosti. Ujistěte se, že aktualizujete `User-Agent` názvem prostředku (nachází se na webu Azure Portal) a nastavte `X-Microsoft-OutputFormat` na preferovaný zvuku. Úplný seznam formátech výstupu najdete v tématu [zvuk výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Potom vytvořte datovou část požadavku pomocí řeči syntézu Markup Language (SSML). Tato ukázka definuje strukturu a používá `tts` vstup jste vytvořili dříve.

>[!NOTE]
> Tento příklad používá `Guy24KRUS` hlasového písma. Pro úplný seznam Microsoft poskytuje hlasy/jazyky, naleznete v tématu [jazykovou podporu](language-support.md).
> Pokud vás zajímá vytváření jedinečné, rozpoznat hlas pro hodnotu značky, přečtěte si téma [vytváření vlastního hlasového písma](how-to-customize-voice-font.md).

A konečně provede požadavek do služby. Pokud je žádost úspěšná a se vrátí stavový kód 200 kódu, odpověď řeči je zapsána do souboru časovým razítkem.

Zkopírujte tento kód do `TextToSpeech` třídy:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je vytvoření instance své třídy a volání funkce.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit převod textu na řeč ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
python tts.py
```

Po zobrazení výzvy zadejte cokoli, co byste chtěli převést z převodu textu na řeč. V případě úspěšného ověření řeči soubor je umístěn ve složce vašeho projektu. Přehrávání pomocí vašeho oblíbeného přehrávač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Převod textu na řeč pomocí Pythonu a sady Speech SDK](quickstarts/speech-to-text-from-microphone.md)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
