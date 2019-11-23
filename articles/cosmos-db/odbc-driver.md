---
title: Připojení k Azure Cosmos DB pomocí analytických nástrojů BI
description: Zjistěte, jak vytvořit tabulky a zobrazení normalizovaná data lze zobrazit v BI a data analytický software pomocí ovladače Azure Cosmos DB ODBC.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e8a982a100655934d4ae3ecd64564cf2da82dbbc
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035589"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Připojení ke službě Azure Cosmos DB pomocí nástroje pro analýzu BI pomocí ovladače ODBC

Ovladač Azure Cosmos DB ODBC umožňuje připojení k Azure Cosmos DB pomocí nástrojů BI analytics, například SQL Server Integration Services, Power BI Desktopu a tableau. představují tak, že můžete analyzovat a vytvářet vizualizace dat služby Azure Cosmos DB v těchto řešeních.

Ovladač ODBC pro Azure Cosmos DB je kompatibilní s rozhraním ODBC 3.8 a podporuje syntaxi ANSI SQL-92. Tento ovladač nabízí bohaté funkce, které vám pomůžou znovu normalizovat data ve službě Azure Cosmos DB. S využitím tohoto ovladače můžete data ve službě Azure Cosmos DB reprezentovat jako tabulky a zobrazení. Tento ovladač umožňuje provádět operace SQL s tabulkami a zobrazeními, včetně seskupení podle dotazů a operací vložení, aktualizace a odstranění.

> [!NOTE]
> Připojení ke službě Azure Cosmos DB pomocí ovladače ODBC aktuálně podporuje pouze účty SQL API služby Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Proč je potřeba údaje normalizovat?
Azure Cosmos DB je schemaless databáze, která umožňuje rychlý vývoj aplikací a možnost iterovat datové modely aplikace bez se omezuje na striktní schéma. Jedna databáze Azure Cosmos může obsahovat dokumenty JSON různých struktur. To je skvělé pro rychlý vývoj aplikací, ale pokud chcete analyzovat a vytváření sestav svá data pomocí analýzy dat a nástroje BI, často je potřeba data sloučí a dodržovat určité schéma.

To je, kde je k dispozici ovladač ODBC ve. Pomocí ovladače ODBC, můžete teď znovu normalizovat data ve službě Azure Cosmos DB do tabulek a zobrazení, které odpovídají vaší analýzy dat a sestav. Renormalized schémata mít vliv na podkladová data a ne omezit vývojářům dodržovat je. Místo toho umožňují využívat kompatibilní se standardem ODBC nástroje pro přístup k datům. Takže teď vaše databáze Azure Cosmos nebude jenom oblíbená pro váš vývojový tým, ale vaše analytiky dat je uvidí.

Pusťme se do práce pomocí ovladače ODBC.

## <a id="install"></a>Krok 1: Nainstalujte ovladač Azure Cosmos DB ODBC

1. Stáhněte si ovladače pro vaše prostředí:

    | Instalační program | Podporované operační systémy| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) pro 64bitová verze Windows| 64bitová verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/cosmos-odbc-32x64) pro 32bitového systému Windows 64-bit| 64bitová verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 a Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) pro 32bitová verze Windows|32bitové verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP a Windows Vista.|

    Spusťte soubor msi místně, která se spouští **Microsoft Azure Cosmos DB ODBC Driver Průvodce instalací**. 

1. Dokončení Průvodce instalací pomocí výchozí vstup do nainstalujte ovladač ODBC.

