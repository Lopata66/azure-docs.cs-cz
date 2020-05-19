---
title: Osvědčené postupy pro sestavení aplikace v LUIS
description: Seznamte se s osvědčenými postupy pro dosažení nejlepších výsledků z modelu vaší aplikace v LUIS.
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 43ca033c98d9997aecaf919b994a89d4e618d49b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589801"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Osvědčené postupy pro sestavování aplikace pro porozumění jazyku (LUIS)
Pomocí procesu vytváření aplikací sestavte aplikaci LUIS:

* Modely jazyka sestavení (záměry a entity)
* Přidání několika školicích příkladů projevy (15-30 na jeden záměr)
* Publikovat do koncového bodu
* Test z koncového bodu

Po [publikování](luis-how-to-publish-app.md)vaší aplikace použijte životní cyklus vývoje k přidání funkcí, publikování a testování z koncového bodu. Nespouštějte další cyklus vytváření, protože přidáte další příklad projevy, protože to neumožňuje LUISí modelu pomocí reálného uživatele projevy.

Nerozšiřujte projevy, dokud aktuální sada obou příkladů a projevy koncového bodu nevrátí jistotu, skóre s vysokou předpověďí. Vylepšete skóre pomocí [aktivního učení](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>A ne
Následující seznam obsahuje osvědčené postupy pro aplikace LUIS:

|Správný postup|Chybný postup|
|--|--|
|[Definovat jedinečné záměry](#do-define-distinct-intents)<br>[Přidání funkcí do záměrů](#do-add-features-to-intents) |[Přidejte spoustu příkladů projevy k záměrům](#dont-add-many-example-utterances-to-intents)<br>[Použití několika nebo jednoduchých entit](#dont-use-few-or-simple-entities) |
|[Najděte sladkou skvrnu mezi příliš obecným a příliš specifickou pro každý záměr.](#do-find-sweet-spot-for-intents)|[Použití LUIS jako školicí platformy](#dont-use-luis-as-a-training-platform)|
|[Opakované sestavení aplikace s použitím verzí](#do-build-your-app-iteratively-with-versions)<br>[Sestavení entit pro rozložení modelu](#do-build-for-model-decomposition)|[Přidejte spoustu příkladů projevy stejného formátu a ignorujte jiné formáty.](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Přidat vzory v pozdějších iteracích](#do-add-patterns-in-later-iterations)|[Kombinace definice záměrů a entit](#dont-mix-the-definition-of-intents-and-entities)|
|[Vyvážení projevy napříč všemi záměry](#balance-your-utterances-across-all-intents) s výjimkou záměru None.<br>[Přidat příklad projevy k žádnému záměru](#do-add-example-utterances-to-none-intent)|[Vytvoření seznamů frází se všemi možnými hodnotami](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Využijte funkci navrhnout pro aktivní učení.](#do-leverage-the-suggest-feature-for-active-learning)|[Přidat příliš mnoho vzorů](#dont-add-many-patterns)|
|[Sledování výkonu aplikace pomocí dávkového testování](#do-monitor-the-performance-of-your-app)|[Školení a publikování s každým jedním příkladem utterance přidání](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definování jedinečných záměrů
Ujistěte se, že slovník pro každý záměr je pouze pro tento záměr a nepřekrývá s jiným záměrem. Například pokud chcete mít aplikaci, která zpracovává cestovní mechanismy, jako jsou letecké lety a hotely, můžete se rozhodnout, že tyto oblasti budou mít samostatné záměry nebo stejný záměr s entitami pro konkrétní data v rámci utterance.

Je-li slovník mezi dvěma záměry stejný, slučte záměr a použijte entity.

Vezměte v úvahu následující příklad projevy:

|Ukázkové promluvy|
|--|
|Kniha a letu|
|Kniha a Hotel|

`Book a flight`a `Book a hotel` Používejte stejný slovník pro `book a ` . Tento formát je stejný, takže by měl být stejný záměr s různými slovy `flight` a `hotel` jako extrahované entity.

## <a name="do-add-features-to-intents"></a>Přidání funkcí do záměrů

Funkce popisují koncepty pro záměr. Může se jednat o Frázový seznam slov, která jsou významné pro daný záměr nebo na entitu, která je pro tento záměr významná.

## <a name="do-find-sweet-spot-for-intents"></a>Najděte si pro záměry sladkou skvrnu
Data předpovědi z LUIS můžete použít k určení, jestli se jejich záměry překrývají. Překrývající se záměry Zaměňujte LUIS. Výsledkem je, že nejvyšší záměr bodování je příliš blízko k jinému záměru. Vzhledem k tomu, že LUIS nepoužívá přesnou cestu přes data pro školení pokaždé, překrývající se záměr je pravděpodobné, že je první nebo druhý v rámci školení. Chcete, aby se utterance skóre každého záměru více rozdělilo, aby tato funkce překlopení nedocházelo. Dobrý rozdíl pro záměry by měl vést k očekávanému nejvyššímu záměru pokaždé.

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Opakované sestavování aplikace pomocí verzí

Každý cyklus vytváření obsahu by měl být v rámci nové [verze](luis-concept-version.md), který je klonován z existující verze.

## <a name="do-build-for-model-decomposition"></a>Sestavit pro dekompozici modelu

Dekompozice modelu má typický proces:

* vytvoření **záměru** založeného na záměrech uživatelů klientské aplikace
* Přidat vzorový projevy 15-30 založený na vstupu uživatele Real-World
* označení konceptu dat na nejvyšší úrovni v příkladu utterance
* přerušit koncept dat do subentit
* Přidat funkce k subentitám
* Přidání funkcí do záměrů

Jakmile vytvoříte záměr a přidáte příklad projevy, následující příklad popisuje dekompozici entit.

Začněte tím, že identifikujete kompletní koncepty dat, které chcete extrahovat v utterance. Toto je vaše entita získaná počítačem. Pak rozložíte frázi na její části. To zahrnuje identifikaci subentit a funkcí.

Pokud například chcete extrahovat adresu, mohla by být volána nejdůležitější entita zjištěná počítačem `Address` . Při vytváření adresy Identifikujte některé z jejích podřízených entit, jako je adresa ulice, město, stát a poštovní směrovací číslo.

Pokračovat v deskládání těchto elementů:
* Přidání požadované funkce poštovního kódu jako entity regulárního výrazu.
* Deskládání adresy ulice do částí:
    * **Číslo ulice** s požadovanou funkcí předem připravené entity čísla.
    * **Název ulice**
    * **Typ ulice** s požadovanou funkcí entity seznamu, jako je například způsob, Circle, Road a Lane.

Rozhraní API pro vytváření obsahu pro sestavení V3 umožňuje dekompozici modelu.

## <a name="do-add-patterns-in-later-iterations"></a>Přidejte vzory do pozdějších iterací.

Měli byste porozumět tomu, jak se aplikace chová před přidáním [vzorů](luis-concept-patterns.md) , protože vzory jsou vážeější, než je například projevy a jejich spolehlivost se bude zkosit.

Jakmile pochopíte, jak se vaše aplikace chová, přidejte vzory, které se vztahují k vaší aplikaci. Nemusíte je přidávat s každou [iterací](luis-concept-app-iteration.md).

Nemusíte je přidávat na začátek návrhu modelu, ale je snazší zjistit, jak každý vzorek mění model po otestování modelu pomocí projevy.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Vyrovnávání projevy napříč všemi záměry

Aby LUIS předpovědi bylo přesné, množství příkladu projevy v každém záměru (s výjimkou záměru None) musí být poměrně rovno.

Pokud máte záměr s 100 příkladem projevy a záměrem s 20 příkladem projevy, bude mít záměr 100-utterance vyšší míru předpovědi.

## <a name="do-add-example-utterances-to-none-intent"></a>Přidejte příklad projevy k žádnému záměru.

Tento záměr je záložním záměrem, který označuje všechno mimo vaši aplikaci. Přidejte příklad utterance do záměru None pro každých 10 příkladů projevy ve zbývající části vaší aplikace LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Využijte funkci navrhnout pro aktivní učení.

**Projevy** pravidelně pomocí [aktivního vzdělávacího bodu služby Active Learning](luis-how-to-review-endpoint-utterances.md)místo přidání dalších příkladů projevy k záměrům. Vzhledem k tomu, že aplikace neustále přijímá koncový bod projevy, tento seznam roste a mění.

## <a name="do-monitor-the-performance-of-your-app"></a>Sledování výkonu aplikace

Sledujte přesnost předpovědi pomocí sady [dávkových testů](luis-concept-batch-test.md) .

Ponechte samostatnou sadu projevy, která se nepoužívá jako [příklad projevy](luis-concept-utterance.md) nebo Endpoint projevy. Udržujte lepší aplikaci pro sadu testů. Přizpůsobte sadu testů tak, aby odrážela reálného uživatele projevy. Pomocí této sady testů vyhodnoťte každou iteraci nebo verzi aplikace.

## <a name="dont-add-many-example-utterances-to-intents"></a>Nepřidávejte spoustu příkladů projevy k záměrům

Po publikování aplikace se do procesu životního cyklu vývoje přidají jenom projevy z aktivního učení. Pokud je projevy příliš podobný, přidejte vzor.

## <a name="dont-use-few-or-simple-entities"></a>Nepoužívejte pár nebo jednoduché entity.

Entity jsou sestaveny pro extrakci a předpověď dat. Je důležité, aby každý záměr získal entity, které popisují data v záměru. To pomáhá LUIS odhadnout záměr, i když klientská aplikace nemusí používat extrahovanou entitu.

## <a name="dont-use-luis-as-a-training-platform"></a>Nepoužívejte LUIS jako školicí platformu

LUIS je specifický pro doménu jazykového modelu. Nejedná se o to, aby fungoval jako obecná platforma pro školení v přirozeném jazyce.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nepřidávejte spoustu příkladů projevy stejného formátu a ignorují se jiné formáty.

LUIS očekává variace v projevy záměru. Projevy se může lišit se stejným celkovým významem. Variace můžou zahrnovat délku utterance, volbu slov a umístění slov.

|Nepoužívat stejný formát|Používejte proměnlivý formát|
|--|--|
|Koupit lístek na Seattle<br>Koupit lístek na Paříž<br>Koupit lístek na Orlandu|Koupit 1 lístek na Seattle<br>Vyhrazení dvou míst červeného oka k Paříži dalšímu pondělí<br>Chci kniha 3 lístky k Orlandu pro přerušení pružiny|

Druhý sloupec používá různé operace (nákup, rezervace, kniha), různá množství (1, 2, 3) a různá ustanovení slov, ale všechny mají stejný záměr koupit letecké lístky pro cestování.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nekombinovat definice záměrů a entit

Vytvořte záměr pro všechny akce, které vaše bot bude trvat. Použijte entity jako parametry, které umožní tuto akci.

V případě robota, který bude v leteckém letu, vytvořte **BookFlight** záměr. Nevytvářejte záměr pro všechny letecké společnosti nebo všechny cíle. Tyto části dat používejte jako [entity](luis-concept-entity-types.md) a označte je v příkladech projevy.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nevytvářejte seznamy frází se všemi možnými hodnotami.

Zadejte několik příkladů v [seznamech frází](luis-concept-feature.md) , ale ne všechna slova nebo fráze. LUIS generalizace a bere kontext na účet.

## <a name="dont-add-many-patterns"></a>Nepřidávat mnoho vzorů

Nepřidávejte příliš mnoho [vzorů](luis-concept-patterns.md). LUIS je určeno pro rychlé seznámení s méně příklady. Nepoužívejte zbytečně přetížení systému.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nevytvářejte výuku a publikujte s každým jedním příkladem utterance

Před školením a publikováním přidejte projevy 10 nebo 15. To vám umožní vidět dopad na přesnost předpovědi. Přidání jednoho utterance nemusí mít viditelný dopad na skóre.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [naplánovat aplikaci](luis-how-plan-your-app.md) v aplikaci Luis.
