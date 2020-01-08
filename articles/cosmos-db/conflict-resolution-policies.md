---
title: Typy řešení konfliktů a zásady řešení v Azure Cosmos DB
description: Tento článek popisuje kategorie konfliktů a zásady řešení konfliktů v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441983"
---
# <a name="conflict-types-and-resolution-policies"></a>Zásady řešení a typy konfliktů

Zásady konfliktů a řešení konfliktů se použijí, pokud je váš účet Azure Cosmos DB nakonfigurovaný s více oblastmi zápisu.

U účtů Azure Cosmos konfigurovaných s více oblastmi zápisu může dojít ke konfliktům aktualizace, když zapisovač současně aktualizuje stejnou položku ve více oblastech. Konflikty aktualizací můžou mít následující tři typy:

* **Konflikty při vkládání**: tyto konflikty mohou nastat, pokud aplikace současně vloží dvě nebo více položek se stejným jedinečným indexem ve dvou nebo více oblastech. K tomuto konfliktu může například dojít s vlastností ID.

* **Konflikty při nahrazení**: tyto konflikty mohou nastat, pokud aplikace aktualizuje stejnou položku současně ve dvou nebo více oblastech.

* **Konflikty při odstraňování**: k těmto konfliktům může dojít, když aplikace současně odstraní položku v jedné oblasti a aktualizuje ji v jiné oblasti.

## <a name="conflict-resolution-policies"></a>Zásady řešení konfliktů

Azure Cosmos DB nabízí flexibilní mechanizmy založené na zásadách pro řešení konfliktů při zápisu. V kontejneru Azure Cosmos můžete vybrat ze dvou zásad řešení konfliktů:

* **Poslední zápis WINS (LWW)** : Tato zásada řešení ve výchozím nastavení používá systémem definovanou vlastnost časového razítka. Vychází z protokolu časových hodin synchronizace. Pokud používáte rozhraní SQL API, můžete zadat jakoukoli jinou vlastní číselnou vlastnost (například vlastní fiktivní časovou známku), která se použije pro řešení konfliktů. Vlastní numerická vlastnost je také označována jako *cesta řešení konfliktů*. 

  Pokud dojde ke konfliktu dvou nebo více položek u operací vložení nebo nahrazení, bude se jednat o položku s nejvyšší hodnotou pro cestu k řešení konfliktů. Systém Určuje, zda má více položek stejnou číselnou hodnotu pro cestu k řešení konfliktů. Všechny oblasti jsou zaručené sblížení s jednou vítězí a končí stejnou verzí potvrzené položky. Pokud jsou zapojené konflikty při odstraňování, Odstraněná verze vždy vyhrává z konfliktů vložení nebo nahrazení. K tomuto výsledku nedochází bez ohledu na to, co je hodnota cesty pro řešení konfliktů.

  > [!NOTE]
  > Poslední zápis WINS je výchozí zásada řešení konfliktů a používá `_ts` časového razítka pro následující rozhraní API: SQL, MongoDB, Cassandra, Gremlin a Table. Vlastní číselná vlastnost je k dispozici pouze pro rozhraní SQL API.

  Další informace najdete v tématu [Příklady použití zásad řešení konfliktů LWW](how-to-manage-conflicts.md).

* **Vlastní**: Tato zásada řešení je určená pro sémantiku definovanou aplikací pro odsouhlasení konfliktů. Když nastavíte tuto zásadu v kontejneru Azure Cosmos, musíte taky zaregistrovat *uloženou proceduru sloučení*. Tento postup je automaticky vyvolán při zjištění konfliktů v rámci transakce databáze na serveru. Poskytuje tento systém přesně jednou zaručit k provádění procedury sloučení jako součást závazku protokolu.  

  Pokud nakonfigurujete kontejner s možností vlastní rozlišení a nebudete registrovat proceduru sloučení v kontejneru nebo procedura sloučení vyvolá výjimku za běhu, jsou konflikty zapisovány do *kanálu konfliktů*. Vaše aplikace pak potřebuje ručně vyřešit konflikty v kanálu konfliktů. Další informace najdete v tématu [Příklady použití vlastních zásad řešení a způsobu použití kanálu konfliktů](how-to-manage-conflicts.md).

  > [!NOTE]
  > Vlastní zásady řešení konfliktů jsou k dispozici pouze pro účty rozhraní SQL API.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nakonfigurovat zásady řešení konfliktů:

* [Jak v aplikacích nakonfigurovat více hlavních serverů](how-to-multi-master.md)
* [Jak spravovat zásady řešení konfliktů](how-to-manage-conflicts.md)
* [Jak číst z informačního kanálu konfliktů](how-to-manage-conflicts.md#read-from-conflict-feed)
