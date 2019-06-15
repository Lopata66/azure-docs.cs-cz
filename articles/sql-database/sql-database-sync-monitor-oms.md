---
title: Monitorování synchronizace dat SQL Azure s využitím protokolů Azure Monitor | Dokumentace Microsoftu
description: Další informace o monitorování synchronizace dat SQL Azure s použitím protokoly Azure monitoru
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 6e94aac47ce5b45e700e2413d2e86d5f36596348
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614959"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorování synchronizace dat SQL s protokoly Azure monitoru 

Synchronizace dat SQL v protokolu aktivit a detekovat chyby a upozornění, dříve museli jste ruční kontrola synchronizace dat SQL na webu Azure Portal nebo pomocí Powershellu nebo rozhraní REST API. Postupujte podle kroků v tomto článku můžete nakonfigurovat vlastní řešení, která zvyšuje synchronizace dat možností monitorování. Toto řešení, aby vyhovovala vašemu scénáři můžete přizpůsobit.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data synchronizace provádí vložení změn **není** v tuto chvíli podporován Azure SQL Database Managed Instance.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Řídicí panel monitorování pro všechny skupiny synchronizace 

Už nemusíte hledat prostřednictvím protokolů všech skupin synchronizace jednotlivě k vyhledání problémů. Pomocí vlastních zobrazení Azure Monitor můžete sledovat všechny skupiny synchronizace z některého z vašich předplatných na jednom místě. Toto zobrazení poskytuje informace, které je důležité informace pro zákazníky používající synchronizaci dat SQL.

