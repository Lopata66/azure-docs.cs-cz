---
title: Analýza výpovědi zákazníků
titleSuffix: Azure Machine Learning Studio
description: Případová studie vývoje modelu integrované analýzy a vyhodnocování výpovědi zákazníků pomocí Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 320dd28ba22d7919e3f3afdcd8baa47780f363dd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856106"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio"></a>Analýza výpovědí zákazníků pomocí Azure Machine Learning Studio
## <a name="overview"></a>Přehled
Tento článek představuje referenční implementaci projektu pro analýzu přechodu se zákazník je vytvořená pomocí Azure Machine Learning Studio. V tomto článku se podíváme na přidružených modelech obecné pro holistické řešení problému přechodu firemních zákazníků změn. Můžeme také měřit přesnost modelů, které jsou vytvořeny pomocí Machine Learning a posoudit pokynů pro další vývoj.  

### <a name="acknowledgements"></a>Potvrzení
Tento experiment se vývoji a testování Serge Berger, odborník přes Data objektu zabezpečení v Microsoftu a Roger Barga, dříve produktový manažer pro Microsoft Azure Machine Learning Studio. Děkujeme za jejich má tým dokumentace Azure potvrzuje své znalosti a díky pro tento dokument white paper pro sdílení obsahu.

> [!NOTE]
> Data pro tento experiment není veřejně k dispozici. Příklad toho, jak sestavit model strojového učení pro analýzu provozu naleznete v tématu: [Maloobchodní změny šablony modelu](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) v [galerii Azure AI](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Problém výpovědi zákazníků
Firmy na trhu příjemce a ve všech odvětvích enterprise mají se změny. Někdy je příliš vysoká četnost změn a ovlivňuje rozhodnutí o zásadách. Tradiční řešení je k předvídání vysoce tendence churners a řešit jejich potřeby prostřednictvím služby concierge marketingových kampaní, nebo použitím zvláštní výjimky. Tyto přístupy se může lišit od odvětví průmyslu. Můžete dokonce se liší podle konkrétního příjemce clusteru v rámci jednoho oboru (například telekomunikace).

Běžné faktorem je, podniky potřebují, chcete-li minimalizovat tyto aktivity dál rozšiřuji uchování speciální zákazníka. Proto by bylo skóre každý zákazník s pravděpodobnost výpovědi a řešit N nejlepších ty přirozené metody. Hlavní zákazníky může být těm, které jsou nejvíce ziskové. Ve složitějších scénářích je například zisk funkce použijí při výběru kandidáty pro zvláštní výjimku. Tyto aspekty jsou však pouze součástí komplexní strategie pro řešení změn. Také firem vzít v účtu rizika (a související odolnosti vůči rizikům), úroveň a náklady na zásah a segmentu přesvědčivého zákazníků.  

## <a name="industry-outlook-and-approaches"></a>Outlook odvětví a přístupů
Sofistikované zpracování změn je znak až po zralé odvětví. Klasickým příkladem je odvětví telekomunikace, ve kterém se ví často přepínání z jednoho poskytovatele do jiného předplatitele. Těchto dobrovolné častých změn dat je primárním zájmem. Kromě toho poskytovatelé nashromáždili významné znalosti o *změny ovladače*, které jsou faktory, které řídí zákazníkům přepnout.

Například telefonu nebo zařízení je dobře známé ovladač četností změn ve firmě mobilního telefonu. Oblíbené zásady v důsledku toho je subvencovat cena telefonu pro nové předplatitele a účtovat plné ceny pro stávající zákazníky na upgrade. Tyto zásady v minulosti vedlo k zákazníkům přepínání z jednoho poskytovatele do druhého a získat tak nové slevu. To, pak má zprostředkovatele pro upřesnění jejich strategie výzva.

Vysoká volatility v nabídkách telefonního sluchátka je faktor, který rychle zruší platnost modely četností změn, které jsou založeny na aktuální modely telefonu. Kromě toho mobilní telefony nejsou pouze telekomunikační zařízení, jsou také podporuje příkazy (vezměte v úvahu iPhone). Tyto sociální prediktory jsou nad rámec regulární telekomunikace datových sad.

Net výsledek pro modelování je zvukový zásad nelze navrhnout jednoduše tak, že odstranění známých důvody pro změny. Strategie průběžné modelování, včetně klasické modely, které umožňuje vyčíslit zařazené do kategorií proměnné (jako je například rozhodovacích stromů), se ve skutečnosti **povinné**.

Použití velkých datových sad na zákazníky, organizace provádění analýzy velkých objemů dat (zejména detekce změn na základě velkých objemů dat) jako efektivního přístupu k problému. Můžete najít další informace o přístupu velkých objemů dat na problém ve doporučení v části ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologie k fluktuaci zákazníků modelu
Běžné řešení problémů procesu k vyřešení výpovědi zákazníků je znázorněn v obrázky 1 – 3:  

1. Model rizika umožňuje zvažte, jaký vliv na akce pravděpodobnosti a rizika.
2. Model zásah můžete vzít v úvahu, jak úroveň zásah by mohly ovlivnit pravděpodobnost výpovědi a množství zákazníků hodnotu doby života (CLV).
3. Tato analýza různě kvalitativní analýze, který je eskalován jej proaktivní marketingovou kampaň, která cílí na segmenty zákazníků k zajištění optimální nabídky.  

![Diagram znázorňující, jak modely proti chybám a rozhodnutí rizika poskytuje užitečné přehledy](./media/azure-ml-customer-churn-scenario/churn-1.png)

Tento přístup vpřed vypadající je nejlepší způsob, jak zpracovávat změny, ale obsahuje složitost: máme pro vývoj vícemodelová archetype a trasování závislosti mezi modely. Interakce mezi modely lze zapouzdřit, jak je znázorněno v následujícím diagramu:  

![Změny v diagramu interakce modelu](./media/azure-ml-customer-churn-scenario/churn-2.png)

*Obrázek 4: Sjednocené archetype více modelů*  

Interakce mezi modely je klíč, pokud se nám poskytovat holistický přístup k udržení zákazníků. Každý model nutně sníží, přetrénujte časem; Tato architektura je proto implicitní smyčky (archetype nastavit Standard OSTRÉ-DM dolování dat, podobně jako [***3***]).  

Celkové cyklu riziko rozhodnutí marketingových segmentace/rozložené je stále zobecněný strukturu, která platí pro mnoho obchodních problémů. Analýzy změn je jednoduše silné zástupce této skupiny problémů, protože vykazuje osobnostní rysy komplexní obchodní problém, který neumožňuje zjednodušené prediktivní řešení. Sociální aspektů moderní přístup ke změny nejsou zejména zvýrazněno přístup, ale na sociálních sítích aspekty jsou zapouzdřeny v archetype modelování, jako by byly v jakékoli modelu.  

Doplněk zajímavé zde je analýzy velkých objemů dat. Dnešní telekomunikace a prodejní firmy shromažďování vyčerpávající dat o svých zákazníků a jsme můžete snadno předvídat, potřebné pro více modelů připojení se stanou běžné trend dané nově vznikající trendy, jako je například Internet věcí a všudypřítomná zařízení, které umožňují firmy, abyste mohli využívat inteligentní řešení na více úrovních.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementace archetype modelování v nástroji Machine Learning Studio
Daný problém popsaný, co je nejlepší způsob, jak implementovat integrované modelování a vyhodnocování přístup? V této části si předvedeme, jak jsme provádí to s využitím Azure Machine Learning Studio.  

Více modelů přístup je nezbytnost při navrhování globální archetype pro změny. I bodování (prediktivní) část přístup by měl být více modelů.  

Následující diagram znázorňuje prototyp, že jsme vytvořili, která používá čtyři bodování algoritmy Machine Learning Studio k předvídání změny. Důvod pro více modelů přístup je jenom pro účely vytvoření třídění skupiny stromů zvýšit přesnost, ale také pro ochranu před útoky over-pass-the přizpůsobování a zlepšení výběr Doporučené funkce.  

![Snímek obrazovky znázorňující komplexní pracovní prostor Studio s mnoha propojených modulů](./media/azure-ml-customer-churn-scenario/churn-3.png)

*Obrázek 5: Prototyp četností modelování přístup*  

Následující části obsahují další podrobnosti o prototypu vyhodnocování model, který jsme implementovali pomocí Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Výběr dat a příprava
Data se používají k vytváření modelů a zákazníci skóre byl získán z svislé řešení CRM, s daty obfuskovaný na ochranu soukromí zákazníků. Data obsahují informace o předplatných 8 000 v USA a kombinuje tři zdroje: zřizování data (metadata odběru), data o aktivitě (použití systému) a data podpory zákazníků. Data neobsahuje žádné obchodní informace o zákaznících; nezahrnuje například metadata nebo kredit skóre věrnostních programů.  

Pro zjednodušení ETL a procesy pro čištění dat jsou mimo rozsah protože předpokládáme, že už má přípravu dat neprovedlo jinde.

Výběr funkce pro modelování je založená na významu předběžné vyhodnocení sady prediktory, součástí procesu, který používá modul náhodné doménové struktury. Pro implementaci v nástroji Machine Learning Studio můžeme vypočítat průměr, medián a rozsahů reprezentativní funkcí. Například jsme přidali agregace kvalitativní dat, jako je například minimální a maximální hodnoty pro aktivity uživatelů.

Také zachyceného dočasné informace pro posledních šest měsíců. Analyzovali jsme data po dobu jednoho roku a nastavení jsme, že i v případě, že došlo k statisticky významná trendy, vliv na četnosti změn je výrazně oslabená po šesti měsících.  

Nejdůležitější bod je, že celý proces, včetně ETL, výběr funkce pro modelování a bylo implementováno v nástroji Machine Learning Studio, použití zdrojů dat v Microsoft Azure.   

Následující obrázky znázorňují data, která byla použita.  

![Snímek obrazovky zobrazující ukázku datům používaným prostřednictvím nezpracované hodnoty](./media/azure-ml-customer-churn-scenario/churn-4.png)

*Obrázek 6: Výňatek ze zdroje dat (obfuskovaný)*  

![Snímek obrazovky zobrazující statistické funkce extrahovaná ze zdroje dat](./media/azure-ml-customer-churn-scenario/churn-5.png)

*Obrázek 7: Funkce extrahovaná ze zdroje dat*
 

> Všimněte si, že tato data jsou privátní a proto není možné model a data sdílet.
> Ale podobný modelu pomocí veřejně dostupných dat, naleznete v tématu této ukázce experimentovat v [galerii Azure AI](https://gallery.azure.ai/): [Výpovědi zákazníků](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Další informace o implementace modelu analysis změn pomocí Cortana Intelligence Suite, doporučujeme také [toto video](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) tak Tok Hyong praco vedoucí manažer programu. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmy používané v prototypu
Následující čtyři algoritmů strojového učení jsme použili k vytvoření prototypu (bez přizpůsobení):  

1. Logistické regrese (LR)
2. Posílený rozhodovací strom (BT)
3. Průměrné perceptron (AP)
4. Podpora vector machine (SVM)  

Následující diagram znázorňuje části návrhové plochy experiment, který určuje pořadí, ve kterém byly vytvořeny modely:  

![Snímek obrazovky malá část studio experiment plátna](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*Obrázek 8: Vytváření modelů ve službě Machine Learning Studio*  

### <a name="scoring-methods"></a>Vyhodnocení metody
Jsme skóre čtyři modely s využitím s popiskem trénovací datové sady.  

Můžeme také odeslat bodování datovou sadu, která srovnatelné model sestavený pomocí klasické pracovní plochy edice SAS Enterprise Miner 12. Jsme měří přesnost modelu SAS a všechny čtyři modely Machine Learning Studio.  

## <a name="results"></a>Výsledky
V této části Představujeme naše poznatky o přesnost modelů, které jsou založené na hodnocení datové sadě.  

### <a name="accuracy-and-precision-of-scoring"></a>Přesnost a přesnost bodování
Obecně platí implementace v Azure Machine Learning Studio je za SAS v přesnost přibližně 10 – 15 % (oblasti pod křivkou nebo AUC).  

Nejdůležitější metriky v četnosti změn je ale frekvence chybnou: to znamená z hlavních churners jako předpokládané pomocí třídění, který z nich ve skutečnosti nebyla **není** změny a nepřijal zvláštní zacházení? Následující diagram porovnává tohoto kurzu chybnou pro všechny modely:  

![Oblasti pod křivkou porovnání výkonu 4 algoritmy](./media/azure-ml-customer-churn-scenario/churn-7.png)

*Obrázek 9: Passau prototypu oblasti pod křivkou*

### <a name="using-auc-to-compare-results"></a>Použití AUC k porovnání výsledků
Oblasti v rámci křivky (AUC) je metrika, který představuje globální měřítko *separability* mezi distribucí skóre, které se pro plnění kladné a záporné. Se podobá tradiční grafu příjemce operátor charakteristiku (roc s více TŘÍDAMI), ale jeden důležitý rozdíl je, že AUC metrika není nutné zvolit prahovou hodnotu. Místo toho shrnuje výsledky přes **všechny** možností. Naproti tomu tradiční roc s více TŘÍDAMI graf zobrazuje míru pozitivních výsledků svislá osa a míru falešně pozitivních výsledků na vodorovné ose a prahovou hodnotu klasifikace se liší.   

AUC je použít jako měřítko vhodné pro různé algoritmy (nebo jiné systémy), protože umožňuje modelů, který se má porovnat pomocí hodnoty jejich AUC. Toto je oblíbený přístup v oborech jako jsou meteorologické a biosciences. Proto AUC představuje oblíbený nástroj pro vyhodnocení výkonu třídění.  

### <a name="comparing-misclassification-rates"></a>Porovnání chybnou sazby
Porovnáním chybnou sazby na této datové sadě s použitím dat CRM přibližně 8 000 předplatných.  

* Míra chybnou SAS se 10 až 15 %.
* Míra chybnou Machine Learning Studio se 15 až 20 % po dobu prvních 200 300 churners.  

V odvětví telekomunikace je potřeba vyřešit pouze zákazníci, kteří mají nejvyšší riziko pro změny tím, že je nabízí službu concierge nebo jiné speciální zacházení. V tomto ohledu dosahuje implementace Machine Learning Studio výsledky jsou modelu SAS.  

Ze stejného důvodu je důležitější než přesnost přesnost, protože nás zajímají hlavně správně klasifikace potenciální churners.  

Z Wikipedia následující diagram znázorňuje vztah živá, snadno pochopitelné obrázku:  

![Dva cíle. Ukazuje jeden cíl přístupů značky volně seskupené ale téměř terče označený "nedostatek přesnost: dobré pravdivost, nízký přesnosti. Jiný cíl úzce seskupené ale daleko od terče označena "nedostatek přesnost: špatná pravdivost, dobré přesnosti"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*Obrázek 10: Kompromis mezi přesnost*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Přesnost výsledků pro model posíleného rozhodovacího stromu
Následující graf zobrazí nezpracované výsledky vyhodnocování pomocí prototypu Machine Learning pro model Posílený rozhodovací strom, což je nejpřesnější mezi čtyři modely:  

![Fragment kódu tabulka zobrazující přesnost, zaokrouhlení, odvolání, skóre F, AUC, průměrná ztráta protokolu a ztráta protokolu školení pro čtyři algoritmy](./media/azure-ml-customer-churn-scenario/churn-9.png)

*Obrázek 11: Vlastnosti model posíleného rozhodovacího stromu*

## <a name="performance-comparison"></a>Porovnání výkonu
Porovnání jsme rychlost, jakou se data za použití modelů Machine Learning Studio a srovnatelné model vytvořený pomocí klasické pracovní plochy edice SAS Enterprise Miner 12.1.  

Následující tabulka shrnuje výkon algoritmy:  

*Tabulka 1. Obecné informace o výkonu (přesnost) z algoritmů*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Průměrná modelu |Nejlepší Model |Nevedou podle očekávání |Průměrná modelu |

Modelech hostovaných v nástroji Machine Learning Studio překonal SAS 15-25 % pro rychlost provádění, ale přesnost je do značné míry na pamětích.  

## <a name="discussion-and-recommendations"></a>Informace a doporučení
V odvětví telekomunikace vznikly několik postupů k analýze četnosti změn, včetně:  

* Odvození metriky pro čtyři základní kategorie:
  * **Entity (například předplatné)**. Zřízení základních informací o odběru a/nebo zákazníkovi, který je předmětem změn.
  * **Aktivita**. Získáte všechny informace o možných využití, která souvisí s entitou, například počet přihlášení.
  * **Zákaznická podpora**. Získejte informace z protokolů podpory zákazníků k označení, zda u odběru byl problémy nebo interakce s zákaznickou podporu.
  * **Konkurenceschopnost a obchodní data**. Získat všechny informace o možných o zákazníkovi (například může být obtížné sledovat nebo není k dispozici).
* Použijte význam pro výběr funkcí jednotky. Z toho vyplývá, že model posíleného rozhodovacího stromu je vždy slibně přístup.  

Použijte tyto čtyři kategorie vytváří iluzi, který jednoduchý *deterministické* přístup založený na indexy vytvořené v rozumné faktory podle jednotlivých kategorií, by měla stačit k určení zákazníků riziko pro změny. Bohužel i když se vyskytují zdá se, že přesvědčivého, je false principy. Důvodem je, že změny jsou dočasné účinek a faktory přispívající k změn dat se obvykle nacházejí v přechodném stavu. Čeho zákazník vzít v úvahu byste museli opustit ještě dnes se může lišit zítra, a jistě bude různých šest měsíců od této chvíle. Proto *pravděpodobnostní* je nezbytné v modelu.  

Tato důležité zjišťování je často přehlédnuta ve firmě, což obecně upřednostňuje analýzy a business intelligence objektově orientovaný přístup, většinou, protože se jedná jednodušší prodávat a zavést jednoduché automatizace.  

Ale promise samoobslužné analýzy pomocí Machine Learning Studio, že je čtyři kategorie informací, divize nebo oddělení, se stupněm cenné zdroje pro machine learning o četnosti změn.  

Další zajímavé funkce v Azure Machine Learning Studio je možnost přidávat vlastní modul do úložiště předdefinované modulů, které jsou k dispozici. Tato funkce, které vytvoří v podstatě možnost vybrat knihovny a vytvořit šablony pro vertikální trhy. Jde důležitý rozdíl placenými nástroje Azure Machine Learning Studio na místo na trhu.  

Věříme, že chcete pokračovat v tomto tématu v budoucnu, zejména související s analýzy velkých objemů dat.
  

## <a name="conclusion"></a>Závěr
Tento dokument popisuje rozumné přístup k řešení běžných problémů výpovědi zákazníků pomocí obecného rozhraní. Můžeme považovat za prototyp pro vyhodnocení modelů a implementovaný s využitím Azure Machine Learning Studio. Nakonec jsme posuzuje přesnost a výkon prototypu řešení s ohledem na srovnatelné algoritmy v SAS.  

 

## <a name="references"></a>Odkazy
[1] prediktivní analýzy: Kromě správy informace Predikcí, západ McKnight, července/Srpen 2011, p.18 20.  

[2] článku na wikipedii: [Přesnost](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Průvodce krok za krokem dat dolování](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing velké objemy dat: Upoutat zákazníky efektivněji a posílení hodnoty](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco změny šablony modelu](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) v [galerii Azure AI](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Příloha
![Snímek prezentace na změny v prototypu](./media/azure-ml-customer-churn-scenario/churn-10.png)

*Obrázek 12: Snímek prezentace na změny v prototypu*
