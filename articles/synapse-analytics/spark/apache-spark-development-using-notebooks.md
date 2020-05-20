---
title: Vytváření, vývoj a údržba notebooků Azure synapse Studio (Preview)
description: V tomto článku se dozvíte, jak vytvořit a vyvíjet notebooky Azure synapse Studio (Preview), abyste mohli provádět přípravu a vizualizaci dat.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 21e3ba8cbf60cbbdc6480719016fc48db4fe390c
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702096"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Vytváření, vývoj a údržba notebooků Azure synapse Studio (Preview)

Poznámkový blok Azure synapse Studio (Preview) je webové rozhraní, které umožňuje vytvářet soubory, které obsahují živý kód, vizualizace a mluvený text. Poznámkové bloky jsou vhodné místo pro ověřování nápadů a k získání přehledu z vašich dat můžete využít rychlé experimenty. Poznámkové bloky se také často používají při přípravě dat, vizualizaci dat, strojovém učení a dalších scénářích s velkými objemy dat.

Pomocí poznámkového bloku Azure synapse Studio můžete:

* Začněte s nulovým úsilím nastavení.
* Zajistěte zabezpečení dat pomocí integrovaných funkcí podnikového zabezpečení.
* Analyzujte data napříč nezpracovanými formáty (CSV, txt, JSON atd.), zpracovanými formáty souborů (Parquet, rozdílových Lake, ORC atd.) a SQL tabelárních datových souborů pro Spark a SQL.
* Získejte produktivitu s využitím vylepšených možností vytváření a integrovaných vizualizací dat.

Tento článek popisuje, jak používat notebooky v Azure synapse Studio.

## <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

Existují dva způsoby, jak vytvořit Poznámkový blok. Můžete vytvořit nový Poznámkový blok nebo importovat existující Poznámkový blok do pracovního prostoru Azure synapse z **Průzkumník objektů**. Poznámkové bloky Azure synapse Studio můžou rozpoznávat standardní soubory Jupyter Notebook IPYNB.

