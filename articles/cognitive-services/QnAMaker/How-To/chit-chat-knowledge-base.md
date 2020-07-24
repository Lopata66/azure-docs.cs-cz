---
title: Přidání funkce CHITEST – chat k QnA Maker znalostní bázi Knowledge Base
titleSuffix: Azure Cognitive Services
description: Přidání osobní funkce CHITEST – chat k robotovi usnadňuje další konverzaci a poutavější při vytváření KB. QnA Maker vám umožní snadno přidat předem vyplněnou sadu horního programu CHITEST-chat do vaší znalostní báze.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 0aca072a9e659d242dec9dd45a12f9568d419599
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124421"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Přidání funkce CHITEST – chat do znalostní báze

Přidání funkce CHITEST – chat k robotovi usnadňuje konverzaci a zajímavější. Funkce CHITEST-chat v QnA maker umožňuje snadno přidat předem vyplněnou sadu horních funkcí CHITEST-chat do znalostní báze (KB). To může být výchozí bod pro vaši osobnost robota a ušetří vám čas a náklady na jejich zápis od začátku.

Tato datová sada obsahuje přibližně 100 scénářů funkce CHITEST – chat v hlasu více osoby, jako je Professional, Friendly and Witty. Vyberte si, co nejpřesněji připomíná hlas robota. Když se uživatel dotazuje, QnA Maker se pokusí ho porovnat s nejbližším známým programem CHITEST-chat QnA.

Níže jsou uvedeny některé příklady různých osobních druhů. Můžete zobrazit všechny [datové sady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) osobnosti společně s podrobnostmi o osobních členech.

Pro dotaz na uživatele `When is your birthday?` má každá osobnost odpověď ve stylu:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobnost|Příklad|
|--|--|
|Professional|Stáří neplatí pro mě.|
|Vhodná|Nejedná se opravdu o stáří.|
|Witty|Jsem zdarma.|
|Caring|Nemáte stáří.|
|Dostáváme|Jsem robot, takže nemám věk.|
||


## <a name="language-support"></a>Podpora jazyků

CHITEST – datové sady chatu jsou podporovány v následujících jazycích:

|Jazyk|
|--|
|Čínština|
|Angličtina|
|Francouzština|
|Německo|
|Italština|
|Japonština|
|Korejština|
|Portugalština|
|Španělština|


## <a name="add-chit-chat-during-kb-creation"></a>Přidání funkce CHITEST-chat během vytváření KB
Při vytváření znalostní báze je po přidání vašich zdrojových adres URL a souborů k dispozici možnost pro přidání funkce CHITEST-chat. Vyberte si osobnost, kterou chcete, jako základ funkce CHITEST – chat. Pokud nechcete přidat CHITEST – chat nebo pokud již máte ve svých zdrojích dat podporu funkce CHITEST-chat, vyberte možnost **žádný**.

## <a name="add-chit-chat-to-an-existing-kb"></a>Přidat CHITEST – chat do existující znalostní báze
Vyberte své znalostní báze a přejděte na stránku **Nastavení** . V příslušném formátu **. TSV** je odkaz na všechny datové sady CHITEST-chat. Stáhněte si osobnost, kterou chcete, a pak ji nahrajte jako zdroj souborů. Ujistěte se, že při stažení a nahrání souboru neupravujte formát ani metadata.

![Přidat CHITEST – chat do existující znalostní báze](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Úprava funkce CHITEST – dotazy a odpovědi chatu
Při úpravách znalostní báze se zobrazí nový zdroj pro funkce CHITEST-chat na základě vámi vybrané preference. Nyní můžete přidat změněné otázky nebo upravit odpovědi stejně jako u jakéhokoli jiného zdroje.

![Úprava funkce CHITEST – chat QnAs](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Chcete-li zobrazit metadata, vyberte možnost **zobrazení možností** na panelu nástrojů a pak vyberte možnost **zobrazit metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Přidat další funkce CHITEST – otázky a odpovědi chatu
Můžete přidat nový pár CHITEST-chat QnA, který není v předdefinované sadě dat. Ujistěte se, že neduplikujete dvojici QnA, která je již pokryta v sadě CHITEST-chat. Když přidáte všechny nové funkce CHITEST-chat QnA, přidají se do vašeho **redakčního** zdroje. Chcete-li zajistit, aby toto pořadí porozumělo tomu, že se jedná o funkci CHITEST-chat, přidejte dvojici klíč/hodnota metadat "redakční: chitchat", jak je vidět na následujícím obrázku:

![! [Přidání funkce CHITEST-chat QnAs] (.. /Media/qnamaker-How-to-Chit-Chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Odstranit CHITEST – chat z existující znalostní báze
Vyberte své znalostní báze a přejděte na stránku **Nastavení** . Konkrétní funkce CHITEST-chat je uvedena jako soubor s vybraným názvem preference. Tuto možnost můžete odstranit jako zdrojový soubor.

![Odstranit CHITEST – chat z KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Import znalostní báze](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Viz také

[Přehled služby QnA Maker](../Overview/overview.md)
