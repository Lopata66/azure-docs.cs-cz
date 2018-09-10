---
title: Kurz vytvoření aplikace LUIS pro získání dat odpovídajících regulárnímu výrazu – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a entitu regulárního výrazu k extrakci dat.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 9672215c8cc5f95775e3b7fba74b27379a58ff49
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162915"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>Kurz: 3. Přidání entity regulárního výrazu
V tomto kurzu vytvoříte aplikaci, která ukazuje extrakci konzistentně formátovaných dat z promluvy pomocí entity **regulárního výrazu**.


<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit regulárního výrazu 
> * Použití aplikace LUIS pro doménu lidských zdrojů se záměrem FindForm (Vyhledat formulář)
> * Přidání entity regulárního výrazu pro extrakci čísla formuláře z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [předem připraveným entitám](luis-tutorial-prebuilt-intents-entities.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) na webu [LUIS](luis-reference-regions.md#luis-website) do nové aplikace JSON z úložiště [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json) na GitHubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `regex`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 


## <a name="purpose-of-the-regular-expression-entity"></a>Účel entity regulárního výrazu
Účelem entity je získat důležitá data obsažená v promluvě. Aplikace využívá entitu regulárního výrazu k získání formátovaných čísel formulářů lidských zdrojů z promluvy. Nevyužívá se přitom strojové učení. 

Mezi jednoduché ukázkové promluvy patří:

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Mezi zkrácené nebo hovorové verze promluv patří:

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Entita regulárního výrazu, který hledá shodu s číslem formuláře, je `hrf-[0-9]{6}`. Tento regulární výraz hledá shodu se znaky literálů `hrf -`, ale ignoruje velikost a kulturní varianty. Hledá shodu s číslicemi 0–9 o délce přesně 6 číslic.

HRF je zkratka pro Human Resources Form (Formulář lidských zdrojů).

### <a name="tokenization-with-hyphens"></a>Tokenizace s využitím spojovníků
Služba LUIS při přidání promluvy do záměru promluvu tokenizuje. Tokenizací těchto promluv se přidají mezery před a za spojovník: `Where is HRF - 123456?`. Regulární výraz se použije na promluvu v nezpracované podobě ještě před tím, než se tokenizuje. Vzhledem k tomu, že se regulární výraz používá na _nezpracovanou_ podobu, nemusí se starat o hranice slov. 


## <a name="add-findform-intent"></a>Přidání záměru FindForm (Vyhledat formulář)

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `FindForm` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem Create new intent (Vytvořit nový záměr) s textem Utilities (Nástroje) ve vyhledávacím poli](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |What is the URL for hrf-123456? (Jaká je adresa URL formuláře hrf-123456?)|
    |Where is hrf-345678? (Kde je hrf-345678?)|
    |When was hrf-456098 updated? (Kdy se aktualizoval formulář hrf-456098?)|
    |Did John Smith update hrf-234639 last week? (Aktualizoval John Smith minulý týden hrf-234639?)|
    |How many version of hrf-345123 are there? (Kolik verzí hrf-345123 existuje?)|
    |Who needs to authorize form hrf-123456? (Kdo musí autorizovat hrf-123456?)|
    |How many people need to sign off on hrf-345678? (Kolik lidí musí podepsat hrf-345678?)|
    |hrf-234123 date? (datum hrf-234123?)|
    |author of hrf-546234? (autor hrf-546234?)|
    |title of hrf-456234? (název hrf-456234?)|

    [ ![Snímek obrazovky se stránkou záměru a zvýrazněnými novými promluvami](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    Aplikace má z předchozího kurzu přidanou předem připravenou entitu čísla, takže je každé číslo formuláře označené. Pro klientskou aplikaci to může být dostačující, ale číslo nebude označené typem čísla. Vytvoření nové entity s odpovídajícím názvem umožní klientské aplikaci odpovídajícím způsobem zpracovat entitu po vrácení ze služby LUIS.

## <a name="create-an-hrf-number-regular-expression-entity"></a>Vytvoření entity regulárního výrazu HRF-number 
Podle následujících kroků vytvořte entitu regulárního výrazu, která službě LUIS poskytne informace o formátu HRF-number:

1. Na levém panelu vyberte **Entities** (Entity).

2. Na stránce Entities (Entity) vyberte tlačítko **Create new entity** (Vytvořit novou entitu). 

3. V automaticky otevíraném okně zadejte název nové entity `HRF-number`, jako typ entity vyberte **RegEx** (Regulární výraz), jako regulární výraz zadejte `hrf-[0-9]{6}` a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s automaticky otevíraným dialogovým oknem s nastavením vlastností nové entity](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Vyberte **Intents** (Záměry) a pak záměr **FindForm** (Vyhledat formulář), aby se v promluvách zobrazil označený regulární výraz. 

    [![Snímek obrazovky s promluvami s označenou stávající entitou a vzorem regulárního výrazu](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Vzhledem k tomu, že entita není strojově naučená, se popisek na promluvy použije a zobrazí se na webu LUIS okamžitě po vytvoření.

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `When were HRF-123456 and hrf-234567 published in the last year?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `FindForm` se dvěma čísly formuláře `HRF-123456` a `hrf-234567`.

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Čísla v promluvách se vrátí dvakrát, jednou jako nová entita `hrf-number` a jednou jako předem připravená entita `number`. Jak ukazuje tento příklad, promluva může obsahovat více než jednu entitu a více než jednu entitu stejného typu. Pomocí entity regulárního výrazu služba LUIS extrahuje pojmenovaná data, což usnadňuje programové použití v klientské aplikaci, která přijímá odpověď JSON.

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace identifikovala záměr a vrátila extrahovaná data. 

Váš chatbot má teď dostatek informací k určení primární akce `FindForm` a čísel formulářů, která se vyskytovala v hledání. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a čísla formulářů a prohledat rozhraní API třetí strany. Služba LUIS tuto práci neprovádí. Služba LUIS pouze určuje, co je záměrem uživatele, a extrahuje data o tomto záměru. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o entitě seznamu](luis-quickstart-intent-and-list-entity.md)

