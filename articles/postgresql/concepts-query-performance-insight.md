---
title: Query Performance Insight – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci Query Performance Insight v rámci Azure Database for PostgreSQL na jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768380"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6, 10, 11

Query Performance Insight vám pomůže rychle zjistit, co nejdéle běží dotazy, jak se v průběhu času mění a co se na ně budou jejich vlivovat.

## <a name="permissions"></a>Oprávnění
K zobrazení textu dotazů v Query Performance Insight jsou nutná oprávnění **vlastníka** nebo **přispěvatele**. **Čtenář** může zobrazit grafy a tabulky, ale ne text dotazu.

## <a name="prerequisites"></a>Požadavky
Aby Query Performance Insight fungoval, musí existovat data v [úložišti dotazů](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Zobrazení přehledů výkonu
Zobrazení [Query Performance Insight](concepts-query-performance-insight.md) na portálu Azure, bude přinášet vizualizace o klíčových informacích z Query Storu. 

Na stránce portálu vašeho serveru Azure Database for PostgreSQL v části **inteligentní výkon** v řádku nabídek vyberte **Query Performance Insight** .

![Query Performance Insight dlouho běžící dotazy](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Karta **dlouho běžící dotazy** zobrazuje prvních pět dotazů na základě průměrné doby trvání za běhu, agregované v intervalech 15 minut. Další dotazy můžete zobrazit tak, že v rozevíracím seznamu vyberete **Počet dotazů**. Barvy grafu se mohou při této akci změnit pro konkrétní ID dotazu.

Můžete kliknout a přetáhnout v grafu, abyste zmenšili konkrétní časové okno. Alternativně můžete použít ikony přiblížení a ven k zobrazení kratšího nebo většího časového období.

Tabulka pod grafem obsahuje další podrobnosti o dlouhotrvajících dotazech v daném časovém intervalu.

Vyberte kartu **Statistiky čekání** k zobrazení odpovídající vizualizace týkající se čekání na serveru.

![Query Performance Insight čeká na statistiku](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Důležité informace
* Query Performance Insight není pro [repliky čtení](concepts-read-replicas.md)k dispozici.

## <a name="next-steps"></a>Další kroky
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.


