---
title: Identifikace scénářů pro Azure Machine Learning – vědecké zpracování týmových dat
description: Vyberte odpovídající scénáře pro pokročilé prediktivní analytiky pomocí vědeckého zpracování týmových dat tím.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 130ab6ee8e59b157d1fbdd8769f14814445dea18
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202746"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scénáře pro pokročilé analýzy ve službě Azure Machine Learning
Tento článek popisuje různé ukázkové zdroje dat a cílové scénáře, které mohou být zpracovány [vědecké zpracování týmových dat (TDSP)](overview.md). TDSP nabízí systematický přístup pro týmy spolupracovat na sestavování inteligentních aplikací. Scénáře uvedené tady ukazují možnosti dostupné v pracovním postupu zpracování dat, které se liší podle vlastností dat, zdrojová umístění a cíl úložiště v Azure.

**Rozhodovací strom** pro výběr ukázkové scénáře, které je vhodné pro vaše data a cíl je uveden v předchozí části.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Každá z těchto částí představuje vzorový scénář. Pro každý scénář možné datové vědy a pokročilé analýzy toku a podpůrné prostředky Azure, jsou uvedeny.

> [!NOTE]
> **Pro všechny z následujících scénářů budete muset:**
> <br/>
> 
> * [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md)
>   <br/>
> * [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scénář \#1: Malé a střední tabulkové datové sady v místních souborů
![Malé a střední místní soubory][1]

#### <a name="additional-azure-resources-none"></a>Další prostředky Azure: Žádný
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Nahrajte datovou sadu.
1. Vytvoření toku experimentu Azure Machine Learning počínaje nahrané datových sad.

## <a name="smalllocalprocess"></a>Scénář \#2: Malé a střední datové sady místních souborů, které vyžadují zpracování
![Malé a střední místních souborů se zpracování][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (IPython Notebook server)
1. Vytvořte virtuální počítač Azure s IPython Notebook.
1. Nahrání dat do kontejneru služby Azure storage.
1. Předběžně zpracovat a vyčistit data v IPython Notebook, přístup k datům z kontejneru úložiště Azure.
1. Transformace data vyčistit formě tabulky.
1. Uložení transformovaných dat v objektech BLOB Azure.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečíst data z Azure objektů BLOB pomocí [Import dat] [ import-data] modulu.
1. Vytvoření toku experimentu Azure Machine Learning počínaje přijaté datových sad.

## <a name="largelocal"></a>Scénář \#3: Velké datové sady místních souborů, které cílí na objektech BLOB Azure
![Velké místní soubory][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (IPython Notebook server)
1. Vytvořte virtuální počítač Azure s IPython Notebook.
1. Nahrání dat do kontejneru služby Azure storage.
1. Předběžně zpracovat a vyčistit data v IPython Notebook, přístup k datům v objektech BLOB Azure.
1. Transformace data vyčistit formě tabulky, v případě potřeby.
1. Zkoumání dat a podle potřeby vytvořit funkce.
1. Extrahujte ukázková data malé až střední.
1. Uložte jen Vzorkovaná data v objektech BLOB Azure.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečíst data z Azure objektů BLOB pomocí [Import dat] [ import-data] modulu.
1. Vytvoření toku experiment Azure Machine Learning počínaje přijaté datových sad.

## <a name="smalllocaltodb"></a>Scénář \#4: Malé a střední datové sady místních souborů, které cílí na SQL Server ve virtuálním počítači Azure
![Malé a střední místní soubory do databáze SQL v Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / IPython Notebook server)
1. Vytvořte virtuální počítač Azure s SQL Server + IPython Notebook.
1. Nahrání dat do kontejneru služby Azure storage.
1. Předběžně zpracovat a vyčistit data v kontejneru úložiště Azure pomocí IPython Notebook.
1. Transformace data vyčistit formě tabulky, v případě potřeby.
1. Ukládání dat do virtuálního počítače – místní soubory (IPython Notebook běží na virtuálním počítači najdete místní jednotky na virtuálním počítači jednotky).
1. Načtení dat do databáze serveru SQL Server běžící na Virtuálním počítači Azure.
   
   Možnost \#1: Pomocí aplikace SQL Server Management Studio.
   
   * Přihlaste se k serveru SQL Server VM
   * Spusťte SQL Server Management Studio.
   * Vytvoření databáze a cílové tabulky.
   * Použijte některou z hromadného importu metody k načtení dat z virtuálního počítače – místní soubory.
   
   Možnost \#2: Pomocí IPython Notebook – není vhodné pro střední nebo větší datové sady
   
   <!-- -->    
   * Použijte připojovací řetězec ODBC pro přístup k systému SQL Server na virtuálním počítači.
   * Vytvoření databáze a cílové tabulky.
   * Použijte některou z hromadného importu metody k načtení dat z virtuálního počítače – místní soubory.
1. Zkoumání dat, vytváření funkcí, podle potřeby. Všimněte si, že funkce nemusí být vyhodnocena v databázových tabulkách. Mějte na paměti pouze nezbytné dotazu k jejich vytvoření.
1. Při rozhodování velikost vzorku dat, pokud potřebné a/nebo potřeby.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečíst data přímo z SQL serveru pomocí [Import dat] [ import-data] modulu. Vložte potřebné dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data, v případě potřeby přímo v [Import dat] [ import-data] dotazu.
1. Vytvoření toku experiment Azure Machine Learning počínaje přijaté datových sad.

## <a name="largelocaltodb"></a>Scénář \#5: Velké datové sady v místních souborů cílit na SQL Server na virtuálním počítači Azure
![Velké místní soubory databáze SQL v Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / IPython Notebook server)
1. Vytvořte virtuální počítač Azure s SQL serveru a serveru IPython Notebook.
1. Nahrání dat do kontejneru služby Azure storage.
1. (Volitelné) Předběžně zpracovat a vyčistit data.
   
   a.  Předběžně zpracovat a vyčistit data v IPython Notebook, přístup k datům z Azure
   
       blobs.
   
   b.  Transformace data vyčistit formě tabulky, v případě potřeby.
   
   c.  Ukládání dat do virtuálního počítače – místní soubory (IPython Notebook běží na virtuálním počítači najdete místní jednotky na virtuálním počítači jednotky).
1. Načtení dat do databáze serveru SQL Server běžící na Virtuálním počítači Azure.
   
   a.  Přihlaste se k virtuálnímu počítači SQL serverem.
   
   b.  Pokud data nejsou již uložena, stahování datových souborů z Azure
   
       storage container to local-VM folder.
   
   c.  Spusťte SQL Server Management Studio.
   
   d.  Vytvoření databáze a cílové tabulky.
   
   e.  Použijte některou z hromadného importu metody, které chcete načíst data.
   
   f.  Pokud požadujete spoje tabulky platná, vytváření indexů pro urychlení spojení.
   
   > [!NOTE]
   > Pro rychlejší načítání velké objemy dat, je doporučeno, můžete vytvořit dělené tabulky a hromadného importu dat paralelně. Další informace najdete v tématu [paralelní Import dat do dělené tabulky SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Zkoumání dat, vytváření funkcí, podle potřeby. Všimněte si, že funkce nemusí být vyhodnocena v databázových tabulkách. Mějte na paměti pouze nezbytné dotazu k jejich vytvoření.
1. Při rozhodování velikost vzorku dat, pokud potřebné a/nebo potřeby.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečíst data přímo z SQL serveru pomocí [Import dat] [ import-data] modulu. Vložte potřebné dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data, v případě potřeby přímo v [Import dat] [ import-data] dotazu.
1. Jednoduchý experiment tok Azure Machine Learning, počínaje nahraná datová sada

## <a name="largedbtodb"></a>Scénář \#6: Velkou datovou sadu v databázi SQL serveru v místním prostředí, cílení na serveru SQL Server ve virtuálním počítači Azure
![Velké SQL DB místní k databázi SQL v Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / IPython Notebook server)
1. Vytvořte virtuální počítač Azure s SQL serveru a serveru IPython Notebook.
1. Použijte některou z data exportu metod exportovat data z SQL serveru do souborů s výpisem paměti.
   
   > [!NOTE]
   > Pokud se rozhodnete přesunout všechna data z on premises databáze alternativní metodu (rychlejší) přesunout celé databáze do instance systému SQL Server v Azure. Přeskočte kroky k exportovat data, vytvořit databázi a zatížení a import dat do cílové databáze a použijte alternativní metodu.
   > 
   > 
1. Nahrajte soubory s výpisem paměti do kontejneru úložiště Azure.
1. Načtení dat do databáze SQL serveru spuštěná na virtuálním počítači Azure.
   
   a.  Přihlaste se k virtuálnímu počítači SQL serverem.
   
   b.  Stahování datových souborů do složky místního virtuálního počítače z kontejneru úložiště Azure.
   
   c.  Spusťte SQL Server Management Studio.
   
   d.  Vytvoření databáze a cílové tabulky.
   
   e.  Použijte některou z hromadného importu metody, které chcete načíst data.
   
   f.  Pokud požadujete spoje tabulky platná, vytváření indexů pro urychlení spojení.
   
   > [!NOTE]
   > Rychlejší načítání velké objemy dat, vytváření oddílů tabulky a k hromadnému importu paralelně. Další informace najdete v tématu [paralelní Import dat do dělené tabulky SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Zkoumání dat, vytváření funkcí, podle potřeby. Všimněte si, že funkce nemusí být vyhodnocena v databázových tabulkách. Mějte na paměti pouze nezbytné dotazu k jejich vytvoření.
1. Při rozhodování velikost vzorku dat, pokud potřebné a/nebo potřeby.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečíst data přímo z SQL serveru pomocí [Import dat] [ import-data] modulu. Vložte potřebné dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data, v případě potřeby přímo v [Import dat] [ import-data] dotazu.
1. Počínaje nahraná datová sada jednoduchý tok s experiment Azure Machine Learning.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativní způsob zkopírovat celé databáze z místního SQL serveru do služby Azure SQL Database
![Místní databáze odpojit a připojit k databázi SQL v Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / IPython Notebook server)
Replikovat celou databázi systému SQL Server ve virtuálním počítači SQL serveru, měli byste zkopírovat databázi z jednoho umístění/serveru na jiný, za předpokladu, že databáze můžete provést dočasně v režimu offline. To provedete v Průzkumník objektů systému SQL Server Management Studio nebo pomocí ekvivalentní příkazy jazyka Transact-SQL.

1. Odpojení databáze v umístění zdroje. Další informace najdete v tématu [odpojit databázi](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. V okně Průzkumníka Windows nebo na příkazovém řádku Windows zkopírujte soubor odpojenou databázi nebo soubory a soubor protokolu nebo soubory do cílového umístění na virtuálním počítači SQL serveru v Azure.
1. Zkopírované soubory připojení k cílové instanci SQL serveru. Další informace najdete v tématu [připojit databázi](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Přesunout databázi pomocí odpojit a připojit (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scénář \#7: Velké objemy dat v místních souborů, cílová databáze Hive v clusterech Azure HDInsight Hadoop
![Velké objemy dat v místních cílových Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Azure HDInsight Hadoop Cluster a virtuální počítač Azure (IPython Notebook server)
1. Vytvořte virtuální počítač Azure s serveru IPython Notebook.
1. Vytvoření clusteru Azure HDInsight Hadoop.
1. (Volitelné) Předběžně zpracovat a vyčistit data.
   
   a.  Předběžně zpracovat a vyčistit data v IPython Notebook, přístup k datům z Azure
   
       blobs.
   
   b.  Transformace data vyčistit formě tabulky, v případě potřeby.
   
   c.  Ukládání dat do virtuálního počítače – místní soubory (IPython Notebook běží na virtuálním počítači najdete místní jednotky na virtuálním počítači jednotky).
1. Nahrání dat do výchozího kontejneru cluster Hadoop v kroku 2 vybrali.
1. Načtení dat do databáze Hive v clusteru Azure HDInsight Hadoop.
   
   a.  Přihlaste se k hlavnímu uzlu clusteru Hadoop
   
   b.  Otevřete příkazový řádek systému Hadoop.
   
   c.  Zadejte kořenový adresář Hive příkazem `cd %hive_home%\bin` příkazového řádku v Hadoopu.
   
   d.  Spouštění dotazů Hive k vytvoření databáze a tabulky a načtení dat z úložiště objektů blob do tabulky Hive.
   
   > [!NOTE]
   > Pokud jsou data velká, uživatelé mohou vytvářet tabulky Hive s oddíly. Uživatelé pak můžou použít `for` smyčky v Hadoop příkazový řádek hlavního uzlu pro načtení dat do tabulky Hive rozdělené podle oddílu.
   > 
   > 
1. Zkoumání dat a vytvoření funkcí, podle potřeby v Hadoop příkazového řádku. Všimněte si, že funkce nemusí být vyhodnocena v databázových tabulkách. Mějte na paměti pouze nezbytné dotazu k jejich vytvoření.
   
   a.  Přihlaste se k hlavnímu uzlu clusteru Hadoop
   
   b.  Otevřete příkazový řádek systému Hadoop.
   
   c.  Zadejte kořenový adresář Hive příkazem `cd %hive_home%\bin` příkazového řádku v Hadoopu.
   
   d.  Spouštění dotazů Hive v příkazového řádku Hadoopu hlavního uzlu clusteru Hadoop zkoumat data a vytvářet funkce, podle potřeby.
1. Pokud třeba a/nebo potřeby, ukázková data pro Azure Machine Learning Studio.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Čtení dat přímo z `Hive Queries` pomocí [Import dat] [ import-data] modulu. Vložte potřebné dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data, v případě potřeby přímo v [Import dat] [ import-data] dotazu.
1. Počínaje nahraná datová sada jednoduchý tok s experiment Azure Machine Learning.

## <a name="decisiontree"></a>Rozhodovací strom pro výběr scénáře
---
Následující diagram obsahuje souhrn scénáře popsané výše a pokročilé analýzy procesu a technologické volby, která můžete přejít k jednotlivé rozepsané scénáře. Všimněte si, že může trvat zpracování dat, průzkum, vytváření funkcí a vzorkování umístit do jednoho nebo více metoda/prostředí – ve zdroji, středně pokročilý, a/nebo cílové prostředí – a může pokračovat zavádět postupně, podle potřeby. Diagram pouze slouží jako ilustraci některé možné toků a neposkytuje kompletní výčet.

![Vzorové DS procesu návod scénáře][8]

### <a name="advanced-analytics-in-action-examples"></a>Pokročilé analýzy v akci příklady
Návody začátku do konce Azure Machine Learning, které využívají pokročilé analýzy procesu a technologie, pomocí veřejných datových sad naleznete v tématu:

* [Týmu vědecké zpracování dat v akci: použití serveru SQL Server](sql-walkthrough.md).
* [Týmu vědecké zpracování dat v akci: clusterů systému HDInsight Hadoop](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
