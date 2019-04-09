---
title: Azure SQL Database managed instance auditování | Dokumentace Microsoftu
description: Zjistěte, jak začít pracovat s Azure SQL Database managed instance auditu pomocí T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: fc8b300cea714ee44f826a78ce8c7a10c1443414
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282113"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Začínáme s auditováním služby Azure SQL Database managed instance

[Spravovaná instance](sql-database-managed-instance.md) auditování sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu úložiště Azure. Také auditování:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.
- Povolí a umožňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v článku [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/) místo, kde najdete nejnovější seznam certifikací dodržování předpisů SQL Database.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Nastavení auditování serveru do služby Azure storage

Následující část popisuje konfiguraci auditování pro spravovanou instanci.

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Vytvoření služby Azure Storage **kontejneru** kde jsou uloženy protokoly auditu.

   1. Přejděte do služby Azure Storage, ve kterém chcete ukládat vaše protokoly auditu.

      > [!IMPORTANT]
      > Aby se zabránilo křížová čtení/zápisu použijte účet úložiště ve stejné oblasti jako spravované instance.

   1. V účtu úložiště, přejděte na **přehled** a klikněte na tlačítko **objekty BLOB**.

      ![Azure Blob widgetu](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. V horní nabídce klikněte na tlačítko **+ kontejner** vytvořit nový kontejner.

      ![Vytvoření ikony kontejneru objektů blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Zadejte kontejner **název**, nastavte úroveň pro veřejný přístup **privátní**a potom klikněte na tlačítko **OK**.

      ![Vytvoření konfigurace kontejneru objektů blob](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Po vytvoření kontejneru pro Audit existuje protokoly jsou dva způsoby, jak ho nakonfigurovat jako cíl pro protokoly auditu: [pomocí jazyka T-SQL](#blobtsql) nebo [pomocí SQL Server Management Studio (SSMS) uživatelského rozhraní](#blobssms):

   - <a id="blobtsql"></a>Konfigurace úložiště na blogu k protokolům auditu pomocí T-SQL:

     1. V seznamu kontejnerů klikněte na nově vytvořený kontejner a pak klikněte na tlačítko **vlastnosti kontejneru**.

        ![Tlačítko Vlastnosti kontejneru objektů BLOB](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Kliknutím na ikonu kopírování zkopírujte adresu URL kontejneru a uložit adresu URL (například v poznámkovém bloku) pro budoucí použití. Formát adresy URL kontejneru by měla být `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Adresa URL kopírování objektů BLOB v kontejneru](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Generovat služby Azure Storage **tokenů SAS** udělit spravovanou instanci auditování přístupová práva k účtu úložiště:

        - Přejděte do účtu Azure Storage, ve které jste vytvořili kontejner v předchozím kroku.

        - Klikněte na **sdílený přístupový podpis** v nabídce Nastavení úložiště.

          ![Sdílený přístup podpis ikona v nabídce Nastavení úložiště](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Konfigurace sdíleného přístupového podpisu následujícím způsobem:

          - **Povolené služby**: Objekt blob

          - **Počáteční datum**: abyste zabránili problémům souvisejícím s časové pásmo, doporučuje se použít včerejší datum

          - **Koncové datum**: Zvolte datum, které tento Token SAS vyprší platnost

            > [!NOTE]
            > Obnovení tokenu při vypršení platnosti, aby se zabránilo chybám auditu.

          - Klikněte na **Vygenerovat SAS**.
            
            ![Konfigurace SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Po kliknutí na Generovat SAS SAS Token se zobrazí v dolní části. Kliknutím na ikonu kopírování zkopírujte token a uložte ho (například v poznámkovém bloku) pro budoucí použití.

          ![Zkopírujte SAS token](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Odebrat otazník ("?") znaků od začátku tokenu.

     1. Připojte se k vaší spravované instance pomocí SQL Server Management Studio (SSMS) nebo jakýkoli jiný podporovaný nástroj.

     1. Spuštěním následujícího příkazu T-SQL k **vytvoření nových přihlašovacích údajů** pomocí adresy URL kontejneru a tokenu SAS, který jste vytvořili v předchozích krocích:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Spusťte následující příkaz jazyka T-SQL, chcete-li vytvořit nový Server Audit (zvolte svůj vlastní název auditu, použijte adresu URL kontejneru, který jste vytvořili v předchozích krocích). Pokud není zadán, `RETENTION_DAYS` výchozí hodnota je 0 (neomezenou dobu):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Pokračujte [vytváření specifikace auditu serveru nebo specifikací auditu databáze](#createspec)

   - <a id="blobssms"></a>Konfigurace úložiště objektů blob k protokolům auditu pomocí SQL Server Management Studio (SSMS) 18 (Preview):

     1. Připojte se k spravované instance pomocí SQL Server Management Studio (SSMS) uživatelského rozhraní.

     1. Kořenový uzel Průzkumník objektů rozbalte.

     1. Rozbalte **zabezpečení** uzel, klikněte pravým tlačítkem na **audity** uzel a klikněte na "Nové auditu":

        ![Rozbalíte možnost zabezpečení a audit uzlu](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Přesvědčte se, že "URL" je vybráno v **cíl auditu** a klikněte na **Procházet**:

        ![Procházet úložiště Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Volitelné) Přihlaste se ke svému účtu Azure:

        ![Přihlásit se k Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Vyberte předplatné, účet úložiště a kontejner objektů Blob z rozevíracích seznamů, nebo vytvořit vlastní kontejner kliknutím na **vytvořit**. Po dokončení klikněte na tlačítko **OK**:

        ![Vyberte předplatné Azure, účet úložiště a kontejner objektů blob](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Klikněte na tlačítko **OK** v dialogovém okně "Create Audit".

1. <a id="createspec"></a>Po dokončení konfigurace vytvořit kontejner objektů Blob jako cíl pro protokoly auditu specifikace auditu serveru nebo specifikace auditu databáze stejně jako pro SQL Server:

   - [Vytvořit průvodce pro specifikace jazyka T-SQL Server audit](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Vytvořit průvodce T-SQL specifikace auditu databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Povolte auditu serveru, který jste vytvořili v kroku 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Další informace:

- [Auditování rozdíly mezi izolované databáze, elastický fond, s a spravované instance Azure SQL Database a databází v systému SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [VYTVOŘIT SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [PŘÍKAZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Nastavení auditování serveru do služby Event Hub nebo Azure Monitor protokolů

Protokoly auditu ze spravované instance můžete odesílat i rozbočovače nebo protokoly Azure monitoru. Tato část popisuje, jak nastavit tuto konfiguraci:

1. Přejděte v [webu Azure Portal](https://portal.azure.com/) do spravované instance.

2. Klikněte na **nastavení diagnostiky**.

3. Klikněte na **zapnout diagnostiku**. Pokud už je povolená Diagnostika *+ přidat nastavení diagnostiky* se zobrazí místo.

4. Vyberte **SQLSecurityAuditEvents** v seznamu protokolů.

5. Vyberte cílové umístění událostí auditu – centra událostí a protokoly Azure monitoru. Konfigurace pro každý cíl požadované parametry (například pracovní prostor Log Analytics).

6. Klikněte na **Uložit**.

    ![Konfigurace nastavení diagnostiky](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Připojte se k použití spravované instance **SQL Server Management Studio (SSMS)** nebo jakéhokoli klienta podporované.

8. Spusťte následující příkaz jazyka T-SQL k vytvoření auditu serveru:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Vytvoření specifikace auditu serveru nebo specifikací auditu databáze, stejně jako pro SQL Server:

   - [Vytvořit průvodce pro specifikace jazyka T-SQL Server audit](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Vytvořit průvodce T-SQL specifikace auditu databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Povolte auditování server vytvořili v kroku 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Využívání protokolů auditování

### <a name="consume-logs-stored-in-azure-storage"></a>Využívání protokolů uložená ve službě Azure Storage

Existuje několik metod, které lze použít k zobrazení protokolů auditování objektů blob.

- Použít funkci systému `sys.fn_get_audit_file` (T-SQL) vrátit data protokolu auditování v tabulkovém formátu. Další informace o použití této funkce najdete v článku [sys.fn_get_audit_file dokumentaci](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Protokoly auditu můžete prozkoumat pomocí nástroje [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Ve službě Azure storage se ukládají protokoly auditování jako kolekce souborů, objektů blob do kontejneru, který byl definován pro ukládání protokolů auditu. Další podrobnosti o hierarchii složky úložiště konvence pojmenování a formát protokolu, najdete v článku [odkaz na formát protokolu auditování objektů Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Úplný seznam metod spotřeby protokolu auditu, najdete [Začínáme s auditem SQL database](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Využívání protokolů uložených v Centru událostí

Chcete-li využívají data protokolů auditu z centra událostí, je potřeba nastavit, aby datový proud zpracování událostí a jejich zápisu do cílového. Další informace najdete v článku dokumentace k Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Využití a analyzovat protokoly uložené v protokolech Azure Monitor

Pokud se protokoly auditu se zapisují do protokolů Azure Monitor, jsou k dispozici v pracovním prostoru Log Analytics, ve kterém rozšířené hledání můžete spustit na data auditu. Jako výchozí bod, přejděte do pracovního prostoru Log Analytics a v části *Obecné* klikněte na *protokoly* a zadejte jednoduchý dotaz, jako například: `search "SQLSecurityAuditEvents"` zobrazíte auditování protokoluje.  

Protokoly služby Azure Monitor nabízí provozní informace v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, díky kterým můžete analyzovat miliony záznamů napříč vašimi úlohami a servery. Další užitečné informace o Azure Monitor protokoly vyhledávací jazyk a příkazy, naleznete v tématu [protokoly Azure monitoru reference ke službě search](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Auditování rozdíly mezi databázemi ve službě Azure SQL Database a databází v systému SQL Server

Hlavní rozdíly mezi auditování v databázích v Azure SQL Database a databází v systému SQL Server jsou:

- S možností nasazení spravované instance Azure SQL Database, auditování funguje na úrovni serveru nebo úložišti `.xel` soubory protokolů ve službě Azure Blob storage.
- Izolované databáze a elastický fond možnosti nasazení ve službě Azure SQL Database auditování funguje na úrovni databáze.
- V místním SQL serverem / virtuální počítače, audit funguje na serveru úrovně, ale ukládá události do protokolů událostí systému a soubory.

Relace XEvent auditování ve spravované instanci podporuje cíle úložiště objektů Blob v Azure. Souborová služba a windows protokoly jsou **nepodporuje**.

Klíč rozdíly v `CREATE AUDIT` syntaxe pro auditování objektů Blob v Azure storage jsou:

- Novou syntaxi `TO URL` je k dispozici a umožní vám zadat adresu URL kontejneru objektů blob v Azure Storage, kde `.xel` soubory jsou umístěny.
- Novou syntaxi `TO EXTERNAL MONITOR` je k dispozici pro povolení i Hub a Azure Monitor cíle protokolů.
- Syntaxe `TO FILE` je **nepodporuje** protože SQL Database nemá přístup ke sdílené složky Windows.
- Možnost vypnutí je **nepodporuje**.
- `queue_delay` 0 je **nepodporuje**.

## <a name="next-steps"></a>Další postup

- Úplný seznam metod spotřeby protokolu auditu, najdete [Začínáme s auditem SQL database](sql-database-auditing.md).
- Další informace o Azure programy dodržování standardů tuto podporu, najdete v článku [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/) místo, kde najdete nejnovější seznam certifikací dodržování předpisů SQL Database.

<!--Image references-->









