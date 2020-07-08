---
title: Použití uživatelsky definovaných schémat
description: Tipy pro vývoj řešení v synapse fondu SQL pomocí uživatelsky definovaných schémat T-SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fc5e035215e7cabd02861c6ee2498cadd1ef0534
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213359"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Uživatelsky definovaná schémata ve fondu SQL synapse
Tento článek se zaměřuje na poskytnutí několika tipů pro vývoj řešení v synapse fondu SQL pomocí uživatelsky definovaných schémat T-SQL.

## <a name="schemas-for-application-boundaries"></a>Schémata pro hranice aplikace

Tradiční datové sklady často používají samostatné databáze k vytvoření hranice aplikace na základě zatížení, domény nebo zabezpečení. 

Například tradiční datový sklad SQL Server může zahrnovat pracovní databázi, databázi datového skladu a některé databáze datového tržiště. V této topologii každá databáze funguje jako zatížení a hranice zabezpečení v architektuře.

Naproti tomu fond SQL spouští celou úlohu datového skladu v rámci jedné databáze. Spojení mezi databázemi nejsou povolena. Fond SQL očekává, že všechny tabulky používané datovým skladem budou uloženy v rámci jedné databáze.

> [!NOTE]
> Fond SQL nepodporuje databázové dotazy jakéhokoli druhu. V důsledku toho bude nutné implementace datového skladu, které využívají tento model, upravit.
> 
> 

## <a name="recommendations"></a>Doporučení
Níže jsou uvedeny doporučení pro konsolidaci úloh, zabezpečení, domény a funkční hranice pomocí uživatelsky definovaných schémat:

- Pomocí jedné databáze fondu SQL spusťte celou úlohu datového skladu.
- Konsolidujte stávající prostředí datového skladu tak, aby používalo jednu databázi fondu SQL.
- Využijte **uživatelsky definovaných schémat** k poskytnutí hranice dříve implementovaného pomocí databází.

Pokud se dřív definovaná schémata ještě nepoužila, máte čistou SLAT. Jako základ pro schémata definovaná uživatelem v databázi fondu SQL použijte starý název databáze.

Pokud se už schémata používala, máte několik možností:

- Odeberte starší názvy schémat a začněte znovu.
- Názvy starších schémat si ponechte předem – čeká se na název starší verze schématu na název tabulky.
- Starší názvy schémat zachovejte implementací zobrazení v tabulce ve dodatečném schématu pro opětovné vytvoření staré struktury schématu.

> [!NOTE]
> Při první kontrole 3 se může zdát, že se jedná o možnost nejúčinnějšího odvolání. Nicméně Devil je v podrobnostech. Zobrazení jsou jen pro čtení ve fondu SQL. Veškerá data nebo úpravy tabulek by se musely provést na základě základní tabulky. Možnost 3 také zavádí do systému vrstvu zobrazení. Pokud již používáte zobrazení ve vaší architektuře, je vhodné dát mu další myšlenky.
> 
> 

### <a name="examples"></a>Příklady:
Implementace uživatelsky definovaných schémat na základě názvů databází:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachovat starší názvy schémat podle názvu tabulky před jejich vyřízením Pro hranici zatížení použijte schémata:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Zachovat starší názvy schémat pomocí zobrazení:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Každá změna v strategii schématu vyžaduje přezkoumání modelu zabezpečení pro databázi. V mnoha případech může být možné zjednodušit model zabezpečení přiřazením oprávnění na úrovni schématu. Pokud potřebujete podrobnější oprávnění, můžete použít databázové role.
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

