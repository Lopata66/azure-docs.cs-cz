---
title: Auditování Azure SQL
description: Pomocí auditování Azure SQL Database můžete sledovat události databáze v protokolu auditu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 57c4b22dfe6ef6cf44be64a4b5c042403f64ccf2
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096652"
---
# <a name="azure-sql-auditing"></a>Auditování Azure SQL

Auditování pro Azure [SQL Database](sql-database-technical-overview.md) a [Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage, Log Analytics pracovním prostoru nebo Event Hubs. 

Auditování také:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

- Umožňuje a usnadňuje dodržování předpisů, i když soulad s těmito standardy nezaručuje. Další informace o programech Azure, které podporují dodržování standardů, najdete v [Centrum zabezpečení Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

> [!NOTE] 
> Toto téma se týká jak Azure SQL Database, tak i databází Azure synapse Analytics. Pro zjednodušení se SQL Database používá při odkazování na Azure SQL Database a Azure synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Přehled

Auditování SQL Database můžete použít k těmto účelům:

- **Zachovejte** záznam auditu vybraných událostí. Můžete definovat akce databáze, které se mají auditovat.
- **Sestava** aktivity databáze. Můžete využít nakonfigurované sestavy a řídicí panel, abyste mohli rychle začít s vytvářením sestav aktivit a událostí.
- **Analýza** sestav. Můžete vyhledávat podezřelé události, neobvyklou aktivitu a trendy.

> [!IMPORTANT]
> - Auditování Azure SQL Database je optimalizováno pro zajištění dostupnosti &ho výkonu. Během velmi vysoké aktivity Azure SQL Database umožňuje operacím pokračovat a nemusí nahrávat některé auditované události.

### <a name="auditing-limitations"></a>Omezení auditování

- **Premium Storage** **se v tuto chvíli nepodporuje**.
- **Hierarchický obor názvů** pro **Azure Data Lake Storage Gen2 účet úložiště** se v **tuto chvíli nepodporuje**.
- Povolení auditování u pozastavené **Azure SQL Data Warehouse** se nepodporuje. Chcete-li povolit auditování, obnovte datový sklad.

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definování zásad auditování na úrovni serveru vs. databáze

Zásady auditu je možné definovat pro určitou databázi nebo jako výchozí zásady serveru:

- Zásady serveru se vztahují na všechny existující a nově vytvořené databáze na serveru.

- Pokud *je auditování serveru povoleno*, bude *vždy použito pro databázi*. Databáze bude auditována bez ohledu na nastavení auditování databáze.

- Povolení auditování pro databázi nebo datový sklad, kromě povolení na serveru, *nepřepisuje ani* nemění žádné nastavení auditování serveru. Oba audity budou existovat vedle sebe. Jinými slovy, databáze je auditována dvakrát; jednou zásadami serveru a jednou zásadami databáze.

   > [!NOTE]
   > Měli byste se vyhnout vzájemnému povolení auditování serveru i auditování objektů BLOB databáze, pokud:
    > - Pro konkrétní databázi chcete použít jiný *účet úložiště*, *dobu uchování* nebo *Log Analytics pracovní prostor* .
    > - Chcete auditovat typy a kategorie událostí pro konkrétní databázi, která se liší od ostatních databází na serveru. Například můžete mít vložená vložení tabulky, která musí být auditována pouze pro konkrétní databázi.
   >
   > V opačném případě doporučujeme povolit pouze auditování na úrovni serveru a ponechat auditování na úrovni databáze zakázané pro všechny databáze.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Nastavení auditování pro server

Výchozí zásada auditování zahrnuje všechny akce a následující sadu skupin akcí, které budou auditovat všechny dotazy a uložené procedury provedené proti databázi a také úspěšná a neúspěšná přihlášení:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
Auditování můžete nakonfigurovat pro různé typy akcí a skupin akcí pomocí prostředí PowerShell, jak je popsáno v části [Správa auditování služby SQL Database pomocí Azure PowerShell](#manage-auditing) .

Azure SQL Database audit ukládá 4000 znaků dat pro pole znaků v záznamu auditu. Pokud **příkaz** nebo **data_sensitivity_information** hodnoty vrácené z auditované akce obsahují více než 4000 znaků, všechna data za prvních 4000 znaků budou **zkrácena a nebudou auditována**.
V následující části je popsána konfigurace auditování pomocí Azure Portal.

1. Přejít na [Azure Portal](https://portal.azure.com).
2. V záhlaví zabezpečení v podokně SQL Database/Server přejděte na **audit** .
3. Pokud dáváte přednost nastavení zásad auditování serveru, můžete vybrat odkaz **Zobrazit nastavení serveru** na stránce auditování databáze. Pak můžete zobrazit nebo upravit nastavení auditování serveru. Zásady auditování serveru se vztahují na všechny existující a nově vytvořené databáze na tomto serveru.

    ![Navigační podokno](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Pokud chcete povolit auditování na úrovni databáze, přepněte **auditování** na **zapnuto**. Pokud je povolené auditování serveru, bude audit konfigurovaný pro databázi existovat vedle sebe s auditem serveru.

5. Máte několik možností, jak nakonfigurovat, kam budou zapsány protokoly auditu. Protokoly můžete do účtu služby Azure Storage zapsat do Log Analytics pracovního prostoru pro spotřebu pomocí protokolů Azure Monitor (Preview) nebo do centra událostí pro spotřebu pomocí centra událostí (Preview). Můžete nakonfigurovat libovolnou kombinaci těchto možností a protokoly auditu se zapíší do každého z nich.
  
   ![Možnosti úložiště](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Auditovat cíl úložiště

Pokud chcete nakonfigurovat zápis protokolů auditu na účet úložiště, vyberte **úložiště** a otevřete **Podrobnosti úložiště**. Vyberte účet úložiště Azure, do kterého se budou ukládat protokoly, a pak vyberte dobu uchování. Pak klikněte na **OK**. Protokoly starší než doba uchování se odstraní.

- Výchozí hodnota pro dobu uchování je 0 (neomezená doba uchovávání). Tuto hodnotu můžete změnit přesunutím posuvníku **uchovávání (dny)** v **Nastavení úložiště** při konfiguraci účtu úložiště pro auditování.
  - Pokud změníte dobu uchování z 0 (neomezené uchovávání) na jakoukoli jinou hodnotu, pamatujte na to, že doba uchovávání se bude vztahovat jenom na protokoly zapsané po změně hodnoty uchování (protokoly zapsané během období, kdy byla doba uchování nastavená na neomezený, se zachovají i po povolení uchování).

  ![účet úložiště](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Poznámky

- Protokoly auditu se píší pro **doplňovací objekty blob** v úložišti objektů BLOB v Azure ve vašem předplatném Azure.
- Pokud chcete nakonfigurovat úložiště neměnného protokolu pro události auditu na úrovni serveru nebo databáze, postupujte podle [pokynů Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (Ujistěte se prosím, že jste při konfiguraci neměnného úložiště objektů BLOB vybrali možnost **povolení dalších připojení** ).
- Protokoly auditu můžete zapsat na účet Azure Storage za virtuální sítí nebo bránou firewall. Konkrétní pokyny najdete v tématu [zápis auditu do účtu úložiště za virtuální sítí a branou firewall](create-auditing-storage-account-vnet-firewall.md).
- Po dokončení konfigurace nastavení auditování můžete zapnout funkci detekce nové hrozby a nakonfigurovat e-maily tak, aby přijímaly výstrahy zabezpečení. Pokud používáte detekci hrozeb, obdržíte proaktivní výstrahy týkající se neobvykléch databázových aktivit, které mohou označovat potenciální bezpečnostní hrozby. Další informace najdete v tématu [Začínáme s detekcí hrozeb](sql-database-threat-detection-get-started.md).
- Podrobnosti o formátu protokolu, hierarchii složky úložiště a zásadách vytváření názvů najdete v referenčních informacích o [formátu protokolu auditu objektů BLOB](https://go.microsoft.com/fwlink/?linkid=829599).
- Při použití ověřování AAD se záznamy neúspěšných přihlášení *nezobrazí v* protokolu auditu SQL. Chcete-li zobrazit záznamy auditu neúspěšných přihlášení, je nutné navštívit [portál Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), který protokoluje podrobnosti o těchto událostech.
- Auditování [replik jen pro čtení](sql-database-read-scale-out.md) je povoleno automaticky. Další podrobnosti o hierarchii složek úložiště, konvencí pojmenování a formátu protokolu najdete v tématu [Formát protokolu auditu SQL Database](sql-database-audit-log-format.md). 

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Audit na Log Analytics cíl
  
Pokud chcete nakonfigurovat zápis protokolů auditu do pracovního prostoru Log Analytics, vyberte **Log Analytics (Preview)** a otevřete **Log Analytics podrobnosti**. Vyberte nebo vytvořte Log Analytics pracovní prostor, do kterého budou zapsány protokoly, a pak klikněte na **OK**.
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Auditovat cíl centra událostí

> [!WARNING]
> Povolením auditování na serveru, který má ve fondu SQL, dojde k tomu, že **se fond SQL obnoví a znovu pozastaví** , což může účtovat poplatky za účtování.
> Povolení auditování u pozastaveného fondu SQL není možné. Pokud ho chcete povolit, zrušte pozastavení fondu SQL.

Pokud chcete nakonfigurovat zápis protokolů auditu do centra událostí, vyberte **centrum událostí (Preview)** a otevřete **Podrobnosti centra událostí**. Vyberte centrum událostí, kam budou zapsány protokoly, a pak klikněte na **OK**. Ujistěte se, že centrum událostí je ve stejné oblasti jako databáze a Server.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analýza protokolů a sestav auditu

Pokud se rozhodnete zapisovat protokoly auditu do protokolů Azure Monitor:

- Použijte [Azure Portal](https://portal.azure.com).  Otevřete příslušnou databázi. V horní části stránky **auditování** databáze klikněte na **Zobrazit protokoly auditu**.

    ![Zobrazit protokoly auditu](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Pak máte dva způsoby, jak zobrazit protokoly:
    
    Kliknutím na **Log Analytics** v horní části stránky **Auditovat záznamy** otevřete zobrazení protokoly v pracovním prostoru Log Analytics, kde můžete přizpůsobit časový rozsah a vyhledávací dotaz.
    
    ![otevřít v pracovním prostoru Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Kliknutím na **zobrazit řídicí panel** v horní části stránky **auditu** se otevře řídicí panel s informacemi o protokolech auditu, kde můžete přejít k podrobnostem o zabezpečení, získat přístup k citlivým datům a další informace. Tento řídicí panel je navržený tak, aby vám pomohla získat přehled o zabezpečení vašich dat.
    Můžete také přizpůsobit časový rozsah a vyhledávací dotaz. 
    ![Zobrazit Log Analytics řídicí panel](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Řídicí panel Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics přehledy zabezpečení](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Případně můžete také získat přístup k protokolům auditu z okna Log Analytics. Otevřete pracovní prostor Log Analytics a v části **Obecné** klikněte na **protokoly**. Můžete začít jednoduchým dotazem, jako je: *Search "SQLSecurityAuditEvents"* , chcete-li zobrazit protokoly auditu.
    Z tohoto místa můžete také použít [protokoly Azure monitor](../log-analytics/log-analytics-log-search.md) a spustit tak rozšířené vyhledávání dat v protokolu auditu. Protokoly Azure Monitor poskytují informace o provozu v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, které umožňují snadno analyzovat miliony záznamů napříč všemi vašimi úlohami a servery. Další užitečné informace o tom, jak hledat jazyk a příkazy protokolu Azure Monitor naleznete v tématu [Azure monitor v protokolech hledání](../log-analytics/log-analytics-log-search.md).

Pokud se rozhodnete zapisovat protokoly auditu do centra událostí:

- Aby bylo možné využívat protokoly auditu z centra událostí, budete muset nastavit datový proud, který bude zpracovávat události a zapsat je do cíle. Další informace najdete v [dokumentaci k Azure Event Hubs](../event-hubs/index.yml).
- Protokoly auditu v centru událostí jsou zachyceny v těle událostí [Apache Avro](https://avro.apache.org/) a uloženy pomocí formátu JSON s kódováním UTF-8. Protokoly auditu si můžete přečíst pomocí [nástrojů Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) nebo podobných nástrojů, které zpracovávají tento formát.

Pokud jste se rozhodli zapsat protokoly auditu do účtu služby Azure Storage, můžete k zobrazení protokolů použít několik metod:

- Protokoly auditu se agreguje v účtu, který jste zvolili během instalace. Protokoly auditu můžete prozkoumat pomocí nástroje, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com/). V Azure Storage se protokoly auditování ukládají jako kolekce souborů objektů BLOB v rámci kontejneru s názvem **sqldbauditlogs**. Další podrobnosti o hierarchii složek úložiště, konvencí pojmenování a formátu protokolu najdete v tématu [Formát protokolu auditu SQL Database](https://go.microsoft.com/fwlink/?linkid=829599).

- Použijte [Azure Portal](https://portal.azure.com).  Otevřete příslušnou databázi. V horní části stránky **auditování** databáze klikněte na **Zobrazit protokoly auditu**.

    ![Navigační podokno](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    Otevře se **záznamy auditu** , ze kterých budete moci zobrazit protokoly.

  - Konkrétní data můžete zobrazit kliknutím na **Filtr** v horní části stránky **audit Records** .
  - Můžete přepínat mezi záznamy auditu vytvořenými *zásadami auditu serveru* a *zásadami auditu databáze* přepnutím **zdroje auditu**.
  - Zaškrtnutím políčka **Zobrazit pouze záznamy auditu pro INJEKTÁŽE SQL** můžete zobrazit pouze záznamy auditu související s injektáže SQL.

       ![Navigační podokno]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Pomocí systémové funkce **Sys. fn_get_audit_file** (T-SQL) Vraťte data protokolu auditu v tabulkovém formátu. Další informace o použití této funkce naleznete v tématu [Sys. fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Použít **soubory pro sloučení auditu** v SQL Server Management Studio (počínaje SSMS 17):
    1. V nabídce SSMS vyberte **soubor** > **otevřít** > **Sloučit soubory auditu**.

        ![Navigační podokno](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. Otevře se dialogové okno **Přidat soubory auditu** . Vyberte jednu z možností **Přidání** a zvolte, jestli chcete soubory auditu sloučit z místního disku, nebo je naimportujte z Azure Storage. Je nutné zadat podrobnosti Azure Storage a klíč účtu.

    3. Po přidání všech souborů ke sloučení klikněte na **OK** , aby se operace sloučení dokončila.

    4. Sloučený soubor se otevře v SSMS, kde ho můžete zobrazit a analyzovat a také ho exportovat do souboru XEL nebo CSV nebo do tabulky.

- Použijte Power BI. Data protokolu auditu můžete zobrazit a analyzovat v Power BI. Další informace a přístup k šabloně ke stažení najdete v tématu [Analýza dat protokolu auditu v Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Stáhněte soubory protokolu z Azure Storage kontejneru objektů BLOB prostřednictvím portálu nebo pomocí nástroje, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/).
  - Po stažení souboru protokolu na místním počítači poklikejte na soubor pro otevření, zobrazení a analýzu protokolů v SSMS.
  - Najednou můžete také stáhnout více souborů prostřednictvím Průzkumník služby Azure Storage. Provedete to tak, že kliknete pravým tlačítkem na konkrétní podsložku a vyberete **Uložit jako** a uložíte ji do místní složky.

- Další metody:

  - Po stažení několika souborů nebo podsložky, která obsahuje soubory protokolu, je můžete sloučit lokálně, jak je popsáno v pokynech ke sloučení SSMS souborů auditu, které jsou popsané dříve.
  - Zobrazit protokoly auditování objektů BLOB programově:

    - [Dotazování na soubory rozšířených událostí](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) pomocí prostředí PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Produkční postupy

<!--The description in this section refers to preceding screen captures.-->

### <a name="auditing-geo-replicated-databases"></a>Auditování geograficky replikovaných databází

Když v případě geograficky replikovaných databází povolíte auditování primární databáze, bude mít sekundární databáze identické zásady auditování. U sekundární databáze je také možné nastavit auditování tím, že povolíte auditování na **sekundárním serveru**, nezávisle na primární databázi.

- Na úrovni serveru (**doporučeno**): zapnout auditování na **primárním serveru** i na **sekundárním serveru** – primární a sekundární databáze se každý z nich auditují nezávisle na příslušných zásadách na úrovni serveru.
- Úroveň databáze: auditování na úrovni databáze pro sekundární databáze lze nakonfigurovat pouze z nastavení auditování primární databáze.
  - Auditování je nutné povolit v *samotné primární databázi*, nikoli na serveru.
  - Po povolení auditování v primární databázi bude také zpřístupněna v sekundární databázi.

    >[!IMPORTANT]
    >V případě auditování na úrovni databáze budou nastavení úložiště pro sekundární databázi shodná s hodnotami primární databáze, což způsobuje meziregionální provoz. Doporučujeme povolit jenom auditování na úrovni serveru a ponechat auditování na úrovni databáze zakázané pro všechny databáze.

### <a name="storage-key-regeneration"></a>Obnovení klíče úložiště

V produkčním prostředí pravděpodobně pravidelně aktualizujete klíče úložiště. Při zápisu protokolů auditu do úložiště Azure je nutné při obnovování klíčů znovu uložit zásady auditování. Postup je následující:

1. Otevřete **Podrobnosti úložiště**. V poli **přístupový klíč k úložišti** vyberte **sekundární**a klikněte na **OK**. Pak v horní části stránky Konfigurace auditování klikněte na **Uložit** .

    ![Navigační podokno](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Přejděte na stránku konfigurace úložiště a znovu vygenerujte primární přístupový klíč.

    ![Navigační podokno](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Vraťte se na stránku Konfigurace auditování, přepněte přístupový klíč úložiště ze sekundární na primární a pak klikněte na **OK**. Pak v horní části stránky Konfigurace auditování klikněte na **Uložit** .
4. Vraťte se na stránku konfigurace úložiště a znovu vygenerujte sekundární přístupový klíč (při přípravě na další cyklus obnovení klíče).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Správa Azure SQL Server a auditování databáze

### <a name="using-azure-powershell"></a>Použití Azure Powershell

**Rutiny PowerShellu (včetně podpory klauzule WHERE pro další filtrování)**:

- [Vytvořit nebo aktualizovat zásady auditování databáze (set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Vytvořit nebo aktualizovat zásady auditování serveru (set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Získat zásady auditování databáze (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Získat zásady auditování serveru (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Odebrat zásady auditování databáze (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Odebrat zásady auditování serveru (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Příklad skriptu najdete v tématu [Konfigurace auditování a detekce hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

### <a name="using-rest-api"></a>Pomocí rozhraní REST API

**REST API**:

- [Vytvořit nebo aktualizovat zásady auditování databáze](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Vytvořit nebo aktualizovat zásady auditování serveru](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Získat zásady auditování databáze](/rest/api/sql/database%20auditing%20settings/get)
- [Získat zásady auditování serveru](/rest/api/sql/server%20auditing%20settings/get)

Rozšířené zásady s podporou klauzule WHERE pro další filtrování:

- [Vytvořit nebo aktualizovat *Rozšířené* zásady auditování databáze](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Vytvořit nebo aktualizovat *Rozšířené* zásady auditování serveru](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Získat *rozšířenou* zásadu auditování databáze](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Získat *Rozšířené* zásady auditování serveru](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

Auditování Azure SQL Database můžete spravovat pomocí [Azure Resource Manager](../azure-resource-manager/management/overview.md) šablon, jak je znázorněno v těchto příkladech:

- [Nasazení Azure SQL Server s povoleným auditováním pro zápis protokolů auditu do účtu Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Nasazení Azure SQL Server s povoleným auditováním pro zápis protokolů auditu do Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Nasazení Azure SQL Server s povoleným auditováním pro zápis protokolů auditu do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Připojené ukázky se nacházejí v externím veřejném úložišti a jsou poskytovány "tak, jak jsou", bez záruky a nejsou podporovány v rámci žádného programu nebo služby podpory společnosti Microsoft.
