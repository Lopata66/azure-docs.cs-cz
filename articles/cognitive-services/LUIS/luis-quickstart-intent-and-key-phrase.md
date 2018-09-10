---
title: Kurz vytvoření aplikace LUIS vracející klíčové fráze – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak do své aplikace LUIS přidat entitu klíčové fráze a na základě jejího vracení analyzovat klíčová témata v promluvách.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: ef7a1c81f453a8d4ff9526a4844518782e152c4f
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159482"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Kurz: 8. Přidání entity klíčové fráze 
V tomto kurzu použijete aplikaci, která ukazuje, jak z promluv extrahovat klíčová témata.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit klíčové fráze 
> * Použití aplikace LUIS v doméně lidských zdrojů 
> * Přidání entity klíčové fráze pro extrahování obsahu z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS obsahující klíčové fráze

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [jednoduchým entitám](luis-quickstart-primary-and-secondary-data.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `keyphrase`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="keyphrase-entity-extraction"></a>Extrakce entity klíčové fráze
Klíčová témata poskytuje předem připravená entita klíčové fráze **keyPhrase**. Tato entita vrací klíčové téma promluvy.

Následující promluvy ukazují příklady klíčových frází:

|Promluva|Hodnoty entity klíčové fráze|
|--|--|
|Bude příští rok v nabídce nový plán zdravotní péče s nižší spoluúčastí?|„nižší spoluúčast“<br>„nový plán zdravotní péče“<br>„rok“|
|Pokrývá plán zdravotní péče s vysokou spoluúčastí léčbu zraku?|„plán zdravotní péče s vysokou spoluúčastí“<br>„léčba zraku“|

Vaše klientská aplikace může tyto hodnoty používat spolu s dalšími extrahovanými entitami k rozhodování o dalším kroku v konverzaci.

## <a name="add-keyphrase-entity"></a>Přidání entity klíčové fráze 
Přidejte předem připravenou entitu klíčové fráze pro extrahování témat z promluv.

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

2. V levé nabídce vyberte **Entities** (Entity).

    [ ![Snímek obrazovky se zvýrazněnou možností Entities (Entity) na levém navigačním panelu v části Build (Sestavení)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Vyberte **Manage prebuilt entities** (Spravovat předem připravené entity).

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. V automaticky otevíraném dialogovém okně vyberte **keyPhrase** (Klíčová fráze) a pak vyberte **Done** (Hotovo). 

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. V levé nabídce vyberte **Intents** (Záměry) a pak vyberte záměr **Utilities.Confirm** (Potvrzení nástrojů). Entita klíčové fráze je označená v několika promluvách. 

    [ ![Snímek obrazovky záměru Utilities.Confirm (Potvrzení nástrojů) s klíčovými frázemi označenými v promluvách](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publikování aplikace do koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]


## <a name="query-the-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `does form hrf-123456 cover the new dental benefits and medical plan`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Při hledání formuláře poskytl uživatel víc informací, než bylo potřeba k nalezení formuláře. Další informace se vrátí jako hodnota **builtin.keyPhrase**. Klientská aplikace může tyto další informace použít pro následnou otázku, například „Chtěli byste hovořit se zástupcem oddělení lidských zdrojů o nových benefitech v oblasti péče o chrup“, nebo může poskytnou nabídku s dalšími možnostmi včetně položky „Další informace o nových benefitech v oblasti péče o chrup nebo o zdravotním připojištění“.

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s rozpoznáváním entity klíčové fráze identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data, včetně hlavního tématu. 

Váš chatbot má teď dostatek informací k určení dalšího kroku v konverzaci. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data entity klíčové fráze z promluvy a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání analýzy mínění do aplikace](luis-quickstart-intent-and-sentiment-analysis.md)