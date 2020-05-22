---
title: Nejčastější dotazy k Azure synapse Analytics
description: Nejčastější dotazy k Azure synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 007fe7cb90c651c346bc7fbea46d74aa41605d2d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744085"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Nejčastější dotazy ke službě Azure synapse Analytics (pracovní prostor ve verzi Preview)

V této příručce najdete nejčastější dotazy k synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Obecné

### <a name="q-what-is-azure-synapse-analytics"></a>Otázka: co je Azure synapse Analytics

Odpověď: Azure synapse je integrovaná datová platforma pro BI, AI a nepřetržitou analýzu. Připojuje různé analytické moduly, jako je SQL a Spark, prostřednictvím jediné platformy, která poskytuje jednotný způsob:

- Zabezpečte své analytické prostředky, včetně sítě, spravujte přístup s jednotným přihlašováním ke fondům, datům a vývojovým artefaktům.
- Snadno Sledujte a rychle Optimalizujte, reagují a ladíte události, které probíhají v aktivitách pracovního prostoru v libovolné vrstvě.
- Spravujte svá metadata napříč moduly. Vytvoří tabulku Spark a bude automaticky dostupná ve vašich databázích Azure synapse.
- Interakci s daty prostřednictvím jednotného uživatelského prostředí. Synapse Studio přináší vývojářům pro velké objemy dat, datové inženýry, specializující, analytiky dat a odborníky na data na stejné platformě.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>Otázka: Návody Začínáme s Azure synapse Analytics

O: Pokud chcete začít používat Azure synapse Analytics, [Zaregistrujte poskytovatele prostředků Azure synapse](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) , a to tak, že v seznamu poskytovatelů prostředků vyberete *Microsoft. synapse* . Pak vytvořte [pracovní prostor synapse](https://portal.azure.com) (je zdarma!) a v rámci tohoto pracovního prostoru vytvořte požadované prostředky. Můžete postupovat podle některého z našich kurzů pro rychlý Start, například [vytvořit synapse fond SQL](quickstart-create-sql-pool-portal.md) nebo [vytvořit pracovní prostor](quickstart-create-workspace.md), který vás provede jednoduchým případem použití. V našem [úložišti](https://github.com/Azure-Samples/Synapse)můžete také najít ukázkové poznámkové bloky a skripty SQL. Pokud se potřebujete připojit k veřejné datové sadě, vytvořte novou propojenou službu s následujícími atributy:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (Write **"**")

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>Otázka: Jaké jsou hlavní součásti Azure synapse Analytics

Odpověď: Azure synapse má následující možnosti:

- Analytické možnosti jsou nabízeny prostřednictvím fondu SQL nebo SQL na vyžádání (verze Preview) (bez serveru).
- Fond Apache Spark (Preview) s plnou podporou pro Scala, Python, SparkSQL a C #
- Tok dat nabízí prostředí pro transformaci velkých objemů dat bez kódu.
- Integrace dat & orchestrace pro integraci vašich dat a zprovozněníí veškerého vývoje kódu
- Studio pro přístup ke všem těmto funkcím prostřednictvím jediného webového uživatelského rozhraní

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Otázka: jak se vztahuje Azure synapse Analytics na Azure SQL Data Warehouse

Odpověď: Azure synapse Analytics je vývojem Azure SQL Data Warehouse na analytické platformě, která zahrnuje fond SQL jako řešení datového skladu. Tato platforma kombinuje zkoumání dat, ingestování, transformaci, přípravu a obsluhu analytických vrstev.

## <a name="use-cases"></a>Případy použití

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>Otázka: co je dobrým případem použití pro synapse fond SQL

A: fond SQL je srdcem vašich potřeb datového skladu. Je to přední řešení datového skladu v [ceně a výkonu](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Fond SQL je špičkovým řešením cloudového datového skladu, protože můžete:

- Díky vysoké souběžnosti a izolaci úloh slouží velká a smíšená škála úloh bez dopadu na výkon.
- Snadné zabezpečení dat prostřednictvím pokročilých funkcí v rozsahu od zabezpečení sítě až po jemně odstupňovaný přístup
- výhoda z široké škály ekosystémového systému

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>Otázka: co je dobrý případ použití pro Spark v synapse

Odpověď: naším prvním cílem je zajistit Skvělé prostředí pro datovou přípravu pro transformaci dat prostřednictvím jezera v dávce nebo streamu. Jeho těsná a jednoduchá integrace k naší orchestraci dat usnadňuje práci vývojového prostředí.

Dalším případem použití Sparku je vědecký odborník na data:

- extrakce funkce
- Prozkoumat data
- Výuka modelu

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>Otázka: co je dobrý případ použití pro SQL na vyžádání v synapse

Odpověď: SQL na vyžádání je dotazovací služba na základě dat ve službě Data Lake. Umožňuje Demokratizujte přístup ke všem datům tím, že poskytuje známou syntaxi T-SQL pro dotazování dat na místě, aniž by bylo nutné kopírovat nebo načítat data do specializovaného úložiště.

Příklady případů použití zahrnují následující:

- základní zjišťování a průzkum – poskytuje analytikům dat, vznikajícím datovým specialistům a datovým technikům jednoduchou cestu k prvnímu přehledu o živých datech v Data Lake pomocí schématu pro čtení dotazů T-SQL.
- logický datový sklad – datové analytiky můžou spouštět úplné expresivity jazyka T-SQL, aby se mohli přímo dotazovat a analyzovat data, která se nacházejí v Azure Storage, a pomocí známých nástrojů BI (např. analýzy Azure, Power BI Premium atd.) aktualizovat řídicí panely tím, že se znovu spustí dotazy dotazů Starlight.
- ETL "Single Query" – umožňuje datovým technikům transformovat Azure Storage data založená na jiném formátu na jiný, filtrovat, agregovat atd. při výkonném paralelním zpracování, uchovávat výsledky dotazů Azure Storage a zpřístupnit je okamžitě pro další zpracování v Starlight dotazech nebo jiných službách, které vás zajímají.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>Otázka: co je dobrý případ použití pro tok dat v synapse

Odpověď: tok dat umožňuje inženýrům dat vyvíjet grafické logiky pro transformaci dat bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci integrace dat & orchestrace. Aktivity toku dat je možné provozovat prostřednictvím stávajících možností plánování, řízení, toku a monitorování.

## <a name="security-and-access"></a>Zabezpečení a přístup

O: ucelené prostředí jednotného přihlašování je důležitým ověřovacím procesem v synapse Analytics. Správa a předání identity prostřednictvím úplné integrace AAD je nutné.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>Otázka: Návody získat přístup k souborům a složkám v ADLS Gen2

Odpověď: přístup k souborům a složkám je aktuálně spravován prostřednictvím ADLS Gen2. Další informace najdete v tématu [Data Lake řízení přístupu k úložišti](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>Otázka: je možné použít business intelligence nástroje třetích stran pro přístup ke službě Azure synapse Analytics

Odpověď: Ano, můžete použít obchodní aplikace třetích stran, jako je Tableau a Power BI, pro připojení k fondu SQL a k SQL na vyžádání. Spark podporuje IntelliJ.

## <a name="next-steps"></a>Další kroky

- [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
- [Použití synapse studia](quickstart-synapse-studio.md)
- [Vytvoření fondu SQL](quickstart-create-sql-pool-portal.md)
- [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
- [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool-portal.md) 
