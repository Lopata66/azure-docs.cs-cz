---
title: Rychlý start popisující použití služby Machine Learning Services (s jazykem R) ve službě Azure SQL Database (Preview) | Microsoft Docs
description: V tomto tématu se dozvíte, jak používat službu Machine Learning Services ve službě Azure SQL Database a jak spouštěním skriptů jazyka R poskytovat pokročilé analýzy ve velkém měřítku a možnost přenést výpočty a zpracování do umístění uložení dat a eliminovat tak potřebu přenášení dat přes síť.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: e15cf93514f921223fea37aa480730bba46dd195
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864945"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Rychlý start: Pomocí služby Machine Learning (s jazykem R) ve službě Azure SQL Database (preview)

Tento článek vysvětluje, jak můžete pomocí verze public preview [Machine Learning Services (s jazykem R) ve službě Azure SQL Database](sql-database-machine-learning-services-overview.md). Provede vás základy přesunu dat mezi databází SQL a jazykem R. Vysvětluje také, jak zabalit kód R ve správném formátu do uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) pro vyváření, trénování a používání modelů strojového učení v databázi SQL.

Využijte sílu jazyka R k poskytování pokročilých analýz a strojového učení v databázi. Tato schopnost přináší výpočty a zpracování, kde jsou data uložená, takže odpadá potřeba k získání dat přes síť. Také využijte možnosti balíčky R organizace k poskytování pokročilé analýzy ve velkém měřítku.

Služby Machine Learning zahrnuje základní distribuci R překrývající se balíčky jazyka R enterprise od Microsoftu. Funkce jazyka R a algoritmy Microsoftu jsou navržené pro škálování a nástroj pro prediktivní analýzy, statistické modelování, vizualizace dat a špičkový algoritmů strojového učení.

Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

