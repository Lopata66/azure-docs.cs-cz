---
title: 'Rychlý start: Získání délky věty, Javy – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte jak určit délka větu pomocí Javy a rozhraní Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 252be03bfb0f24f0de5868d64d5add0e635cef34
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445110"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-java"></a>Rychlý start: Použití rozhraní Translator Text API k určení délky větu pomocí Javy

V tomto rychlém startu zjistíte určení délky větu pomocí Javy a rozhraní Translator Text API.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* [JDK 7 nebo novější](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt s Gradle

Začněme vytvořením pracovní adresář pro tento projekt. Z příkazového řádku (nebo terminál) spusťte tento příkaz:

```console
mkdir length-sentence-sample
cd length-sentence-sample
```

V dalším kroku budete inicializovat projekt Gradle. Tento příkaz vytvoří soubory nezbytné sestavení pro Gradle, nejdůležitější ale je, `build.gradle.kts`, který se používá v době běhu k vytvoření a konfigurace aplikace. Spuštěním tohoto příkazu z pracovní adresář:

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurace souboru sestavení

Vyhledejte `build.gradle.kts` a otevřete jej pomocí vašeho oblíbeného integrovaného vývojového prostředí textového editoru nebo editoru. Zkopírujte tuto konfiguraci sestavení:

```
plugins {
    java
    application
}
application {
    mainClassName = "LengthSentence"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Povšimněte si, že tato ukázka obsahuje závislosti na OkHttp pro požadavky HTTP a Gson ke zpracování a parsování formátu JSON. Pokud chcete získat další informace o konfiguracích sestavení, naleznete v tématu [vytváření nového sestavení Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Vytvořte soubor Java

Pojďme vytvořit složku pro ukázkovou aplikaci. Z pracovního adresáře spusťte:

```console
mkdir -p src/main/java
```

Dále v této složce vytvořte soubor s názvem `LengthSentence.java`.

## <a name="import-required-libraries"></a>Importujte požadované knihovny

Otevřít `LengthSentence.java` a přidat tyto příkazy import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definování proměnných

Nejprve budete muset vytvořit veřejnou třídu pro váš projekt:

```java
public class LengthSentence {
  // All project code goes here...
}
```

Přidejte tyto řádky do `LengthSentence` třídy. Uvidíte, že spolu s `api-version`, můžete definovat jazyk. V této ukázce je angličtina.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```
Pokud používáte víc služeb předplatného služeb Cognitive Services, musíte taky zahrnout `Ocp-Apim-Subscription-Region` v parametry požadavku. [Další informace o dvojúrovňovém víc služeb předplatného](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a žádost o sestavení

Přidejte tento řádek, který `LengthSentence` třída pro vytvoření instance `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

V dalším kroku Vytvořme požadavek POST. Nebojte se změní celý text. Text musí být uvozeny řídicími znaky.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Vytvoření funkce pro parsování odpovědi

Tato jednoduchá funkce analyzuje a prettifies odpověď JSON od službu Translator Text.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je vytvořit žádost a získejte odpověď. Do projektu přidejte tyto řádky:

```java
public static void main(String[] args) {
    try {
        LengthSentence lengthSentenceRequest = new LengthSentence();
        String response = lengthSentenceRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do kořenového adresáře pracovního adresáře a spusťte:

```console
gradle build
```

Po dokončení sestavení spusťte:

```console
gradle run
```

## <a name="sample-response"></a>Ukázková odpověď

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Další postup

Prozkoumejte vzorový kód pro tento rychlý start a další, včetně překladu a transkripce, a také další vzorové projekty Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady v Javě na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Další informace najdete v tématech

* [Překlad textu](quickstart-java-translate.md)
* [Transliterace textu](quickstart-java-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-java-detect.md)
* [Získání alternativních překladů](quickstart-java-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-java-languages.md)
* [Určení délky věty ze vstupu](quickstart-java-sentences.md)
