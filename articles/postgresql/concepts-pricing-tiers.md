---
title: Cenové úrovně pro službu Azure Database for PostgreSQL
description: Tento článek popisuje cenové úrovně pro službu Azure Database for PostgreSQL.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 0df36f4c939ed9bd7bb6a134a356b37d699abc1d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586339"
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Azure Database for PostgreSQL cenové úrovně

Serveru Azure Database for PostgreSQL můžete vytvořit v jednom ze tří různých cenových úrovní: Basic, pro obecné účely a paměťově optimalizovaná. Cenové úrovně se liší podle množství výpočetních prostředků ve virtuálních jader, které je možné zřídit paměti na vCore a technologie úložiště používají k ukládání dat. Všechny prostředky jsou zřízené na úrovni serveru PostgreSQL. Server může mít jeden nebo více databází.

|    | **Basic** | **Obecné účely** | **Optimalizované z hlediska paměti** |
|:---|:----------|:--------------------|:---------------------|
| Generace výpočetních funkcí | Gen 4, generace 5 | Gen 4, generace 5 | Gen 5 |
| Virtuální jádra | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Paměť na vCore | 2 GB | 5 GB | 10 GB |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Typ úložiště | Azure Storage úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Období uchovávání záloh databáze | 7 až 35 dnů | 7 až 35 dnů | 7 až 35 dnů |

Zvolte cenovou úroveň, použijte jako výchozí bod v následující tabulce.

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| Basic | Úlohy vyžadující nízký výpočetní a I/O výkon. Mezi příklady patří servery používané pro vývoj a testování nebo rozsáhlé a zřídka používané aplikace. |
| Obecné použití | Většinu obchodních úloh, které vyžadují vyvážené výpočetní a paměťové prostředky se škálovatelnou I/O propustností. Mezi příklady patří pro servery hostující webové a mobilní aplikace a jiné podnikové aplikace.|
| Paměťově optimalizované | Vysoce výkonné databázové úlohy, které vyžadují výkon v paměti kvůli rychlejšímu zpracování transakcí a lepší souběžnosti. Mezi příklady patří servery pro zpracování dat v reálném čase a vysoce výkonné transakční a analytické aplikace.|

