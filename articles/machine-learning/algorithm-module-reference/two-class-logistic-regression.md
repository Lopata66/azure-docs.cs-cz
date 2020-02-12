---
title: 'Logistické regrese dvou tříd: reference na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu logistické regrese dvou tříd v Azure Machine Learning vytvořit model logistické regrese, který se dá použít k předpovědi dvou (a jenom dvou) výsledků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 541d1001f8b5881f2773f795d7bd849704cbd796
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153685"
---
# <a name="two-class-logistic-regression-module"></a>Modul logistické regrese se dvěma třídami

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete vytvořit model logistické regrese, který se dá použít k předpovědi dvou (a jenom dvou) výsledků. 

Logistická regrese je dobře známá statistická technika, která se používá k modelování mnoha druhů problémů. Tento algoritmus je metoda *učení pod dohledem* ;  Proto je nutné poskytnout datovou sadu, která již obsahuje výsledky pro výuku modelu.  

### <a name="about-logistic-regression"></a>O logistické regresi  

Logistická regrese je dobře známá metoda, která se používá k předpovědi pravděpodobnosti výsledku, a je obzvláště oblíbená pro úlohy klasifikace. Algoritmus předpovídá pravděpodobnost výskytu události tím, že se data namontuje na logistickou funkci.
  
V tomto modulu je klasifikační algoritmus optimalizovaný pro dichotomous nebo binární proměnné. Pokud potřebujete klasifikovat více výsledků, použijte modul [logistické regrese](./multiclass-logistic-regression.md) pro více tříd.

##  <a name="how-to-configure"></a>Jak nakonfigurovat  

Chcete-li tento model naučit, je nutné poskytnout datovou sadu, která obsahuje sloupec popisku nebo třídy. Vzhledem k tomu, že tento modul je určený pro problémy se dvěma třídami, sloupec popisku nebo třídy musí obsahovat přesně dvě hodnoty. 

Sloupec popisku může být například [hlasovalo] s možnými hodnotami "Ano" nebo "ne". Nebo může to být [úvěrové riziko] s možnými hodnotami "vysoká" nebo "nízká". 
  
1.  Přidejte do svého kanálu modul **logistické regrese dvou tříd** .  
  
2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.  
  
3.  V případě **tolerance optimalizace**zadejte mezní hodnotu, která se má použít při optimalizaci modelu. Pokud vylepšení mezi iteracemi klesne pod zadanou prahovou hodnotu, je algoritmus považován za sblížený v řešení a školení se zastaví.  
  
4.  Pro **váhu regulárních hodnot L1** a **váhu pravidelného použití L2**zadejte hodnotu, která se má použít pro parametry pro pravidelnost L1 a L2. Pro obojí se doporučuje nenulová hodnota.  
     *Pravidelná* metoda představuje způsob, jak zabránit přeložení pomocí postihu modelů s hodnotami extrémního součinitele. Pravidelnou práci můžete provést přidáním pokuty, která je spojená s hodnotami koeficientu, na chybu hypotézy. Přesný model s hodnotami extrémních koeficientů by tedy byl potrestánější, ale méně přesný model s více konzervativními hodnotami by byl méně trestný.  
  
     Pravidelná L1 a L2 mají různé účinky a použití.  
  
    -   L1 se dá použít na řídké modely, což je užitečné při práci s vysokými objemy dat.  
  
    -   Na rozdíl od je pro data, která nejsou zhuštěná, vhodnější pravidelná navýšení L2.  
  
     Tento algoritmus podporuje lineární kombinaci hodnot L1 a L2 pro účely depravidelnosti: to znamená, že pokud <code>x = L1</code> a <code>y = L2</code>, <code>ax + by = c</code> definuje lineární rozsah pro účely pravidelného navýšení podmínek.  
  
    > [!NOTE]
    >  Chcete získat další informace o L1 a propravidelnosti L2? Následující článek poskytuje diskuzi o tom, jak se v proneuronovéch L1 a L2 liší a jak mají vliv na model, s ukázkami kódu pro logistické regrese a síťové modely: [L1 a L2 pro Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Pro logistické regresní modely byly navrženy různé lineární kombinace L1 a L2: například [elastické čisté](https://wikipedia.org/wiki/Elastic_net_regularization)účely. Doporučujeme, abyste na tyto kombinace odkazovali a definovali lineární kombinaci, která je v modelu platná.
      
5.  Pro **velikost paměti pro l-BFGS**určete velikost paměti, která se má použít pro optimalizaci *l-BFGS* .  
  
     L-BFGS představuje "omezené paměťové Broyden-Fletcher-Goldfarb-Shanno". Jedná se o optimalizační algoritmus, který je oblíbený pro odhad parametru. Tento parametr určuje počet minulých pozic a přechodů, které se mají uložit pro výpočet dalšího kroku.  
  
     Tento parametr optimalizace omezuje velikost paměti, která se používá k výpočtu dalšího kroku a směru. Když zadáte méně paměti, školení je rychlejší, ale je méně přesné.  
  
6.  V případě **náhodného čísla**zadejte celočíselnou hodnotu. Definování počáteční hodnoty je důležité, pokud chcete, aby byly výsledky reprodukovatelné přes více spuštění stejného kanálu.  
  
  
8. Přidejte do kanálu příznakovou datovou sadu a propojte jeden ze [školicích modulů](module-reference.md).  
  
    -   Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](./train-model.md) .  
  
9. Spuštění kanálu  
  
## <a name="results"></a>Výsledky

Po dokončení školení:
 
  
+ Chcete-li vytvořit předpovědi pro nová data, použijte školený model a nová data jako vstup do modulu určení [skóre modelu](./score-model.md) . 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 