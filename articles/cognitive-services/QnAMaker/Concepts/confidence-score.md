---
title: Skóre spolehlivosti – QnA Maker
titleSuffix: Azure Cognitive Services
description: Skóre spolehlivosti označuje jistotu, že odpověď je doprava odpovídá dotazu daného uživatele.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 97c44c9285ec7a29827361111599db37bc6a86f3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282572"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Skóre spolehlivosti znalostní báze QnA Maker
Když uživatelský dotaz je hledána znalostní báze, vrátí QnA Maker příslušné odpovědi, spolu s skóre spolehlivosti. Toto skóre označuje jistotu, že odpověď je doprava odpovídá dotazu daného uživatele. 

Skóre spolehlivosti je číslo mezi 0 a 100. Skóre 100 je pravděpodobně přesnou shodu, zatímco skóre 0 znamená, která nebyla nalezena žádná odpovídající odpověď. Čím vyšší skóre – větší jistotou v odpovědi na dotaz. Pro daný dotaz může být vrátil více odpovědí. V takovém případě odpovědi se vrátí v pořadí podle snižuje pravděpodobnost.

V následujícím příkladu vidíte jednu entitu QnA 2 otázek. 


![Ukázka QnA pár](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Pro výše uvedeného příkladu – můžete očekávat, že skóre jako rozsah skóre ukázka níže – pro různé typy uživatelských dotazů:


![Klasifikátor rozsahu skóre](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Následující tabulka uvádí typické přidruženou skóre spolehlivosti.

|Hodnota skóre|Význam skóre|Příklad dotazu|
|--|--|--|
|90 – 100|A téměř přesnou shodu uživatele dotazu a dotaz KB|"Provedené změny se neaktualizují v článku KB po publikování"|
|> 70|Vysokou spolehlivostí – obvykle dobrou odpověď, která úplně odpovědi na dotaz uživatele|"Jsem publikoval znalostní BÁZE, ale není prováděna"|
|50 - 70|Střední jistotou – obvykle velmi dobrou odpověď, která by měla obsahovat odpovědi hlavním záměrem uživatelský dotaz|"Měli uložit Moje aktualizace před můžu publikovat znalostní BÁZE?"|
|30 – 50|S nízkou spolehlivostí – obvykle související odpovědí, který odpovídá částečně záměru uživatele|"Co uložit a trénování dělá?"|
|< 30|Velmi nízkou spolehlivostí – obvykle neodpovídá dotaz uživatele, ale má některé odpovídající slova nebo fráze |"Kde si můžu přidat synonyma znalostní BÁZE"|
|0|Žádná shoda, proto se vrátí odpověď.|"Kolik službu stojí"|

## <a name="choose-a-score-threshold"></a>Zvolte prahové hodnoty skóre
V tabulce výše najdete skóre, které se očekává, že na většině znalostní báze. Ale protože každých KB se liší a má různé druhy slova, záměry a cíle, které doporučujeme test a vyberte prahovou hodnotu, které bude nejlépe vyhovuje. Je výchozí a doporučenou prahovou hodnotu, která by měla fungovat pro většinu znalostní báze **50**.

Pokud zvolíte, že vaše mezní hodnota, mějte na paměti rovnováhu mezi přesnost a pokrytí a upravit vaše mezní hodnota, na základě vašich požadavků.

- Pokud **přesnost** (nebo přesnosti) je důležité pro váš scénář, pak zvýšit vaše mezní hodnota. Tímto způsobem pokaždé, když se vrátí odpověď, bude mnohem více důvěrné velikosti písmen a mnohem vyšší pravděpodobnost, že hledá odpověď uživatele. V takovém případě může skončit opuštění další nezodpovězené dotazy. *Příklad:* Pokud provedete prahovou hodnotu **70**, můžete přijít o některé příklady nejednoznačný lajků "co je uložte a trénování?".

- Pokud **pokrytí** (nebo odvolání) je více důležité – a vy chcete odpovězte na tolik otázek, kolik nejvíce, i v případě částečné vztahu uživatele otázku – pak nižší prahová hodnota. To znamená, že může být více případů, kdy odpověď neodpovídá skutečné dotaz uživatele, ale poskytuje některé poněkud související odpovědí. *Příklad:* Pokud provedete prahovou hodnotu **30**, dáte velmi související odpovědi jako odpovídá na výše uvedeném příkladu, například dotazy pro "kde je možné upravovat znalostní BÁZE?"

> [!NOTE]
> Novější verze nástroje QnA Maker vylepšení bodovací logiku zahrnout a může mít vliv na vaše mezní hodnota. Kdykoli můžete aktualizovat službu, ujistěte se, že pro testování a upravit prahovou hodnotu v případě potřeby. Služba QnA verzi, můžete zjistit [tady](https://www.qnamaker.ai/UserSettings)a zjistit, jak získat nejnovější aktualizace [tady](../How-To/troubleshooting-runtime.md).

## <a name="improve-confidence-scores"></a>Zvýšení skóre spolehlivosti
Pro zvýšení skóre spolehlivosti konkrétní odpovědi na dotaz uživatele, můžete přidat uživatelský dotaz ve znalostní bázi jako alternativní dotaz na odpověď. Můžete také použít velkých a malých písmen [word rozšiřuje](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) synonyma přidáte klíčová slova v znalostní BÁZÍ.


## <a name="similar-confidence-scores"></a>Podobně jako skóre spolehlivosti
Když ukládat více odpovědí mají podobné skóre spolehlivosti, je pravděpodobné, že dotaz byl příliš obecný a proto shodné s stejnou pravděpodobnost, že s více odpovědí. Vystavět strukturu vaší maximálně lépe tak, aby každá entita QnA obsahuje různé záměr.


## <a name="confidence-score-differences"></a>Skóre rozdíly spolehlivosti
Skóre spolehlivosti odpověď může změnit zanedbatelně mezi testu a publikovanou verzi znalostní báze i v případě, obsah je stejný. Je to proto, že obsah testu a publikované znalostní báze jsou umístěné v různých indexů Azure Search. Při publikování znalostní bázi otázek a odpovědí obsah znalostní báze přesune z index testu do produkčního prostředí indexu ve službě Azure search. V tématu Jak [publikovat](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) operace funguje.

Pokud máte znalostní báze v různých oblastech, každé oblasti používá vlastní index Azure Search. Protože se používají různé indexy, skóre nebudou shodovat. 


## <a name="no-match-found"></a>Nebyla nalezena žádná odpovídající
Když dobré shoda nenajde ve klasifikátor, vrátí se skóre spolehlivosti 0,0 nebo "None" a výchozí odpověď je "Dobrá nebyla nalezena žádná odpovídající v KB". Toto můžete přepsat [výchozí odpověď](#change-default-answer) v kódu bot nebo aplikaci volání koncového bodu. Alternativně přepsání odpovědi můžete také nastavit v Azure a tím se změní na výchozí hodnoty pro všechny znalostních bází nasazených v konkrétní služba QnA Maker.

## <a name="change-default-answer"></a>Změnit výchozí odpověď

1. Přejděte [webu Azure portal](https://portal.azure.com) a přejděte do skupiny prostředků, který představuje službu QnA Maker, kterou jste vytvořili.

2. Kliknutím otevřete **služby App Service**.

    ![Na webu Azure Portal přístup k App service pro nástroj QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klikněte na **nastavení aplikace** a upravit **DefaultAnswer** do požadovaného výchozí odpověď. Klikněte na **Uložit**.

    ![Vyberte nastavení aplikace a pak upravte DefaultAnswer pro nástroj QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Restartujte službu App service

    ![Po změně DefaultAnswer restartujte App Service QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Podporované zdroje dat](./data-sources-supported.md)