1. Otevřít **správce zdrojů dat ODBC** aplikace ve vašem počítači. Můžete to provést tak, že zadáte **zdroje dat ODBC** pole vyhledávání v Windows. 
    Můžete ověřit kliknutím byl nainstalován ovladač **ovladače** kartu a zajištění **ovladače ODBC Microsoft Azure Cosmos DB** je uvedena.

    ![Azure Cosmos DB správce zdrojů dat ODBC](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Krok 2: připojení k databázi Azure Cosmos

1. Po [instalaci ovladače Azure Cosmos DB ODBC](#install)v **správce zdrojů dat ODBC** okna, klikněte na tlačítko **přidat**. Můžete vytvořit uživatele nebo název DSN systému. V tomto příkladu vytvoříte uživatelské DSN.

1. V **vytvořit nový zdroj dat** okně **ovladače ODBC Microsoft Azure Cosmos DB**a potom klikněte na tlačítko **Dokončit**.

1. V **Azure Cosmos DB ODBC – ovladače SDN nastavení** okno, zadejte následující informace: 

    ![Azure Cosmos DB ODBC ovladač DSN okno](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Název zdroje dat**: vlastní popisný název DSN rozhraní ODBC. Tento název je jedinečný pro váš účet Azure Cosmos DB, tak pojmenujte ho správně Pokud máte více účtů.
    - **Popis**: stručný popis datového zdroje.
    - **Hostitel**: identifikátor URI pro váš účet Azure Cosmos DB. Můžete načíst to ze stránky klíče služby Azure Cosmos DB na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky. 
    - **Přístup ke klíči**: klíč primární nebo sekundární, čtení a zápis nebo jen pro čtení z klíče Azure Cosmos DB stránce na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky. Doporučujeme že použít klíč jen pro čtení, pokud název zdroje dat se používá pro zpracování dat jen pro čtení a vytváření sestav.
    ![Stránka služby Azure Cosmos DB klíče](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Přístupový klíč pro šifrování**: vybrat nejlepší volbou založená na uživatelích tohoto počítače. 
    
1. Klikněte na tlačítko **Test** tlačítko a ujistěte se, že můžete připojit ke svému účtu Azure Cosmos DB. 

1.  Klikněte na tlačítko **rozšířené možnosti** a nastavte následující hodnoty:
    *  **REST API verze**: vyberte [verzi REST API](https://docs.microsoft.com/rest/api/cosmos-db/) pro vaše operace. Výchozí hodnota je 2015-12-16. Pokud máte kontejnery s [velkými klíči oddílů](large-partition-keys.md) a vyžadujete REST API verze 2018-12-31:
        - Zadejte **2018-12-31** pro REST API verzi.
        - V nabídce **Start** zadejte příkaz regedit a vyhledejte a otevřete aplikaci **Editor registru** .
        - V editoru registru přejděte na cestu: **počítač \ HKEY_LOCAL_MACHINE \software\odbc\odbc. INI**
        - Vytvořte nový podklíč se stejným názvem jako název DSN, např. "účet contoso ODBC DSN".
        - Přejděte do podklíče "účet" ODBC DSN "účtu contoso.
        - Kliknutím pravým tlačítkem přidáte novou **řetězcovou** hodnotu:
            - Název hodnoty: **IgnoreSessionToken**
            - Údaj hodnoty: **1**
            ![nastavení editoru registru](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Dotazování konzistence**: vyberte [úrovně konzistence](consistency-levels.md) pro operace. Výchozí hodnota je relace.
    - **Počet opakovaných pokusů**: Zadejte počet pokusů o zopakování operace, pokud v prvotní žádosti z důvodu omezení rychlosti služby nedokončí.
    - **Soubor schématu**: tady máte několik možností.
        - Ve výchozím nastavení zachová tuto položku jako (prázdná), ovladač prohledá první stránku dat pro všechny kontejnery a určí schéma každého kontejneru. Toto je známé jako mapování kontejneru. Bez souboru schématu definice ovladač nemá k provedení kontroly pro každou relaci ovladač a může mít za následek vyšší dobu spuštění aplikace pomocí DSN. Doporučujeme vždy přiřadit soubor schématu pro zdroje dat DSN.
        - Pokud už máte soubor schématu (Možná ho vytvoříte pomocí editoru schémat), můžete kliknout na **Procházet**, přejít k souboru, kliknout na **Uložit**a pak na **OK**.
        - Pokud chcete vytvořit nové schéma, klikněte na tlačítko **OK**a potom klikněte na tlačítko **Editor schémat** v hlavním okně. Pak přejděte k informacím editoru schématu. Po vytvoření nového souboru schématu, nezapomeňte přejít zpět **rozšířené možnosti** okna k zahrnutí souboru nově vytvořeného schématu.

1. Po dokončení a zavření **Azure Cosmos DB ODBC – ovladače DSN nastavení** okně Nový uživatelský název DSN se přidá na kartu uživatelské DSN.

    ![Nové Azure Cosmos DB název DSN rozhraní ODBC na kartě Uživatelské DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>Krok 3: vytvoření definice schématu pomocí metody mapování kontejnerů

Existují dva typy metod vzorkování, které lze použít: **mapování kontejnerů** nebo **oddělovače tabulek**. Relace vzorkování může využívat jak metody vzorkování, ale každý kontejner může použít pouze konkrétní metodu vzorkování. Následující postup vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování kontejnerů. Tato metoda vzorkování načítá data na stránce kontejneru pro určení struktury dat. Předává kontejner na straně rozhraní ODBC jako tabulku. Tato metoda vzorkování je efektivní a rychlá, když jsou data v kontejneru homogenní. Pokud kontejner obsahuje heterogenní typ dat, doporučujeme použít [metodu mapování oddělovače tabulky](#table-mapping) , protože poskytuje robustnější metodu vzorkování pro určení datových struktur v kontejneru. 

1. Po dokončení kroků 1-4 v tématu [připojení k databázi Azure Cosmos](#connect)klikněte na **editor schémat** v okně **Nastavení DSN ovladače Azure Cosmos DB ODBC** .

    ![Tlačítko editoru schématu v okně instalace ovladačů název zdroje dat v Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver-schema-editor.png)
1. V **Editor schémat** okna, klikněte na tlačítko **vytvořit nový**.
    V okně **generovat schéma** se zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte jeden nebo více kontejnerů, které chcete vzorkovat, a potom klikněte na tlačítko **Ukázka**. 

1. V **návrhové zobrazení** jsou reprezentovány kartu, databázi, schéma a tabulku. V zobrazení tabulky vyhledávání zobrazuje sadu vlastností, které jsou spojené s názvy sloupců (název systému SQL, název zdroje atd.).
    Pro každý sloupec, můžete upravit název sloupce SQL, datový typ SQL, SQL délka (Pokud je k dispozici), škálování (Pokud je k dispozici), přesnosti (Pokud je k dispozici) a s možnou hodnotou Null.
    - Můžete nastavit **skrýt sloupec** k **true** Pokud budete chtít vyloučit tento sloupec ve výsledcích dotazu. Sloupce označené skrýt sloupec = true nebudou zobrazeny pro výběr a projekce, i když jsou stále součástí schématu. Například můžete skrýt všechny vlastnosti služby Azure Cosmos DB je požadován systém počínaje "_".
    - **Id** sloupec je pouze pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaných schématu. 

1. Až dokončíte definici schématu, klikněte na **souboru** | **Uložit**, přejděte do adresáře, chcete-li uložit schéma a pak klikněte na tlačítko **Uložit**.

1. Pokud chcete toto schéma použít se zdrojem DSN, otevřete **okno Azure Cosmos DB nastavení DSN ovladače ODBC** (přes Správce zdrojů dat ODBC), klikněte na **Upřesnit možnosti**a pak v poli **schématu** přejděte do uloženého schématu. Ukládání souboru schématu do existující DSN upraví připojení DSN do oboru k datům a struktura definovaná pomocí schématu.

## <a id="table-mapping"></a>Krok 4: Vytvoření schématu definice pomocí oddělovače tabulky mapování – metoda

Existují dva typy metod vzorkování, které lze použít: **mapování kontejnerů** nebo **oddělovače tabulek**. Relace vzorkování může využívat jak metody vzorkování, ale každý kontejner může použít pouze konkrétní metodu vzorkování. 

Následující postup vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování **oddělovače tabulky** . Tuto metodu vzorkování doporučujeme použít, pokud vaše kontejnery obsahují heterogenní typ dat. Tuto metodu můžete použít k určení rozsahu vzorkování na sadu atributů a jejich odpovídající hodnoty. Například pokud dokument obsahuje vlastnost "Type", můžete upřesnit rozsah vzorkování na hodnoty této vlastnosti. Konečný výsledek vzorkování by sadu tabulek pro jednotlivé hodnoty pro typ, které jste zadali. Zadejte například = Auto vytvoří tabulku Car při typ = roviny byste mohli vytvořit tabulku roviny.

1. Po dokončení kroků 1-4 v tématu [připojení k databázi Azure Cosmos](#connect)klikněte na **editor schémat** v okně nastavení DSN ovladače Azure Cosmos DB ODBC.

1. V **Editor schémat** okna, klikněte na tlačítko **vytvořit nový**.
    V okně **generovat schéma** se zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte kontejner na kartě **ukázkové zobrazení** ve sloupci **definice mapování** pro kontejner klikněte na **Upravit**. Pak v **mapování definice** okna, vyberte **tabulky oddělovače** metoda. Potom udělejte následující:

    a. V **atributy** zadejte název vlastnosti oddělovač. Toto je vlastnost v dokumentu, který chcete určit obor vzorkování pro město, a stiskněte klávesu enter. 

    b. Pokud chcete určit obor vzorkování na určité hodnoty pro atribut, který jste zadali výše, vyberte atribut v poli pro výběr, zadejte hodnotu do **hodnotu** pole (například v Praze) a stiskněte klávesu enter. Chcete-li přidat více hodnot pro atributy můžete pokračovat. Jenom zkontrolujte, že je vybraný správný atribut, pokud zadáváte hodnoty.

    Například pokud zahrnete **atributy** hodnoty města a můžete chtít omezit vaše tabulku, aby obsahovala pouze řádky s hodnotou města New York a Dubaj, Město by zadejte atributy pole, New York a potom Dubaj v **Hodnoty** pole.

1. Klikněte na tlačítko **OK**. 

1. Po dokončení definic mapování pro kontejnery, které chcete vzorkovat, v okně **editor schémat** klikněte na položku **Ukázka**.
     Pro každý sloupec, můžete upravit název sloupce SQL, datový typ SQL, SQL délka (Pokud je k dispozici), škálování (Pokud je k dispozici), přesnosti (Pokud je k dispozici) a s možnou hodnotou Null.
    - Můžete nastavit **skrýt sloupec** k **true** Pokud budete chtít vyloučit tento sloupec ve výsledcích dotazu. Sloupce označené skrýt sloupec = true nebudou zobrazeny pro výběr a projekce, i když jsou stále součástí schématu. Například můžete skrýt všechny služby Azure Cosmos DB je požadován systém vlastnosti počínaje `_`.
    - **Id** sloupec je pouze pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaných schématu. 

1. Až dokončíte definici schématu, klikněte na **souboru** | **Uložit**, přejděte do adresáře, chcete-li uložit schéma a pak klikněte na tlačítko **Uložit**.

1. Zpátky **Azure Cosmos DB ODBC – ovladače DSN nastavení** okno, klikněte na tlačítko **rozšířené možnosti**. Potom v **soubor schématu** pole, přejděte k souboru uloženého schématu a klikněte na tlačítko **OK**. Klikněte na tlačítko **OK** znovu a uložit název DSN. Název zdroje dat tím uložíte schéma, které jste vytvořili. 

## <a name="optional-set-up-linked-server-connection"></a>(Volitelné) Nastavit připojení odkazovaného serveru

Díky nastavení připojení odkazovaného serveru se můžete dotazovat službu Azure Cosmos DB z SQL Server Management Studio (SSMS).

1. Vytvořit zdroj dat systému, jak je popsáno v [kroku 2](#connect), například s názvem `SDS Name`.

1. [Nainstalujte SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a připojte se k serveru. 

1. V editoru dotazů SSMS vytvořit objekt odkazovaný server `DEMOCOSMOS` pro zdroj dat pomocí následujících příkazů. Nahraďte `DEMOCOSMOS` s názvem pro odkazovaný server, a `SDS Name` s názvem zdroje dat systému.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Pokud chcete zobrazit nový název odkazovaný server, aktualizujte seznam propojené servery.

![Odkazovaný Server v aplikaci SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Dotazování propojené databáze

K dotazování propojené databáze, zadejte dotaz služby SSMS. V tomto příkladu se dotaz vybere z tabulky v kontejneru s názvem `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Spusťte dotaz. Výsledek by měl být podobný tomuto:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Propojené služby Cosmos DB server nepodporuje názvy složené ze čtyř částí. Vrátí se chyba podobná následující zpráva:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Volitelné) Vytváření zobrazení
Můžete definovat a vytvořit zobrazení jako součást procesu vzorkování. Tato zobrazení jsou ekvivalentní zobrazení SQL. Jsou jen pro čtení a výběry a projekce dotazu SQL služby Azure Cosmos DB definované jsou oboru. 

Chcete-li vytvořit zobrazení pro data, v okně **editor schémat** klikněte ve sloupci **definice zobrazení** na položku **Přidat** na řádku kontejneru a proveďte ukázku. 
    ![Vytvoření zobrazení dat](./media/odbc-driver/odbc-driver-create-view.png)


Pak v **definice zobrazení** okno, postupujte takto:

1. Klikněte na tlačítko **nový**, zadejte název zobrazení, například EmployeesfromSeattleView a potom klikněte na tlačítko **OK**.

1. V **upravit zobrazení** okno, zadejte dotaz služby Azure Cosmos DB. Musí se jednat [dotaz SQL služby Azure Cosmos DB](how-to-sql-query.md), například `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`a potom klikněte na tlačítko **OK**.

    ![Přidat dotaz při vytváření zobrazení](./media/odbc-driver/odbc-driver-create-view-2.png)


Chcete, můžete vytvořit mnoha zobrazení. Po dokončení definování zobrazení, můžete pak Vzorkovat data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Zobrazení dat v nástrojích BI, jako je například Power BI Desktopu

Váš nový název zdroje dat můžete použít pro připojení ke službě Azure Cosmos DB se žádné kompatibilní se standardem ODBC nástroje – tento krok jednoduše se dozvíte, jak se připojit k Power BI Desktopu a vytvoření vizualizace pomocí Power BI.

1. Otevřete Power BI Desktop.

1. Klikněte na tlačítko **získat Data**.

    ![Získání dat v Power BI Desktopu](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. V **získat Data** okna, klikněte na tlačítko **jiných** | **ODBC** | **připojit**.

    ![Vyberte zdroj dat rozhraní ODBC, v Power BI získat Data](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. V **z rozhraní ODBC** okna, vyberte název vytvořeného zdroj dat a klikněte na **OK**. Můžete nechat **rozšířené možnosti** položky prázdné.

    ![Zvolte název zdroje dat (DSN) v Power BI získat Data](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. V **přístup ke zdroji dat pomocí ovladače ODBC** okně **výchozí nebo vlastní** a potom klikněte na tlačítko **připojit**. Není potřeba zahrnout **vlastnosti připojovacího řetězce přihlašovacích údajů**.

1. V **Navigátor** okna, v levém podokně rozbalte databázi, schéma a potom vyberte v tabulce. V podokně Výsledky zahrnuje data pomocí schématu, kterou jste vytvořili.

    ![Výběr tabulky v Power BI získat Data](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. K vizualizaci dat v Power BI desktopu, zaškrtněte políčko u názvu tabulky a pak klikněte na tlačítko **zatížení**.

1. V Power BI Desktopu, úplně vlevo, vyberte na kartě Data ![Karta data v Power BI Desktopu](./media/odbc-driver/odbc-driver-data-tab.png) Potvrďte data byla importována.

1. Nyní můžete vytvořit vizuály s použitím Power BI po kliknutí na kartu sestavy ![kartu sestavy v Power BI Desktopu](./media/odbc-driver/odbc-driver-report-tab.png), kliknutím na příkaz **nového Visual**a potom přizpůsobte dlaždice. Další informace o vytváření vizualizací v Power BI Desktopu najdete v tématu [typy vizualizací v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí následující chyba, ujistěte se, **hostitele** a **přístupový klíč** hodnoty, které jste zkopírovali na webu Azure portal v [krok 2](#connect) jsou správné a opakujte. Pomocí tlačítek kopírování na pravé straně **hostitele** a **přístupový klíč** hodnoty na webu Azure Portal ke zkopírování bez hodnoty chyb.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Další kroky

Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](introduction.md).
