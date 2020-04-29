---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81421781"
---
K dokončení rychlého startu pro rozpoznávání záměrů budete muset vytvořit účet LUIS a projekt pomocí portálu LUIS Preview. Tento rychlý Start vyžaduje jenom předplatné LUIS. Předplatné služby Speech *není* vyžadováno.

První věc, kterou je potřeba udělat, je vytvořit účet LUIS a aplikaci pomocí portálu LUIS Preview. Aplikace LUIS, kterou vytvoříte, bude používat předem vytvořenou doménu pro automatizaci domů, která poskytuje záměry, entity a příklady projevy. Až budete hotovi, budete mít v cloudu spuštěný koncový bod LUIS, který můžete volat pomocí sady Speech SDK. 

Pokud chcete vytvořit aplikaci LUIS, postupujte podle těchto pokynů:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Rychlý Start: sestavení předem sestavené doménové aplikace<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Až budete hotovi, budete potřebovat čtyři věci:

* Znovu publikovat se zapnutým přepnutým dočtením **řeči**
* Váš **primární klíč** Luis
* Vaše **umístění** Luis
* **ID aplikace** Luis

Tyto informace můžete najít na [portálu Luis Preview](https://preview.luis.ai/):

1. Na portálu LUIS Preview vyberte svoji aplikaci a pak vyberte tlačítko **publikovat** .

2. Vyberte **produkční** slot, pokud používáte `en-US` možnost přepnout u možnosti vytváření **řeči** **na pozici.** Pak vyberte tlačítko **publikovat** .

    > [!IMPORTANT]
    > Důrazně se doporučuje používat **rozpoznávání řeči** , protože se tím vylepšit přesnost rozpoznávání řeči.

    > [!div class="mx-imgBorder"]
    > ![Publikování LUIS do koncového bodu](../../../media/luis/publish-app-popup.png)

3. Na portálu LUIS Preview vyberte **Spravovat**a pak vyberte **prostředky Azure**. Na této stránce najdete LUIS klíč a umístění (někdy označované jako _oblast_).

   > [!div class="mx-imgBorder"]
   > ![LUIS klíč a umístění](../../../media/luis/luis-key-region.png)

4. Až budete mít klíč a polohu, budete potřebovat ID aplikace. Vybrat **nastavení aplikace** – na této stránce je k dispozici vaše ID aplikace.

   > [!div class="mx-imgBorder"]
   > ![ID aplikace LUIS](../../../media/luis/luis-app-id.png)
