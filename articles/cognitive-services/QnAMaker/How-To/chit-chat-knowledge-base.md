---
title: Přidání chit chat znalostní báze QnA Maker
titleSuffix: Azure Cognitive Services
description: Když přidáte osobní chat chit pro svého robota budete konverzační a poutavé při vytváření znalostní bázi. Nástroj QnA Maker umožňuje snadno přidat předvyplněný sadu nejvyšší chit konverzace, do znalostní BÁZÍ.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/07/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 08d09680ee1797e73666c185f4430c7ef3079477
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153683"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Přidat Chit chat znalostní báze

Přidání chit chat pro svého robota díky konverzační a poutavé. Funkci chit konverzace v nástroje QnA maker umožňuje snadno přidat předvyplněný sadu nejvyšší chit konverzace, do znalostní báze (KB). To může být výchozím bodem pro posouzení vašich osobnostních svého robota, a to vám ušetří čas a náklady na jejich vytváření od začátku.  

Tato datová sada obsahuje přibližně 100 scénáře chit chat v hlasových více osob, jako jsou Professional, zařízení a Witty. Vyberte osoby, která se nejvíce podobá hlasové svého robota. Zadaný uživatelský dotaz, nástroje QnA Maker se pokusí shodovat s nejbližší QnA známé chit chat.  

Níže jsou příklady různých osobnosti. Zobrazí všechny datové sady pro posouzení vašich osobnostních spolu s podrobnostmi o osobnosti [tady](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets).

<!-- added quotes so acrolinx doesn't score these sentences -->
|Uživatelský dotaz|Professional|Popisný|Witty|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`Aw, I'm blushing.`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`Those who can, do. Those who can't, don't sing.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Definitely didn't see that coming!`|`Sure. Take me to city hall. See what happens.`|



> [!NOTE]
> Podpora chit formou chatu je pouze aktuálně k dispozici v angličtině. 

## <a name="add-chit-chat-during-kb-creation"></a>Přidání chit chat během vytváření KB
Během vytváření znalostních bází, po přidání zdrojovými adresami a soubory je možnost pro přidání chit konverzace. Zvolte charakteru, který chcete použít jako základ vašich chit konverzace. Pokud nechcete přidat chit chatu, nebo pokud už máte chit chat podporují ve zdrojích dat zvolit **žádný**. 
   
![Přidat během vytváření chit chatu](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Přidat do existující KB Chit chatu
Vyberte znalostní BÁZÍ a přejděte **nastavení** stránky. Odkaz na všechny konverzace chit datové sady do příslušné **TSV** formátu. Posouzení vašich osobnostních, které chcete stáhnout, potom nahrajte ho jako souboru použitému jako zdroj. Zajistěte, aby při stažení a nahrání souboru upravit formát nebo metadata. 
  
![Přidat do existující KB chit chatu](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Upravit chit chat otázky a odpovědi
Při úpravě znalostní BÁZÍ, zobrazí se nový zdroj pro chit chat, založené na posouzení vašich osobnostních, kterou jste vybrali. Teď můžete přidat, změnit dotazy nebo upravit odpovědi, stejně jako s jakýmkoli zdrojem. 

![Upravit maximálně chit chatu](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Chcete-li zobrazit metadata, vyberte **možnosti zobrazení** na panelu nástrojů vyberte **zobrazit metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Přidat další chit chat otázek a odpovědí
Můžete přidat nové QnA chit chatu, který ne v předdefinovaném nastaven. Ujistěte se, že nereplikujete QnA pár, který je již zahrnut v sadě chit konverzace. Když přidáte všechny nové nástroje QnA chit chatu, získá přidá do vašeho **redakční** zdroje. Aby klasifikátor rozumí, že se jedná o chit chatu, přidejte dvojici klíč/hodnota metadat "redakční: chit chat", jak je znázorněno na následujícím obrázku:
   
![! [Přidat maximálně chit chat] (.. / media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Odstranit chit chat z existující KB
Vyberte znalostní BÁZÍ a přejděte **nastavení** stránky. Váš konkrétní chit chat zdroj je uveden jako soubor s názvem vybrané posouzení vašich osobnostních. Odstranit tento jako zdrojový soubor.

![Odstranit chit chat ze znalostní BÁZE](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Importovat znalostní báze](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled služby QnA Maker](../Overview/overview.md)
