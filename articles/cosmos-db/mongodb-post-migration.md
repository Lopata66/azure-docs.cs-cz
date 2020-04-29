---
title: Kroky optimalizace po migraci s rozhraním API Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje techniky optimalizace po migraci z MongoDB k rozhraní APi pro Azure Cosmos DB pro Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063617"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Kroky optimalizace po migraci při použití rozhraní API Azure Cosmos DB pro MongoDB

Po migraci dat uložených v databázi MongoDB Azure Cosmos DB k rozhraní API pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Tento průvodce popisuje kroky, které byste měli zvážit po dokončení migrace. Postup migrace najdete v [kurzu migrace MongoDB Azure Cosmos DB do rozhraní API pro MongoDB](../dms/tutorial-mongodb-cosmos-db.md) .

V tomto průvodci se naučíte:

- [Připojení aplikace](#connect-your-application)
- [Optimalizace zásad indexování](#optimize-the-indexing-policy)
- [Konfigurace globální distribuce pro rozhraní API Azure Cosmos DB pro MongoDB](#globally-distribute-your-data)
- [Nastavit úroveň konzistence](#set-consistency-level)

> [!NOTE]
> Jediným krokem po migraci na úrovni aplikace je změna připojovacího řetězce v aplikaci tak, aby odkazoval na nový Azure Cosmos DB účet. Všechny ostatní kroky migrace jsou doporučené optimalizace.
>

## <a name="connect-your-application"></a>Připojení aplikace

1. V novém okně se přihlaste k [Azure Portal](https://www.portal.azure.com/)
2. Z [Azure Portal](https://www.portal.azure.com/)v levém podokně otevřete nabídku **všechny prostředky** a vyhledejte účet Azure Cosmos DB, na který jste migrovali svá data.
3. Otevřete okno **připojovací řetězec** . Pravé podokno obsahuje všechny informace, které potřebujete pro úspěšné připojení ke svému účtu.
4. Použijte informace o připojení v konfiguraci vaší aplikace (nebo na jiných relevantních místech), aby odrážely Azure Cosmos DB rozhraní API pro připojení MongoDB ve vaší aplikaci.
![Připojovací řetězec](./media/mongodb-post-migration/connection-string.png)

Další podrobnosti najdete na stránce [připojení aplikace MongoDB k Azure Cosmos DB](connect-mongodb-account.md) .

## <a name="optimize-the-indexing-policy"></a>Optimalizace zásad indexování

Všechna datová pole jsou ve výchozím nastavení automaticky indexována během migrace dat do Azure Cosmos DB. V mnoha případech je tato výchozí zásada indexování přijatelná. Obecně platí, že odebráním indexů optimalizujete požadavky na zápis a výchozí zásady indexování (tj. automatické indexování) optimalizují požadavky na čtení.

Další informace o indexování najdete v tématu [indexování dat v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-indexing.md) a [indexování v Azure Cosmos DBch](index-overview.md) článcích.

## <a name="globally-distribute-your-data"></a>Globální distribuce dat

Azure Cosmos DB je k dispozici ve všech [oblastech Azure](https://azure.microsoft.com/regions/#services) po celém světě. Po výběru výchozí úrovně konzistence pro účet Azure Cosmos DB můžete přidružit jednu nebo více oblastí Azure (v závislosti na vašich globálních potřebách distribuce). Pro zajištění vysoké dostupnosti a kontinuity podnikových aplikací vždy doporučujeme, abyste spustili aspoň dvě oblasti. Tipy pro [optimalizaci nákladů na nasazení ve více oblastech najdete v Azure Cosmos DB](optimize-cost-regions.md).

Pokud chcete globálně distribuovat data, přečtěte si prosím [globálně distribuované data na rozhraní API Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Nastavit úroveň konzistence

Azure Cosmos DB nabízí 5 jasně definovaných [úrovní konzistence](consistency-levels.md). Pokud si chcete přečíst o mapování mezi MongoDB a Azure Cosmos DB úrovní konzistence, načtěte si [úrovně konzistence a rozhraní API pro Azure Cosmos DB](consistency-levels-across-apis.md). Výchozí úroveň konzistence je úroveň konzistence relace. Změna úrovně konzistence je volitelná a můžete ji optimalizovat pro vaši aplikaci. Postup změny úrovně konzistence pomocí Azure Portal:

1. V části Nastavení otevřete okno **výchozí konzistence** .
2. Vyberte [úroveň konzistence](consistency-levels.md) .

Většina uživatelů opouští úroveň konzistence ve výchozím nastavení konzistence relace. Existují však [kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md).

## <a name="next-steps"></a>Další kroky

* [Připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md)
* [Připojení k účtu Azure Cosmos DB pomocí Studio 3T](mongodb-mongochef.md)
* [Jak globálně distribuovat čtení pomocí rozhraní Azure Cosmos DB API pro MongoDB](mongodb-readpreference.md)
* [Vypršení platnosti dat s využitím rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md).
* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Indexování ve službě Azure Cosmos DB](index-overview.md)
* [Jednotky žádosti v Azure Cosmos DB](request-units.md)