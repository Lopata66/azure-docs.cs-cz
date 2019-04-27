---
title: Publikování znalostní báze, REST, Go
titleSuffix: QnA Maker - Azure Cognitive Services
description: V tomto rychlém startu založené na protokolu REST přejít provede publikování znalostní báze, která odesílá na nejnovější verzi znalostní báze otestované vyhrazené index Azure Search představující publikované znalostní báze. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 058365f4dcc6e4be7c14c162afb1c0d4a9f3d9a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794250"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Publikování znalostní báze v nástroje QnA Maker pomocí jazyka Go

V tomto rychlém startu založené na protokolu REST provede programově publikování znalostní bázi (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Search a vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 

* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud ještě nemáte znalostní báze, můžete vytvořit ukázky, jednu pro účely tohoto rychlého startu: [Vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE] 
> Soubory tak získají kompletní řešení jsou k dispozici na [ **Azure – ukázky/cognitive-services – QnA maker go** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Vytvořte soubor Go

Otevřete VSCode a vytvořte nový soubor s názvem `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb.go` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Vytvoření funkce main

Po požadované závislosti přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Uvnitř **hlavní**


 Funkce, přidejte požadované konstanty na používání nástroje QnA Maker. Nahraďte hodnoty vlastními.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Přidejte požadavek POST pro publikování znalostní báze

Za požadované konstanty přidejte následující kód, který odešle požadavek HTTPS API nástroje QnA Maker na publikování znalostní báze a přijímat odpovědi:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Zadáním následujícího příkazu soubor zkompilujte. Příkazový řádek nevrací žádné informace o úspěšném sestavení.

```bash
go build publish-kb.go
```

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Žádost se odešle do API nástroje QnA Maker publikovat KB a pak vytiskněte 204 úspěchu nebo chyby.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

Po publikování znalostní báze, je nutné [adresu URL koncového bodu pro generování odpovědi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
