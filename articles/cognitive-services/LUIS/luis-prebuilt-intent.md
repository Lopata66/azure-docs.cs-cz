---
title: Předem připravené záměry pro Language Understanding
titleSuffix: Azure Cognitive Services
description: Služba LUIS zahrnuje sadu předem připravených příkazů pro rychlé přidání běžné, conversational uživatelských scénářů.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: eb9d9b366dd9c4dbb02e9c79a908ba3b1bf1e3f0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637887"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Přidání záměrů předem připravených pro běžné příkazy 

Služba LUIS zahrnuje sadu předem připravených záměry z předem připravených domén pro rychlé přidání běžné záměry a projevy. Toto je rychlý a snadný způsob, jak přidat možnosti do vaší aplikace lze klienta bez nutnosti modely pro tyto možnosti návrhu. 

## <a name="add-a-prebuilt-intent"></a>Přidat předem připravených záměr

1. Na **Moje aplikace** stránky, vyberte svou aplikaci. Tím se otevře aplikaci tak, aby **sestavení** části aplikace. 

1. Na **záměry** stránce **přidat předem připravených záměr** z panelu nástrojů nad seznamem záměry. 

1. Vyberte **Utilities.Cancel** záměru v místním dialogovém okně. 

    ![Přidat předem připravených záměr](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Vyberte **provádí** tlačítko.

## <a name="train-and-test"></a>Trénování a testování

1. Po přidání záměr trénování aplikace tak, že vyberete **trénování** v horní části, klikněte pravým tlačítkem myši nástrojů. 

1. Otestovat novou záměr tak, že vyberete **Test** na panelu nástrojů vpravo. 

1. V textovém poli zadejte projevy zrušení:

    |Utterance testu|Skóre předpovědi|
    |--|:--|
    |Chcete zrušit své cestě.|0.67|
    |Zrušte nákup.|0.52|
    |Zrušte schůzky.|0.56|

    ![Testování předem připravených záměr](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Předem připravených entit](./luis-prebuilt-entities.md)
