---
title: Zotavení po havárii SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak obnovit databázi z místních datacenter výpadku nebo selhání pomocí Azure SQL Database aktivní geografickou replikaci a geografické obnovení funkce.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/21/2019
ms.openlocfilehash: 00fa1128df03befda8b15be2d7f2c527f65f9973
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341078"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Obnovení služby Azure SQL Database a převzetí služeb při selhání sekundární lokalitou

Azure SQL Database nabízí následující funkce pro obnovení po výpadku:

- [Aktivní geografická replikace](sql-database-active-geo-replication.md)
- [Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md)
- [Geo-restore](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Zónově redundantní databáze](sql-database-high-availability.md)

Další informace o obchodní kontinuity podnikových procesů scénáře a funkce podporuje tyto scénáře, naleznete v tématu [kontinuita podnikových procesů](sql-database-business-continuity.md).

> [!NOTE]
> Pokud používáte zónově redundantní databáze úrovně Premium nebo pro důležité obchodní informace nebo fondy, je automatizovaný proces obnovení a zbývající část tohoto materiálu se nedá použít.

> [!NOTE]
> Primární a sekundární databáze musí mít stejné úrovně služeb. Také důrazně doporučujeme, aby se vytvoří sekundární databáze se stejnou velikostí výpočetní prostředky (počet jednotek Dtu nebo virtuálních jader) jako primární. Další informace najdete v tématu [upgradu nebo downgradu jako primární databáze](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database).

