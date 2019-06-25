---
title: Začínáme s R
titleSuffix: Azure Machine Learning Studio
description: Pomocí tohoto kurzu programovacího jazyka R a začněte využívat jazyka R s Azure Machine Learning Studio k vytvoření řešení Prognózování.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 5c4fa2260b00043e016748010528926b1b9d74a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726533"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio"></a>Začínáme s R programovacím jazyku v nástroji Azure Machine Learning Studio

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Úvod

Tento kurz vám pomůže spustit programovací jazyk R pomocí rozšíření Azure Machine Learning Studio. V tomto kurzu R programování pro vytvoření, testování a spouštění kódu R studia. Při práci prostřednictvím kurzu vytvoříte pomocí jazyka R v sadě Studio kompletní řešení Prognózování.  

Microsoft Azure Machine Learning Studio obsahuje mnoho modulů výkonném počítači pro manipulaci s učení a data. Výkonné jazyka R je popsán jako lingua franca obchodu analýzy. Využívá elastic manipulaci analytics a data v sadě Studio je možné rozšířit pomocí jazyka R. Tato kombinace poskytuje škálovatelnost a snadné nasazení sady Studio flexibilitu a hloubkové analýzy R.

### <a name="forecasting-and-the-dataset"></a>Prognózování a datové sady

Prognózování je metoda analytické široce proces a velmi užitečné. Běžné použití rozsahu od předpověď sales sezónní položek, určení optimální zásob se predikce makroekonomické proměnné. Prognózy se obvykle provádí s modely řady čas.

Data časových řad se data, ve kterém mají hodnoty indexu čas. Index čas může být pravidelné, např. měsíčně nebo po minutách, nebo je nestandardní. Model časové řady vychází z dat časových řad. Programovací jazyk R obsahuje flexibilní rozhraní framework a rozsáhlé analýzy dat časové řady.

V této příručce budeme se práce s produkci mléka Kalifornie a data o cenách. Tato data zahrnují měsíční údaje o produkci mléka několik produktů a ceně mléka fat, komoditním srovnávacího testu.

