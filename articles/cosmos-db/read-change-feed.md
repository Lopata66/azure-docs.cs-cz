---
title: Přístup k změnu informačního kanálu ve službě Azure Cosmos DB Azure Cosmos DB
description: Tento článek popisuje různé možnosti, které jsou k dispozici ke čtení a přístup ke kanálu ve službě Azure Cosmos DB Azure Cosmos DB změn.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 459ed4b6f16d3cfe5bb792be7f063a1253a3006e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60927796"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Kanál změn čtení Azure Cosmos DB

Můžete pracovat pomocí kanálu změn služby Azure Cosmos DB pomocí kteréhokoli z následujících možností:

* Pomocí služby Azure Functions
* Změny pomocí kanálu knihovny procesoru
* Pomocí sady SDK služby Azure Cosmos DB SQL API

## <a name="using-azure-functions"></a>Pomocí služby Azure Functions

Azure Functions je nejjednodušší a doporučená možnost. Když vytvoříte aktivační událost Azure Cosmos DB v aplikaci Azure Functions, můžete vybrat kontejner pro připojení a funkce Azure Functions se spustí pokaždé, když dojde ke změně do kontejneru. Aktivační události lze vytvořit pomocí portálu Azure Functions na portálu služby Azure Cosmos DB nebo programově pomocí sad SDK. VS Code a Visual Studio poskytují podporu pro zápis Azure Functions a Azure Functions CLI můžete použít i pro vývoj pro různé platformy. Můžete napsat a ladění kódu na vašem počítači a pak nasadit funkci s jedním kliknutím. Zobrazit [výpočetní prostředí bez serveru databázi s využitím Azure Functions](serverless-computing-database.md) a [změn pomocí kanálu s využitím Azure Functions](change-feed-functions.md)) články Další informace.

## <a name="using-the-change-feed-processor-library"></a>Změny pomocí kanálu knihovny procesoru

Změna kanálu knihovny procesoru skrývá složitosti a pořád vám nabízí naprostou kontrolu nad kanálu změn. Knihovny následuje vzor pozorovatele, ve kterém je volána funkce zpracování knihovny. Pokud máte kanálu změn vysokou propustnost, můžete vytvořit instanci více klientů ke čtení kanálu změn. Vzhledem k tomu, že při použití knihovny procesoru kanálu změn, ji budou automaticky rozdělit zatížení mezi různé klienty aniž by bylo nutné implementovat tuto logiku. Všem složitosti zařizuje služba knihovny. Pokud chcete mít vlastní nástroje pro vyrovnávání zatížení, pak budete moct implementovat `IPartitionLoadBalancingStrategy` pro oddíl vlastní informační kanál strategie pro zpracování změn. Další informace najdete v tématu [změn pomocí kanálu knihovny procesoru](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Pomocí sady SDK služby Azure Cosmos DB SQL API

Díky sadě SDK budete mít nižší úrovně ovládacího prvku kanálu změn. Můžete spravovat kontrolní bod, přístup ke konkrétní logický oddíl klíče, atd. Pokud máte více čtenářům, můžete použít `ChangeFeedOptions` distribuci další zátěže do různých vláken nebo různých klientů. 

## <a name="next-steps"></a>Další postup

Teď můžete přejít k další informace o změně v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Změna kanálu s využitím Azure Functions](change-feed-functions.md)
* [Změna kanálu knihovny procesoru](change-feed-processor.md)
