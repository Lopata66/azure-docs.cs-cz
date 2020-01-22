---
title: 'Převést na sdílený svazek clusteru: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul převést do sdíleného svazku clusteru v Azure Machine Learning k převodu datové sady do formátu CSV, který se dá stáhnout, exportovat nebo sdílet s moduly R nebo Python Script.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 0e92201552b4d448a619a801d2ee64032c8bbefe
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314432"
---
# <a name="convert-to-csv-module"></a>Převést na modul CSV

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete převést datovou sadu na formát CSV, který se dá stáhnout, exportovat nebo sdílet s moduly R nebo Python Script.

### <a name="more-about-the-csv-format"></a>Další informace o formátu CSV 

Formát CSV, který představuje "hodnoty oddělené čárkami", je formát souboru, který používá mnoho externích nástrojů strojového učení. CSV je běžný formát pro výměnu při práci s open source jazyky, jako je R nebo Python.

I v případě, že většinu práce v Azure Machine Learning používáte, v některých případech může být užitečné převést datovou sadu na CSV a použít ji v externích nástrojích. Například:

+ Stáhněte soubor CSV a otevřete ho v Excelu nebo ho importujte do relační databáze.  
+ Uložte soubor CSV do cloudového úložiště a připojte se k němu z Power BI, abyste mohli vytvářet vizualizace.  
+ Pomocí formátu CSV Připravte data pro použití v R a Pythonu. 

Když datovou sadu převedete na CSV, uloží se sdílený svazek clusteru do pracovního prostoru Azure ML. Pomocí nástroje Azure Storage můžete soubor otevřít a použít přímo. Ke sdílenému svazku clusteru se dostanete také tak, že vyberete modul **převést na sdílený svazek clusteru** a potom vyberete ikonu histogramu na kartě **výstupy** na pravém panelu, abyste zobrazili výstup. Sdílený svazek clusteru můžete stáhnout ze složky výsledků do místního adresáře.  

## <a name="how-to-configure-convert-to-csv"></a>Jak nakonfigurovat převod na CSV


1.  Přidejte do svého kanálu modul převést do sdíleného svazku clusteru. Tento modul můžete najít ve skupině **transformace dat** v návrháři. 

2. Připojte ho ke všem modulům, které výstupují datovou sadu.   
  
3.  Spuštění kanálu

### <a name="results"></a>Výsledky
  

Vyberte kartu **výstupy** v pravém panelu **převést na sdílený svazek clusteru**a v části **výstupy portů**vyberte jednu z těchto ikon.  

+ **Registrovat datovou sadu**: vyberte ikonu a uložte soubor CSV zpátky do pracovního prostoru Azure ml jako samostatnou datovou sadu. Datovou sadu můžete najít jako modul ve stromu modulu pod částí **Moje datové sady** .

 + **Zobrazit výstup**: vyberte ikonu oka a postupujte podle pokynů pro procházení složky **Results_dataset** a Stáhněte si soubor data. csv.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 