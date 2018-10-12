---
title: Příprava dat pro kurz klasifikace Iris ve službě Azure Machine Learning (Preview) | Microsoft Docs
description: V tomto kurzu na pokračování se dozvíte, jak komplexně používat službu Azure Machine Learning (Preview). Toto je první část, ve které se probírá příprava dat.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 272b8250a80fee42780311dec92f6d47c221c160
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990157"
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>Kurz 1: Klasifikace Iris – Příprava dat

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Služba Azure Machine Learning (verze Preview) představuje integrované, komplexní řešení datové vědy a pokročilé analýzy pro profesionální datové vědce, které slouží k přípravě dat, vývoji experimentů a nasazování modelů na úrovni cloudu.

Tento kurz je **první částí z třídílné série**. V tomto kurzu se názorně seznámíte se základy služby Azure Machine Learning (Preview) a naučíte se následující postupy:

> [!div class="checklist"]
> * Vytvoření projektu v aplikaci Azure Machine Learning Workbench
> * Vytvoření balíčku pro přípravu dat
> * Vygenerování kódu Pythonu/PySpark k vyvolání balíčku pro přípravu dat

Tento kurz používá nadčasovou [datovou sadu Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

K dokončení tohoto kurzu potřebujete:
- Účet Experimentování ve službě Azure Machine Learning
- Nainstalovanou aplikaci Azure Machine Learning Workbench

Pokud tyto požadavky ještě nemáte, postupujte podle kroků v článku [Rychlý start: Instalace a spuštění](quickstart-installation.md) a nastavte své účty a nainstalujte aplikaci Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Vytvoření nového projektu v aplikaci Workbench

Pokud jste postupovali podle kroků v článku [Rychlý start: Instalace a spuštění](quickstart-installation.md), měli byste tento projekt už mít a můžete přeskočit k další části.

1. Otevřete aplikaci Azure Machine Learning Workbench a v případě potřeby se přihlaste. 
   
   + Ve Windows ji spustíte pomocí zástupce **Machine Learning Workbench** na ploše. 
   + V systému macOS vyberte **Azure ML Workbench** na hlavním panelu.

1. V podokně **PROJEKTY** vyberte symbol plus (+) a zvolte **Nový projekt**.  

   ![Nový pracovní prostor](./media/tutorial-classifying-iris/new_ws.png)

1. Vyplňte pole formuláře a výběrem tlačítka **Vytvořit** v aplikaci Workbench vytvořte nový projekt.

   Pole|Navrhovaná hodnota pro tento kurz|Popis
   ---|---|---
   Název projektu | myIris |Zadejte jedinečný název, který identifikuje váš účet. Můžete použít své jméno nebo třeba název oddělení nebo projektu, který experiment nejlépe identifikuje. Název musí mít délku od 2 do 32 znaků. Může obsahovat jenom alfanumerické znaky a znak spojovníku (-). 
   Adresář projektu | c:\Temp\ | Zadejte adresář, ve kterém se projekt vytvoří.
   Popis projektu | _ponechte prázdné_ | Volitelné pole, které je užitečné pro popis projektů.
   Adresa URL úložiště GIT Visualstudio.com |_ponechte prázdné_ | Volitelné pole. Projekt můžete přidružit k úložišti Git v Azure DevOps, které umožňuje správu zdrojového kódu a spolupráci. [Tady najdete potřebné informace](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Vybraný pracovní prostor | IrisGarden (pokud existuje) | Zvolte pracovní prostor, který jste vytvořili pro svůj účet Experimentování na webu Azure Portal. <br/>Pokud jste postupovali podle tohoto rychlého startu, měli byste mít pracovní prostor IrisGarden. Pokud ne, vyberte pracovní prostor, který jste vytvořili při vytváření účtu Experimentování, nebo jakýkoli jiný, který chcete použít.
   Šablona projektu | Klasifikace Iris | Šablony obsahují skripty a data, pomocí kterých můžete produkt zkoumat. Tato šablona obsahuje skripty a data, které potřebujete pro tento rychlý start a další kurzy na tomto webu dokumentace. 

   ![Nový projekt](media/tutorial-classifying-iris/new_project.png)
 
 Vytvoří se nový projekt a otevře se řídicí panel projektu s tímto projektem. Teď můžete prozkoumat domovskou stránku projektu, zdroje dat, poznámkové bloky a soubory zdrojového kódu. 

   ![Otevřený projekt](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Vytvoření balíčku pro přípravu dat

Dále můžete prozkoumat a začít připravovat data v aplikaci Azure Machine Learning Workbench. Každá transformace, kterou provedete v aplikaci Workbench, se uloží ve formátu JSON do místního balíčku pro přípravu dat (soubor *.dprep). Tento balíček pro přípravu dat je primárním kontejnerem pro práci na přípravě dat v aplikaci Workbench.

Tento balíček pro přípravu dat je možné později předat do modulu runtime, jako je místní C#/CoreCLR, Scala/Spark, nebo Scala/HDI. 

1. Výběrem ikony složky otevřete zobrazení Soubory a pak výběrem otevřete soubor **iris.csv**.

   Tento soubor obsahuje tabulku s 5 sloupci a 50 řádky. Čtyři sloupce obsahují čísla. Pátý sloupec je cílový sloupec řetězcového typu. Žádný sloupec neobsahuje název záhlaví.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Nezahrnujte datové soubory do složky projektu, zejména pokud jsou soubory velké. Protože je datový soubor **iris.csv** malý, zahrnuli jsme ho do této šablony pro demonstrační účely. Další informace najdete v tématu [Čtení a zápis velkých datových souborů](how-to-read-write-files.md).

2. V **Zobrazení dat** vyberte symbol plus (**+**) a přidejte nový zdroj dat. Otevře se stránka **Přidat zdroj dat**. 

   ![Zobrazení dat v aplikaci Azure Machine Learning Workbench](media/tutorial-classifying-iris/data_view.png)

3. Vyberte **Textové soubory (\*.csv, \*.json, \*.txt., ...)** a klikněte na **Další**.
   ![Zdroj dat v aplikaci Azure Machine Learning Workbench](media/tutorial-classifying-iris/data-source.png)

4. Přejděte k souboru **iris.csv** a klikněte na **Dokončit**. Tím se pro parametry, jako jsou oddělovač a datové typy, použijí výchozí hodnoty.

   >[!IMPORTANT]
   >Zkontrolujte, že jste pro účely tohoto cvičení vybrali soubor **iris.csv** z aktuálního adresáře projektu. Jinak se pozdější kroky nemusí podařit.
 
   ![Výběr iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Vytvoří se nový soubor s názvem **iris-1.dsource**. Soubor má jedinečný název s „-1“, protože ukázkový projekt už obsahuje neočíslovaný soubor **iris.dsource**.  

   Soubor se otevře a zobrazí se data. Do této datové sady se automaticky přidá řada záhlaví sloupců od **Column1** po **Column5**. Přejděte do dolní části a všimněte si, že poslední řádek datové sady je prázdný. Řádek je prázdný, protože v souboru CSV je zalomení řádku navíc.

   ![Zobrazení dat iris](media/tutorial-classifying-iris/iris_data_view.png)

1. Vyberte tlačítko **Metriky**. Vygenerují a zobrazí se histogramy.

   Zpět na zobrazení dat můžete přepnout výběrem tlačítka **Data**.
   
   ![Zobrazení dat iris](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Podívejte se na histogramy. Pro každý sloupec se vypočítala úplná sada statistik. 

   ![Zobrazení dat iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Začněte vytvářet balíček pro přípravu dat výběrem tlačítka **Připravit**. Otevře se dialogové okno **Připravit**. 

   Ukázkový projekt obsahuje balíček pro přípravu dat **iris.dprep**, který je vybraný ve výchozím nastavení. 

   ![Zobrazení dat iris](media/tutorial-classifying-iris/prepare.png)

1. Vytvořte nový balíček pro přípravu dat tak, že v rozevírací nabídce vyberete **+ Nový balíček pro přípravu dat**.

   ![Zobrazení dat iris](media/tutorial-classifying-iris/prepare_new.png)

1. Zadejte novou hodnotu názvu balíčku (použijte **iris-1**) a pak vyberte **OK**.

   Vytvoří se nový balíček pro přípravu dat s názvem **iris-1.dprep** a otevře se v editoru přípravy dat.

   ![Zobrazení dat iris](media/tutorial-classifying-iris/prepare_iris-1.png)

   Teď provedeme základní přípravu dat. 

1. Výběrem záhlaví jednotlivých sloupců umožníte úpravu jejich textu. Pak jednotlivé sloupce přejmenujte následujícím způsobem: 

   V uvedeném pořadí zadejte pro jednotlivé sloupce názvy **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width** a **Species**.

   ![Přejmenování sloupců](media/tutorial-classifying-iris/rename_column.png)

1. Počet jedinečných hodnot:
   1. Vyberte sloupec **Species**.
   1. Kliknutím pravým tlačítkem ho vyberte. 
   1. Z rozevírací nabídky vyberte **Četnost hodnot**. 

   Pod daty se otevře podokno **Kontroly**. Zobrazí se histogram se čtyřmi pruhy. Cílový sloupec obsahuje čtyři různé hodnoty: **Iris-virginica**, **Iris-versicolor**, **Iris-setosa** a hodnotu **(null)**.

   ![Výběr možnosti Četnost hodnot](media/tutorial-classifying-iris/value_count.png)

   ![Histogram počtu hodnot](media/tutorial-classifying-iris/filter_out.png)

1. Pokud chcete vyfiltrovat hodnoty null, vyberte pruh (null) a pak symbol minus (**-**). 

   Záznam (null) pak zešedne, což značí, že je vyfiltrovaný. 

   ![Vyfiltrování hodnot null](media/tutorial-classifying-iris/filter_out2.png)

1. Všimněte si jednotlivých kroků přípravy dat, které jsou podrobně popsané v podokně **KROKY**. Při přejmenování sloupců a vyfiltrování řádků s hodnotou null se každá akce zaznamenala jako krok přípravy dat. Jednotlivé kroky můžete upravit a tím upravit jejich nastavení, změnit pořadí kroků a odebrat jednotlivé kroky.

   ![Kroky](media/tutorial-classifying-iris/steps.png)

1. Zavřete editor přípravy dat. Výběrem ikony **x** na kartě **iris-1** s ikonou grafu kartu zavřete. Vaše práce se automaticky uloží do souboru **iris-1.dprep** zobrazeného pod nadpisem **Příprava dat**.

   ![Zavřít](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Vygenerování kódu Pythonu/PySpark k vyvolání balíčku pro přípravu dat

 Výstup balíčku pro přípravu dat můžete prozkoumat přímo v Pythonu nebo v poznámkovém bloku Jupyter. Balíčky je možné spouštět napříč různými moduly runtime, včetně místního Pythonu, Spark (i v Dockeru) a HDInsight. 

1. Na kartě Příprava dat vyhledejte soubor **iris-1.dprep**.

1. Klikněte pravým tlačítkem na soubor **iris-1.dprep** a v místní nabídce vyberte **Vygenerovat soubor s kódem pro přístup k datům**. 

   ![Generování kódu](media/tutorial-classifying-iris/generate_code.png)

   Otevře se nový soubor **iris-1.py** s následujícími řádky kódu vyvolávajícími logiku, kterou jste vytvořili jako balíček pro přípravu dat:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   V závislosti na kontextu, ve kterém je tento kód spuštěný, představuje `df` různý druh datového rámce:
   + Při spuštění v modulu runtime Pythonu se použije [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).
   + Při spuštění v kontextu Spark se použije [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html). 
   
   Další informace o přípravě dat v aplikaci Azure Machine Learning Workbench najdete v příručce [Začínáme s přípravou dat](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí aplikace Azure Machine Learning Workbench provedli následující úlohy:
> [!div class="checklist"]
> * Vytvoření nového projektu
> * Vytvoření balíčku pro přípravu dat
> * Vygenerování kódu Pythonu/PySpark k vyvolání balíčku pro přípravu dat

Jste připraveni přejít k další části této série kurzů, ve které se naučíte sestavit model ve službě Azure Machine Learning:
> [!div class="nextstepaction"]
> [Kurz 2 – Sestavení modelů](tutorial-classifying-iris-part-2.md)
