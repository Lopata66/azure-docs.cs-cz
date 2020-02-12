---
title: Vyhodnocení výkonu modelu
titleSuffix: ML Studio (classic) - Azure
description: Přečtěte si, jak vyhodnotit výkon modelu v Azure Machine Learning Studio (Classic) a o metrikách dostupných pro tuto úlohu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: baf2352c8842a07691288c9296438624d53d3990
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153481"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Postup vyhodnocení výkonu modelu v Azure Machine Learning Studio (Classic)

Tento článek ukazuje, jak vyhodnotit výkon modelu v Azure Machine Learning Studio (Classic) a poskytuje stručné vysvětlení metrik dostupných pro tuto úlohu. K dispozici jsou tři běžné scénáře učení s dohledem: 

* nevýhody
* binární klasifikace 
* Klasifikace s více třídami



Vyhodnocení výkonu modelu je jedním z hlavních fází procesu zpracování dat. Označuje, jak úspěšné je hodnocení (předpovědi) datové sady vyškolený model. 

Azure Machine Learning Studio (Classic) podporuje hodnocení modelu prostřednictvím dvou z hlavních modulů strojového učení: [vyhodnoťte model][evaluate-model] a [model křížového ověřování][cross-validate-model]. Tyto moduly vám umožní zjistit, jak model vykonává, v rámci řady metrik, které se běžně používají v machine learningu a statistice.

## <a name="evaluation-vs-cross-validation"></a>Vyhodnocení vs. vzájemné ověřování
Vyhodnocení a vzájemné ověřování představují standardní způsoby měření výkonu modelu. Generují metriky vyhodnocení, které můžete kontrolovat nebo porovnávat s nimi i s ostatními modely.

[Vyhodnotit model][evaluate-model] očekává jako vstup datovou sadu s skóre (nebo dvě pro případ, že chcete porovnat výkon dvou různých modelů). Proto budete muset svůj model prodávat pomocí modulu [výuka modelu][train-model] a před vyhodnocením výsledků použít modul předpovědi pro určitou datovou sadu pomocí modulu [skóre modelu][score-model] . Vyhodnocování vychází z popisků nebo pravděpodobností s použitím hodnot true, z nichž všechny jsou výstupem modulu určení [skóre modelu][score-model] .

Alternativně můžete použít křížové ověřování k provedení několika operací vyhodnocení výukového skóre (10 skládání) automaticky v různých podmnožinách vstupních dat. Vstupní data jsou rozdělená na 10 částí, kde jedna je vyhrazena pro testování, a druhý 9 pro školení. Tento proces se opakuje desetkrát a průměrné metriky se vyhodnocuje. To pomáhá určit, jak dobře má model zobecnit do nových datových sad. Modul pro [křížové ověřování modelu][cross-validate-model] přebírá v nevýukovém modelu a některém datové sadě s popisky a vypisuje výsledky vyhodnocení každého 10 přeložení, a to kromě průměrných výsledků.

V následujících částech sestavíme jednoduché modely regrese a klasifikace a vyhodnocujeme jejich výkon pomocí [modelu vyhodnocení][evaluate-model] i modulů [modelu křížové validace][cross-validate-model] .

## <a name="evaluating-a-regression-model"></a>Vyhodnocení regresního modelu
Předpokládejme, že chceme předpovědět cenu auta pomocí funkcí, jako jsou dimenze, aut, specifikace stroje atd. Toto je typický problém regrese, kde Cílová proměnná (*Price*) je souvislá číselná hodnota. Můžeme přizpůsobit lineární regresní model, který s ohledem na hodnoty funkcí určitého automobilu může odhadnout cenu tohoto auta. Tento model regrese se dá použít k určení skóre stejné datové sady, kterou jsme si vyškole. Jakmile budeme mít předpovězené ceny za automobil, můžeme vyhodnotit výkon modelu tím, že si vyhledáte, kolik předpovědi se odchyluje od skutečných cen v průměru. K tomu je potřeba použít *datovou sadu dat auto (RAW)* , která je k dispozici v části **uložené datové sady** v Machine Learning Studio (Classic).

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte do svého pracovního prostoru následující moduly v Azure Machine Learning Studio (Classic):

