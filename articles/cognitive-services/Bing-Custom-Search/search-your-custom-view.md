---
title: Hledání vlastního zobrazení – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: V této části najdete popis postupu při hledání vlastního zobrazení webu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 814f57d4011823da80e53cce41ffcb523fc0bf1b
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404998"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Volání instance Vlastní vyhledávání Bingu z portálu

Po nakonfigurování vlastního vyhledávacího prostředí ho můžete otestovat na [portálu](https://customsearch.ai)vlastní vyhledávání Bingu. 

![snímek obrazovky s portálem pro vlastní vyhledávání Bingu](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Vytvoření vyhledávacího dotazu 

Po přihlášení k [portálu](https://customsearch.ai)vlastní vyhledávání Bingu vyberte svou instanci hledání a klikněte na kartu **Výroba** . V části **koncové body**vyberte koncový bod rozhraní API (například webové rozhraní API). Vaše předplatné určuje, jaké koncové body se zobrazí.

Vyhledávací dotaz vytvoříte zadáním hodnot parametrů pro koncový bod. Všimněte si, že parametry zobrazené na portálu se můžou měnit v závislosti na koncovém bodu, který zvolíte. Další informace najdete v referenčních informacích k [rozhraní API pro vlastní vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) . Chcete-li změnit předplatné, které vaše instance vyhledávání používá, přidejte příslušný klíč předplatného a aktualizujte příslušné parametry trhu nebo jazyka.

Níže jsou uvedeny některé důležité parametry:


|Parametr  |Popis  |
|---------|---------|
|Dotaz     | Hledaný termín. K dispozici pouze pro koncové body webu, obrázku, videa a Automatický návrh |
|ID vlastní konfigurace | ID konfigurace vybrané instance vlastního vyhledávání. Toto pole je jen pro čtení. |
|Market     | Trh, ze kterého budou výsledky pocházet. K dispozici pouze pro koncové body webu, obrázku, videa a hostovaného uživatelského rozhraní.        |
|Klíč předplatného | Klíč předplatného, pomocí kterého se má testovat. Můžete vybrat klíč z rozevíracího seznamu nebo ho zadat ručně.          |

Kliknutím na **Další parametry** se odhalí následující parametry:  

|Parametr  |Popis  |
|---------|---------|
|Safe Search     | Filtr použitý k filtrování webových stránek pro obsah určený pro dospělé K dispozici pouze pro koncové body webu, obrázku, videa a hostovaného uživatelského rozhraní.        |
|Jazyk uživatelského rozhraní    | Jazyk používaný pro řetězce uživatelského rozhraní. Pokud například povolíte image a videa v hostovaném uživatelském rozhraní, na kartách **Obrázek** a **video** se použije zadaný jazyk.        |
|Count     | Počet výsledků hledání, které se mají vrátit v odpovědi K dispozici pouze pro koncové body webu, obrázku a videa.         |
|Offset    | Počet výsledků hledání, které se mají přeskočit před vrácením výsledků K dispozici pouze pro koncové body webu, obrázku a videa.        |
    
Po zadání všech požadovaných možností klikněte na **zavolat** , aby se zobrazila odpověď JSON v pravém podokně. Pokud vyberete koncový bod hostovaného uživatelského rozhraní, můžete otestovat možnosti vyhledávání v dolním podokně.

## <a name="change-your-bing-custom-search-subscription"></a>Změna předplatného Vlastní vyhledávání Bingu

Předplatné přidružené k vaší instanci Vlastní vyhledávání Bingu můžete změnit bez vytvoření nové instance. Pokud chcete, aby se volání rozhraní API poslala a vyrovnala novému předplatnému, vytvořte v Azure Portal nový prostředek Vlastní vyhledávání Bingu. Použijte nový klíč předplatného v požadavcích rozhraní API spolu s ID vlastní konfigurace vaší instance.

## <a name="next-steps"></a>Další postup

- [Zavolejte vlastní zobrazení pomocíC#](./call-endpoint-csharp.md)
- [Volání vlastního zobrazení pomocí Java](./call-endpoint-java.md)
- [Volání vlastního zobrazení pomocí NodeJs](./call-endpoint-nodejs.md)
- [Volání vlastního zobrazení pomocí Pythonu](./call-endpoint-python.md)

- [Volání vlastního zobrazení pomocí C# sady SDK](./sdk-csharp-quick-start.md)
