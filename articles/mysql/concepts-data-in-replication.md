---
title: Replikace dat do služby Azure Database for MySQL.
description: Tento článek popisuje dat replikace pro službu Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996800"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikace dat do služby Azure Database for MySQL

Replikace dat můžete synchronizovat data z externího serveru MySQL do služba Azure Database for MySQL. Externí server může být místní, v databázi služba hostovaná společností jiných poskytovatelů cloudových služeb nebo virtuálních počítačích. Replikace dat je založená na binární protokol (binlog) na základě pozice replikace souboru nativní MySQL. Další informace o binlog replikace, najdete v článku [Přehled replikace binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikace dat
Hlavní scénáře pro zvážení použití replikace dat jsou:

- **Hybridní synchronizace dat:** Pomocí Data replikace můžete zachovat data synchronizovat mezi vašimi místními servery a Azure Database for MySQL. Tato synchronizace je užitečné pro vytváření hybridních aplikací. Tato metoda je přitažlivými, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Multicloudové synchronizace:** Pro komplexní cloudové řešení pomocí replikace dat pro synchronizaci dat mezi – Azure Database for MySQL a různí poskytovatelé cloudových, včetně virtual machines a databáze služby hostované v tyto cloudy.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Není replikace dat
[ *Systémovou databázi mysql* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) na hlavním serveru se nereplikuje. Nebudou se replikovat změny v účtech a oprávněních v hlavní server. Pokud vytvoříte účet na hlavním serveru a tento účet musí mít přístup k serveru repliky, ručně vytvořte stejný účet na straně serveru repliky. Vysvětlení, co tabulky jsou obsaženy v systémové databázi, najdete v tématu [MySQL ruční](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Požadavky
- Verze hlavního serveru musí být dlouhý aspoň MySQL verze 5.6. 
- Verze serveru master a repliky se musí shodovat. Například musí být MySQL verze 5.6 nebo MySQL 5.7 verze musí být oba.
- Každá tabulka musí obsahovat primární klíč.
- Hlavní server by měl používat modul MySQL InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binární protokolování a vytváření nových uživatelů na hlavní server.

### <a name="other"></a>Ostatní
- Replikace dat je jen pro podporované obecné účely a optimalizované pro paměť cenové úrovně.
- Identifikátory globální transakce (GTID) nejsou podporovány.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nastavení replikace dat](howto-data-in-replication.md)
- Další informace o [replikaci do Azure s využitím čtení replik](concepts-read-replicas.md)
