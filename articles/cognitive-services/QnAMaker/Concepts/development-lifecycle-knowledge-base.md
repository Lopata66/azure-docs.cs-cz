---
title: Životní cyklus znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker se učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 4acecb9d15f820ba092f36d8fa3ea204658d2dba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276775"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Životní cyklus znalostní báze Knowledge base v nástroje QnA Maker
Nástroj QnA Maker se učí nejlepší v iterativní cyklus změny modelu, příklady utterance, publikování a shromažďování dat z koncového bodu dotazů. 

![Cyklus vytváření](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze QnA Maker
Koncový bod znalostní báze knowledge base (KB) nástroje QnA Maker poskytuje nejlepší shody odpověď na dotaz uživatele na základě obsahu KB. Vytvoření znalostní báze se o jednorázovou akci pro nastavení úložiště obsahu otázky, odpovědi a přidružená metadata. Procházení existující obsah, například stránky – nejčastější dotazy, produktových příruček nebo strukturovaných Q A páry je možné vytvořit znalostní báze. Zjistěte, jak [vytvoření znalostní báze](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testování a aktualizují znalostní báze

Znalostní báze připravený k testování, jakmile se vyplní s obsahem, buď pomocí redakčních úprav nebo Automatická extrakce. Interaktivní testování lze provést na portálu QnA Maker prostřednictvím **Test** panelu zadáním běžné dotazy na uživatele a ověření, že odpovědi vrátila správnou odpověď a dostatečné skóre spolehlivosti. 

* **Chcete-li vyřešit skóre, které se s nízkou spolehlivostí**: Přidat alternativní otázky. 
* **Když dotaz nesprávně vrátí [výchozí odpověď](confidence-score.md#change-default-answer)**: Přidat nové odpovědi na otázku správná. 

Tato těsné smyčce test aktualizace pokračuje, dokud budete spokojeni s výsledky. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).

Pro velké znalostní báze, použijte automatické testování pomocí [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) a `isTest=true` parametr řetězce dotazu, které dotazy `test` místo znalostní báze publikované znalostní báze knowledge base. 

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze
Po dokončení testování ve znalostní bázi, můžete ji publikovat. Publikovat na nejnovější verzi otestované znalostní báze k vyhrazené Azure Search indexovat představující nabízených oznámení **publikované** znalostní báze knowledge base. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tímto způsobem veškerých změnách prováděných na testovací verzi znalostní báze nemají vliv publikovanou verzi, které můžou být naživo v produkčním prostředí aplikace.

Každá z těchto znalostních bází může služba je určená pro testování samostatně. Pomocí rozhraní API, můžete se zaměřit na testovací verzi ve znalostní bázi s `isTest=true` příznak generateAnswer volání.

Zjistěte, jak [publikovat znalostní báze](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorování využití
Aby bylo možné protokol protokoly chatu vaší služby, je třeba, povolte Application Insights při vám [vytvoření služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Můžete získat různé analýzy využití služeb. Další informace o tom, jak pomocí služby application insights získáte [analytics pro vaši službu QnA Maker](../How-To/get-analytics-knowledge-base.md).

Podle další analýzy, provést příslušné [aktualizace do znalostní báze](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Skóre spolehlivosti](./confidence-score.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Znalostní báze](./knowledge-base.md)
[přehled nástroje QnA Maker](../Overview/overview.md)