![Řídicí panel monitorování synchronizace dat](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Automatizovaných e-mailových oznámení

Už nemusíte protokolu ručně na webu Azure portal nebo pomocí Powershellu nebo rozhraní REST API. S [protokoly Azure monitoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), můžete vytvářet upozornění, které přejít přímo na e-mailové adresy lidí, třeba zobrazit, když dojde k chybě.

![Data synchronizace e-mailových oznámení](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Jak můžete nastavit tyto funkce monitorování? 

Implementace vlastního monitorování Azure protokolech řešení monitorování synchronizace dat SQL za kratší dobu než hodinu provedením následujících akcí:

Musíte nakonfigurovat tři komponenty:

-   Powershellový runbook ke kanálu dat protokolu synchronizace dat SQL na protokoly Azure monitoru.

-   Azure Monitor upozornění e-mailových oznámení.

-   Zobrazení monitorování Azure pro monitorování.

### <a name="samples-to-download"></a>Ukázky ke stažení

Stáhněte si následující dvě ukázky:

-   [Synchronizovat data protokolu Powershellového Runbooku](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení Azure monitorování synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste nastavili následující věci:

-   Účet Azure Automation

-   Pracovní prostor Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Powershellový Runbook se získat protokol synchronizace dat SQL 

Pomocí Powershellového runbooku hostovaná ve službě Azure Automation můžete načítat data protokolů synchronizace dat SQL a odeslat protokoly Azure monitoru. Ukázkový skript je v ceně. Předpokladem je musíte mít účet Azure Automation. Je nutné k vytvoření sady runbook a naplánovat jeho spuštění. 

### <a name="create-a-runbook"></a>Vytvoření runbooku

Další informace o vytvoření sady runbook najdete v tématu [Můj první Powershellový runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  V rámci vašeho účtu Azure Automation, vyberte **sady Runbook** kartě automatizace procesů.

2.  Vyberte **přidat Runbook** v levém horním rohu stránky sady Runbook.

3.  Vyberte **importovat existující Runbook**.

4.  V části **soubor sady Runbook**, použijte daný `DataSyncLogPowerShellRunbook` souboru. Nastavte **typ Runbooku** jako `PowerShell`. Dejte runbooku název.

5.  Vyberte **Vytvořit**. Nyní máte sadu runbook.

6.  V rámci vašeho účtu Azure Automation, vyberte **proměnné** kartu v části sdílené prostředky.

7.  Vyberte **přidat proměnnou** na proměnné stránky. Vytvořte proměnnou pro uložení posledního času spuštění sady runbook. Pokud máte více sad runbook, musíte pro každou sadu runbook jednu proměnnou.

8.  Nastavte název proměnné jako `DataSyncLogLastUpdatedTime` a nastavte její typ jako datový typ DateTime.

9.  Vyberte sadu runbook a klikněte na tlačítko Upravit v horní části stránky.

10. Změny vyžadované pro váš účet a konfigurace synchronizace dat SQL. (Podrobnější informace najdete v tématu ukázkového skriptu.)

    1.  Informace o Azure.

    2.  Informace o skupině synchronizace.

    3.  Azure Monitor protokoluje informace. Tyto informace najdete na webu Azure Portal | Nastavení | Připojené zdroje. Další informace o odesílání dat do Azure monitoru protokolů najdete v tématu [odesílání dat do Azure monitoru protokolů pomocí rozhraní API kolekce dat HTTP (preview)](../azure-monitor/platform/data-collector-api.md).

11. Spustíte sadu runbook v testovací podokno. Zkontrolujte, ujistěte se, že byla úspěšná.

    Pokud máte chyby, ujistěte se, že máte nejnovější modul Powershellu nainstalovaný. Můžete nainstalovat nejnovější modul Powershellu ve **Galerie modulů** ve vašem účtu Automation.

12. Klikněte na tlačítko **publikování**

### <a name="schedule-the-runbook"></a>Naplánování runbooku

Naplánování runbooku:

1.  V rámci sady runbook, vyberte **plány** na kartě prostředky.

2.  Vyberte **přidat plán** na stránce plány.

3.  Vyberte **připojení plánu k runbooku**.

4.  Vyberte **vytvořit nový plán.**

5.  Nastavte **opakování** periodický a nastavte interval chcete. Ve skriptu a protokoly Azure monitoru, použijte zde stejný interval.

6.  Vyberte **Vytvořit**.

### <a name="check-the-automation"></a>Zkontrolujte automatizace

Ke sledování, zda automatizace běží podle očekávání, v části **přehled** u vašeho účtu automation, vyhledejte **statistiky úlohy** zobrazení v části **monitorování**. Připnete na řídicí panel pro snadné prohlížení tohoto zobrazení. Úspěšná spuštění sady runbook zobrazit jako "Dokončeno" a neúspěšné spuštění zobrazí jako "Se nezdařilo."

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Vytvoření výstrahy Azure Monitor čtečky e-mailových oznámení

Pokud chcete vytvořit upozornění, která používá protokoly Azure monitoru, proveďte následující akce. Předpokladem je budete muset mít protokoly Azure monitoru propojený s pracovním prostorem Log Analytics.

1.  Na webu Azure Portal, vyberte **prohledávání protokolů**.

2.  Vytvořte dotaz pro výběr chyby a upozornění ve skupině synchronizace v rámci intervalu, který jste vybrali. Příklad:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Po spuštění dotazu, vyberte na zvonek, že **výstrah**.

4.  V části **generovat výstrahu na základě**vyberte **měření metriky**.

    1.  Nastavte na agregovanou hodnotu **větší než**.

    2.  Po **větší než**, zadejte mezní hodnotu uplynout, než budete dostávat oznámení. V synchronizaci dat se očekává přechodné chyby. Jak snížit šum, nastavena na 5 prahovou hodnotu.

5.  V části **akce**, nastavte **e-mailová oznámení** na "Ano". Zadejte požadované e-mailové příjemce.

6.  Klikněte na **Uložit**. Zadaným příjemcům nyní přijímat e-mailová oznámení, když dojde k chybám.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Vytvoření zobrazení Azure Monitor pro monitorování

Tento krok vytvoří zobrazení Azure Monitor vizuálně monitorovat všechny zadané synchronizace skupiny. Zobrazení obsahuje několik komponent:

-   Dlaždice přehledu, který ukazuje, kolik chyb, úspěchy a upozornění mají všechny skupiny synchronizace.

-   Dlaždice pro všechny skupiny synchronizace, který zobrazuje počet chyb a upozornění na skupinu synchronizace. Skupiny bez problémů se nezobrazují na tuto dlaždici.

-   Dlaždice pro každou skupinu synchronizace, který zobrazuje počet chyb, úspěchy a upozornění a poslední chybové zprávy.

Konfigurace zobrazení monitorování Azure, proveďte následující akce:

1.  Na domovské stránce pracovního prostoru Log Analytics vyberte plus na levé straně otevřete **Návrhář zobrazení**.

2.  Vyberte **Import** na horním panelu Návrhář zobrazení. Potom vyberte ukázkový soubor "DataSyncLogOMSView".

3.  Ukázkové zobrazení je pro správu dvě skupiny synchronizace. Upravte toto zobrazení tak, aby vyhovovala vašemu scénáři. Klikněte na tlačítko **upravit** a proveďte následující změny:

    1.  Vytváření nových objektů "Prstenec a seznam" v galerii podle potřeby.

    2.  Na každé dlaždici aktualizujte dotazy s informacemi o.

        1.  Na každé dlaždici změňte interval TimeStamp_t podle potřeby.

        2.  U dlaždic pro každou skupinu synchronizace aktualizuje skupinu synchronizace názvy.

    3.  Na každé dlaždici aktualizujte název, podle potřeby.

4.  Klikněte na tlačítko **Uložit** a zobrazení je připravený.

## <a name="cost-of-this-solution"></a>Náklady na toto řešení

Ve většině případů toto řešení je zdarma.

**Azure Automation:** Můžou existovat náklady pomocí účtu Azure Automation, v závislosti na využití. Prvních 500 minut úlohy spuštění za měsíc je zdarma. Ve většině případů toto řešení by měl používat méně než 500 minut za měsíc. Chcete-li nemuseli platit případné poplatky, naplánujte runbook, aby se spouštěla v intervalu nejméně dvě hodiny. Další informace najdete v tématu [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

**Protokoly Azure monitoru:** Můžou existovat náklady spojené s protokoly Azure monitoru v závislosti na využití. Bezplatná úroveň zahrnuje 500 MB přijatých dat za den. Ve většině případů toto řešení má ingestování menší než 500 MB za den. Ke snížení využití, použijte jen selhání filtrování zahrnuty v sadě runbook. Pokud používáte více než 500 MB za den, upgradujte na placenou úroveň, aby nedošlo k ohrožení analytics zastaví, když je dosaženo omezení. Další informace najdete v tématu [protokoly Azure monitoru ceny](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Ukázky kódů

Stáhněte si ukázky kódu, které jsou popsané v tomto článku v následujících umístěních:

-   [Synchronizovat data protokolu Powershellového Runbooku](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Zobrazení Azure monitorování synchronizace dat](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Další postup
Další informace o Synchronizaci dat SQL:

-   Přehled – [synchronizaci dat napříč několika cloudu a místními databázemi pomocí synchronizace dat SQL Azure](sql-database-sync-data.md)
-   Nastavení synchronizace dat
    - Na portálu – [kurzu: Nastavení synchronizace dat SQL, synchronizaci dat mezi Azure SQL Database a SQL Server v místním](sql-database-get-started-sql-data-sync.md)
    - S využitím PowerShellu
        -  [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent – synchronizace dat [Data synchronizovat Agent pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
-   Osvědčené postupy – [osvědčené postupy pro synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)
-   Řešení potíží – [řešení potíží se synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)
-   Aktualizovat schéma synchronizace
    -   Pomocí příkazů jazyka Transact-SQL - [automatizace replikace změn schématu synchronizace dat SQL Azure](sql-database-update-sync-schema.md)
    -   Pomocí Powershellu – [aktualizovat schéma synchronizace ve stávající skupině synchronizace pomocí Powershellu](scripts/sql-database-sync-update-schema.md)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
