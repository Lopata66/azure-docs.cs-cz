---
title: 'Rychlý start: Získání odpovědí ze znalostní báze knowledge base - REST, Javy – QnA Maker'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu založené na protokolu REST Javy provede získat odpověď ze znalostní báze, prostřednictvím kódu programu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: a8e51534fec3dacb577ef9b79bb42c48557dc15b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60920157"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Získejte odpovědi na dotaz zadaný ze znalostní báze s Javou

Tento rychlý start vás provede prostřednictvím kódu programu získávání odpovědi z publikovaných znalostní báze QnA Maker. Znalostní báze obsahuje otázky a odpovědi z [zdroje dat](../Concepts/data-sources-supported.md) například nejčastější dotazy. [Otázku](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) se odesílají službě nástroje QnA Maker. [Odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) zahrnuje horní předpovědět odpověď. 

## <a name="prerequisites"></a>Požadavky

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Tato ukázka používá Apache [klienta HTTP](http://hc.apache.org/httpcomponents-client-ga/) ze součástí HTTP. Je potřeba přidat následující knihovny klienta Apache HTTP do projektu: 
    * httpclient 4.5.3.jar
    * httpcore 4.4.6.jar
    * Commons. protokolování 1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Chcete-li načíst kód Product key, vyberte **klíče** pod **správy prostředků** v řídicím panelu Azure pro prostředek nástroje QnA Maker. 
* **Publikování** stránce nastavení. Pokud nemáte publikované znalostní báze, vytvořte prázdný znalostní báze a potom importujte znalostní báze na **nastavení** stránce a potom publikovat. Můžete stáhnout a použít [tento základní znalostní báze](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Stránka nastavení publikování zahrnují hodnoty trasy příspěvek, hodnota hostitele a EndpointKey hodnoty. 

    ![Nastavení publikování](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Kód v tomto rychlém startu se [ https://github.com/Azure-Samples/cognitive-services-qnamaker-java ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer) úložiště. 

## <a name="create-a-java-file"></a>Vytvořte soubor java

Otevřete VSCode a vytvořte nový soubor s názvem `GetAnswer.java` a přidejte následující třídu:

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Tento rychlý start využívá Apache třídy pro požadavky HTTP. Nad GetAnswer třídy, v horní části `GetAnswer.java` potřebné závislosti přidejte do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části `GetAnswer.java` třídy, přidejte požadované konstanty na používání nástroje QnA Maker. Tyto hodnoty jsou na **publikovat** stránce po publikování znalostní báze.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Přidání požadavku POST k odesílání otázku

Následující kód odešle požadavek HTTPS API nástroje QnA Maker odeslat dotaz do znalostní báze a přijímat odpovědi:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

`Authorization` Hodnotu hlavičky obsahuje řetězec `EndpointKey`. 

Další informace o [požadavek](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavit a spustit program z příkazového řádku. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker a vytiskne se do okna konzoly.

1. Sestavení souboru:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Spusťte soubor:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
