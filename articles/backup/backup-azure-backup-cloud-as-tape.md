---
title: Použití služby Azure Backup k náhradě páskové infrastruktury
description: Zjistěte, jak Azure Backup poskytuje sémantiku podobné pásku, což vám umožní zálohovat a obnovit data v Azure
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: d768f0fae9487a555f6ace12303f8a4bd7cb8bd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146027"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Přesunout vaše dlouhodobé úložiště z pásky do cloudu Azure
Zákazníci Azure Backup a System Center Data Protection Manager můžete:

* Zálohování dat v plány, které nejlépe vyhovovat potřebám organizace.
* Zálohovaná data uchovávat po delší dobu
* Ujistěte se, Azure, které jsou součástí jejich dlouhodobé uchovávání potřebám (místo na pásku).

Tento článek vysvětluje, jak zákazníci mohou povolit zásady zálohování a uchovávání. Zákazníci, kteří používají pásek k řešení jejich dlouhé-dlouhodobé uchovávání musí mít teď výkonné a přijatelné alternativní s dostupností tuto funkci. Aby byla povolená funkce v nejnovější verzi služby Azure Backup (která je k dispozici [tady](https://aka.ms/azurebackup_agent)). System Center DPM musí zákazníci provést aktualizaci, minimálně, DPM 2012 R2 UR5 před použitím aplikace DPM pomocí služby Azure Backup.

## <a name="what-is-the-backup-schedule"></a>Co je plán zálohování?
Plán zálohování určuje četnost zálohování. Nastavení na následující obrazovce například znamenat, že zálohy jsou prováděny každý den na 18: 00 a půlnoci.

![Denní plán](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Zákazníky můžete také naplánovat týdenní zálohování. Nastavení na následující obrazovce například znamenat, že zálohy jsou prováděny každou neděli alternativní & středa 9:30:00 a 1:00:00.

![Týdenní plán](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co jsou zásady uchovávání informací?
Zásady uchovávání informací Určuje dobu, po kterou musí být uložená zálohování. Místo zadání "ploché zásady" pro všechny body zálohy, Zákazníci si můžete vybrat různé zásady uchovávání informací na základě když dochází k zálohování. Zálohování bodu pořizovat denně, která slouží jako bod obnovení, je třeba zachována po dobu 90 dnů. Bod zálohy provedených na konci každého čtvrt roku pro účely auditu se zachovají na delší dobu.

![Zásady uchovávání informací](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Total number of "počet bodů uchovávání informací" zadat v těchto zásadách je 90 (denní body) + 40 (jeden pro každé čtvrtletí po dobu 10 let) = 130.

## <a name="example--putting-both-together"></a>Příklad – obě sestavení
![Ukázka obrazovky](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Denní zásady uchovávání informací**: Denní zálohy se ukládají po dobu sedmi dní.
2. **Týdenní zásady uchovávání informací**: Zálohy prováděny každý den o půlnoci a 18: 00 Sobota jsou zachovány čtyři týdny
3. **Měsíční zásady uchovávání informací**: Zálohy pořízené o půlnoci a 18: 00 na poslední neděle v měsíci jsou zachovány po dobu 12 měsíců
4. **Roční zásady uchovávání dat**: Zálohy pořízené půlnoci na poslední sobota každý dne se zachovají pro po dobu 10 let

Celkový počet "počet bodů uchovávání informací" (body, ze kterých může zákazník obnovení dat) na předchozím obrázku je vypočítán takto:

* dva body za den pro sedm dnů = 14 bodů obnovení
* dva body za týden pro čtyři týdny = 8 body obnovení
* dva body měsíčně po dobu 12 měsíců = 24 bodů obnovení
* ukazuje jeden bod za rok na 10 let = 10 obnovení

Celkový počet bodů obnovení je 56.

> [!NOTE]
> Pomocí služby Azure Backup můžete vytvořit až 9999 bodů obnovení na chráněnou instanci. Chráněná instance je počítač, server (fyzický nebo virtuální) nebo úloha, která zálohuje do Azure.
>

## <a name="advanced-configuration"></a>Pokročilá konfigurace
Kliknutím na **změnit** na předchozí obrazovce, zákazníci mají další flexibilitu při zadávání plány uchovávání informací.

![Úpravy](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Další kroky
Další informace o službě Azure Backup najdete v tématu:

* [Seznámení s Azure Backup](backup-introduction-to-azure-backup.md)
* [Vyzkoušejte Azure Backup](backup-try-azure-backup-in-10-mins.md)
