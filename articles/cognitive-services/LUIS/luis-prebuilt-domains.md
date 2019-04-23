---
title: Pro jazyk Understa předem připravených domén
titleSuffix: Azure Cognitive Services
description: Služba LUIS zahrnuje sadu předem připravených domén pro rychlé přidání běžné, conversational uživatelských scénářů.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198931"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Přidat předem připravených domén pro běžné scénáře použití 

Služba LUIS zahrnuje sadu předem připravených záměry z předem připravených domén pro rychlé přidání běžné záměry a projevy. Toto je rychlý a snadný způsob, jak přidat možnosti do vaší aplikace lze klienta bez nutnosti modely pro tyto možnosti návrhu. 

## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény

1. Na **Moje aplikace** stránky, vyberte svou aplikaci. Tím se otevře aplikaci tak, aby **sestavení** části aplikace. 

1. Na **záměry** stránce **přidat předem připravených domén** z dole, vlevo nástrojů. 

1. Vyberte **kalendáře** záměru pak vyberte **přidáním domény** tlačítko.

    ![Přidání domény předem připravených kalendáře](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Vyberte **záměry** v levém navigačním panelu zobrazíte záměry kalendáře. Každý záměr z této domény předchází `Calendar.`. Spolu s projevy, jsou přidány dvě entity pro tuto doménu aplikace: `Calendar.Location` a `Calendar.Subject`. 

## <a name="train-and-publish"></a>Trénování a publikování

1. Po přidání domény trénování aplikace tak, že vyberete **trénování** v horní části, klikněte pravým tlačítkem myši nástrojů. 

1. V horním panelu nástrojů vyberte **publikovat**. Publikovat do **produkční**. 

1. Jakmile se zobrazí oznámení zelené úspěchu, vyberte **najdete v seznamu koncových bodů** odkaz zobrazíte koncových bodů.

1. Vyberte koncový bod. Do tohoto koncového bodu se otevře na nové kartě prohlížeče. Ponechat otevřenou kartu prohlížeče a i nadále **Test** oddílu.

## <a name="test"></a>Test

Test nové záměr na koncovém bodu podle přidán hodnotu **q** parametr: `Schedule a meeting with John Smith in Seattle next week`.

LUIS vrátí správné záměr a meeting subjektu:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Informace o předem připravených domény](./luis-reference-prebuilt-domains.md)
