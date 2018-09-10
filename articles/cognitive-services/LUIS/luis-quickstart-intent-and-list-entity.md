---
title: Kurz vytvoření aplikace LUIS pro získání přesné shody textu s daty v seznamu – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a entity seznamu k extrakci dat v tomto rychlém startu.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 04411f415b7cfe07d893c43e758bd2a4a226472a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162194"
---
# <a name="tutorial-4-add-list-entity"></a>Kurz: 4. Přidání entity seznamu
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak získat data odpovídající předdefinovanému seznamu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit seznamu 
> * Vytvoření nové aplikace LUIS pro doménu lidských zdrojů se záměrem MoveEmployee (Přesunutí zaměstnance)
> * Přidání entity seznamu pro extrakci zaměstnance z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [entitám regulárních výrazů](luis-quickstart-intents-regex-entity.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `list`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="purpose-of-the-list-entity"></a>Účel entity seznamu
Tato aplikace predikuje promluvy o přesunutí zaměstnance z jedné budovy do jiné. Tato aplikace používá k extrakci zaměstnance entitu seznamu. Na zaměstnance je možné odkazovat pomocí jména, telefonního čísla, e-mailu nebo čísla amerického federálního sociálního pojištění. 

Entita seznamu může obsahovat mnoho položek se synonymy pro každou položku. U malých až středně velkých společností slouží entita seznamu k extrakci informací o zaměstnanci. 

Název každé položky v kanonickém tvaru je číslo zaměstnance. Příklady synonym v této doméně: 

|Účel synonyma|Hodnota synonyma|
|--|--|
|Název|John W. Smith|
|E-mailová adresa|john.w.smith@mycompany.com|
|Telefonní linka|x12345|
|Číslo osobního mobilního telefonu|425-555-1212|
|Číslo amerického federálního sociálního pojištění|123-45-6789|

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text v promluvě se přesně shoduje se synonymem. 

Služba LUIS extrahuje informace o zaměstnanci takovým způsobem, že klientská aplikace může vytvořit standardní pokyn k přesunutí zaměstnance.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Přidání záměru MoveEmployee (Přesunutí zaměstnance)

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `MoveEmployee` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |move John W. Smith from B-1234 to H-4452 (přesunout Johna W. Smithe z B-1234 do H-4452).|
    |mv john.w.smith@mycompany.com from office b-1234 to office h-4452 (john.w.smith@mycompany.com se přesune z kanceláře b-1234 do kanceláře h-4452).|
    |shift x12345 to h-1234 tomorrow (přestěhujte zítra x12345 do h-1234).|
    |place 425-555-1212 in HH-2345 (umístěte 425-555-1212 do HH-2345).|
    |move 123-45-6789 from A-4321 to J-23456 (přesunout 123-45-6789 z A-4321 do J-23456).|
    |mv Jill Jones from D-2345 to J-23456 (Jill Jonesová se přesune z D-2345 do J-23456).|
    |shift jill-jones@mycompany.com to M-12345 (přestěhujte jill-jones@mycompany.com do M-12345).|
    |x23456 to M-12345 (x23456 do M-12345).|
    |425-555-0000 to h-4452 (425-555-0000 do h-4452)|
    |234-56-7891 to hh-2345 (234 7891 56 do hh 2345)|

    [ ![Snímek obrazovky se stránkou záměru a zvýrazněnými novými promluvami](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

## <a name="create-an-employee-list-entity"></a>Vytvoření entity seznamu zaměstnanců
Když teď má záměr **MoveEmployee** (Přesunutí zaměstnance) promluvy, musí služba LUIS porozumět tomu, co je zaměstnanec. 

1. Na levém panelu vyberte **Entities** (Entity).

2. Vyberte **Create new entity** (Vytvořit novou entitu).

3. V automaticky otevíraném dialogovém okně entity zadejte `Employee` jako název entity a **List** (Seznam) jako typ entity. Vyberte **Done** (Hotovo).  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Snímek obrazovky s dialogovým oknem pro vytváření nové entity")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Na stránce entity Employee (Zaměstnanec) zadejte jako novou hodnotu `Employee-24612`.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Snímek obrazovky se zadáváním hodnoty")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Pro synonyma zadejte následující hodnoty:

    |Účel synonyma|Hodnota synonyma|
    |--|--|
    |Název|John W. Smith|
    |E-mailová adresa|john.w.smith@mycompany.com|
    |Telefonní linka|x12345|
    |Číslo osobního mobilního telefonu|425-555-1212|
    |Číslo amerického federálního sociálního pojištění|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Snímek obrazovky se zadáváním synonym")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Jako novou hodnotu zadejte `Employee-45612`.

7. Pro synonyma zadejte následující hodnoty:

    |Účel synonyma|Hodnota synonyma|
    |--|--|
    |Název|Jill Jones|
    |E-mailová adresa|jill-jones@mycompany.com|
    |Telefonní linka|x23456|
    |Číslo osobního mobilního telefonu|425-555-0000|
    |Číslo amerického federálního sociálního pojištění|234-56-7891|

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Na konec adresy URL zadejte `shift 123-45-6789 from Z-1242 to T-54672`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `MoveEmployee` s extrahovanou hodnotou `Employee`.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  Zaměstnanec byl nalezen a vrácen jako typ `Employee` s rozlišovací hodnotou `Employee-24612`.

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Kde se v entitě seznamu provádí zpracování přirozeného jazyka? 
Vzhledem k tomu, že entita seznamu hledá přesnou shodu textu, nespoléhá se na zpracování přirozeného jazyka (ani strojové učení). Služba LUIS však využívá zpracování přirozeného jazyka (nebo strojové učení) k výběru správného záměru s nejvyšším skóre. Promluva navíc může být kombinací více než jedné entity nebo dokonce více než jednoho typu entity. V každé promluvě se zpracovávají všechny entity v aplikaci včetně entit zpracování přirozeného jazyka (nebo strojově naučených entit).

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace extrahovala pomocí entity sezamu správného zaměstnance. 

Váš chatbot má teď dostatek informací k určení primární akce `MoveEmployee` a toho, který zaměstnanec se má přesunout. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání hierarchické entity do aplikace](luis-quickstart-intent-and-hier-entity.md)