> [!IMPORTANT]
> Služby Machine Learning Azure SQL Database je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Zaregistrujte si verzi preview](sql-database-machine-learning-services-overview.md#signup).

## <a name="prerequisites"></a>Požadavky

Abyste mohli spustit ukázkový kód v těchto cvičeních, musíte nejprve mít databázi SQL s povolenou službou Machine Learning Services (s jazykem R). Ve verzi public preview, společnost Microsoft bude připojit je a povolit strojového učení pro existující nebo nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

K připojení ke službě SQL Database a spouštění skriptů R můžete použít jakýkoli nástroj pro správu databáze nebo dotazování, pokud umožňuje připojení ke službě SQL Database a spuštění dotazu T-SQL nebo uložené procedury. V tomto rychlém startu se používá [SQL Server Management Studio](sql-database-connect-query-ssms.md).

Pro účely cvičení [přidání balíčku](#add-package) bude také potřeba na místní počítač nainstalovat jazyk [R](https://www.r-project.org/) a aplikaci [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

Tento rychlý start dále vyžaduje, abyste nakonfigurovali pravidlo brány firewall na úrovni serveru. Rychlý start, který vám ukáže, jak to udělat, najdete v článku týkajícím se [vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).

## <a name="verify-r-exists"></a>Ověření existence jazyka R

Můžete ověřit, že je pro vaši databázi SQL povolená služba Machine Learning Services (s jazykem R). Postupujte následovně.

1. Otevřete aplikaci SQL Server Management Studio a připojte se ke své databázi SQL. Další informace o tom, jak připojit najdete v tématu [rychlý start: Pomocí SQL Server Management Studio k připojení a dotazování Azure SQL database](sql-database-connect-query-ssms.md).

1. Spusťte následující kód. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Pokud je vše v pořádku, měla by se zobrazit podobná zpráva s výsledkem.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Postup registrace verze Public Preview najdete výše.

## <a name="grant-permissions"></a>Udělení oprávnění

Pokud jste správce, můžete spustit externí kód automaticky. Všichni ostatní musí být udělena oprávnění.

Nahraďte `<username>` s platnou databázi přihlášení uživatele před spuštěním příkazu.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Základní interakce s jazykem R

Kód R můžete ve službě SQL Database spustit dvěma způsoby:

+ Přidat skript jazyka R jako argument systémové uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ Ze [vzdáleného klienta R](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client) se připojte ke své databázi SQL a spusťte kód s použitím výpočetního kontextu služby SQL Database.

Následující cvičení se zaměřuje na první model interakce: jak předat kód R do uložené procedury.

1. Spusťte jednoduchý skript, který naleznete v tématu Jak spustí skript R ve službě SQL database.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. Za předpokladu, že máte vše správně nastavené, se vypočítá správný výsledek a funkce R `print` vrátí výsledek do okna **Zprávy**.

    **Results**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Přestože je získávání zpráv **stdout** užitečné při testování kódu, častěji potřebujete vrátit výsledky v tabulkovém formátu, abyste je mohli použít v aplikaci nebo zapsat do tabulky. Další informace najdete níže v části Vstupy a výstupy.

Mějte na paměti, že vše uvnitř argumentu `@script` musí být platný kód R.

## <a name="inputs-and-outputs"></a>Vstupy a výstupy

Ve výchozím nastavení přijímá [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) jednu vstupní datovou sadu, kterou obvykle zadáte v podobě platného dotazu SQL. Jiné typy vstupu je možné předat jako proměnné SQL.

Uložená procedura jako výstup vrátí jeden datový rámec R. Ve výstupu však můžete vrátit také skaláry a modely jako proměnné. Ve výstupu můžete vrátit například natrénovaný model jako binární proměnnou a předat ji do příkazu T-SQL INSERT, aby se tento model zapsal do tabulky. Můžete také generovat diagramy (v binárním formátu) nebo skaláry (jednotlivé hodnoty, jako je datum a čas, uplynulý čas trénování modelu atd.).

Prozatím se podíváme pouze na výchozí vstupní a výstupní hodnoty pro sp_execute_external_script: `InputDataSet` a `OutputDataSet`.

1. Spuštěním následujícího příkazu T-SQL vytvořte malou tabulku testovacích dat:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    Po vytvoření tabulky pomocí následujícího příkazu odešlete dotaz na tabulku:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Results**

    ![Obsah tabulky RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Data z tabulky můžete získat jako vstup pro svůj skript R. Spusťte následující příkaz. Tento příkaz získá data z tabulky, projde celý modul runtime R a vrátí hodnoty s názvem sloupce *NewColName*.

    Data vrácená dotazem se předají do modulu runtime R, který data vrátí do služby SQL Database jako datový rámec. Klauzule WITH RESULT SETS definuje schéma vrácené tabulky dat pro službu SQL Database.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Výstup skriptu R, který vrací data z tabulky](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Teď změníme název vstupní a výstupní proměnné. Výše uvedený skript používá výchozí názvy vstupní a výstupní proměnné _InputDataSet_ a _OutputDataSet_. Chcete-li definovat vstupní data přidružená k _InputDatSet_, použijete  *\@input_data_1* proměnné.

    V tomto skriptu se názvy výstupní a vstupní proměnné pro uloženou proceduru změnily na *SQL_out* a *SQL_in*:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Nezapomeňte, že jazyk R rozlišuje malá a velká písmena, takže velikost písmen ve vstupní a výstupní proměnné `@input_data_1_name` a `@output_data_1_name` musí odpovídat velikosti písmen v kódu R v proměnné `@script`. 

    Kromě toho je důležité také pořadí parametrů. Je nutné zadat požadované parametry  *\@input_data_1* a  *\@output_data_1* první, chcete-li použít nepovinné parametry  *\@ input_data_1_name* a  *\@output_data_1_name*.

    Jako parametr je možné předat pouze jednu vstupní datovou sadu. Zároveň je možné vrátit pouze jednu datovou sadu. V kódu R však můžete volat i jiné datové sady a kromě datové sady vracet také výstupy jiných typů. K jakémukoli parametrů také můžete přidat klíčové slovo OUTPUT, aby se vrátil s výsledky. 

    Příkaz `WITH RESULT SETS` definuje schéma dat, které se používá ve službě SQL Database. Pro každý sloupec, který vracíte v kódu R, je potřeba zadat datový typ kompatibilní s jazykem SQL. K zadání názvů sloupců můžete použít také definici schématu, protože v datovém rámci R nepotřebujete používat názvy sloupců.

4. Také můžete generovat hodnoty pomocí skriptu R a nechat vstupní řetězec dotazu v proměnné _@input_data_1_ prázdný.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Výsledky dotazu s použitím proměnné @script jako vstupu](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Kontrola verze jazyka R

Pokud chcete zjistit, jaká verze jazyka R je nainstalovaná ve vaší databázi SQL, postupujte následovně:

1. Spusťte pro databázi SQL následující skript.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. Funkce R `print` vrátí verzi do okna **Zprávy**. V následujícím příkladu výstupu vidíte, že v tomto případě je ve službě SQL Database nainstalovaný jazyk R verze 3.4.4.

    **Results**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>Výpis balíčků R

Microsoft poskytuje v rámci služby Machine Learning Services v databázi SQL řadu předinstalovaných balíčků R. Pokud chcete zobrazit seznam nainstalovaných balíčků R, včetně informací o verzi, závislostech, licenci a cestě ke knihovně, postupujte následovně. Pokud chcete přidat další balíčky, přečtěte si část [Přidání balíčku](#add-package).

1. Spusťte pro databázi SQL následující skript.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. Výstup pochází z funkce `installed.packages()` v jazyce R a vrací se jako sada výsledků dotazu.

    **Results**

    ![Nainstalované balíčky v jazyce R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>Vytvoření prediktivního modelu

Pomocí jazyka R můžete natrénovat model a uložit ho do tabulky v databázi SQL. V tomto cvičení natrénujete jednoduchý regresní model, který na základě rychlosti předpovídá brzdnou dráhu auta. Použijete datovou sadu `cars`, která je součástí jazyka R, protože je malá a snadno pochopitelná.

1. Nejprve vytvořte tabulku pro ukládání trénovacích dat.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Součástí modulu runtime R je řada malých i velkých datových sad. Pokud chcete získat seznam datových sad nainstalovaných s jazykem R, zadejte na příkazovém řádku R příkaz `library(help="datasets")`.

2. Vytvořte regresní model. Data o rychlosti aut obsahují dva číselné sloupce `dist` a `speed`. U některých rychlostí existuje několik pozorování. Z těchto dat vytvoříte model lineární regrese, který popisuje určitý vztah mezi rychlostí a brzdnou dráhou auta.

    Požadavky na lineární model jsou jednoduché:

   - Definování vzorce, který popisuje vztah mezi závislou proměnnou `speed` a nezávislou proměnnou `distance`.

   - Poskytnutí vstupních dat pro použití při trénování modelu.

     > [!TIP]
     > Pokud potřebujete občerstvit lineární modelů, doporučujeme vám tento kurz, který popisuje postup přizpůsobení modelu pomocí rxLinMod: [Přizpůsobení lineární modelů](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

     Model sestavíte tak, že v kódu R definujete vzorec a jako vstupní parametr předáte data.

     ```sql
     DROP PROCEDURE IF EXISTS generate_linear_model;
     GO
     CREATE PROCEDURE generate_linear_model
     AS
     BEGIN
       EXEC sp_execute_external_script
       @language = N'R'
       , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
           trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
       , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
       , @input_data_1_name = N'CarsData'
       , @output_data_1_name = N'trained_model'
       WITH RESULT SETS ((model VARBINARY(max)));
     END;
     GO
     ```

     První argument funkce rxLinMod je parametr *formula*, který definuje závislost proměnné distance (vzdálenost) na proměnné speed (rychlost). Vstupní data jsou uložená v proměnné `CarsData`, která se naplní dotazem SQL. Pokud vstupním datům nepřiřadíte konkrétní název, výchozí název proměnné bude _InputDataSet_.

2. Dále vytvořte tabulku, do které model uložíte, abyste ho mohli přetrénovat nebo použít k předpovědi. Výstupem balíčku R, který vytváří model, je obvykle **binární objekt**. Proto tabulka musí obsahovat sloupec typu **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Pokud chcete model uložit, spusťte následující příkaz Transact-SQL, který zavolá uloženou proceduru, vygeneruje model a uloží ho do tabulky.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Všimněte si, že když tento kód spustíte podruhé, zobrazí se tato chyba:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Jednou z možností, jak se této chybě vyhnout, je pro každý nový model aktualizovat název. Můžete například použít popisnější název a zahrnout do něj typ modelu, den vytvoření atd.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Obecně platí, že výstup kódu R z uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) je omezený na jeden datový rámec.

    Kromě datového rámce však můžete vracet i výstupy jiných typů, například skaláry.

    Předpokládejme například, že chcete trénovat model a okamžitě zobrazit tabulku koeficientů z tohoto modelu. Jako hlavní sadu výsledků dotazu můžete vytvořit tabulku koeficientů a výstup natrénovaného modelu uložit do proměnné SQL. Model můžete okamžitě opakovaně používat zavoláním této proměnné nebo můžete model uložit do tabulky, jak je znázorněno zde.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Results**

    ![Natrénovaný model s dalším výstupem](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Předpověď

Použijte model, který jste vytvořili v předchozí části, k hodnocení předpovědí s novými daty. Pokud chcete provést _bodování_ s použitím nových dat, získejte z tabulky některý z natrénovaných modelů a pak zavolejte novou sadu dat, na které chcete založit předpovědi. Termín bodování se někdy používá v datových vědách a znamená generování předpovědí, pravděpodobností nebo jiných hodnot na základě nových dat předaných do natrénovaného modelu.

1. Nejprve vytvořte tabulku s novými daty o rychlosti. Všimli jste si, že původní trénovací data končila rychlostí 25 MPH? Je to proto, že původní data vycházela z experimentu z roku 1920. Možná vás zajímá, jak dlouho by automobilu z dvacátých let trvalo zastavit, kdyby mohl dosáhnout rychlosti 60 MPH nebo dokonce 100 MPH. K zodpovězení této otázky je potřeba zadat několik nových hodnot rychlosti.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    Vzhledem k tomu, že je váš model založený na algoritmu **rxLinMod**, který je součástí balíčku **RevoScaleR**, místo obecné funkce R `predict` zavoláte v tomto příkladu funkci [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict).

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
                current_model <- unserialize(as.raw(speedmodel));
                new <- data.frame(NewCarData);
                predicted.distance <- rxPredict(current_model, new);
                str(predicted.distance);
                OutputDataSet <- cbind(new, ceiling(predicted.distance));
                '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    Výše uvedený skript provádí následující kroky:

   + Pomocí příkazu SELECT získá z tabulky jeden model a předá ho jako vstupní parametr.

   + Po načtení modelu z tabulky zavolá pro model funkci `unserialize`.

       > [!TIP] 
       > Prozkoumejte také nové [funkce serializace](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) v balíčku RevoScaleR, které podporují bodování v reálném čase.
   + Použije na model funkci `rxPredict` s odpovídajícími argumenty a zadá nová vstupní data.

   + V tomto příkladu se ve fázi testování přidá funkce `str`, která kontroluje schéma dat vracených z kódu R. Tento příkaz můžete později odebrat.

   + Názvy sloupců použité ve skriptu R není nutné předávat do výstupu uložené procedury. Tady jsme použili klauzuli WITH RESULTS k definování několika nových názvů sloupců.

     **Results**

     ![Sada výsledků dotazu pro předpověď brzdné dráhy](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

     K vygenerování předpovězené hodnoty nebo skóre na základě uloženého modelu je možné použít také příkaz [PREDICT v jazyce Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql).

<a name="add-package"></a>

## <a name="add-a-package"></a>Přidání balíčku

Pokud potřebujete použít balíček, který ještě ve vaší databázi SQL není nainstalovaný, můžete ho nainstalovat pomocí nástroje [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Při instalaci balíčku použijte následující postup.

1. Z webu [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) si do místního počítače stáhněte soubor ZIP s nejnovější verzí nástroje **sqlmlutils**. Soubor nemusíte rozbalovat.

1. Pokud nemáte nainstalovaný jazyk R, stáhněte si ho z webu [www.r-project.org](https://www.r-project.org/) a nainstalujte ho na místní počítač. Jazyk R je k dispozici pro Windows, MacOS a Linux. V tomto příkladu používáme Windows.

1. Nejprve nainstalujte balíček **RODBCext**, který je pro nástroj **sqlmlutils** nezbytný. **RODBCext** nainstaluje také závislý balíček **RODBC**. Otevřete **příkazový řádek** a spusťte následující příkaz:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Pokud se zobrazí následující chyba, "" R"nebyl rozpoznán jako vnitřního ani vnějšího příkazu, spustitelného programu nebo dávkového souboru", pravděpodobně znamená, že cesta k R.exe není součástí vaší **cesta** proměnné prostředí na Windows. Můžete přidat adresář do proměnné prostředí nebo přejděte do adresáře, v příkazovém řádku (například `cd C:\Program Files\R\R-3.5.1\bin`) před spuštěním příkazu.

1. Pomocí příkazu **R CMD INSTALL** nainstalujte nástroj **sqlmlutils**. Zadejte cestu k adresáři, do kterého jste stáhli soubor ZIP, a název soubor ZIP. Příklad:

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. V tomto příkladu jako integrované vývojové prostředí (IDE) použijete RStudio Desktop. Pokud chcete, můžete použít jiné integrované vývojové prostředí. Pokud ještě nemáte nainstalovanou aplikaci RStudio, z webu [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/) si stáhněte a nainstalujte RStudio Desktop.

1. Otevřete **RStudio** a vytvořte nový soubor **skriptu R**. Pomocí následujícího kódu R nainstalujte balíček s využitím nástroje sqlmlutils. V následujícím příkladu nainstalujete balíček [glue](https://cran.r-project.org/web/packages/glue/), který dokáže formátovat a interpolovat řetězec.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > Hodnota **scope** (Obor) může být **PUBLIC** (VEŘEJNÝ) **PRIVATE** (SOUKROMÝ). Veřejný obor je užitečný pro správce databáze, kterému umožňuje instalovat balíčky, které můžou používat všichni uživatelé. Soukromý obor zpřístupní balíček pouze uživateli, který ho nainstaluje. Pokud obor nezadáte, výchozí obor bude **SOUKROMÝ**.

1. Teď ověřte, že se balíček **glue** nainstalovat.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Results**

    ![Obsah tabulky RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. Po dokončení instalace balíčku ho můžete prostřednictvím **sp_execute_external_script** použít ve svém skriptu R. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL. Spusťte tento skript:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    Na kartě Zprávy se zobrazí následující výsledek.

    **Results**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Pokud chcete balíček odebrat, spusťte v aplikaci **RStudio** na místním počítači následující skript R.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Dalším způsobem, jak do databáze SQL nainstalovat balíčky R, je nahrát balíček R z datového proudu bajtů pomocí příkazu [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Další postup

Další informace o Machine Learning Services najdete v níže uvedených článků. I když některé z těchto článků pro SQL Server, většina informace platí také pro Machine Learning Services (s jazykem R) ve službě Azure SQL Database.

- [Azure SQL Database služby Machine Learning (s jazykem R)](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Kurz: Zjistěte, analýza v databázi pomocí jazyka R na SQL serveru](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Ucelený průvodce datovými vědami pro jazyk R a SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Kurz: Funkce RevoScaleR R s daty formátu SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