![synapse-Create-import-Poznámkový blok](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Vývoj poznámkových bloků

Poznámkové bloky se skládají z buněk, což jsou jednotlivé bloky kódu nebo textu, které mohou být spuštěny nezávisle nebo jako skupina.

### <a name="add-a-cell"></a>Přidat buňku

Existuje několik způsobů, jak přidat novou buňku do poznámkového bloku.

1. Rozbalte tlačítko horní levý **+ buňka** a vyberte **Přidat znaková buňka** nebo **Přidat textovou buňku**.

    ![Přidání buňky s tlačítkem do buňky](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Najeďte myší na místo mezi dvěma buňkami a vyberte **přidat kód** nebo **Přidat text**.

    ![přidávání buněk mezi místy](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [V režimu příkazu použijte klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **klávesy** vložte buňku nad aktuální buňku. Stisknutím tlačítka **B** vložte buňku pod aktuální buňku.

### <a name="set-a-primary-language"></a>Nastavení primárního jazyka

Poznámkové bloky Azure synapse Studio podporují čtyři jazyky Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET pro Apache Spark (C#)

V rozevíracím seznamu na horním panelu příkazů můžete nastavit primární jazyk pro nové přidané buňky.

   ![výchozí – synapse – jazyk](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Použít více jazyků

V jednom poznámkovém bloku můžete použít více jazyků zadáním správného příkazu jazyka Magic na začátku buňky. V následující tabulce jsou uvedeny příkazy Magic pro přepínání jazyků buněk.

|Magic – příkaz |Jazyk | Popis |  
|---|------|-----|
|%% pyspark| Python | Spustí dotaz **Pythonu** v kontextu Sparku.  |
|%% Spark| Scala | Spustí dotaz **Scala** proti kontextu Spark.  |  
|%% SQL| SparkSQL | Spustí dotaz **SparkSQL** proti kontextu Spark.  |
|%% CSharp | .NET pro Spark C # | Spustí dotaz **.NET pro Spark C#** proti kontextu Spark. |

Následující obrázek je příkladem, jak můžete napsat dotaz PySpark pomocí příkazu **%% PySpark** Magic nebo dotazu SparkSQL s příkazem **%% SQL** Magic v poznámkovém bloku **Spark (Scala)** . Všimněte si, že primární jazyk pro Poznámkový blok je nastavený na pySpark.

   ![synapse-Spark-Magic](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Použití dočasných tabulek k odkazování na data napříč jazyky

V poznámkovém bloku synapse Studio nejde odkazovat na data ani proměnné přímo v různých jazycích. Ve Sparku může být na dočasné tabulce odkazováno napříč jazyky. Tady je příklad, jak číst datový `Scala` rámec v `PySpark` a `SparkSQL` použít dočasnou tabulku Spark jako alternativní řešení.

1. V buňce 1 Přečtěte z konektoru fondu SQL datový rámec pomocí Scala a vytvořte dočasnou tabulku.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. V buňce 2 se Dotazujte na data pomocí Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. V buňce 3 použijte data v PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>Vývojové prostředí – styly IntelliSense

Poznámkové bloky Azure synapse Studio jsou integrované s editorem Monako a umožňují technologii IntelliSense ve stylu IDE pro Editor buněk. Zvýrazňování syntaxe, chyba maker a automatické dokončování kódu vám pomůžou psát kód a identifikovat problémy rychleji.

Funkce IntelliSense mají různé úrovně splatnosti pro různé jazyky. Pomocí následující tabulky můžete zjistit, co je podporováno.

|Jazyky| Zvýrazňování syntaxe | Značka chyby syntaxe  | Dokončování kódu syntaxe | Dokončování kódu proměnné| Dokončování kódu systémové funkce| Dokončování kódu uživatelské funkce| Inteligentní odsazení | Skládání kódu|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Spark (Scala)|Ano|Ano|Ano|Ano|-|-|-|Ano|
|SparkSQL|Ano|Ano|-|-|-|-|-|-|
|.NET for Spark (C#)|Ano|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formátování textové buňky pomocí tlačítek panelu nástrojů

Pomocí tlačítek formát v panelu nástrojů textové buňky můžete provádět běžné akce Markdownu. Zahrnuje tučný text, italicizing text, vkládání fragmentů kódu, vkládání neuspořádaného seznamu, vkládání seřazeného seznamu a vkládání obrázku z adresy URL.

  ![synapse-text-buňka – panel nástrojů](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Vrátit operace s buňkami
Chcete-li odvolat poslední operaci buňky, klikněte na tlačítko **zpět** nebo stiskněte klávesy **CTRL + Z** . Nyní můžete vrátit zpět až 20 posledních 20 historických akcí. 

   ![synapse – vrácení buněk zpět](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Přesunutí buňky

Vyberte tři tečky (...) pro přístup k nabídce Další akce buňky úplně vpravo. Pak vyberte **přesunout buňku nahoru** nebo **přesunout buňku dolů** , aby se přesunula aktuální buňka. 

[V režimu příkazu](#shortcut-keys-under-command-mode)můžete také použít klávesové zkratky. Stisknutím **kombinace kláves CTRL + ALT + ↑** přesunete aktuální buňku. Stisknutím **kombinace kláves CTRL + ALT + ↓** přesunete aktuální buňku dolů.

   ![přesunout do buňky](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Odstranění buňky

Pokud chcete odstranit buňku, vyberte tři tečky (...) pro přístup k nabídce Další akce buňky úplně vpravo a pak vyberte **Odstranit buňku**. 

[V režimu příkazu](#shortcut-keys-under-command-mode)můžete také použít klávesové zkratky. Aktuální buňku odstraníte stisknutím klávesy **d, d** .
  
   ![Odstranění buňky](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Sbalení vstupu buňky
Kliknutím na tlačítko se šipkou v dolní části aktuální buňky ho sbalíte. Chcete-li ji rozbalit, klikněte na tlačítko se šipkou, zatímco je buňka sbalená.

   ![sbalení – vstup z buňky](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Sbalit výstup buňky

Kliknutím na tlačítko **sbalit výstup** v levém horním rohu aktuální buňky na výstupu ho sbalíte. Chcete-li ji rozbalit, klikněte na tlačítko **Zobrazit výstup buňky** , zatímco je výstup buňky sbalen.

   ![sbalení – výstup z buňky](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Spustit poznámkové bloky

Buňky kódu můžete na svém poznámkovém bloku spustit jednotlivě nebo najednou. Stav a průběh každé buňky je reprezentován v poznámkovém bloku.

### <a name="run-a-cell"></a>Spuštění buňky

Existuje několik způsobů, jak kód spustit v buňce.

1. Najeďte myší na buňku, kterou chcete spustit, a vyberte tlačítko **Spustit buňku** nebo stiskněte klávesy **CTRL + ENTER**.

   ![spustit – buňka-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Chcete-li získat přístup k nabídce Další akce v buňce úplně vpravo, vyberte tři tečky (**...**). Pak vyberte **Spustit buňku**.

   ![spustit – buňka 2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. [V režimu příkazu použijte klávesové zkratky](#shortcut-keys-under-command-mode). Stisknutím **SHIFT + ENTER** spusťte aktuální buňku a vyberte buňku níže. Stisknutím **kombinace kláves ALT + ENTER** spusťte aktuální buňku a vložte novou buňku níže.


### <a name="run-all-cells"></a>Spustit všechny buňky
Kliknutím na tlačítko **Spustit vše** spustíte všechny buňky v aktuálním poznámkovém bloku v sekvenci.

   ![Spustit – všechny buňky](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Spustit všechny buňky výše nebo níže

Chcete-li získat přístup k nabídce Další akce v buňce úplně vpravo, vyberte tři tečky (**...**). Pak vyberte **Spustit buňky výše** a spusťte tak všechny buňky nad aktuální sekvencí. Vyberte **Spustit buňky níže** , aby se spouštěly všechny buňky pod aktuálním pořadím.

   ![Run-Cells-nad nebo – níže](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indikátor stavu buňky

Podrobný stav spuštění buňky se zobrazí pod buňkou, která vám umožní zobrazit jeho aktuální průběh. Po dokončení spuštění buňky se zobrazí souhrn spuštění s celkovou dobou trvání a časem ukončení a bude se uchovávat pro budoucí použití.

![stav buňky](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indikátor průběhu Sparku

Notebook Azure synapse Studio je čistě založený na Sparku. Buňky kódu se spouštějí ve fondu Spark vzdáleně. Indikátor průběhu úlohy Spark je k dispozici s pruhem průběhu v reálném čase, který vám pomůže pochopit stav provádění úlohy.


![Spark – indikátor průběhu](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfigurace relace Spark

Můžete zadat dobu trvání, číslo a velikost prováděcích modulů, které se mají přiřadit aktuální relaci Sparku v části **konfigurovat relaci**. Restartujte relaci Spark, aby se projevily změny v konfiguraci. Všechny proměnné poznámkového bloku uložené v mezipaměti jsou vymazány.

![Správa relací](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Přenesení dat do poznámkového bloku

Data můžete načíst z Azure Blob Storage Azure Data Lake Store Gen 2 a SQL, jak je znázorněno na následujících ukázkách kódu.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Čtení sdíleného svazku clusteru z Azure Data Lake Store Gen2 jako Spark dataframe

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Čtení sdíleného svazku clusteru z Azure Blob Storage jako Spark dataframe

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Čtení dat z primárního účtu úložiště

K datům v primárním účtu úložiště můžete přistupovat přímo. Není nutné zadávat tajné klíče. V Průzkumník dat klikněte pravým tlačítkem na soubor a vyberte **Nový Poznámkový blok** pro zobrazení nového poznámkového bloku s automaticky generovaným nástrojem pro extrakci dat.

![data na buňku](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Vizualizace dat v poznámkovém bloku

### <a name="display"></a>Zobrazit ()

Zobrazení tabelárních výsledků je k dispozici s možností vytvoření pruhového grafu, spojnicového grafu, výsečového grafu, bodového grafu a plošného grafu. Data můžete vizualizovat bez nutnosti psát kód. Grafy je možné přizpůsobit v **možnostech grafu**. 

V zobrazení vykreslené tabulky je ve výchozím nastavení zobrazen výstup příkazů **%%** Magic. Zobrazení vykreslené tabulky můžete vyvolat voláním funkce **Display ( `<DataFrame name>` )** pro datové rámce Spark nebo odolné distribuované datové sady (RDD).

   ![předdefinované – grafy](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Můžete vykreslit HTML nebo interaktivní knihovny, jako je **rozostření**, pomocí **displayHTML ()**.

Následující obrázek je příkladem vykreslení glyfů přes mapu pomocí **rozostření**.

   ![rozostření – příklad](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Spusťte následující vzorový kód pro vykreslení obrázku výše.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Ukládat poznámkové bloky

V pracovním prostoru můžete uložit jeden Poznámkový blok nebo všechny poznámkové bloky.

1. Pokud chcete uložit změny, které jste provedli v jednom poznámkovém bloku, vyberte tlačítko **publikovat** na panelu příkazů poznámkového bloku.

   ![Publisher – Poznámkový blok](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Pokud chcete uložit všechny poznámkové bloky v pracovním prostoru, vyberte na panelu příkazů pracovní prostor tlačítko **publikovat vše** . 

   ![publikovat – vše](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Ve vlastnostech poznámkového bloku můžete nakonfigurovat, jestli se má při ukládání zahrnout výstup buňky.

   ![vlastnosti poznámkového bloku](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic – příkazy
V poznámkových blocích Azure synapse Studio můžete používat známé příkazy Jupyter Magic. V následujícím seznamu vyhledejte aktuální dostupné příkazy Magic. Řekněte nám své případy použití na GitHubu, abychom mohli pokračovat v sestavování dalších příkazů Magic pro splnění vašich požadavků.

Dostupné magicy řádků: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Dostupné buňky Magic: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% CSharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Klávesové zkratky

Podobně jako Jupyter poznámkové bloky mají poznámkové bloky Azure synapse Studio modální uživatelské rozhraní. Klávesnice funguje jinak v závislosti na tom, v jakém režimu je buňka poznámkového bloku. Poznámkové bloky synapse Studio podporují pro danou buňku kódu tyto dva režimy: režim příkazů a režim úprav.

1. Buňka je v režimu příkazu, když není k dispozici žádný textový kurzor, který je vyzván k zadání. Když je buňka v režimu příkazu, můžete Poznámkový blok upravit jako celek, ale ne psát do jednotlivých buněk. Stisknutím `ESC` nebo pomocí myši můžete kliknout mimo oblast editoru buňky a přejít do režimu příkazu.

   ![režim příkazu](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. Režim úprav je označen textovým kurzorem, který vás vyzve k zadání v oblasti editoru. Když je buňka v režimu úprav, nemůžete do ní zadávat text. Stisknutím `Enter` nebo pomocí myši přejděte na oblast editoru buňky a přejděte do režimu úprav.
   
   ![rezim-uprav](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Klávesové zkratky v režimu příkazu

Pomocí následujících klávesových zkratek můžete snáze procházet a spouštět kód v poznámkových blocích Azure synapse.

| Akce |Zástupci poznámkových bloků synapse Studio  |
|--|--|
|Spustit aktuální buňku a vybrat níže | Shift+Enter |
|Spustit aktuální buňku a vložit níže | Alt+Enter |
|Vybrat buňku výše| Nahoru |
|Vybrat buňku níže| Dolů |
|Vložit buňku výše| A |
|Vložit buňku níže| B |
|Zvětšit vybrané buňky výše| Shift + šipka nahoru |
|Zvětšit vybrané buňky níže| Shift + šipka dolů|
|Přesunout buňku nahoru| CTRL + ALT + ↑ |
|Přesunout buňku dolů| CTRL + ALT + ↓ |
|Odstranit vybrané buňky| D, D |
|Přepnout do režimu úprav| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Klávesové zkratky v režimu úprav

Pomocí následujících klávesových zkratek můžete v režimu úprav snadněji Procházet a spouštět kód v poznámkových blocích Azure synapse.

| Akce |Zástupci poznámkových bloků synapse Studio  |
|--|--|
|Přesunout kurzor nahoru | Nahoru |
|Přesunout kurzor dolů|Dolů|
|Zpět|CTRL + Z|
|Opakovat|CTRL + Y|
|Komentář/odkomentovat|CTRL +/|
|Odstranit slovo před|Ctrl + Backspace|
|Odstranit slovo za|CTRL + Delete|
|Přejít na začátek buňky|Ctrl + Home|
|Přejít na konec buňky |CTRL + END|
|Přejít o jedno slovo doleva|Ctrl + šipka vlevo|
|Přejít o jedno slovo doprava|Ctrl + šipka vpravo|
|Vybrat vše|CTRL + A|
|Rážce| CTRL +]|
|Zmenšit odsazení|CTRL + [|
|Přepnout do režimu příkazu| Esc |

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Vytvoření fondu Apache Spark (ve verzi Preview) ve službě Azure synapse Analytics pomocí nástrojů pro web](../quickstart-apache-spark-notebook.md)
- [Co je Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)
- [Použití .NET pro Apache Spark s Azure synapse Analytics](spark-dotnet.md)
- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