> [!NOTE]
> Použití jednoho nebo několika skupin převzetí služeb při selhání pro správu převzetí služeb při selhání několika databází.
> Pokud chcete přidat existující relaci geografické replikace do skupiny převzetí služeb při selhání, nezapomeňte že GEO-secondary má nakonfigurovanou na stejné úrovně služeb a velikost výpočetního jako primární. Další informace najdete v tématu [pomocí skupin – automatické převzetí služeb při selhání můžete povolit transparentní a koordinovaný převzetí služeb při selhání několika databází](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Příprava pro případ výpadku

Pro úspěšné dokončení obnovení do jiné oblasti dat pomocí skupiny převzetí služeb při selhání nebo geograficky redundantní zálohy, je nutné připravit server v jiném datovém centru výpadek stane novým primárním serverem by potřeba nastat také mít dobře definované kroky zdokumentované a testováním k zajištění obnovení proběhlo bez problémů. Tyto přípravné kroky patří:

- Identifikace serveru SQL Database v jiné oblasti stane novým primárním serverem. Pro geografické obnovení, obvykle se jedná server v [spárované oblasti](../best-practices-availability-paired-regions.md) pro oblast, ve kterém je umístěna databáze. Tím se eliminují náklady na dodatečný provoz během operací geografické obnovení.
- Identifikujte a volitelně můžete definovat pravidla firewallu protokolu IP úrovni serveru třeba uživatelům přístup k nové primární databázi.
- Zjistěte, jak chcete přesměrovat uživatele na nový primární server, například tak, že změníte připojovací řetězce nebo při změně položky DNS.
- Identifikovat a volitelně vytvořit přihlašovací údaje, které se musí nacházet v hlavní databázi v nové primární server a ujistěte se, že tyto přihlašovací údaje mít příslušná oprávnění v hlavní databázi, pokud existuje. Další informace najdete v tématu [zabezpečení služby SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md)
- Určete pravidla výstrah, které je potřeba aktualizovat tak, aby namapovat na novou primární databázi.
- Dokument konfigurace auditování u aktuální primární databáze
- Provést [zotavení po havárii](sql-database-disaster-recovery-drills.md). Pro simulaci výpadku pro geografické obnovení, můžete odstranit nebo přejmenovat zdrojové databáze způsobit selhání připojení aplikace. Pro simulaci výpadku pomocí skupin převzetí služeb při selhání, můžete zakázat webové aplikace nebo virtuálního počítače připojené k databázi nebo převzetí služeb při selhání databáze způsobit selhání připojení k aplikaci.

## <a name="when-to-initiate-recovery"></a>Při zahájení obnovení

Operaci obnovení má vliv na aplikaci. Je potřeba změnit připojovací řetězec SQL nebo přesměrování pomocí DNS a může dojít ke ztrátě trvalé. Proto je třeba jej provést pouze v případě, že výpadek by mohla trvají déle než plánovanou dobu obnovení vaší aplikace. Když je aplikace nasazená do produkčního prostředí by měl provádět pravidelné kontroly stavu aplikace a použít k vyhodnocení, zda je oprávněná obnovení těchto datových bodů:

1. Chyba trvalé připojení z aplikační vrstvy do databáze.
2. Na webu Azure portal zobrazuje oznámení o incidentu v oblasti s široký dopad.

> [!NOTE]
> Pokud používáte skupiny převzetí služeb při selhání a rozhodli jste automatické převzetí služeb při selhání, je proces obnovení automatizované a pro aplikace transparentní.

V závislosti na vaší aplikace tolerance výpadků a co obchodní odpovědnost měli zvážit následující možnosti obnovení.

Použití [získat obnovitelné databáze](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) Chcete-li získat nejnovější bod obnovení geograficky replikovaný.

## <a name="wait-for-service-recovery"></a>Vyčkat, než obnovení

Azure týmy pracovní usilovně obnovíte dostupnost služeb, jak rychle nejdříve ale v závislosti na kořenovém adresáři způsobí, že může trvat hodiny nebo dny.  Pokud vaše aplikace může tolerovat možnost významnějších výpadků je jednoduše počkat obnovení pro dokončení. V takovém případě nemusíte vaší nic dělat. Aktuální stav služby najdete na naší [řídicí panel stavu služby Azure](https://azure.microsoft.com/status/). Po obnovení do oblasti obnovení dostupnosti vaší aplikace.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Převzetí služeb při selhání do geograficky replikované sekundární server ve skupině převzetí služeb při selhání

Pokud výpadky vašich aplikací může mít za následek obchodní závazky, byste měli použít skupiny převzetí služeb při selhání. Umožňuje aplikaci rychle obnovit dostupnosti v jiné oblasti i v případě výpadku. Podívejte se kurz [. implementace geograficky distribuované databáze](sql-database-implement-geo-distributed-database.md).

Chcete-li obnovit dostupnosti databází budete potřebovat k zahájení převzetí služeb při selhání na sekundární server pomocí jedné z podporovaných metod.

Převzít služby při selhání do geograficky replikované sekundární databáze, použijte jednu z následujících návodů:

- [Převzetí služeb při selhání do geograficky replikované sekundární server pomocí webu Azure portal](sql-database-geo-replication-portal.md)
- [Převzetí služeb při selhání na sekundární server pomocí Powershellu](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Převzetí služeb při selhání na sekundární server pomocí příkazů jazyka Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Obnovení s využitím geografického obnovení

Pokud výpadky vašich aplikací nemá za následek odpovědnosti firmy můžete [geografické obnovení](sql-database-recovery-using-backups.md) jako způsob obnovení databáze vaší aplikace. Vytvoří kopii databáze z jeho nejnovější geograficky redundantní zálohy.

## <a name="configure-your-database-after-recovery"></a>Nakonfigurujte databázi po obnovení

Pokud používáte geografické obnovení pro obnovení po výpadku, musí zajistíte, že je správně nakonfigurované připojení k nové databáze tak, aby funkce normální aplikaci lze obnovit. Toto je kontrolní seznam úloh připravit obnovené databáze v produkčním prostředí.

### <a name="update-connection-strings"></a>Aktualizovat připojovací řetězce

Vzhledem k tomu obnovené databáze nachází na jiném serveru, musíte aktualizovat připojovací řetězec vaší aplikace tak, aby odkazoval na tomto serveru.

Další informace o změně připojovací řetězce, naleznete v tématu příslušný vývojářský jazyk pro vaše [knihovny připojení](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Konfigurace pravidel brány Firewall

Potřebujete, abyste měli jistotu, že pravidla brány firewall nakonfigurovaná na serveru a databáze odpovídající těm, které byly nakonfigurovány na primární server a v primární databázi. Další informace najdete v tématu [jak: Konfigurace nastavení brány Firewall (Azure SQL Database)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Konfigurace přihlašovacích údajů a uživatelů databáze

Potřebujete, abyste měli jistotu, že všechna přihlášení použít v aplikaci existují na serveru, který je hostitelem obnovené databáze. Další informace najdete v tématu [konfiguraci zabezpečení pro geografickou replikaci](sql-database-geo-replication-security-config.md).

> [!NOTE]
> By měl nakonfigurovat a testovat vaše pravidla firewallu serveru a přihlašovací jména (a jejich oprávnění) během zotavení po havárii. Tyto objekty na úrovni serveru a jejich konfigurace nemusí být během výpadku dostupná.

### <a name="setup-telemetry-alerts"></a>Nastavení výstrah telemetrie

Musíte zajistit, aby že vaše stávající nastavení pravidla upozornění se aktualizují mapovat k obnovené databázi a jiný server.

Další informace o pravidla upozornění databáze najdete v tématu [přijímat oznámení výstrah](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) a [sledovat stav služeb](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Povolení auditování služby

Pokud pro přístup k vaší databázi se vyžaduje auditování, je potřeba povolit auditování po obnovení databáze. Další informace najdete v tématu [auditování databáze](sql-database-auditing.md).

## <a name="next-steps"></a>Další postup

- Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md)
- Další informace o obchodních scénářů návrhu a obnovení kontinuity podnikových procesů najdete v tématu [scénáře kontinuitu podnikových procesů](sql-database-business-continuity.md)
- Další informace o obnovení pomocí automatizovaného zálohování, naleznete v tématu [obnovení databáze ze záloh spouštěných službou](sql-database-recovery-using-backups.md)
