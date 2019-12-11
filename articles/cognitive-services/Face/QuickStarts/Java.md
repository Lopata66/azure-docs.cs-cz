---
title: 'Rychlý Start: detekce ploch v imagi pomocí REST API Azure a Java'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API Azure Face a Java k detekci ploch v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7097f399b2bf5a3b0f9adb990fbd66785adc09aa
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977994"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Rychlý start: Rozpoznávání tváří na obrázku pomocí rozhraní REST API a Javy

V tomto rychlém startu použijete REST API Azure Face a Java k detekci lidských plošek v obraze.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Předpoklady

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Jakékoli Java IDE podle vašeho výběru.

## <a name="create-the-java-project"></a>Vytvoření projektu Java

1. Vytvořte novou aplikaci příkazového řádku Java v integrovaném vývojovém prostředí a přidejte do ní **Hlavní** třídu s metodou **Main** .
1. Následující knihovny naimportujte do projektu Java. Pokud používáte Maven, jsou k dispozici souřadnice Maven pro každou knihovnu.
   - [Klient Apache HTTP](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: HttpClient: 4.5.6)
   - [Apache HTTP Core](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.10)
   - [Knihovna JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Protokolování pro Apache](https://commons.apache.org/proper/commons-logging/download_logging.cgi) -nahlašování (pro čtení z více protokolů): přečtení z protokolu: 1.1.2)

## <a name="add-face-detection-code"></a>Přidat kód pro detekci obličeje

Otevřete hlavní třídu projektu. Zde přidáte kód potřebný k načtení obrázků a detekci ploch.

### <a name="import-packages"></a>Import balíčků

Do horní části souboru přidejte následující příkazy `import`.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Přidat důležitá pole

Nahraďte **Hlavní** třídu následujícím kódem. Tato data určují, jak se připojit ke službě obličeje a kde získat vstupní data. Musíte aktualizovat pole `subscriptionKey` hodnotou vašeho klíče předplatného a změnit řetězec `uriBase` tak, aby obsahoval správný řetězec koncového bodu. Můžete také chtít nastavit `imageWithFaces` hodnotu na cestu, která odkazuje na jiný soubor obrázku.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Pole `faceAttributes` je jednoduše seznam určitých typů atributů. Určí, které informace se mají načíst o zjištěných plochách.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Volání detekce obličeje REST API

Přidejte metodu **Main** s následujícím kódem. Vytvoří volání REST do Face API k detekci informací o obličejích ve vzdálené imagi (řetězec `faceAttributes` určuje, které atributy obličeje mají být načteny). Pak zapíše výstupní data do řetězce JSON.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

Přímo pod předchozím kódem přidejte následující blok, který převede vracená data JSON do čitelnějšího formátu před jeho tiskem do konzoly. Nakonec zavřete blok try-catch, metodu **Main** a **hlavní** třídu.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Spusťte aplikaci

Zkompilujte kód a spusťte jej. Úspěšná odpověď zobrazí tvářená data v snadno čitelném formátu JSON v okně konzoly. Například:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili jednoduchou konzolovou aplikaci v jazyce Java, která používá volání REST s Azure Face API k detekci ploch v obrázku a vrácení jejich atributů. V dalším kroku se dozvíte, jak s touto funkcí v aplikaci pro Android dělat víc.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikace pro Android pro detekci a orámování řezů](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
