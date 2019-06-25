---
title: 'Rychlý start: Vytvoření první instance vlastního vyhledávání Bingu | Dokumentace Microsoftu'
titlesuffix: Azure Cognitive Services
description: Chcete-li vytvořit vlastní Bing, jež může hledat domény a webové stránky, které definujete pomocí tohoto článku.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 6949824f598194456837544526223b823dcfc3e5
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273344"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rychlý start: Vytvoření první instanci vlastního vyhledávání Bingu

Pokud chcete použít vlastní vyhledávání Bingu, musíte vytvořit instanci vlastního vyhledávání, která definuje vaše zobrazení nebo řez webu. Tato instance obsahuje veřejné domény, weby a webové stránky, které chcete hledat, spolu s nějaké úpravy hodnocení, budete potřebovat. 

Chcete-li vytvořit instanci, použijte [vlastní vyhledávání Bingu portál](https://customsearch.ai). 

![Obrázek portálu pro vlastní vyhledávání Bingu](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

Vytvoření instance vlastního vyhledávání Bingu:

1. Klikněte na tlačítko **Začínáme** na [vlastní vyhledávání Bingu portál](https://customsearch.ai) webové stránky a přihlaste se pomocí svého účtu Microsoft.

2. Klikněte na tlačítko **novou instanci**a zadejte popisný název. Kdykoli můžete změnit název vaší instance.
 
3. Na kartě **Aktivní** v části **Vyhledávací prostředí** zadejte adresy URL webů, které chcete do vyhledávání zahrnout. 

    > [!NOTE]
    > Instance vlastního vyhledávání Bingu se vrátí pouze výsledky pro domény a webové stránky, které jsou veřejné a byla indexované bingem.

4. Zadejte dotaz a podívejte se na výsledky hledání vrácenou instanci hledání můžete použít pravé straně portálu pro vlastní vyhledávání Bingu. Pokud se nezobrazí žádné výsledky, zkuste zadat jinou adresu URL.  

5. Klikněte na tlačítko **publikovat** publikujte své změny do produkčního prostředí, a aktualizovat koncové body instance.

6.  Klikněte na **produkční** kartu **koncové body**a zkopírujte váš **ID vlastní konfigurace**. Toto ID pro volání rozhraní API pro vlastní vyhledávání přidáním tak budete potřebovat `customconfig=` parametr ve volání dotazu.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Volání váš koncový bod pro vlastní vyhledávání Bingu](./call-endpoint-csharp.md)