* Údaje o cenách automobilu (RAW)
* [Lineární regrese][linear-regression]
* [Model výuky][train-model]
* [Model skóre][score-model]
* [Vyhodnotit model][evaluate-model]

Připojte porty, jak je znázorněno na obrázku 1, a nastavte sloupec popisek modulu [vlakového modelu][train-model] na hodnotu *Price*.

![Vyhodnocení regresního modelu](./media/evaluate-model-performance/1.png)

Obrázek 1. Vyhodnocení regresního modelu.

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků hodnocení
Po spuštění experimentu můžete kliknout na výstupní port modulu [vyhodnocení modelu][evaluate-model] a vybrat *vizualizovat* a zobrazit výsledky vyhodnocení. K dispozici jsou metriky vyhodnocení pro regresní modely: *střední absolutní chyba*, *absolutní absolutní*chyba, *relativní absolutní chyba*, *relativní čtvercová chyba*a *koeficient určení*.

Termín "Chyba" zde představuje rozdíl mezi předpokládanou hodnotou a skutečnou hodnotou. Absolutní hodnota nebo mocnina tohoto rozdílu je obvykle vypočítávána k zachycení celkové hodnoty chyby napříč všemi instancemi, protože rozdíl mezi předpovězenou a skutečnou hodnotou může být v některých případech negativní. Metriky chyb měří prediktivní výkon regresního modelu v souvislosti se střední odchylkou jeho předpovědi od hodnot true. Nižší chybové hodnoty znamenají, že model je přesnější při vytváření předpovědi. Celková metrika chyb nula znamená, že model přesně přizpůsobí data.

Koeficient stanovitelnosti, který se také označuje jako R, je také standardním způsobem měření, jak dobře model odpovídá datům. Může být interpretována jako poměr variací, které je vysvětleno v modelu. Vyšší podíl je v tomto případě lepší, přičemž 1 označuje dokonalé přizpůsobení.

![Metriky vyhodnocení lineární regrese](./media/evaluate-model-performance/2.png)

Obrázek 2. Metriky vyhodnocení lineární regrese.

### <a name="using-cross-validation"></a>Použití vzájemného ověřování
Jak bylo zmíněno dříve, můžete automaticky provádět opakované školení, bodování a hodnocení pomocí modulu [model křížové validace][cross-validate-model] . V tomto případě budete potřebovat datovou sadu, nevýukový model a modul [modelu křížového ověřování][cross-validate-model] (viz obrázek níže). Ve vlastnostech modulu [více než ověřených modelů][cross-validate-model] musíte nastavit sloupec popisek na hodnotu *Price* .

![Křížové ověření regresního modelu](./media/evaluate-model-performance/3.png)

Obrázek 3. Křížové ověření regresního modelu.

Po spuštění experimentu můžete zkontrolovat výsledky vyhodnocení kliknutím na správný výstupní port modulu [model křížové validace][cross-validate-model] . Tím získáte detailní pohled na metriky pro každou iteraci (skládání) a průměrné výsledky každé z těchto metrik (obrázek 4).

![Výsledky křížového ověřování modelu regrese](./media/evaluate-model-performance/4.png)

Obrázek 4. Výsledky křížového ověřování modelu regrese.