Data použitá v tomto článku, spolu s skriptů R, si můžete stáhnout z [MachineLearningSamples-poznámkových bloků a studio – ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Data v souboru `cadairydata.csv` byla původně syntetizovat z informací z University of Wisconsin na [ https://dairymarkets.com ](https://dairymarkets.com).

### <a name="organization"></a>Organizace

Jak se dozvíte, jak vytvářet, testovat a spustit kód analytics a data manipulaci s R v prostředí Azure Machine Learning Studio jsme se projděte několik kroků.  

* Nejprve se podíváme na základní informace o používání jazyka R v prostředí Azure Machine Learning Studio.
* Potom jsme průběh pojednávající o různých aspektů vstupně-výstupních operací pro data, kód R a grafiky v prostředí Azure Machine Learning Studio.
* Potom jsme se vytvořit první část našeho řešení Prognózování tak, že vytvoříte kód pro transformaci a čištění dat.
* Pomocí našich dat připravené budeme provádět analýzu korelace mezi několika proměnných v naší datové sadě.
* Nakonec vytvoříme předpovědi modelu sezónní časové řady pro produkci mléka.

## <a id="mlstudio"></a>Pracovat s jazykem R v nástroji Machine Learning Studio

Tato část vás provede základy interakci s programovací jazyk R v prostředí Machine Learning Studio. Jazyk R poskytuje výkonný nástroj pro vytvoření přizpůsobené analytics a modulů pro manipulaci s dat v rámci prostředí Azure Machine Learning Studio.

Použiji RStudio pro vývoj, testování a ladění kódu R v malém měřítku. Tento kód je pak vyjmutí a vložení do [spustit skript jazyka R] [ execute-r-script] modulů v Machine Learning Studio připraven ke spuštění.  

### <a name="the-execute-r-script-module"></a>Modul spustit skript jazyka R

V nástroji Machine Learning Studio, jsou skripty jazyka R poběží [spustit skript jazyka R] [ execute-r-script] modulu. Příkladem [spustit skript jazyka R] [ execute-r-script] modulu v nástroji Machine Learning Studio je znázorněno na obrázku 1.

 ![Programovací jazyk R: Spustit skript jazyka R modulu vybraná v nástroji Machine Learning Studio](./media/r-quickstart/fig1.png)

*Obrázek 1. Zobrazuje vybraný modul spustit skript jazyka R prostředí Machine Learning Studio.*

Odkazující na obrázku 1, Pojďme se podívat na některé z klíčových částí tohoto prostředí Machine Learning studia pro práci s [spustit skript jazyka R] [ execute-r-script] modulu.

* V prostředním podokně se zobrazí moduly do experimentu.
* Horní část v pravém podokně obsahuje okno zobrazit a upravovat svoje skripty jazyka R.  
* Pravé dolní části ukazuje některé vlastnosti [spustit skript jazyka R][execute-r-script]. Protokoly chyb a výstupu můžete zobrazit tak, že vyberete příslušný míst v tomto podokně.

Jsme se, samozřejmě, mluvit o [spustit skript jazyka R] [ execute-r-script] podrobněji ve zbývající části tohoto článku.

Při práci s komplexní funkce R je můžu jenom doporučit úpravy, testování a ladění v RStudio. Stejně jako u jakékoli vývoje softwaru postupně rozšiřovat kódu a testování na malé jednoduchý testovací případy. Pak vyjímejte a vkládejte vaše funkce do okna skriptu R [spustit skript jazyka R] [ execute-r-script] modulu. Tento přístup umožňuje využít RStudio integrovaného vývojového prostředí (IDE) a sílu Azure Machine Learning Studio.  

#### <a name="execute-r-code"></a>Spouštění kódu R

Veškerý kód R ve [spustit skript jazyka R] [ execute-r-script] modulu se spustí při spuštění experimentu tak, že vyberete **spustit** tlačítko. Po dokončení spuštění se zobrazí zaškrtávací políčko na [spustit skript jazyka R] [ execute-r-script] ikonu.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Obranné psaní kódu jazyka R pro službu Azure Machine Learning

Pokud vyvíjíte kód R pro, například webovou službu pomocí Azure Machine Learning Studio, měli byste jednoznačně naplánovat, jak váš kód se bude zabývat neočekávaný datový vstup a výjimek. Pokud chcete zachovat přehlednost, můžu nezahrnuli většinu cestě kontrolu nebo zpracování výjimek v většina příkladů kódu zobrazí. Ale jak můžeme pokračovat můžu vám poskytne několik příkladů funkcí pomocí funkce pro zpracování výjimek R.  

Pokud potřebujete podrobnější zacházení s zpracování výjimek jazyka R, doporučuji čtení příslušných částech kniha od Wickham uvedené níže v [další materiály](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Ladění a testování R v nástroji Machine Learning Studio

Zdůrazňujeme, doporučuji, testování a ladění kódu R v malém měřítku v RStudio. Existují však případy, kdy je potřeba sledovat problémy kódu R v [spustit skript jazyka R] [ execute-r-script] samotný. Kromě toho je vhodné zkontrolovat výsledky v nástroji Machine Learning Studio.

Především v souboru výstup.log se nachází výstup z provádění kódu jazyka R a na platformě Azure Machine Learning Studio. Další informace se zobrazí ve error.log.  

Pokud dojde k chybě v nástroji Machine Learning Studio při spuštění kódu jazyka R, by mělo být prvním kurzu akce podívejte se na error.log. Tento soubor může obsahovat užitečné chybové zprávy, které vám pomůžou pochopit a opravit chyby. Chcete-li zobrazit error.log, vyberte **naleznete v protokolu chyb** na **podokno vlastností** pro [spustit skript jazyka R] [ execute-r-script] obsahující chybu.

Například mám spuštěných následující kód R nedefinované proměnné y, v [spustit skript jazyka R] [ execute-r-script] modul:

```R
x <- 1.0
z <- x + y
```

Tento kód se nepodaří spustit, což vede k chybě. Výběr **naleznete v protokolu chyb** na **podokno vlastností** vytvoří zobrazení je znázorněno na obrázku 2.

  ![Chybová zpráva je překryvné](./media/r-quickstart/fig2.png)

*Obrázek 2. Chybová zpráva automaticky.*

Zdá se, musíme se podívat v souboru výstup.log zobrazíte R chybová zpráva. Vyberte [spustit skript jazyka R] [ execute-r-script] a pak vyberte **zobrazení souboru výstup.log** položku **podokno vlastností** vpravo. Otevře se nové okno prohlížeče a I v tomto tématu.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Tato chybová zpráva obsahuje žádná překvapení a jednoznačně identifikuje potíže.

Chcete-li zkontrolovat hodnoty libovolný objekt v jazyce R, můžete vytisknout tyto hodnoty do souboru výstup.log souboru. Pravidla pro zkoumání hodnoty objektu jsou v podstatě stejné jako interaktivní relace jazyka R. Například pokud zadáte název proměnné na řádek, hodnota objektu budou zobrazeny souboru výstup.log souboru.  

#### <a name="packages-in-machine-learning-studio"></a>Balíčky v nástroji Machine Learning Studio

Obsahuje víc než 350 předinstalované balíčky jazyka R Studio. Můžete použít následující kód [spustit skript jazyka R] [ execute-r-script] modulu, který chcete načíst seznam předinstalované balíčky.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Pokud nevíte, poslední řádek tohoto kódu v okamžiku, přečtěte si o. Ve zbývající části tohoto článku bude výrazně probereme využití R v prostředí sady Studio.

### <a name="introduction-to-rstudio"></a>Úvod k Rstudiu

RStudio je často používaný integrované vývojové prostředí pro jazyk R. Použiji RStudio pro úpravy, testování a ladění některý kód R použitá v tomto průvodci. Jakmile se kód R otestovaný a připravený, můžete jednoduše vyjmout a vložit do nástroje Machine Learning Studio z editoru RStudio [spustit skript jazyka R] [ execute-r-script] modulu.  

Pokud nemáte programovací jazyk R nainstalovaný na desktopovém počítači, doporučujeme provést to nyní. Zdarma ke stažení z open-source jazyk R jsou k dispozici na komplexní R Archive sítě (CRAN) na [ https://www.r-project.org/ ](https://www.r-project.org/). K dispozici ke stažení pro Windows, Mac OS a Linux/UNIX. Zvolte nejbližší zrcadlení a postupujte podle pokynů ke stažení. Kromě toho CRAN obsahuje celou řadu užitečných analytics a data manipulaci s balíčky.

Pokud jste ještě na přihlašovací stránce RStudio, by měl stáhněte a nainstalujte verzi pro stolní počítače. Můžete najít přihlašovací stránce RStudio, soubory ke stažení pro Windows, Mac OS a Linux/UNIX na http://www.rstudio.com/products/RStudio/. Postupujte podle pokynů k instalaci RStudio na desktopovém počítači k dispozici.  

Je k dispozici na kurz Úvod k Rstudiu [pomocí RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Mám poskytnout další informace o používání nástroje RStudio v [Průvodce RStudio dokumentaci](#appendixa) níže.  

## <a id="scriptmodule"></a>Načtení dat do a z modulu spustit skript jazyka R

V této části probereme, jak získat data do a z celkového počtu [spustit skript jazyka R] [ execute-r-script] modulu. Podíváme se zpracování čtení do a z různých datových typů [spustit skript jazyka R] [ execute-r-script] modulu.

Kompletní kód pro tento oddíl je v [MachineLearningSamples-poznámkových bloků a studio – ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio"></a>Načíst a zkontrolujte data v nástroji Machine Learning Studio

#### <a id="loading"></a>Načíst datovou sadu

Začneme tím, **csdairydata.csv** souboru do Azure Machine Learning Studio.

1. Spuštění prostředí Azure Machine Learning Studio.
1. Vyberte **+ nová** v levém dolním rohu obrazovky a vyberte **datovou sadu**.
1. Vyberte **z místního souboru**a potom **Procházet** a vyberte soubor.
1. Ujistěte se, že jste vybrali **obecný CSV soubor s hlavičkou (CSV)** jako typ datové sady.
1. Výběrem symbolu zaškrtnutí.
1. Po uložení datové sady, by se měla zobrazit novou datovou sadu vyberete **datových sad** kartu.  

#### <a name="create-an-experiment"></a>Vytvoření experimentu

Když teď máme některá data ve službě Machine Learning Studio, potřebujeme vytvořit nový experiment provést analýzu.  

1. Vyberte **+ nová** na nižší vlevo a pak vyberte **Experiment**, pak **prázdný Experiment**.
1. Název experimentu můžete výběrem a úpravy, **experimentu vytvořili na...**  nadpis v horní části stránky. Například změny na **certifikační Autority mléka analýzy**.
1. Na levé straně stránky experiment, rozbalte **uložení datové sady**a potom **Moje datové sady**. Měli byste vidět **cadairydata.csv** , který jste nahráli dříve.
1. Přetáhnout myší **datovou sadu csdairydata.csv** do experimentu.
1. V **hledání experimentovat položky** pole nahoře v levém podokně, typ [spustit skript jazyka R][execute-r-script]. Zobrazí se v modulu se zobrazí v seznamu hledání.
1. Přetáhnout myší [spustit skript jazyka R] [ execute-r-script] modulu do vaší palety.  
1. Propojte výstup modulu **datovou sadu csdairydata.csv** k levému vstupu (**Dataset1**) z [spustit skript jazyka R][execute-r-script].
1. **Nezapomeňte si vyberte možnost 'Uložit'!**  

V tomto okamžiku experiment by měl vypadat přibližně jako obrázek 3.

![Analýza mléka certifikační Autority experimentovat s datovou sadu a modul spustit skript jazyka R](./media/r-quickstart/fig3.png)

*Obrázek 3. Analýza mléka certifikační Autority můžete experimentovat s datovou sadu a spustit skript jazyka R modulu.*

#### <a name="check-on-the-data"></a>Podívejte se na data

Pojďme Podíváme se na data, která jsme načetli do našich experimentu. V testu, vyberte výstup **datovou sadu cadairydata.csv** a vyberte **vizualizovat**. Měli byste vidět něco jako obrázek 4.  

![Souhrn cadairydata.csv datové sady](./media/r-quickstart/fig4.png)

*Obrázek 4. Souhrn cadairydata.csv datové sady.*

V tomto zobrazení vidíme spoustu užitečných informací. Můžeme vidět prvních několik řádků z této datové sadě. Pokud vybereme sloupec, v části Statistika zobrazí další informace o sloupci. Typ funkce řádek příkladu nás jaké typy dat Azure Machine Learning Studio přiřazené ke sloupci. Rychlý pohled tímto způsobem je kontrolu správnosti dobré předtím, než začneme jakékoli závažné práci.

### <a name="first-r-script"></a>První skript jazyka R

Pojďme vytvořit jednoduchý první skript jazyka R můžete experimentovat v Azure Machine Learning Studio. Můžu mít vytvořený a otestovaný následující skript v RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('cadairydata')
```

Nyní potřebuji převést tento skript k Azure Machine Learning studiu. Může stačí vyjmout a vložit. Ale v tomto případě jsem přenášet Můj skript jazyka R prostřednictvím souboru zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Vložení dat do modulu spustit skript jazyka R

Pojďme Podíváme se na vstupy [spustit skript jazyka R] [ execute-r-script] modulu. V tomto příkladu jsme bude číst data mléka Kalifornie do [spustit skript jazyka R] [ execute-r-script] modulu.  

Existují tři možné vstupy pro [spustit skript jazyka R] [ execute-r-script] modulu. V závislosti na vaší aplikaci můžete použít některé nebo všechny tyto vstupy. Je také použít skript R, který nemá žádný vstup vůbec nemusíte zajistit dokonalou přiměřené.  

Pojďme se podívat na všechny tyto vstupy, že přejdete zleva doprava. Můžete zobrazit názvy každý ze vstupů umístit ukazatel myši nad vstupu a popis pro čtení.  

#### <a name="script-bundle"></a>Skript sady

Zadejte skript sady umožňuje předat obsah souboru zip do [spustit skript jazyka R] [ execute-r-script] modulu. Číst obsah souboru zip do kódu jazyka R můžete použít jednu z následujících příkazů.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio považuje soubory v souboru zip, jako by šlo v src / directory, takže budete muset před názvy souborů s tímto názvem adresáře. Například, pokud soubor zip obsahuje soubory `yourfile.R` a `yourData.rdata` v kořenovém adresáři souboru zip, chcete řešit jako `src/yourfile.R` a `src/yourData.rdata` při použití `source` a `load`.

Jsme již probírali načítání datových sad v [načíst datovou sadu](#loading). Po vytvoření a testování skript jazyka R je znázorněno v předchozí části, postupujte takto:

1. Uložte skript jazyka R do. Soubor R. Můžu volat můj soubor skriptu "simpleplot. R". Tady je obsah.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio
   maml.mapOutputPort('cadairydata')
   ```

1. Vytvořte soubor zip a zkopírujte skript do tohoto souboru zip. Na Windows, klikněte pravým tlačítkem na soubor a vyberte **odeslat**a potom **komprimovaná složka**. Tím se vytvoří nový soubor zip obsahující "simpleplot. Soubor R".

1. Přidání souboru **datových sad** v nástroji Machine Learning Studio, určení typu jako **zip**. Měli byste vidět soubor zip do vaší datové sady.

1. Přetažení souboru zip z **datových sad** na **ML Studio plátna**.

1. Propojte výstup modulu **zip data** ikony **skript sady** vstupní sady [spustit skript jazyka R] [ execute-r-script] modulu.

1. Typ `source()` funkci s názvem souboru zip do okna kódu pro [spustit skript jazyka R] [ execute-r-script] modulu. V případě Moje zadané mnou `source("src/simpleplot.R")`.  

1. Ujistěte se, že vyberete **Uložit**.

Po dokončení těchto kroků [spustit skript jazyka R] [ execute-r-script] modulu spustí skript jazyka R v souboru zip při spuštění experimentu. V tomto okamžiku experiment by měl vypadat přibližně jako obrázek 5.

![Experimentování s využitím ZIP skript jazyka R](./media/r-quickstart/fig6.png)

*Obrázek 5. Experimentování s využitím ZIP skript jazyka R.*

#### <a name="dataset1"></a>Dataset1

Můžete předat obdélníkové tabulky dat kódu R pomocí Dataset1 vstup. V našem jednoduchý skript `maml.mapInputPort(1)` funkce načítá data z portu 1. Tato data se pak přiřadí název proměnné datového rámce v kódu. V našem jednoduchý skript provede první řádek kódu přiřazení.

```R
cadairydata <- maml.mapInputPort(1)
```

Spusťte experiment tak, že vyberete **spustit** tlačítko. Po dokončení provádění vyberte [spustit skript jazyka R] [ execute-r-script] modulu a pak vyberte **zobrazení výstupu protokolu** v podokně vlastností. Nová stránka by se zobrazit v prohlížeči zobrazující obsah souboru výstup.log souboru. Když se posunete dolů mělo zobrazit něco jako následující.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Dále snížit na stránce je podrobnější informace ve sloupcích, které bude vypadat podobně jako následující.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Tyto výsledky se většinou podle očekávání, s 228 připomínky a 9 sloupců v datového rámce. Můžeme vidět názvy sloupců, typ dat R a vzorky každý sloupec.

> [!NOTE]
> Tento stejný výstupu je pohodlně k dispozici z výstupu zařízení jazyka r. [spustit skript jazyka R] [ execute-r-script] modulu. Probereme výstupů [spustit skript jazyka R] [ execute-r-script] modulu v další části.  

#### <a name="dataset2"></a>Dataset2

Chování Dataset2 vstupu je shodná s Dataset1. Pomocí tohoto vstupního můžete předat obdélníkové druhou tabulku dat do kódu R. Funkce `maml.mapInputPort(2)`, s argumentem 2 slouží k předání tato data.  

### <a name="execute-r-script-outputs"></a>Spustit skript jazyka R výstupy

#### <a name="output-a-dataframe"></a>Výstupní datový rámec

Obsah datového rámce R můžete výstup jako obdélníkový tabulku přes port Dataset1 výsledek pomocí `maml.mapOutputPort()` funkce. V našem jednoduchý skript jazyka R se provádí pomocí následující řádek.

```
maml.mapOutputPort('cadairydata')
```

Po spuštění testu, vyberte na výstupní port Dataset1 výsledek a pak vyberte **vizualizovat**. Měli byste vidět něco jako obrázek 6.

![Vizualizace výstupu mléka Kalifornie dat](./media/r-quickstart/fig7.png)

*Obrázek 6. Vizualizace výstupu Kalifornie mléka data.*

Tento výstup by měl vypadat stejný jako vstup, přesně tak, jak jsme očekávání.  

### <a name="r-device-output"></a>Výstup zařízení jazyka r.

Výstup zařízení [spustit skript jazyka R] [ execute-r-script] modul obsahuje zprávy a grafický výstup. Zprávy i na standardní výstup a chyby z jazyka R se odesílají na výstupní port R zařízení.  

Chcete-li zobrazit výstup zařízení R, vyberte port a pak na **vizualizovat**. Vidíme standardní výstup a chyby ze skriptu R na obrázku 7.

![Standardní výstup a chyby z portu zařízení jazyka r.](./media/r-quickstart/fig8.png)

*Obrázek 7. Standardní výstup a chyby z portu R zařízení.*

Posun dolů jsme zobrazit výstup grafiky z našich skript jazyka R na obrázku 8.  

![Grafického výstupu z portu zařízení jazyka r.](./media/r-quickstart/fig9.png)

*Obrázek 8. Výstup z portu R zařízení grafiky.*  

## <a id="filtering"></a>Filtrování dat a transformace

V této části budeme provádět některé základní filtrování dat a transformace operací s daty mléka Kalifornie. Na konci této části máme data ve formátu vhodném pro sestavování analytických modelů.  

Přesněji řečeno, v této části budeme provádět několik běžných dat čištění a transformace úloh: Zadejte transformace, filtrování datových rámců, přidává nové počítané sloupce a hodnota transformace. Toto pozadí by měly pomoct vypořádat se se mnoho variant v reálných problémů.

Kompletní kód R pro tento oddíl je k dispozici v [MachineLearningSamples-poznámkových bloků a studio – ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Typ transformace

Teď, když jsme může číst data mléka Kalifornie do kódu jazyka R v [spustit skript jazyka R] [ execute-r-script] modulu, potřebujeme zajistit, že data ve sloupcích odpovídající typ a formát.  

R je dynamicky psaný jazyk, což znamená, že se na další podle potřeby převést datové typy. Atomic datové typy v jazyce R zahrnují číselné literály, logické a znak. Faktor typ slouží k uložení kompaktně data zařazená do kategorií. Mnohem více informace o typech dat najdete v odkazech v [další materiály](#appendixb) níže.

Pokud tabulkových dat je pro čtení do jazyka R z externího zdroje, je vždy vhodné zkontrolujte výsledné typy ve sloupcích. Chcete sloupec – znak typu, ale v mnoha případech to se zobrazí jako faktor nebo naopak. V ostatních případech sloupec, který si myslíte, že by měl být číselné je reprezentována znaková data, například "1,23", spíše než 1,23 jako plovoucí bodu číslo.  

Naštěstí je snadný převod jednoho typu na jiný, za předpokladu, je možné mapování. Například 'Nevada' nelze převést na číselnou hodnotu, ale můžete ji převést na faktor (zařazené do kategorií proměnné). Další příklad můžete převést číselné 1 znak '1' nebo faktor.  

Syntaxe pro některý z těchto převodů je jednoduchý: `as.datatype()`. V následujících bodech tyto funkce pro převod typů.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Na datové typy sloupců zadáme v předchozí části: jsou všechny sloupce s výjimkou sloupec s názvem "Měsíc", což je typ znaku číselného typu. Pojďme to převést na faktorem a testovat výsledky.  

Odstraněný řádek, který diagnostického matice a přidá řádek převodu ve sloupci "Měsíc" faktor. V experimentu jsem se jenom operace vyjmutí a vložení kódu R do okna kódu [spustit skript jazyka R] [ execute-r-script] modulu. Můžete také aktualizovat soubor zip a nahrajte ho do Azure Machine Learning Studio, ale tato akce trvá několik kroků.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('cadairydata')
```

Umožňuje spustit tento kód a podívejte se na výstup protokolu pro skript R. Relevantní data z protokolu je znázorněno na obrázku 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 9. Souhrn datového rámce s proměnnou faktor.*

Teď by měl být typu měsíc "**faktor plánovaným bodem obnovení kratším 14 úrovní**". To je problém, protože existují jenom 12 měsíců v roce. Můžete také zkontrolovat, Všimněte si, že typ v **vizualizovat** datové sady, výsledek je port "**zařazené do kategorií**".

Problém je, že sloupec "Měsíc" nebyl systematicky programového. V některých případech se nazývá měsíc dne a v jiných se zkracuje jako dubna. Ořízne řetězec, který má 3 znaky jsme můžete tento problém vyřešit. Řádek kódu nyní vypadá takto:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Znovu spustit experiment a zobrazte výstup protokolu. Očekávané výsledky jsou uvedené na obrázku 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 10. Souhrn datového rámce s správný počet úrovní faktor.*

Naše proměnná faktor teď má požadované úrovně 12.

### <a name="basic-data-frame-filtering"></a>Základní datové rámce filtrování

R dataframes podporují výkonné možnosti filtrování. Datové sady mohou být podsady pomocí logické filtry řádků nebo sloupců. V mnoha případech se bude vyžadovat komplexní filtrovací kritéria. Odkazy v [další materiály](#appendixb) níže obsahují příklady rozsáhlé filtrování datových rámců.  

Existuje jeden bit filtrování by měl uděláme v naší datové sadě. Když se podíváte na sloupce v cadairydata datového rámce, zobrazí se dvě nepotřebných sloupců. První sloupec obsahuje jenom číslo řádku, který není velmi užitečné. Druhý sloupec Year.Month, obsahuje nadbytečné informace. Tyto sloupce snadno vylučujeme pomocí následujícího kódu jazyka R.

> [!NOTE]
> Od této chvíle v této části, stačí vám ukážu, další kód se přidává do [spustit skript jazyka R] [ execute-r-script] modulu. Přidat každý nový řádek **před** `str()` funkce. Můžu tuto funkci použít k ověření své výsledky v Azure Machine Learning Studio.

Můžu přidejte následující řádek kódu jazyka R v [spustit skript jazyka R] [ execute-r-script] modulu.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Tento kód spustit v experimentu a podívat se na výsledek z výstupu protokolu. Tyto výsledky jsou zobrazeny v obrázek 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 11. Souhrn datový rámec se dvěma sloupci odebrat.*

Dobrá zpráva! Jsme dosáhli očekávaných výsledků.

### <a name="add-a-new-column"></a>Přidat nový sloupec

Vytváření modelů řady času bude vhodné mít sloupce obsahujícího měsíců od začátku časové řady. Vytvoříme nový sloupec "Month.Count".

Kvůli lepší organizaci kódu vytvoříme naši první jednoduchou funkci `num.month()`. Potom použije této funkce vytvoří nový sloupec v datového rámce. Nový kód je následujícím způsobem.

```R
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Nyní spusťte aktualizovanou experiment a používat protokol výstup zobrazíte výsledky. Tyto výsledky jsou zobrazeny v obrázek 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 12. Souhrn datový rámec se další sloupec.*

Vypadá všechno funguje. Máme nový sloupec s očekávané hodnoty v našem datového rámce.

### <a name="value-transformations"></a>Hodnota transformace

V této části budeme provádět některé jednoduché transformace na hodnoty v některé sloupce naše datového rámce. Jazyk R podporuje téměř libovolnou hodnotu transformace. Odkazy v [další materiály](#appendixb) níže obsahují rozsáhlé příklady.

Když se podíváte na hodnoty v souhrnné informace o našich datový rámec byste měli vidět něco liché tady. Dosažení více zmrzliny než mléka v Kalifornii? Ne, samozřejmě, jak to nemá smysl sad jako tato skutečnost může být na některé z nás ice cream lovers. Jednotky se liší. Cena je v jednotkách, které nám librách, kterého je v jednotkách 1 milion librách USA, ice cream je v jednotkách 1 000 nám galony a produkci minipivovarů ve wisconsinu byt je v jednotkách 1 000 librách USA. Za předpokladu, že se asi 6,5 librách na paliva váží ice cream, můžeme snadno udělat násobení převést tyto hodnoty tak, aby byly všechny ve stejné jednotky librách 1 000.

Pro náš model Prognózování používáme násobení model pro vývoj a sezónní úpravy tato data. Transformace protokolu nám umožňuje používat lineární model, zjednodušuje tento proces. Můžete použít ve stejné funkci použití násobitel transformace protokolu.

V následujícím kódu, definovat novou funkci, `log.transform()`a použijte ji pro řádky obsahující číselné hodnoty. R `Map()` funkce se používá k aplikování `log.transform()` funkce, která se vybrané sloupce datového rámce. `Map()` je podobný `apply()` , ale umožňuje více než jeden seznam argumentů funkce. Všimněte si, že poskytuje seznam multiplikátory druhým argumentem `log.transform()` funkce. `na.omit()` Funkce se používá jako hodně vyčištění k zajištění nemáme chybí nebo je nedefinované hodnoty datového rámce.

```R
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Je v poměrně bit děje `log.transform()` funkce. Většina tento kód kontroluje potenciální problémy s argumenty nebo nakládání s výjimkami, které můžou stále nastat během výpočtů. Stačí několik řádků tohoto kódu dělají výpočtů.

Cílem obranné programování je zabránit selhání jednu funkci, která zabrání zpracování pokračovat. K náhlému selhání dlouhotrvajících analýzy může být poměrně frustrující pro uživatele. Abyste předešli této situaci, musí se zvolit výchozí návratové hodnoty, který bude omezovat poškození zpracování příjmu dat. Zprávu je také vytvořen uživatele upozorňuje, že se něco nepovedlo.

Pokud není jste zvyklí obranné programování v jazyce R, tento kód se může zdát poněkud náročný. Můžu vás provede hlavními kroky:

1. Vektor čtyř zpráv je definována. Tyto zprávy se používají ke sdělování informací o některých možných chyb a výjimek, které se mohou vyskytnout s tímto kódem.
2. Hodnota není k dispozici pro každý případ se lze vrátit. Existuje mnoho dalších možností, které může mít méně vedlejší účinky. Lze může vrátit vektor nuly nebo vstupní původního vektoru, např.
3. Kontroly jsou spuštěny na argumenty funkci. V obou případech, pokud dojde k chybě, vrátí se výchozí hodnota a vytvořil zprávu `warning()` funkce. Používám `warning()` spíše než `stop()` jako druhá možnost se ukončí provádění, přesně jak jsem snažím se vyhnout. Všimněte si, že mám napsali tento kód ve stylu postupu jako v tomto případě funkční přístup vypadal jako komplexní a skrytého.
4. Výpočty protokolu jsou zabaleny v `tryCatch()` tak, aby výjimky nezpůsobí náhlé zastavení zpracování. Bez `tryCatch()` Většina chyb vyvolaných výsledek funkce R v signál k zastavení, který právě tohle dělá.

Spustit tento kód R v experimentu a podíváme se na tištěné výstup do souboru výstup.log souboru. Nyní uvidíte transformovaný hodnoty čtyři sloupce v protokolu, jak je znázorněno na obrázku 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Obrázek 13. Souhrn transformovaný hodnot v datového rámce.*

Vidíme, že byly transformovány hodnoty. Nyní produkci mléka výrazně přesahuje všechny ostatní mléka produktu produkčního prostředí, vrací, že jsou nyní se díváme na logaritmické měřítko.

V tomto okamžiku je naše data vyčistí a jsme připraveni pro některé modelování. Prohlížení souhrn pro datovou sadu výsledků výstupu vizualizace naše [spustit skript jazyka R] [ execute-r-script] modul, zobrazí se sloupec "Měsíc" je "Zařazené do kategorií" s 12 jedinečné hodnoty, znovu, stejně jako My jsme chtěli.

## <a id="timeseries"></a>Čas řady objekty a analýzy korelace

V této části budeme prozkoumejte pár základních objektů řady čas R a analyzovat korelace mezi určité proměnné. Naším cílem je výstupní datový rámec obsahující informace o pairwise korelace v několika zjevně demonstruje výskyt pomalé.

Kompletní kód R pro tento oddíl je v [MachineLearningSamples-poznámkových bloků a studio – ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Čas řady objekty v jazyce R

Jak už jsme už zmínili, time series jsou řady hodnot data indexované podle času. R čas řady objekty se používají k vytváření a správě index čas. Existuje několik výhod používání času řada objektů. Čas řady objekty vás zbaví mnoho podrobností správy časových řad index hodnoty, které jsou zapouzdřeny v objektu. Kromě toho čas řady objekty vám umožňují použít metody řady mnoho času pro vykreslení, tisk, modelování a tak dál.

POSIXct time series třída se obvykle používá a je poměrně jednoduché. Toto časové řady třídy opatření čas od začátku epochy, 1. ledna 1970. V tomto příkladu budeme používat POSIXct čas řady objekty. Další často používaný R čas řadu tříd objektů patří zoo a xts, rozšiřitelné časové řady.

### <a name="time-series-object-example"></a>Objekt časové řadě – příklad

Pusťme se do práce v našem příkladu. Přetáhnout myší **nové** [spustit skript jazyka R] [ execute-r-script] modulu do svého experimentu. Připojte výstupní port výsledek Dataset1 existující [spustit skript jazyka R] [ execute-r-script] modulu Dataset1 vstupním portem modulu nové [spustit skript jazyka R] [ execute-r-script] modulu.

Jak jsme to udělali pro prvních příkladů, jak jsme průběhu v příkladu, v některých jsem se zobrazí pouze přírůstkové další řádky kódu R v každém kroku.  

#### <a name="reading-the-dataframe"></a>Čtení datového rámce

Jako první krok pojďme si přečíst datový rámec a ujistěte se, že jsme dosáhli očekávaných výsledků. Následující kód by měl provádět úlohy.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Nyní spusťte experiment. Protokol nový obrazec spustit skript jazyka R by měl vypadat jako obrázek 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Obrázek 14. Souhrn datového rámce v modulu spustit skript jazyka R*

Tato data jsou z očekávaných typů a formát. Mějte na paměti, že sloupec "Měsíc" je faktoru typ a má očekávaný počet úrovní.

#### <a name="creating-a-time-series-object"></a>Vytváření time series objekt

Potřebujeme přidat time series objekt do našich datového rámce. Nahraďte následující příkaz, který přidá nový sloupec třídy POSIXct stávajícího kódu.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Nyní zkontrolujte protokol. Měl by vypadat jako obrázek 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Obrázek 15. Souhrn datového rámce s time series objekt.*

Můžeme vidět ze souhrnu nového sloupce, který je ve skutečnosti třídy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Zkoumání a transformace dat

Podívejme se na určité proměnné v této datové sadě. Matice diagnostického je dobrým způsobem, jak vytvořit krátce podívat. Mám teď nahrazuje `str()` funkce v předcházejícím kódu R s následujícím řádkem.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Tento kód spustit a zobrazit, co se stane. Vykreslení vytvořený v zařízení jazyka r. port by měl vypadat jako obrázek 16.

![Diagnostického matice vybrané proměnné](./media/r-quickstart/fig17.png)

*Obrázek 16. Matice diagnostického vybraných proměnných.*

Je některé struktura odd-looking vztahy mezi tyto proměnné. Možná vzniká to z trendy v datech a ze skutečnosti, že jsme ještě standardizované proměnné.

### <a name="correlation-analysis"></a>Korelace analýzy

K provedení analýzy korelace musíme zrušit trend i standardizovat proměnné. Mohli bychom jednoduše použít R `scale()` funkce, která centra i škáluje proměnné. Tato funkce může dobře pracovat rychleji. Ale chci zobrazit příklad obranné programování v jazyce R.

`ts.detrend()` Funkce uvedené níže provede oba z těchto operací. Následující dva řádky kódu zrušit trend dat a pak standardizovat hodnoty.

```R
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Je v poměrně bit děje `ts.detrend()` funkce. Většina tento kód kontroluje potenciální problémy s argumenty nebo nakládání s výjimkami, které můžou stále nastat během výpočtů. Stačí několik řádků tohoto kódu dělají výpočtů.

Už jsme projednat příkladem obranné programování v transformacích hodnotu. Obě výpočtu bloky jsou zabaleny v `tryCatch()`. Některé chyby smysl vraťte původního vstupního vektoru a v jiných případech lze vrátit vektor nulami.  

Všimněte si, že lineární regrese, použít pro zrušení vzroste zájem o času řady regrese. Prediktivní proměnná je time series objekt.  

Jednou `ts.detrend()` je definována jsme použít na proměnné zájem o náš datového rámce. Jsme musí vynucení výsledný seznam vytvořené `lapply()` do dat datový rámec s použitím `as.data.frame()`. Z důvodu obranné aspektů `ts.detrend()`, nepodařilo se zpracovat jeden z proměnné nezabrání správné zpracování ostatních.  

Poslední řádek kódu vytvoří pairwise diagnostického. Po spuštění kódu jazyka R se zobrazují výsledky diagnostického v obrázek 17.

![Pairwise diagnostického zrušit trendu a standardizovaným časové řady](./media/r-quickstart/fig18.png)

*Obrázek 17. Pairwise diagnostického zrušit trendu a standardizovaným časové řady.*

Můžete porovnat tyto výsledky jsou uvedeny v obrázek 16. Trend odebrán a proměnné standardizované vidíme mnohem méně struktury v vztahy mezi tyto proměnné.

Kód pro výpočet korelace jako objekty PVJS R je následujícím způsobem.

```R
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Spuštěním tohoto kódu vytvoří protokol znázorňuje obrázek 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Obrázek 18. Seznam PVJS objekty z analýzy pairwise korelace.*

Existuje korelace hodnota pro každou prodleva. Žádný z těchto hodnot korelace není dostatečně velký, aby se důležité. Závěru jsme proto, že jsme každou proměnnou modelu nezávisle na sobě.

### <a name="output-a-dataframe"></a>Výstupní datový rámec
Jako seznam objektů R PVJS jsme jste vypočítali pairwise korelace. To představuje bit problém se jako výstupní port datové sady výsledků ve skutečnosti vyžaduje datový rámec. Dále PVJS je objekt samotný seznam a chceme, aby pouze hodnoty v první prvek seznamu, korelace v různých zjevně demonstruje výskyt pomalé.

Následující kód extrahuje hodnoty prodleva ze seznamu PVJS objekty, které představují samy o sobě seznamy.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

První řádek kódu je trochu složité, a vysvětlení vám můžou pomoct porozumět ho. Práce zevnitř jsme měli tyto položky:

1. " **[[** 'Operator s argumentem'**1**" vybere vektor korelace v zjevně demonstruje výskyt pomalé od prvního prvku seznamu PVJS objektů.
2. `do.call()` Funkce se vztahuje `rbind()` funkce přes prvky seznamu vrací podle `lapply()`.
3. `data.frame()` Funkce převede výsledek produkovaný `do.call()` na datový rámec.

Všimněte si, že názvy řádků ve sloupci datového rámce. Způsobem tak zachová řádku názvy, když se výstup z [spustit skript jazyka R][execute-r-script].

Spuštění kódu vytvoří výstup znázorňuje obrázek 19 když mám **vizualizovat** výstupu na port datové sady výsledků. Názvy řádků jsou v prvním sloupci tak, jak má.

![Výsledky výstupu z analýzy korelace](./media/r-quickstart/fig20.png)

*Obrázek 19. Výsledky výstupu z analýzy korelace.*

## <a id="seasonalforecasting"></a>Časové řadě – příklad: sezónní Prognózování

Naše data je teď ve formátu vhodném pro analýzy a zjistili jsme, že nejsou žádné významné korelace mezi proměnné. Teď přejdeme a vytvořit časové řady model Prognózování. Pomocí tohoto modelu jsme se prognózy produkci mléka Kalifornie po dobu 12 měsíců 2013.

Náš model Prognózování bude mít dvě komponenty, trendů a sezónní součásti. Kompletní prognózy je produkt z těchto dvou komponentách. Tento typ modelu se označuje jako model násobení. Alternativou je sčítání modelu. Proměnné, které vás zajímají, který provádí tuto analýzu tractable jsme už jste použili transformace protokolu.

Kompletní kód R pro tento oddíl je v [MachineLearningSamples-poznámkových bloků a studio – ukázky](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Vytvoření datového rámce pro analýzu

Začněte přidáním **nové** [spustit skript jazyka R] [ execute-r-script] modulu do experimentu. Připojení **datovou sadu výsledků** výstup existující [spustit skript jazyka R] [ execute-r-script] modulu **Dataset1** vstupní nového modulu. Výsledek by měl vypadat podobně jako obrázek 20.

![Experiment s modulem nové spustit skript jazyka R přidán](./media/r-quickstart/fig21.png)

*Obrázek 20. Experiment s modulem nové spustit skript jazyka R přidán.*

Jako s analýzou korelace, které jsme právě dokončili, potřebujeme přidat sloupec s POSIXct time series objekt. Následující kód to stačí.

```R
# If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Tento kód spustit a podívejte se na protokol. Výsledek by měl vypadat jako obrázek 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Obrázek 21. Souhrn datového rámce.*

Pomocí tohoto výsledku jsou připravené ke spuštění analýzy.

### <a name="create-a-training-dataset"></a>Vytvoření trénovací datové sady

Vytvořený datového rámce potřebujeme vytvořit trénovací datové sady. Tato data bude obsahovat všechny vyjádření kromě posledních 12 v roce 2013, což je naše testovací datové sady. Následující kód podmnožiny datového rámce a vytvoří vykreslení mléka proměnných produkčního prostředí a ceny. Potom vytvořit grafy čtyři produkčního prostředí a cena proměnné. Anonymní funkce se používá k definování argumentech pro vykreslení a potom iteraci přes seznam dalších dvou argumentů s `Map()`. Pokud přemýšlíte, který pro smyčky by mít fungovaly správně tady, jsou správné. Ale vzhledem k tomu, že je funkční jazyk R uvádíme je funkční přístup.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Spuštění kódu vytváří řadu času z výstupu zařízení jazyka r. znázorňuje obrázek 22, který řady. Všimněte si, že je časová osa v jednotkách, které data, skvělé výhody čas řady grafu – metoda.

![První čas řady vykreslení Kalifornie mléka produkčního prostředí a cena dat.](./media/r-quickstart/unnamed-chunk-161.png)

![Druhý čas řady vykreslení Kalifornie mléka produkčního prostředí a cena dat.](./media/r-quickstart/unnamed-chunk-162.png)

![Třetí čas řady vykreslení Kalifornie mléka produkčního prostředí a cena dat.](./media/r-quickstart/unnamed-chunk-163.png)

![Čtvrtý vykreslení řad čas Kalifornie mléka produkčního prostředí a cena dat.](./media/r-quickstart/unnamed-chunk-164.png)

*Obrázek 22. Vykreslení řad čas produkci mléka Kalifornie a cenové údaje.*

### <a name="a-trend-model"></a>Vývoj modelu

Máte vytvořený objekt řady čas a podívejte se na data měla, Začněme vytvořit model trend Kalifornie mléka produkční data. Můžeme to udělat pomocí řady regrese čas. Je však zřejmé z diagram, který budeme potřebovat vyšší než sklon a zachytit přesně modelování zjištěnou trend v trénovací data.

Zadaný malém měřítku data, můžu se sestavení modelu pro vývoj v RStudio a pak vyjímejte a vkládejte výsledný model do Azure Machine Learning Studio. RStudio nabízí interaktivní prostředí pro interaktivní analýzu tohoto typu.

Jako první pokus bude proveden pokus polynomické regrese s mocniny až 3. Je skutečných nebezpečí over-pass-the přizpůsobení těchto typů modelů. Proto se nedoporučuje nejvyšších podmínky. `I()` Funkce potlačuje výklad obsah (interpretuje obsah "je") a umožňuje psát doslova interpretovaný funkce v rovnici regrese.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Tím se vytvoří následující.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Z hodnoty P (`Pr(>|t|)`) tento výstup, vidíme, že ve čtverci termín nemusí být významné. Můžu používat `update()` funkce k úpravě tohoto modelu přetažením kvadratických termín.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Tím se vytvoří následující.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

To vypadá lépe. Podmínky jsou významné. Však hodnotu 2e-16 je výchozí hodnota a nesmí být přijata příliš vážně.  

Jako test správností vytvoříme vykreslení řad čas Kalifornie dat produkci mléka s trend křivky zobrazí. Jsem přidali následující kód v Azure Machine Learning Studio [spustit skript jazyka R] [ execute-r-script] modelu (ne RStudio) k vytvoření modelu a ujistěte se, vykreslení. Výsledek se zobrazí obrázek 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kalifornie mléka provozních dat pomocí vzoru trend](./media/r-quickstart/unnamed-chunk-18.png)

*Obrázek 23. Kalifornie mléka provozních dat s trend vzoru.*

Zdá se, trend modelu odpovídá zpracovávaným datům poměrně dobře. Kromě toho existuje zřejmě není zahrnuta do legitimaci typu over-pass-the přizpůsobování, jako je liché rychlé pohyby v křivce modelu.  

### <a name="seasonal-model"></a>Sezónním vzoru

S modelem trend v rukou musíme zapnutí a zahrnují sezónní účinky. Měsíce v roce jako fiktivní proměnnou lineární model použije k zaznamenání efekt po měsících. Mějte na paměti, když zavádíte faktor proměnné do modelu, intercept nesmí být vypočítán. Pokud to neprovedete, vzorec je over-pass-the zadané a R budou odstraňte jednu z požadovaných faktory, ale ponechat termín zachycení.

Vzhledem k tomu, že máme modelu uspokojivé trendů můžete použít `update()` funkce pro přidání nové podmínky do existujícího modelu. -1 v aktualizace vzorec zahodí termín zachycení. Pokračování v RStudio pro tuto chvíli:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Tím se vytvoří následující.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vidíme, že model už má některý výraz zachycení a má 12 faktory důležité měsíc. To je přesně to co jsme chtěli byste vidět.

Vytvoříme jiného diagramu čas řady Kalifornie dat produkci mléka zobrazíte, jak dobře funguje sezónním vzoru. Jsem přidali následující kód v Azure Machine Learning Studio [spustit skript jazyka R] [ execute-r-script] vytvořit model a zadáte vykreslení.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Tento kód spuštěný v Azure Machine Learning Studio vytváří diagram znázorňuje obrázek 24.

![Produkci mléka Kalifornie s modelem včetně sezónní efekty](./media/r-quickstart/unnamed-chunk-20.png)

*Obrázek 24. Produkci mléka Kalifornie s modelem včetně sezónní účinky.*

Přizpůsobit data zobrazená v obrázek 24 je spíše podporovat. Oblast přiměřené sezónní efekt (měsíční variace) i trend.

Jako další kontrolu na náš model dopřejeme si podívat odchylky. Následující kód vypočítá predikované hodnoty z našich dva modely, vypočítá zbytky sezónní modelu a poté vytvoří tyto zbytky pro trénovací data.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Zbývající diagram je zobrazena ve obrázek 25.

![Zbytky sezónní model pro trénovací data](./media/r-quickstart/unnamed-chunk-21.png)

*Obrázek 25. Zbytky sezónní model pro trénovací data.*

Tyto odchylky vyhledejte rozumný. Neexistuje žádná konkrétní struktura, s výjimkou efekt recesí 2008-2009, který náš model nezodpovídá za obzvláště dobře.

Diagram znázorňuje obrázek 25 je užitečné pro zjišťování všechny závislé na čase vzory v odchylky. Explicitní přístup výpočetního prostředí a vykreslení zbytky, který jsem používal umístí odchylky v pořadí podle času na vykreslení. Pokud na druhé straně jsem měl promítají `milk.lm$residuals`, vykreslení by nebyl v pořadí podle času.

Můžete také použít `plot.lm()` vytvoří řady diagnostických vykreslení.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Tento kód vytvoří řady diagnostických vykreslení znázorňuje obrázek 26.

![První z diagnostické vykreslení sezónní modelu](./media/r-quickstart/unnamed-chunk-221.png)

![Druhé diagnostických vykreslení sezónní modelu](./media/r-quickstart/unnamed-chunk-222.png)

![Třetí diagnostických vykreslení sezónní modelu](./media/r-quickstart/unnamed-chunk-223.png)

![Čtvrtý diagnostických vykreslení sezónní modelu](./media/r-quickstart/unnamed-chunk-224.png)

*Obrázek 26. Diagnostika vykreslí sezónní modelu.*

Nejsou k dispozici několik vysoce vlivní body identifikované v těchto vykreslení, ale nic vyvolalo obavy skvělé. Dále můžeme vidět z diagramů normální Q-Q toto políčko, budou se zavřít obvykle distribuován důležité předpokladů pro lineární modely.

### <a name="forecasting-and-model-evaluation"></a>Prognózování a model hodnocení

Existuje pouze jeden další věc, kterou chcete udělat, abyste dokončili náš příklad. Musíme compute předpovědí a vyhodnocování chybu podle Skutečná data. Naše předpovědí bude po dobu 12 měsíců 2013. Můžeme můžete následujícím způsobem vypočítat míru chyb pro této předpovědi na skutečná data, která není součástí naší trénovací datové sady. Kromě toho jsme můžete porovnat výkon na 18 let trénovacích dat do 12 měsíců testovací data.  

Počet metriky se používají k měření výkonu čas řady modely. V našem případě použijeme chyba kořenové směrodatná (RMS). Následující funkce vypočítá chybu RMS mezi dvou řad.  

```R
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Stejně jako u `log.transform()` funkce jsme probírali v části "Hodnoty transformace" je poměrně velké množství chyb kontrolu a výjimek kód pro obnovení této funkce. Zásady použijí jsou stejné. Práce se provádí na dvou místech, který je obalen `tryCatch()`. Nejprve časové řady jsou exponentiated, protože Pracujeme s protokoly hodnoty. Za druhé je vypočítán skutečná Chyba služby RMS.  

Vybavený funkci měření chybu RMS, Pojďme sestavení a výstupní datový rámec obsahující chyby služby RMS. Zavedeme podmínky pro model trend samostatně a úplný model s sezónní faktory. Následující kód provede úlohu pomocí dvou lineární modely, které jsme vytvořen.

```R
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio
maml.mapOutputPort('RMS.df')
```

Spuštěním tohoto kódu vytvoří výstup znázorňuje obrázek 27 na výstupní port datové sady výsledků.

![Porovnání chyb služby RMS pro modely](./media/r-quickstart/fig26.png)

*Obrázek 27. Porovnání chyb služby RMS pro modely.*

Z těchto výsledků vidíme, že přidání sezónní faktory do modelu snižuje chyby RMS výrazně. Chyba služby RMS pro trénovací data je příliš logicky chvíli menší než prognózy.

## <a id="appendixa"></a>Příručka k dokumentaci nástroje RStudio

RStudio poměrně dobře zdokumentovat. Tady je pár odkazů na klíčové části RStudio dokumentace vám pomůžou začít.

* **Vytváření projektů** – můžete organizovat a spravovat kódu R do projektů pomocí Rstudia. Zobrazit [pomocí projektů](https://support.rstudio.com/hc/articles/200526207-Using-Projects) podrobnosti. Můžu jenom doporučit postupujte podle těchto pokynů a vytvořte projekt pro příklady kódu R v tomto článku.  
* **Úpravy a spouštění kódu R** -RStudio poskytuje integrované prostředí pro úpravy a spouštění kódu R. Zobrazit [úpravy a provádění kódu](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) podrobnosti.
* **Ladění** -RStudio zahrnuje výkonné možnosti ladění. Zobrazit [ladění s prostředím RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) Další informace o těchto funkcích. Informace o řešení potíží s funkcí zarážku najdete v tématu [zarážky Poradce při potížích](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a id="appendixb"></a>Další čtení

V tomto kurzu programovacího jazyka R vysvětluje co je potřeba použít jazyka R s Azure Machine Learning Studio. Pokud nejste obeznámeni s jazykem R, jsou k dispozici na CRAN dvě úvodní informace:

* [R pro začátečníky](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) Emmanuel Paradis je dobrým začátkem.  
* [Úvod do jazyka R](https://cran.r-project.org/doc/manuals/R-intro.html) W. n. Venables et. al. Přejde do větší hloubky.

Existuje mnoho knih v R, které může pomoci vám začít. Tady je několik, které se mi najít užitečné:

* **Techniky programování v jazyce R: Prohlídka z statistické softwaru návrh** Norman Matloff je vynikající Úvod k programování v jazyce R.  
* **Kuchařka R** díky Paul Teetor poskytuje řešení problému a přístupem k používání jazyka R.  
* **R v akci** Robert Kabacoff je další užitečná úvodní knihy. Doprovodný [rychlé R webu](https://www.statmethods.net/) je užitečný prostředek.
* **R Inferno** Burnse Patrick je překvapivě sloupku knihy, které se zabývají počet témat složité a složité, které mohou nastat při programování v jazyce R. Kniha je k dispozici zdarma na [The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Pokud chcete podrobné informace o Pokročilá témata týkající se jazyka R, podíváme se na knihu **pokročilé R** podle Hadley Wickham. Je k dispozici zdarma na online verze této knihy [ http://adv-r.had.co.nz/ ](http://adv-r.had.co.nz/).

Katalog R čas řady balíčků lze nalézt v [CRAN zobrazení úlohy: Time Series analýzy](https://cran.r-project.org/web/views/TimeSeries.html). Informace o určité časové řady objektu balíčky by měla odkazovat v dokumentaci pro tento balíček.

Knihu **úvodní časové řady** s jazykem R Paul Cowpertwait a Andrew Metcalfe obsahuje úvod k využití R pro analýzu časových řad. Mnoho více teoretické texty najdete příklady R.

Tady jsou některé skvělé internetové prostředky:

* R v pohodlí vašeho prohlížeče s video poznatky a kódování cvičení se dozvíte, jaké Datacampu. Existují interaktivní kurzy o nejnovější R techniky a balíčky. Využijte bezplatnou [Interaktivní kurz R](https://www.datacamp.com/courses/introduction-to-r).
* [Učte se programovat v R, úplnou příručku](https://www.programiz.com/r-programming) z Programiz.
* Stručná [R kurzu](https://www.cyclismo.org/tutorial/R/) podle Kelly černé z Clarkson University.
* Existuje přes 60 R prostředky uvedené v [prostředků jazyka R horní ke zlepšení své dovednosti data](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