Po vytvoření serveru, počet virtuálních jader generace hardwaru a cenové úrovně (s výjimkou do a z Basic) lze změnit navýšit nebo snížit kapacitu během několika sekund. Také můžete samostatně upravit velikost úložiště nahoru a období uchovávání záloh navýšit nebo snížit kapacitu bez výpadků aplikace. Typ úložiště pro zálohování nelze změnit po vytvoření serveru. Další informace najdete v tématu [škálovat prostředky](#scale-resources) oddílu.


## <a name="compute-generations-and-vcores"></a>COMPUTE generace a virtuální jádra

Výpočetní prostředky jsou k dispozici jako virtuální jádra, která představuje logický procesor základního hardwaru. V současné době můžete zvolit ze dvou generací compute Gen 4 a 5 Obecné. Gen 4 logické procesory jsou založeny na Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech. Generace 5 logické procesory jsou založené na Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorech. 4. generace a generace 5 jsou k dispozici v následujících oblastech ("X" označuje k dispozici). 

> [!IMPORTANT]
> Od 12. prosince 2018, nebudou moct zřizovat výpočetní generace 4 servery v oblasti Brazílie – Jih, Kanada – střed, Kanada – východ, Asie – východ, USA – východ 2, střed Indie, Indie – Západ, Japonsko – Západ, střed USA – sever, USA – západ nové zákazníky. Výpočetní generace 4 servery budou migrovány na výpočetní generaci 5 od 1. února 2019 v těchto oblastech předtím vytvořili.
>
> [!IMPORTANT]
> Od 19. února 2019 noví zákazníci nebude možné zřizovat výpočetní generace 4 servery v oblasti střed USA, USA – východ, Japonsko – východ, Severní Evropa, střed USA – Jih, jihovýchodní Asie, západní Evropa. Výpočetní generace 4 servery budou migrovány na výpočetní generaci 5 od 1. dubna 2019 v těchto oblastech předtím vytvořili.

| **Oblasti Azure** | **4. generace** | **Generace 5** |
|:---|:----------:|:--------------------:|
| USA – střed |  | X |
| USA – východ |  | X |
| Východní USA 2 | X | X |
| Středoseverní USA | X | X |
| Středojižní USA | X | X |
| Západní USA | X | X |
| Západní USA 2 |  | X |
| Brazílie – jih | X | X |
| Kanada – střed | X | X |
| Kanada – východ | X | X |
| Severní Evropa | X | X |
| Západní Evropa |  | X |
| Francie – střed |  | X |
| Velká Británie – jih |  | X |
| Spojené království – západ |  | X |
| Východní Asie | X | X |
| Jihovýchodní Asie | X | X |
| Austrálie – východ |  | X |
| Austrálie – střed |  | X |
| Austrálie – střed 2 |  | X |
| Austrálie – jihovýchod |  | X |
| Střed Indie | X | X |
| Indie – jih |  | X |
| Indie – západ | X | X |
| Japonsko – východ | X | X |
| Japonsko – západ | X | X |
| Jižní Korea – střed |  | X |
| Jižní Korea – jih |  | X |
| Východní Čína 1 | X |  |
| Čína – východ 2 |  | X |
| Čína – sever 1 | X |  |
| Čína – sever 2 |  | X |
| Německo – střed |  | X |
| US DoD – střed  | X |  |
| US DoD – východ  | X |  |
| USA (Gov) – Arizona |  | X |
| USA (Gov) – Texas |  | X |
| USA (Gov) – Virginia |  | X |

## <a name="storage"></a>Storage

Úložiště, které zřizujete je objem úložné kapacity k dispozici pro vaši databázi Azure PostgreSQL server. Úložiště se používá pro soubory databáze, dočasných souborů, protokoly transakcí a PostgreSQL server protokoly. Celkový objem úložiště, který zřídíte také definuje vstupně-výstupní kapacity k dispozici pro váš server.

|    | **Basic** | **Obecné účely** | **Optimalizované z hlediska paměti** |
|:---|:----------|:--------------------|:---------------------|
| Typ úložiště | Azure Storage úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Zvýšení velikosti úložiště | 1 GB | 1 GB | 1 GB |
| IOPS | Proměnná |3 IOPS/GB<br/>Minimum 100 vstupně-výstupních operací<br/>Maximální počet 6000 vstupně-výstupních operací | 3 IOPS/GB<br/>Minimum 100 vstupně-výstupních operací<br/>Maximální počet 6000 vstupně-výstupních operací |

Můžete přidat další kapacitu, během a po vytvoření serveru. Úroveň Basic neposkytuje záruka vstupně-výstupních operací. V obecné účely a optimalizovaný pro paměť cenové úrovně se škálují vstupně-výstupních operací s velikost zřízeného úložiště poměr 3:1.

Můžete monitorovat spotřebu vstupně-výstupních operací na webu Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure. Jsou důležité metriky pro monitorování [limitu úložiště, procento úložiště, využité úložiště a vstupně-výstupních operací procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Dosažení limitu úložiště

Server se označí jako jen pro čtení, když velikost volného úložiště klesne pod 5 GB nebo 5 % zřízeného úložiště, podle toho, která hodnota je nižší. Například, pokud jste zřídili 100 GB úložiště a skutečné využití prochází přes 95 GB, na serveru je označen jen pro čtení. Případně pokud jste zřídili 5 GB úložiště, server se označí jako jen pro čtení, když velikost volného úložiště klesne pod 250 MB.  

Když serveru je nastavena na jen pro čtení, jsou odpojené všechny existující relace a nepotvrzené transakce jsou vrácena zpět. Všechny operace zápisu následné a transakce potvrzení nezdaří. Všechny následné čtení dotazy budou fungovat bez přerušení.  

Můžete zvýšit velikost zřízeného úložiště na serveru nebo zahájit novou relaci v datech režimu a drop pro čtení i zápis uvolnit volné úložiště. Spuštění `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` nastaví aktuální relace do režimu zápisu pro čtení. Pokud se chcete vyhnout poškození dat, nebude provádět všechny operace zápisu server je stále ve stavu jen pro čtení.

Doporučujeme nastavit upozornění pro upozornění, úložiště serveru se blíží prahové hodnoty, tomu se můžete vyhnout, převedení do stavu jen pro čtení. Další informace najdete v dokumentaci na [jak nastavit výstrahu](howto-alert-on-metric.md).

## <a name="backup"></a>Backup

Služby trvá automatické zálohy vašeho serveru. Období minimální doby uchování pro zálohy je sedm dní. Můžete nastavit dobu uchování o délce až po dobu 35 dní. Uchovávání je možné upravit kdykoli během životního cyklu serveru. Můžete vybrat mezi místně redundantní a geograficky redundantní zálohy. Geograficky redundantní zálohy jsou také uloženy v [geograficky spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) oblasti, kde se vytvoří váš server. Tuto redundanci poskytuje úroveň ochrany v případě havárie. Můžete také získat lepší schopnost obnovení vašeho serveru do libovolné jiné oblasti Azure ve kterém je služba dostupná s geograficky redundantní zálohy. Není možné změnit mezi těmito dvěma možnostmi úložiště záloh po vytvoření serveru.

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru nezávisle na sobě můžete změnit virtuálních jader generace hardwaru, cenovou úroveň (s výjimkou do a z Basic), jaká část úložiště a období uchovávání záloh. Typ úložiště pro zálohování nelze změnit po vytvoření serveru. Počet virtuálních jader je možné škálovat směrem nahoru nebo dolů. Období uchování zálohy je možné škálovat směrem nahoru nebo dolů na 7 pro po dobu 35 dní. Velikost úložiště může být pouze zvýšena. Škálování prostředků můžete udělat buď na portálu nebo rozhraní příkazového řádku Azure. Příklad škálování pomocí Azure CLI najdete v tématu [sledování a škálování serveru Azure Database for PostgreSQL pomocí Azure CLI](scripts/sample-scale-server-up-or-down.md).

Při změně počtu virtuálních jader generace hardwaru nebo cenovou úroveň, kopii původního serveru se vytvoří s nové přidělení výpočetní prostředky. Po vytvoření a spuštění je nový server, připojení se přepnout na nový server. Během okamžiku, kdy systém přepne na nový server je možné navázat nová připojení a jsou všechny nepotvrzené transakce vráceny zpět. Toto okno se liší, ale ve většině případů je méně než minutu.

Škálování úložiště a změna období uchování zálohy jsou true online operace. Neexistuje žádný výpadek a vaší aplikace neprojeví. Vstupně-výstupních operací škálování s velikost zřízeného úložiště, můžete zvýšit IOPS k dispozici pro váš server vertikálním navýšení kapacity úložiště.

## <a name="pricing"></a>Ceny

Nejnovější informace o cenách najdete v článku Služba [stránce s cenami](https://azure.microsoft.com/pricing/details/PostgreSQL/). Zobrazit náklady pro konfigurace, kterou chcete [webu Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) ukazuje měsíční náklady na **cenová úroveň** kartu podle možností, které zvolíte. Pokud nemáte předplatné Azure, můžete získat Odhadovaná cena cenovou kalkulačku Azure. Na [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/) webu, vyberte **přidat položky**, rozbalte **databází** kategorie a zvolte **– Azure Database for PostgreSQL** přizpůsobit možnosti.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvoříte PostgreSQL server na portálu](tutorial-design-database-using-azure-portal.md).
- Další informace o [omezení služby](concepts-limits.md). 
- Zjistěte, jak [horizontální navýšení kapacity s čtení replik](howto-read-replicas-portal.md).