## <a name="evaluating-a-binary-classification-model"></a>Vyhodnocení binárního klasifikačního modelu
V binárním scénáři klasifikace má cílová proměnná pouze dva možné výsledky, například: {0, 1} nebo {false, true}, {negatived, kladný}. Předpokládejme, že máte datovou sadu zaměstnanců z dospělého s některými demografickými a pracovními proměnnými a že jste požádáni o předpověď úrovně příjmů, binární proměnnou s hodnotami {"< = 50 K", "> 50 K"}. Jinými slovy, záporná třída představuje zaměstnance, kteří činí méně než nebo rovno 50 K za rok, a kladná třída reprezentuje všechny ostatní zaměstnance. Stejně jako ve scénáři regrese by bylo potřeba vytvořit model, určit skóre dat a vyhodnotit výsledky. Hlavní rozdíl je v tomto případě volbou metriky Azure Machine Learning Studio (Classic) výpočetních hodnot a výstupů. K ilustraci scénáře předpovědi úrovně příjmů použijeme datovou sadu pro [dospělé](https://archive.ics.uci.edu/ml/datasets/Adult) k vytvoření studia (klasického) a vyhodnocení výkonu pro model logistické regrese se dvěma třídami, což je běžně používaný binární klasifikátor.

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte do svého pracovního prostoru následující moduly v Azure Machine Learning Studio (Classic):

* Datová sada klasifikace binárního výnosu pro dospělé
* [Logistická regrese dvou tříd][two-class-logistic-regression]
* [Model výuky][train-model]
* [Model skóre][score-model]
* [Vyhodnotit model][evaluate-model]

Připojte porty, jak je znázorněno na obrázku 5, a nastavte sloupec popisku modulu [vlakového modelu][train-model] na *příjem*.

![Vyhodnocení binárního klasifikačního modelu](./media/evaluate-model-performance/5.png)

Obrázek 5. Vyhodnocení binárního klasifikačního modelu.

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků hodnocení
Po spuštění experimentu můžete kliknout na výstupní port modulu [vyhodnocení modelu][evaluate-model] a vybrat *vizualizovat* a zobrazit výsledky vyhodnocení (obrázek 7). K dispozici jsou metriky vyhodnocení pro binární klasifikace modelů: *přesnost*, *přesnost*, *odvolání*, *skóre F1*a *AUC*. Kromě toho modul výstupuje nejednoznačnou matrici, která zobrazuje počet skutečných kladných hodnot, falešně negativních hodnot, falešně pozitivních hodnot a true negativy, jakož i hodnoty *Roc*, *Precision, Recall*a *zvednutí* .

Přesnost je pouze poměr správně klasifikovaných instancí. Obvykle je první metrikou, kterou se zobrazí při vyhodnocování klasifikátoru. Nicméně, pokud jsou testovací data nevyvážená (kde většina instancí patří do jedné z tříd), nebo máte více zajímat výkon v obou třídách, přesnost nezachycuje efektivitu klasifikátoru. V případě klasifikace na úrovni příjmů se předpokládá, že testujete data, kde 99% instancí představuje lidi, kteří získají méně než 50 tis za rok. Je možné dosáhnout přesnosti 0,99, protože pro všechny instance odhadnete třídu "< = 50 tis". Třídění v tomto případě zdá se, že se jedná o dobrý úkol, ale ve skutečnosti se nepovede klasifikovat žádné fyzické jednotlivce (1%). správně.

Z tohoto důvodu je vhodné vypočítat další metriky, které zachycují konkrétnější aspekty vyhodnocení. Než se pustíte do podrobností o takových metrikách, je důležité pochopit nejasnou matrici binárního vyhodnocení klasifikace. Popisky tříd v sadě školení mohou převzít pouze dvě možné hodnoty, které obvykle odkazujete na kladné nebo záporné. Kladné a záporné instance, které jsou v klasifikátoru předpověď správně, se nazývají true pozitivních hodnot (TP) a pravdivé záporné hodnoty (TN), v uvedeném pořadí. Podobně nesprávně klasifikované instance se nazývají falešně pozitivní (FP) a falešně negativní (FN). Nejasná matice je jednoduše Tabulka zobrazující počet instancí spadajících do každé z těchto čtyř kategorií. Azure Machine Learning Studio (Classic) automaticky rozhoduje o tom, která ze dvou tříd v datové sadě je pozitivní třída. Pokud jsou popisky třídy logické nebo celé číslo, přiřadí se kladné třídě instance "true" nebo "1" popsaných instancí. Pokud jsou popisky řetězce, například s datovou datovou hodnotou, popisky jsou seřazeny abecedně a první úroveň je zvolena jako záporná třída, zatímco druhá úroveň je pozitivní třída.

![Nejasná matice binární klasifikace](./media/evaluate-model-performance/6a.png)

Obrázek 6. Matice nedorozumění pro binární klasifikaci

Když se vrátíte k problému s klasifikací příjmů, chceme požádat o několik otázek hodnocení, které nám pomůžou pochopit výkon klasifikátoru, který se používá. Přírodní otázka je: "z jednotlivců, které model předpovídá, aby se dostal > 50 K (TP + FP), kolik byl správně klasifikovaný (TP)? Na tuto otázku se dá odpovědět tak, že se podíváme na **přesnost** modelu, což je poměr pozitivních hodnot, které jsou klasifikované správně: transakční/(TP + FP). Dalším běžným dotazem je "vycházející ze všech zaměstnanců s vysokou mírou využívání s příjmem > 50 tis (TP + FN), kolik znamenalo třídění podle správného zařazení do klasifikačního systému (TP). To je ve skutečnosti **odvolání**nebo pravdivá kladová sazba: TP/(TP + FN) třídění. Můžete si všimnout, že mezi přesností a odvoláním je zřejmé kompromis. Například s ohledem na poměrně vyváženou datovou sadu by třídění, který předpovídá hlavně pozitivních instancí, mělo vysoké odvolání, ale spíše nízká přesnost, jakou má mnoho negativních instancí, bylo chybné klasifikování, což vedlo k velkému počtu falešně pozitivních hodnot. Pokud se chcete podívat, jak se tyto dvě metriky liší, můžete na stránce výstup výsledků hodnocení kliknout na křivku **přesnost/odvolání** (v levé horní části obrázku 7).

![Výsledky vyhodnocení binární klasifikace](./media/evaluate-model-performance/7.png)

Obrázek 7. Výsledky vyhodnocení binární klasifikace

Další související metrikou, která se často používá, je **skóre F1**, které bere v úvahu přesnost i odvolání. Jedná se o harmonický průměr těchto dvou metrik a počítá se takto: F1 = 2 (přesnost x Recall)/(přesnost + odvolání). Skóre F1 je dobrým způsobem, jak shrnout vyhodnocení v jednom čísle, ale je vždy dobrým zvykem pohlížet na přesnost i odvolat dohromady, aby lépe pochopila, jak se třídění chová.

Kromě toho může jedna kontrolovat skutečnou kladnou míru vs. falešně pozitivní míra v křivce s **provozní charakteristikou (Roc)** a odpovídající **oblast pod hodnotou křivka (AUC)** . Nablíže tuto křivku k levému hornímu rohu, lepší výkon třídění je (což maximalizuje skutečnou kladnou sazbu při minimalizaci falešně pozitivních sazeb). Křivky, které jsou blízko diagonálního vykreslení, jsou výsledkem klasifikátorů, které mají za následek vytváření předpovědi, které jsou blízko náhodného odhadování.

### <a name="using-cross-validation"></a>Použití vzájemného ověřování
Stejně jako v příkladu regrese můžeme provést vzájemné ověřování, abyste opakovaně provedli výuku, hodnocení a vyhodnotili různé podmnožiny dat automaticky. Podobně můžeme použít modul [modelu křížové validace][cross-validate-model] , nevýukový model logistické regrese a datovou sadu. Sloupec popisku musí být nastaven na hodnotu *výnosy* ve vlastnostech modulu [křížové validace modelu][cross-validate-model] . Po spuštění experimentu a kliknutí na správný výstupní port modulu [model křížové validace][cross-validate-model] se vám pro každé skládání zobrazí hodnoty binární klasifikace klasifikace, kromě středníku a směrodatné odchylky každého. 

![Křížové ověření modelu binární klasifikace](./media/evaluate-model-performance/8.png)

Obrázek 8. Křížové ověřování modelu binární klasifikace.

![Výsledky křížového ověřování binárního klasifikátoru](./media/evaluate-model-performance/9.png)

Obrázek 9: Výsledky křížového ověřování binárního třídění.

## <a name="evaluating-a-multiclass-classification-model"></a>Vyhodnocení modelu klasifikace s více třídami
V tomto experimentu použijeme oblíbenou datovou sadu [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") , která obsahuje instance tří různých typů (tříd) Iris. Pro každou instanci jsou k dispozici čtyři hodnoty funkcí (sepal Length/Width a Petal Length/Width). V předchozích experimentech jsme provedli a otestovali modely pomocí stejných datových sad. V tomto příkladu použijeme modul [rozdělení dat][split] k vytvoření dvou podmnožiny dat, výuku prvního a skóre a vyhodnocení za sekundu. Datová sada Iris je veřejně dostupná v [úložišti UCI Machine Learning](https://archive.ics.uci.edu/ml/index.html)a je možné ji stáhnout pomocí modulu [Import dat][import-data] .

### <a name="creating-the-experiment"></a>Vytvoření experimentu
Přidejte do svého pracovního prostoru následující moduly v Azure Machine Learning Studio (Classic):

* [Importovat data][import-data]
* [Více tříd – rozhodovací doménová struktura][multiclass-decision-forest]
* [Rozdělit data][split]
* [Model výuky][train-model]
* [Model skóre][score-model]
* [Vyhodnotit model][evaluate-model]

Připojte porty, jak je znázorněno na obrázku 10.

Nastavte index sloupce popisku modulu [vlakového modelu][train-model] na 5. Datová sada neobsahuje řádek záhlaví, ale víme, že popisky třídy jsou v pátém sloupci.

Klikněte na modul [importu dat][import-data] a nastavte vlastnost *zdroj dat* na *adresu URL webu přes http*a *adresu URL* , která se má http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Nastavte zlomek instancí, které se mají použít pro školení v modulu [Split data][split] (například 0,7).

![Vyhodnocení třídění s více třídami](./media/evaluate-model-performance/10.png)

Obrázek 10. Vyhodnocení třídění s více třídami

### <a name="inspecting-the-evaluation-results"></a>Kontrola výsledků hodnocení
Spusťte experiment a klikněte na výstupní port [vyhodnoceného modelu][evaluate-model]. Výsledky hodnocení jsou uvedeny ve formě nejasné matrice v tomto případě. Matice zobrazuje skutečné vs. předpovězené instance pro všechny tři třídy.

![Výsledky vyhodnocení klasifikace na více třídách](./media/evaluate-model-performance/11.png)

Obrázek 11. Výsledky vyhodnocení klasifikace na více třídách.

### <a name="using-cross-validation"></a>Použití vzájemného ověřování
Jak bylo zmíněno dříve, můžete automaticky provádět opakované školení, bodování a hodnocení pomocí modulu [model křížové validace][cross-validate-model] . Potřebovali jste datovou sadu, nevlakový model a modul pro [křížové ověřování modelů][cross-validate-model] (viz obrázek níže). Znovu je třeba nastavit sloupec popisku modulu [model křížové validace][cross-validate-model] (index sloupce 5 v tomto případě). Po spuštění experimentu a kliknutí na správný výstupní port [modelu křížové validace][cross-validate-model]můžete zkontrolovat hodnoty metriky pro každé přeložení a také střední a směrodatnou odchylku. Metriky, které jsou zde zobrazeny, jsou podobné těm, které jsou popsány v binárním případu klasifikace. V klasifikaci s více třídami, výpočet skutečných kladných hodnot nebo záporných hodnot a falešně pozitivních hodnot je však provedeno napočítáním na základě třídy, protože neexistuje žádná kladná nebo záporná třída. Například při výpočtu přesnosti nebo odvolání třídy Iris-setosa se předpokládá, že se jedná o pozitivní třídu a všechny ostatní jsou záporné.

![Křížové ověření modelu klasifikace s více třídami](./media/evaluate-model-performance/12.png)

Obrázek 12. Křížové ověření modelu klasifikace s více třídami.

![Výsledky křížového ověřování modelu klasifikace s více třídami](./media/evaluate-model-performance/13.png)

Obrázek 13. Výsledky křížového ověřování modelu klasifikace s více třídami.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
