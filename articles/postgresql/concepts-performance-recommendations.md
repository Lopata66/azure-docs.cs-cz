---
title: Doporučení týkající se výkonu v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci doporučení výkonu na serveru Azure Database for PostgreSQL-Single.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 5f1b64753d19158b17d4de1b3fbbe50d30ea0254
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764675"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Doporučení týkající se výkonu v Azure Database for PostgreSQL – jeden server

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6, 10, 11

Funkce doporučení k výkonu analyzuje vaše databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Při vytváření doporučení analyzuje tato analýza různé charakteristiky databáze, včetně schématu. Povolením [úložiště dotazů](concepts-query-store.md) na serveru můžete plně využít funkci doporučení pro výkon. Po implementaci jakéhokoli doporučení výkonu byste měli testovat výkon a vyhodnotit dopad těchto změn. 

## <a name="permissions"></a>Oprávnění
Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení pro výkon** z části **inteligentní výkon** na panelu nabídek na stránce Azure Portal pro server PostgreSQL.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Vyberte možnost **analyzovat** a zvolte databázi, která bude začínat analýzou. V závislosti na vašem zatížení může trvat několik minut, než se dokončí analýza. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provede důkladné přezkoumání vaší databáze. Doporučujeme, abyste provedli analýzu v době mimo špičku. 

V okně **doporučení** se zobrazí seznam doporučení, pokud byla nalezena kterákoli z nich.

![Nová stránka s doporučeními pro výkon](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Doporučení se nepoužívají automaticky. Pokud chcete doporučení použít, zkopírujte text dotazu a spusťte ho z vašeho klienta podle vlastního výběru. Nezapomeňte otestovat a monitorovat, abyste vyhodnotili doporučení. 

## <a name="recommendation-types"></a>Typy doporučení

V současné době jsou podporovány dva typy doporučení: *Vytvořte index* a *vyřaďte index*.

### <a name="create-index-recommendations"></a>Vytvořit doporučení indexu
Doporučení *vytvořit index* návrhy nových indexů vám umožní zrychlit nejčastěji spouštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby [úložiště dotazů](concepts-query-store.md) bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.

### <a name="drop-index-recommendations"></a>Vyřadit doporučení indexu
Kromě detekce chybějících indexů Azure Database for PostgreSQL analyzuje výkon existujících indexů. Pokud je index buď zřídka používaný, nebo redundantní, analyzátor ho doporučuje odstranit.

## <a name="considerations"></a>Požadavky
* Doporučení k výkonu nejsou pro [repliky čtení](concepts-read-replicas.md)k dispozici.
## <a name="next-steps"></a>Další postup
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.

