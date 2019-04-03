---
title: Protokolování diagnostiky a metriky Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat databázi SQL Azure pro ukládání statistiky provádění prostředků využití a dotazu.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 785948c78b2b8205c4bebe2d68b62f6de7254d94
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863130"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database metrik a protokolování diagnostiky

Izolované databáze, databáze ve fondu v elastických fondech a instance databáze spravované instance může, streamování protokolů Diagnostika a metriky pro snazší monitorování výkonu. Databáze k přenosu využití prostředků, pracovních procesů a relace a připojení k jednomu z následujících prostředků Azure můžete nakonfigurovat:

- **Azure SQL Analytics**: Chcete-li získat inteligentního monitorování vašich databází Azure SQL, které zahrnuje sestavy o výkonu, upozornění a doporučení pro zmírnění dopadů.
- **Azure Event Hubs**: pro integraci telemetrických dat služby SQL Database s vlastními řešeními monitorování nebo aktivními kanály.
- **Azure Storage**: pro archivaci obrovských objemů telemetrických dat za zlomek ceny.

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

Další informace o kategoriích metrik a protokolů, podporuje různé služby Azure najdete v tématu:

- [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Přehled protokoly diagnostiky Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Tento článek obsahuje pokyny, které vám umožní povolit telemetrická data diagnostiky pro databáze, elastické fondy a spravované instance Azure SQL. Také může pomoct vás seznámí s postupy ke konfiguraci Azure SQL Analytics jako nástroj pro sledování pro zobrazování diagnostické telemetrie databáze.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Povolit protokolování diagnostiky telemetrii

Můžete povolit a spravovat protokolování telemetrická data diagnostiky a metriky pomocí jedné z následujících metod:

- portál Azure
- PowerShell
- Azure CLI
- Rozhraní REST API služby Azure Monitor
- Šablona Azure Resource Manageru

Když povolíte protokolování Diagnostika a metriky, je třeba zadat cílový prostředek Azure k shromažďování údajů o telemetrická data diagnostiky. Mezi dostupné možnosti patří:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Můžete zřídit nového prostředku Azure nebo si vybrat existující prostředek. Po výběru prostředku pomocí **nastavení diagnostiky** určete shromažďovaných údajů.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Podporované protokolování diagnostiky pro Azure SQL Database a instanci databáze

Povolení metrik a Diagnostika protokolování pro databáze SQL – nejsou ve výchozím nastavení povolená.

Můžete nastavit databází Azure SQL a instance databáze shromažďovat následující telemetrická data diagnostiky:

| Monitorování telemetrických dat pro databáze | Izolovaná databáze a podpora databáze ve fondu | Podpora databáze instance |
| :------------------- | ----- | ----- |
| [Všechny metriky](#all-metrics): Obsahuje procento využití DTU/procesoru, omezení jednotek DTU a procesoru, fyzických čtení dat procento, protokolu zapisovat procento, úspěšné/neúspěšné/blokovaná bránou připojení brány firewall, relace procento, procento pracovních procesů, úložiště, procento úložiště a XTP úložiště. | Ano | Ne |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Obsahuje informace o dotazu běhové statistiky, jako je například využití procesoru a statistiky doba trvání dotazu. | Ano | Ano |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Obsahuje informace o statistiky čekání dotazu (co vaše dotazy čekat) jako jsou procesor, protokol a UZAMČENÍ. | Ano | Ano |
| [Chyby](#errors-dataset): Obsahuje informace o chybách SQL v databázi. | Ano | Ano |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Obsahuje informace o tom, kolik času stráví databáze čeká čekání různých typů. | Ano | Ne |
| [Vypršení časových limitů](#time-outs-dataset): Obsahuje informace o vypršení časového limitu pro v databázi. | Ano | Ne |
| [Bloky](#blockings-dataset): Obsahuje informace o blokování události v databázi. | Ano | Ne |
| [Zablokování](#deadlocks-dataset): Obsahuje informace o událostech zablokování v databázi. | Ano | Ne |
| [AutomaticTuning](#automatic-tuning-dataset): Obsahuje informace o doporučení automatického ladění na databázi. | Ano | Ne |
| [SQLInsights](#intelligent-insights-dataset): Obsahuje užitečné přehledy o výkonu. Další informace najdete v tématu [Intelligent Insights](sql-database-intelligent-insights.md). | Ano | Ano |

> [!IMPORTANT]
> Elastické fondy a spravované instance mají svůj vlastní samostatný diagnostickou telemetrii z databází, které obsahují. To je důležité si uvědomit telemetrická data diagnostiky je nakonfigurované samostatně pro každý z těchto prostředků, jak je uvedeno níže.

> [!NOTE]
> Protokoly auditu zabezpečení a SQLSecurityAuditEvents není možné z databáze nastavení diagnostiky. Pokud chcete povolit streamování protokolů auditu, naleznete v tématu [nastavení auditování databáze](sql-database-auditing.md#subheading-2), a [auditování protokoly v protokolech Azure Monitor a Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).

## <a name="azure-portal"></a>portál Azure

Můžete použít **nastavení diagnostiky** nabídku pro každý jednotlivý ve fondu, nebo instanci databáze na webu Azure portal ke konfiguraci vysílání datového proudu telemetrická data diagnostiky. Kromě toho telemetrická data diagnostiky můžete také nakonfigurovat samostatně pro kontejnery database: elastické fondy a spravované instance. Můžete nastavit následující cíle pro streamování telemetrická data diagnostiky: Protokoly Azure Storage, Azure Event Hubs a Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurace datové proudy telemetrická data diagnostiky pro elastické fondy

   ![Ikona elastického fondu](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Můžete nastavit prostředek elastického fondu pro shromažďování následujících telemetrická data diagnostiky:

| Prostředek | Monitorování telemetrických dat |
| :------------------- | ------------------- |
| **Elastický fond** | [Všechny metriky](sql-database-metrics-diag-logging.md#all-metrics) obsahuje procento eDTU a využití procesoru, limit eDTU/procesoru, fyzických čtení dat procento, protokolu zapisovat, procento, procento relací, procento pracovních procesů, úložiště, procento úložiště, limit úložiště a XTP úložiště. |

Pokud chcete nakonfigurovat, vysílání datového proudu telemetrická data diagnostiky pro elastických fondů a databází v elastických fondech, budete muset nakonfigurovat samostatně **obě** z následujících akcí:

- Umožňoval vysílání datového proudu telemetrická data diagnostiky pro elastický fond, **a**
- Umožňoval vysílání datového proudu telemetrická data diagnostiky pro každou databázi v elastickém fondu

Je to proto elastický fond je kontejner databáze pomocí vlastní telemetrická data jsou oddělená od telemetrii jednotlivých databází.

Pokud chcete povolit streamování telemetrická data diagnostiky pro prostředek elastického fondu, postupujte podle těchto kroků:

1. Přejděte **elastického fondu** prostředku na webu Azure portal.
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení.

   ![Povolit diagnostiku pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Zadejte název nastavení pro vlastní referenci.
1. Vyberte cílový prostředek pro streamování dat diagnostiky: **Archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**.
1. Ke službě log analytics, vyberte **konfigurovat** a vytvořte nový pracovní prostor tak, že vyberete **+ vytvořit nový pracovní prostor**, nebo vyberte existující pracovní prostor.
1. Zaškrtněte políčko pro elastický fond diagnostickou telemetrii: **AllMetrics**.
   ![Konfigurovat diagnostiku pro elastické fondy](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Vyberte **Uložit**.
1. Kromě toho konfigurace vysílání datového proudu telemetrická data diagnostiky pro každou databázi v rámci elastického fondu, který chcete monitorovat pomocí kroků popsaných v další části.

> [!IMPORTANT]
> Kromě konfigurace telemetrická data diagnostiky pro elastický fond, můžete také nutné nakonfigurovat telemetrická data diagnostiky pro každou databázi v elastickém fondu, jak je uvedeno níže. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Konfigurace datové proudy telemetrická data diagnostiky pro izolovanou databázi nebo databáze v elastickém fondu

   ![Ikona SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Pokud chcete povolit streamování telemetrická data diagnostiky pro jeden, nebo součástí fondu, postupujte takto:

1. Přejděte na Azure **SQL database** prostředků.
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení.
   - Můžete vytvořit až tři paralelní připojení telemetrická data diagnostiky datového proudu.
   - Vyberte **+ přidat nastavení diagnostiky** konfigurace paralelní streamování dat diagnostiky k více prostředkům.

   ![Povolit diagnostiku pro jeden, ve fondu nebo instanci databáze](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Zadejte název nastavení pro vlastní referenci.
1. Vyberte cílový prostředek pro streamování dat diagnostiky: **Archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**.
1. Standard, založený na událostech monitorování prostředí vyberte následující políčka pro databáze telemetrii protokolů diagnostiky: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **chyby** , **DatabaseWaitStatistics**, **vypršení časových limitů**, **bloky**, a **zablokování**.
1. Monitorování prostředí Upřesnit, na základě jednu minutu, zaškrtněte políčko pro **AllMetrics**.
   ![Konfigurovat diagnostiku pro jeden, ve fondu nebo instanci databáze](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Vyberte **Uložit**.
1. Tento postup opakujte pro každou databázi, kterou chcete monitorovat.

> [!NOTE]
> Protokoly auditu zabezpečení a SQLSecurityAuditEvents není možné z databáze nastavení diagnostiky. Pokud chcete povolit streamování protokolů auditu, naleznete v tématu [nastavení auditování databáze](sql-database-auditing.md#subheading-2), a [auditování protokoly v protokolech Azure Monitor a Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).
> [!TIP]
> Tento postup opakujte pro každý Azure SQL Database, kterou chcete monitorovat.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Konfigurace datové proudy telemetrická data diagnostiky pro spravované instance

   ![Ikona spravované instance](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Můžete nastavit shromažďovat následující telemetrická data diagnostiky prostředků spravovanou instanci:

| Prostředek | Monitorování telemetrických dat |
| :------------------- | ------------------- |
| **Spravovaná instance** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) obsahuje počet virtuálních jader, průměrné procento využití procesoru, vstupně-výstupní požadavky, bajtů načtených/zapsaných, vyhrazený úložný prostor a využitého prostoru úložiště. |

Pokud chcete nakonfigurovat, vysílání datového proudu telemetrická data diagnostiky pro spravovanou instanci a instanci databáze, budete muset nakonfigurovat samostatně **obě** z následujících akcí:

- Umožňoval vysílání datového proudu telemetrická data diagnostiky pro spravovanou instanci **a**
- Umožňoval vysílání datového proudu telemetrická data diagnostiky pro každou instanci databáze

Je to proto managed instance je kontejner databáze pomocí vlastní telemetrie samostatného z telemetrických dat jednotlivé instance databáze.

Pokud chcete povolit streamování telemetrická data diagnostiky pro prostředek spravované instance, postupujte podle těchto kroků:

1. Přejděte **spravovanou instanci** prostředku na webu Azure portal.
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení.

   ![Povolit diagnostiku pro spravovanou instanci](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Zadejte název nastavení pro vlastní referenci.
1. Vyberte cílový prostředek pro streamování dat diagnostiky: **Archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**.
1. Ke službě log analytics, vyberte **konfigurovat** a vytvořte nový pracovní prostor tak, že vyberete **+ vytvořit nový pracovní prostor**, nebo použijte existující pracovní prostor.
1. Zaškrtněte políčko pro instanci telemetrická data diagnostiky: **ResourceUsageStats**.
   ![Konfigurovat diagnostiku pro spravovanou instanci](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Vyberte **Uložit**.
1. Kromě toho konfigurace vysílání datového proudu telemetrická data diagnostiky pro každou instanci databáze ve spravované instanci, kterou chcete monitorovat pomocí kroků popsaných v další části.

> [!IMPORTANT]
> Kromě konfigurace telemetrická data diagnostiky pro spravovanou instanci, také musíte nakonfigurovat telemetrická data diagnostiky pro každou databázi instance, jak je uvedeno níže. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Konfigurace datové proudy diagnostickou telemetrii pro instanci databáze

   ![Instance databáze spravované instance ikona](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Pokud chcete povolit streamování diagnostickou telemetrii pro instanci databáze, postupujte takto:

1. Přejděte na **instance databáze** prostředků v rámci spravované instance.
1. Vyberte **nastavení diagnostiky**.
1. Vyberte **zapnout diagnostiku** Pokud neexistují žádné předchozí nastavení, nebo vyberte **upravit nastavení** upravit předchozí nastavení.
   - Můžete vytvořit až tři (3) paralelní připojení telemetrická data diagnostiky datového proudu.
   - Vyberte **+ přidat nastavení diagnostiky** konfigurace paralelní streamování dat diagnostiky k více prostředkům.

   ![Povolit diagnostiku pro instanci databáze](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Zadejte název nastavení pro vlastní referenci.
1. Vyberte cílový prostředek pro streamování dat diagnostiky: **Archivovat do účtu úložiště**, **Stream do centra událostí**, nebo **odesílat do Log Analytics**.
1. Zaškrtněte políčka pro databáze diagnostickou telemetrii: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** a **chyby**.
   ![Konfigurovat diagnostiku pro instanci databáze](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Vyberte **Uložit**.
1. Tento postup opakujte pro každou instanci databáze, kterou chcete monitorovat.

> [!TIP]
> Tento postup opakujte pro každou instanci databáze, kterou chcete monitorovat.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

Metriky a protokolování diagnostiky můžete povolit pomocí Powershellu.

- Pokud chcete povolit úložiště pro diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku pro cílový účet úložiště.

- Pokud chcete povolit streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Povolení odesílání protokolů diagnostiky k pracovnímu prostoru Log Analytics, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="to-configure-multiple-azure-resources"></a>Konfigurace několika prostředků Azure

Pro podporu více předplatných, pomocí skriptu prostředí PowerShell z [protokolování metrik prostředku povolit Azure pomocí Powershellu](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Zadat ID prostředku pracovního prostoru \<$WSID\> jako parametr při spuštění skriptu `Enable-AzureRMDiagnostics.ps1` k odesílání diagnostických dat z více zdrojů do pracovního prostoru.

- Chcete-li získat ID pracovního prostoru \<$WSID\> cíle pro diagnostická data, pomocí následujícího skriptu:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Nahraďte \<subID\> ID předplatného, \<RG_NAME\> s názvem skupiny prostředků a \<WS_NAME\> s název pracovního prostoru.

### <a name="azure-cli"></a>Azure CLI

Metriky a protokolování diagnostiky můžete povolit pomocí rozhraní příkazového řádku Azure.

> [!NOTE]
> Skripty pro povolení protokolování diagnostiky se podporují pro Azure CLI 1.0. Všimněte si, že se verze CLI 2.0 v tuto chvíli nepodporuje.

- Pokud chcete povolit úložiště diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ID účtu úložiště je ID prostředku pro cílový účet úložiště.

- Pokud chcete povolit streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   ID pravidla služby Service Bus je řetězec v tomto formátu:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pokud chcete povolit odesílání protokolů diagnostiky pro pracovní prostor Log Analytics, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="rest-api"></a>REST API

Přečtěte si informace o tom, jak [změnit nastavení diagnostiky pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Přečtěte si informace o tom, jak [povolení nastavení diagnostiky při vytváření prostředků pomocí šablony Resource Manageru](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream do Azure SQL Analytics

Azure SQL Analytics je cloudové řešení, která monitoruje výkon databáze, elastické fondy a spravované instance ve velkém měřítku a napříč několika předplatnými Azure SQL. Pomáhá shromažďovat a vizualizovat metriky výkonu databáze SQL Azure a má vestavěné inteligentní algoritmy pro řešení potíží s výkonem.

![Přehled služby Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Metriky a diagnostické protokoly SQL Database můžete streamovali do služby Azure SQL Analytics s využitím integrovaného **odesílat do Log Analytics** možnosti na kartě nastavení diagnostiky na portálu. Log analytics můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin Powershellu, rozhraní příkazového řádku Azure nebo REST API služby Azure Monitor.

### <a name="installation-overview"></a>Přehled instalace

Můžete monitorovat fleet SQL Database pomocí služby Azure SQL Analytics. Proveďte následující kroky:

1. Vytvoření Azure SQL Analytics řešení z Azure Marketplace.
2. Vytvoření pracovního prostoru monitorování v řešení.
3. Konfigurovat databáze datového proudu diagnostickou telemetrii do pracovního prostoru.

Pokud používáte elastických fondů nebo spravované instance, musíte také nakonfigurovat telemetrická data diagnostiky Streamovat z těchto prostředků.

### <a name="create-azure-sql-analytics-resource"></a>Vytvořit prostředek služby Azure SQL Analytics

1. Azure SQL Analytics v Azure Marketplace vyhledejte a vyberte ji.

   ![Vyhledávání pro Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Vyberte **vytvořit** na obrazovce Přehled řešení.

3. Vyplňte formulář Azure SQL Analytics s dodatečnými informacemi, které je nutné: název pracovního prostoru, předplatné, skupinu prostředků, umístění a cenovou úroveň.

   ![Konfigurovat Azure SQL Analytics na portálu](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Vyberte **OK** potvrďte a pak vyberte **vytvořit**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurovat databáze záznamu metriky a diagnostické protokoly

Nejjednodušší způsob, jak nakonfigurovat, kde záznam metriky databáze pomocí webu Azure portal. Jak již bylo popsáno dříve, přejděte na váš prostředek databáze SQL v Azure portal a vyberte **nastavení diagnostiky**.

Pokud používáte elastických fondů nebo spravované instance, musíte také nakonfigurovat nastavení diagnostiky na těchto prostředcích, které umožňují telemetrická data diagnostiky ke streamování do pracovního prostoru.

### <a name="use-the-sql-analytics-solution"></a>Použití řešení SQL Analytics

SQL Analytics jako hierarchické řídicí panel můžete použít k zobrazení prostředků vaší databáze SQL. Další informace o použití řešení SQL Analytics, najdete v článku [monitorování SQL Database s použitím řešení SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Metriky a diagnostické protokoly SQL Database můžete Streamovat do služby Event Hubs pomocí integrované **Stream do centra událostí** možnost na webu Azure Portal. ID pravidla služby Service Bus můžete povolit také pomocí nastavení diagnostiky prostřednictvím rutin Powershellu, rozhraní příkazového řádku Azure nebo REST API služby Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co dělat s Diagnostika a metriky protokolů ve službě Event Hubs

Po streamuje vybraná data do služby Event Hubs, jste zase o krok blíž k povolení pokročilých scénářích monitorování. Služba Event Hubs slouží jako branou pro kanál události. Po shromáždění dat do centra událostí můžete transformovat a uložit pomocí poskytovatele analýz v reálném čase nebo adaptérů úložiště. Event Hubs oddělí vytvoření proudu událostí od spotřeby těchto události. Tímto způsobem dostanete příjemci událostí události podle svého vlastního plánu. Další informace o Event Hubs najdete v tématu:

- [Co jsou Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Streamovaná metriky můžete použít ve službě Event Hubs do:

- **Zobrazení stavu služby podle streamovaných dat horká cesta k Power BI**. Pomocí služby Event Hubs, Stream Analytics a Power BI můžete snadno transformovat data metrik a diagnostických nástrojů do téměř v reálném čase na služby Azure. Přehled o tom, jak vytvořit Centrum událostí, zpracování dat pomocí Stream Analytics a pomocí Power BI jako výstup, naleznete v tématu [Stream Analytics a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Stream protokolů do datových proudů telemetrie a protokolování třetích stran**. Pomocí služby Event Hubs streamování můžete získat metriky a diagnostické protokoly do různých výrobců sledování a protokolů analytická řešení.

- **Vytvářejte vlastní telemetrii a protokolování platformy**. Proveďte už máte platformu vlastními silami sestavených telemetrická data nebo zvažuje vytvoříte? Vysoce škálovatelné publikování a odběru povaha služby Event Hubs umožňuje flexibilně ingestovat diagnostické protokoly. Zobrazit [Dan Rosanova návod k použití služby Event Hubs v globálním měřítku telemetrie platformě](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream do služby Storage

Můžete ukládat metriky databáze SQL a diagnostické protokoly v Azure Storage s použitím integrovaného **archivovat do účtu úložiště** možnost na webu Azure Portal. Úložiště můžete také povolit pomocí nastavení diagnostiky prostřednictvím rutin Powershellu, rozhraní příkazového řádku Azure nebo REST API služby Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schéma metriky a diagnostické protokoly v účtu úložiště

Po nastavení metriky a diagnostické protokoly kolekce se vytvoří kontejner úložiště v účtu úložiště, které jste vybrali při první řádky dat, jsou k dispozici. Struktura objektů blob je:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Nebo jednoduše:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například může být název objektu blob pro všechny metriky:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Název objektu blob pro ukládání dat z elastického fondu vypadá takto:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Stažení metrik a protokolů ze služby Storage

Zjistěte, jak [stažení Diagnostika a metriky protokolů ze služby Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Zásady uchovávání dat a ceny

Pokud vyberete služby Event Hubs nebo účet úložiště, můžete zadat zásady uchovávání informací. Tato zásada odstraňuje data, která je starší než vybrané časové období. Pokud chcete zadat Log Analytics, zásady uchovávání informací, závisí na vybrané cenové úrovně. Zadaná volných jednotek příjem dat v takovém případě můžete povolit bezplatné sledování několik databází každý měsíc. Jakékoli využití telemetrická data diagnostiky nad rámec volných jednotek může způsobit náklady. Mějte na paměti, že aktivní databáze s větším zatížení ingestování více dat než nečinné databáze. Další informace najdete v tématu [ceny Log analytics](https://azure.microsoft.com/pricing/details/monitor/).

Pokud používáte Azure SQL Analytics, můžete monitorovat spotřebu příjem dat v řešení tak, že vyberete **pracovní prostor OMS** v navigační nabídce Azure SQL Analytics, a pak vyberete **využití** a **odhadované náklady**.

## <a name="metrics-and-logs-available"></a>Metriky a protokoly, které jsou k dispozici

Monitorování telemetrických dat, které jsou k dispozici pro službu Azure SQL Database, elastických fondů a spravovaná instance je uvedeno níže. Shromážděné monitorování telemetrických dat do SQL Analytics je možné pro své vlastní analýzy a vývoj aplikace s využitím [dotazů na protokoly Azure monitoru](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) jazyka.

## <a name="all-metrics"></a>Všechny metriky

Najdete v následujících tabulkách podrobnosti o všech metrik podle prostředků.

### <a name="all-metrics-for-elastic-pools"></a>Všechny metriky pro elastické fondy

|**Prostředek**|**Metriky**|
|---|---|
|Elastický fond|Procento eDTU eDTU používá, omezení eDTU, procentuální využití procesoru, procentuální čtení fyzické, protokolu zapisovat, procento, procento relací, procento pracovních procesů, úložiště, procento úložiště, limit úložiště, XTP procento úložiště |

### <a name="all-metrics-for-azure-sql-databases"></a>Všechny metriky pro Azure SQL Database.

|**Prostředek**|**Metriky**|
|---|---|
|Databáze SQL Azure|Procento DTU DTU použít, omezení jednotek DTU, procentuální využití procesoru, procento fyzických datových čtení, zápisu protokolu procento, úspěšné nebo neúspěšné/zablokovaný připojení brány firewall, procento relací, procento pracovních procesů, úložiště, procento úložiště, procento XTP úložiště, a zablokování |

## <a name="all-logs"></a>Všechny protokoly

V následujících tabulkách jsou uvedeny podrobnosti telemetrie dostupné pro všechny protokoly. Podrobnosti najdete na [nepodporuje protokolování diagnostiky](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) pochopit, které protokoly jsou podporovány pro konkrétní databázi flavor – jeden, Azure SQL ve fondu, nebo instanci databáze.

### <a name="resource-usage-stats-for-managed-instance"></a>Statistika využití prostředků pro spravovanou instanci

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure|
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: ResourceUsageStats |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: MANAGEDINSTANCES |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název spravované instance |
|ResourceId|Identifikátor URI prostředku |
|SKU_s|Skladová jednotka produktu spravované instance |
|virtual_core_count_s|Počet virtuálních jader, které jsou k dispozici |
|avg_cpu_percent_s|Průměrné procento CPU |
|reserved_storage_mb_s|Kapacita vyhrazeného úložiště spravované instance |
|storage_space_used_mb_s|Využité úložiště spravované instance |
|io_requests_s|Počet vstupně-výstupních operací |
|io_bytes_read_s|Přečtené bajty vstupně-výstupních operací |
|io_bytes_written_s|Zapsané bajty vstupně-výstupních operací |

### <a name="query-store-runtime-statistics"></a>Statistické údaje o Query Store

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: QueryStoreRuntimeStatistics |
|OperationName|Název operace Vždy: QueryStoreRuntimeStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|query_hash_s|Hodnota hash dotazu |
|query_plan_hash_s|Hodnota hash dotazu plán |
|statement_sql_handle_s|Popisovač sql příkazu |
|interval_start_time_d|Spustit datetimeoffset intervalu v počtu taktů z 1900-1-1 |
|interval_end_time_d|End datetimeoffset intervalu v počtu taktů z 1900-1-1 |
|logical_io_writes_d|Celkový počet logických vstupně-výstupní operace zápisu |
|max_logical_io_writes_d|Maximální počet logických vstupně-výstupní operace zapíše za spuštění |
|physical_io_reads_d|Celkový počet fyzických vstupně-výstupní operace čtení |
|max_physical_io_reads_d|Maximální počet logických vstupně-výstupní operace čtení za spuštění |
|logical_io_reads_d|Celkový počet logických čtení vstupně-výstupních operací |
|max_logical_io_reads_d|Maximální počet logických vstupně-výstupní operace čtení za spuštění |
|execution_type_d|Typ spuštění |
|count_executions_d|Počet spuštění dotazu |
|cpu_time_d|Celkový čas procesoru používané dotazu v mikrosekundách |
|max_cpu_time_d|Maximální počet procesorů čas spotřebitelem jedno provedení v mikrosekundách |
|dop_d|Součet stupeň paralelismu |
|max_dop_d|Maximální míra paralelismu používá pro jedno provedení |
|rowcount_d|Celkový počet vrácených řádků |
|max_rowcount_d|Maximální počet řádků vrácených v jedno provedení |
|query_max_used_memory_d|Celkové množství paměti používané KB |
|max_query_max_used_memory_d|Maximální množství paměti používané jedno provedení v článku KB |
|duration_d|Celková doba spuštění v mikrosekundách |
|max_duration_d|Maximální doba spuštění jednoho spuštění |
|num_physical_io_reads_d|Celkový počet fyzických čtení |
|max_num_physical_io_reads_d|Maximální počet fyzických čtení za spuštění |
|log_bytes_used_d|Celkové množství bajtů protokolu |
|max_log_bytes_used_d|Maximální počet bajtů protokolu použitou na spuštění |
|query_id_d|ID dotazu v dotazu Store |
|plan_id_d|ID plánu v Query Store |

Další informace o [Query Store runtime statistická data](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statistiky čekání Query Store

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: QueryStoreWaitStatistics |
|OperationName|Název operace Vždy: QueryStoreWaitStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|wait_category_s|Kategorie čekání |
|is_parameterizable_s|Dotaz je parametrizovat |
|statement_type_s|Typ příkazu |
|statement_key_hash_s|Hodnota hash klíče – příkaz |
|exec_type_d|Typ spuštění |
|total_query_wait_time_ms_d|Celkový čas čekání dotazu na kategorie konkrétní čekání |
|max_query_wait_time_ms_d|Maximální doba čekání dotazu v jednotlivých spuštění na konkrétní čekání kategorie |
|query_param_type_d|0 |
|query_hash_s|Hodnota hash dotazu v dotazu Store |
|query_plan_hash_s|Hodnota hash plánu dotazu v dotazu Store |
|statement_sql_handle_s|Popisovač příkazu v Query Store |
|interval_start_time_d|Spustit datetimeoffset intervalu v počtu taktů z 1900-1-1 |
|interval_end_time_d|End datetimeoffset intervalu v počtu taktů z 1900-1-1 |
|count_executions_d|Počet spuštění dotazu |
|query_id_d|ID dotazu v dotazu Store |
|plan_id_d|ID plánu v Query Store |

Další informace o [Query Store počkejte statistická data](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Chyby datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQ |
|Kategorie|Název kategorie. Vždy: Chyby |
|OperationName|Název operace Vždy: ErrorEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|Zpráva|Chybovou zprávu ve formátu prostého textu |
|user_defined_b|Je ta pasáž, uživatelem definované chyba |
|error_number_d|Kód chyby |
|Severity|Závažnost chyby |
|state_d|Stav chyby |
|query_hash_s|Hodnota hash dotazu se nezdařilo dotazu, pokud je k dispozici |
|query_plan_hash_s|Hodnota hash dotazu plán dotazu se nezdařilo, pokud je k dispozici |

Další informace o [chybových zpráv systému SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Datová sada statistiky čekání databáze

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: DatabaseWaitStatistics |
|OperationName|Název operace Vždy: DatabaseWaitStatisticsEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|wait_type_s|Název typu čekání |
|start_utc_date_t [UTC]|Měří počáteční čas období |
|end_utc_date_t [UTC]|Měří období koncový čas |
|delta_max_wait_time_ms_d|Čekalo se maximální doba spuštění |
|delta_signal_wait_time_ms_d|Signály celková doba čekání |
|delta_wait_time_ms_d|Celkový čas čekání v období |
|delta_waiting_tasks_count_d|Čekání úlohy |

Další informace o [databáze statistiky čekání](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Datová sada časové limity

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: Časové limity |
|OperationName|Název operace Vždy: TimeoutEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|error_state_d|Kód chyby stavu: |
|query_hash_s|Dotazování hash, pokud je k dispozici |
|query_plan_hash_s|Dotazování hash plánu, pokud je k dispozici |

### <a name="blockings-dataset"></a>Blokování datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: bloky |
|OperationName|Název operace Vždy: BlockEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|lock_mode_s|Režim zámku používán dotazem |
|resource_owner_type_s|Vlastník zámku |
|blocked_process_filtered_s|Zablokuje sestava procesu XML |
|duration_d|Doba trvání uzamčení v mikrosekundách |

### <a name="deadlocks-dataset"></a>Zablokování datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC] |Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: Zablokování |
|OperationName|Název operace Vždy: DeadlockEvent |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|deadlock_xml_s|Vzájemné zablokování sestavu XML |

### <a name="automatic-tuning-dataset"></a>Automatické vyladění datové sady

|Vlastnost|Popis|
|---|---|
|TenantId|Vaše ID tenanta |
|SourceSystem|Vždy: Azure |
|TimeGenerated [UTC]|Časové razítko, kdy se přihlášení v protokolu |
|Type|Vždy: AzureDiagnostics |
|ResourceProvider|Název poskytovatele prostředků. Vždy: MICROSOFT.SQL |
|Kategorie|Název kategorie. Vždy: AutomaticTuning |
|Prostředek|Název prostředku |
|ResourceType|Název typu prostředku. Vždy: SERVERY PRO/DATABÁZE |
|SubscriptionId|GUID předplatného pro databázi |
|ResourceGroup|Název skupiny prostředků pro databázi |
|LogicalServerName_s|Název serveru pro databázi |
|LogicalDatabaseName_s|Název databáze |
|ElasticPoolName_s|Název elastického fondu pro databázi, pokud existuje |
|DatabaseName_s|Název databáze |
|ResourceId|Identifikátor URI prostředku |
|RecommendationHash_s|Jedinečnou hodnotu hash doporučení automatického ladění |
|OptionName_s|Operace automatického ladění |
|Schema_s|Schéma databáze |
|Table_s|Tabulka měla vliv na |
|IndexName_s|Název indexu |
|IndexColumns_s|Název sloupce |
|IncludedColumns_s|Zahrnuté sloupce |
|EstimatedImpact_s|Odhadovaný dopad doporučení automatického ladění JSON |
|Event_s|Typ události automatického ladění |
|Timestamp_t|Časové razítko poslední aktualizace |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights datové sady

Další informace o [formát protokolu Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak povolit protokolování a pochopit, metriky a protokolování kategorie podporuje různé služby Azure, najdete v tématech:

- [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Přehled protokoly diagnostiky Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Další informace o službě Event Hubs, přečtěte si:

- [Co je služba Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Další informace o službě Azure Storage najdete v tématu [stahování metriky a diagnostické protokoly z úložiště](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
