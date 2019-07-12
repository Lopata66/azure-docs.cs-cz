---
title: Nejčastější dotazy k Azure Backup
description: 'Odpovědi na běžné dotazy týkající se: Včetně služby Recovery Services trezory, co může zálohovat, jak to funguje, šifrování a omezení funkce Azure Backup. '
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: dacurwin
ms.openlocfilehash: aecad4273493cd573935c78cae51bd0f59461e2e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806968"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – nejčastější dotazy
Tento článek obsahuje odpovědi na běžné dotazy týkající se služby Azure Backup.

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Je v rámci předplatného Azure nějak omezený počet trezorů, které lze vytvořit?
Ano. Můžete vytvořit až 500 trezorů služby Recovery Services, každou podporovanou oblast služby Azure Backup na jedno předplatné. Pokud potřebujete další trezory, vytvořte další předplatné.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Je nějak omezený počet serverů nebo počítačů, které lze zaregistrovat k trezoru?
Můžete zaregistrovat až 1 000 virtuálních počítačích Azure s jeden trezor. Pokud používáte Microsoft Azure Backup Agent, můžete zaregistrovat až 50 agenti MAB jeden trezor. A můžete zaregistrovat 50 MAB servery pro/DPM serverů do trezoru.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Pokud má Moje organizace jeden trezor, jak můžete I data z různých serverů v trezoru během obnovování dat izolovat?
Data serveru, který chcete obnovit společně používejte stejné heslo, při nastavení zálohování. Pokud chcete izolovat obnovení na konkrétní server nebo servery, použijte přístupové heslo pro tento server nebo pouze servery. Například servery lidských zdrojů mohou používat jedno šifrovací heslo, účetní servery jiné a servery úložiště ještě jiné.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Můžete mezi předplatnými přesunout Moje trezoru?
Ano. Chcete-li přesunout trezor služby Recovery Services najdete na tomto [článku](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>U jiného trezoru, přesun záložních dat?
Ne. Data záloh uložená v trezoru nejde přesunout do jiného trezoru.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Můžete změnit z GRS LRS za zálohování?
Ne. Trezor služby Recovery Services můžete pouze změnit možnosti úložiště před všechny zálohy jsou uložené.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Můžete provést položku úrovně obnovení (ILR) virtuálních počítačů zálohovat do trezoru služby Recovery Services?
- Pro virtuální počítače Azure zálohovat pomocí zálohování virtuálních počítačů Azure se podporuje ILR. Další informace najdete v tématu [článku](backup-azure-restore-files-from-vm.md)
- ILR není podporovaná pro body obnovení online z místních virtuálních počítačů zálohovat pomocí Azure backup Server nebo System Center DPM.


## <a name="azure-backup-agent"></a>Agent Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Kde můžu najít běžné otázky týkající se agenta Azure Backup pro zálohování virtuálních počítačů Azure?

- Agent běžící na virtuálních počítačích Azure, najdete v tomto [nejčastější dotazy k](backup-azure-vm-backup-faq.md).
- Pro agenta pro zálohování Azure file složky, najdete v tomto [nejčastější dotazy k](backup-azure-file-folder-backup-faq.md).


## <a name="general-backup"></a>Obecné zálohování

### <a name="are-there-limits-on-backup-scheduling"></a>Existují omezení pro plánování zálohování?
Ano.
- Můžete zálohovat Windows Server nebo Windows počítače až třikrát za den. Zásady plánování můžete nastavit na denní nebo Týdenní plány.
- Aplikace DPM můžete zálohovat až dvakrát denně. Zásady plánování můžete nastavit pro denní, týdenní, měsíční nebo roční.
- Virtuální počítače Azure zálohujete jednou denně.

### <a name="what-operating-systems-are-supported-for-backup"></a>Jaké operační systémy jsou podporovány pro zálohování?
Azure Backup podporuje tyto operační systémy pro zálohování souborů a složek a aplikace, které jsou chráněné službou Azure Backup serveru a aplikace DPM.

**OS** | **SKU** | **Podrobnosti**
--- | --- | ---
Pracovní stanice | |
Windows 10 64-bit | Enterprise, Pro, Home | Počítače by měly běžet nejnovější aktualizace Service Pack a aktualizace.
Windows 8.1 64-bit | Enterprise, Pro | Počítače by měly běžet nejnovější aktualizace Service Pack a aktualizace.
Windows 8 64-bit | Enterprise, Pro | Počítače by měly běžet nejnovější aktualizace Service Pack a aktualizace.
Windows 7 64-bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Počítače by měly běžet nejnovější aktualizace Service Pack a aktualizace.
Server | |
Windows Server 2019 64 bit | Standard, Datacenter, Essentials | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Server 2016 64 bitů | Standard, Datacenter, Essentials | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Server 2012 R2 64 bitů | Standard, Datacenter, Foundation | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Server 2012 64 bitů | Datacenter, Foundation, Standard | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Storage Server 2016 64 bitů | Standard, Workgroup | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Storage Server 2012 R2 64 bitů | Standard, Workgroup, Essential | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Storage Server 2012 64 bitů | Standard, Workgroup | S nejnovější aktualizace service Pack nebo aktualizací.
Windows Server 2008 R2 SP1 64 bitů | Standard, Enterprise, Datacenter, Foundation | S nejnovějšími aktualizacemi.
Windows Server 2008 64 bit | Standard, Enterprise, Datacenter | S nejnovějšími aktualizacemi.

Zálohování virtuálních počítačů Azure s Linuxem, Azure Backup podporuje [seznam distribucí schválených pro Azure](../virtual-machines/linux/endorsed-distros.md), kromě základního OS Linux a 32bitový operační systém. Ostatní Linuxové distribuce přineste vlastní může pracovat za předpokladu, že agent virtuálního počítače je k dispozici na virtuálním počítači a podporovaný Python.


### <a name="are-there-size-limits-for-data-backup"></a>Existují omezení velikosti pro zálohování dat?
Omezení velikosti jsou následující:

/ Počítači s operačním systémem | Maximální velikost zdroje dat
--- | ---
Windows 8 nebo novější | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 nebo novější | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Virtuální počítač Azure | 16 datových disků<br/><br/> Datový disk až 4 095 GB

### <a name="how-is-the-data-source-size-determined"></a>Jak se určí velikost zdroje dat?
Následující tabulka vysvětluje, jak je určená velikost jednotlivých zdrojů dat.

**Zdroj dat** | **Podrobnosti**
--- | ---
Svazek |Množství dat, která zazálohovali z jednoho svazku zálohovaného virtuálního počítače.
Databáze systému SQL Server |Velikost zálohovaných jeden velikosti databáze SQL.
SharePoint | Součet databází obsahu a konfigurace v rámci zálohované farmy služby SharePoint.
Exchange |Součet všech databází systému Exchange v zálohování serveru Exchange server.
BMR/stav systému |Každá jednotlivá kopie BMR nebo stav systému zálohovaného počítače.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Existuje nějaké omezení množství dat zálohovaných pomocí trezoru služby Recovery Services?
Neexistuje žádné omezení na množství dat, která můžete zálohovat pomocí trezoru služby Recovery Services.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Proč je velikost dat, přenesena do trezoru služby Recovery Services menší než data vybraná pro zálohování?
Zálohovat data z Azure Backup Agent aplikace DPM, a komprimovaná a šifrovaná před přenášením Azure Backup serveru. Komprese a šifrování se použije, data v trezoru je 30 – 40 % menší.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Můžete odstranit jednotlivé soubory z bodu obnovení v trezoru?
Ne, Azure Backup nepodporuje odstranění nebo vyprazdňování jednotlivých položek z uložené zálohy.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Pokud zruším úlohu zálohování po jeho spuštění, je odstranění přenášených dat?
Ne. Všechna data, která bylo převedeno do trezoru před úloha zálohování byla zrušena zůstanou v trezoru.

- Azure Backup používá mechanismus kontrolních bodů k příležitostnému přidávání kontrolních bodů do zálohovaných dat během zálohování.
- Díky kontrolním bodům v zálohovaných datech je možné při dalším procesu zálohování ověřit integritu souborů.
- Následující zálohování proběhne jako přírůstkové vzhledem k naposledy zálohovaným datům. Přírůstkové zálohování přenáší jen nová nebo změněná data, což znamená lepší využití přenosové kapacity.

Když ve virtuálním počítači Azure zrušíte úlohu zálohování, budou dosud přenesená data ignorována. Při následujícím přírůstkovém zálohování se přenesou data, která se změnila od poslední úspěšně dokončené úlohy zálohování.

## <a name="retention-and-recovery"></a>Uchovávání a obnovení

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Shodné zásady uchovávání informací pro DPM a Windows počítače bez aplikace DPM?
Ano, obě mají zásady uchovávání informací pro denní, týdenní, měsíční nebo roční.

### <a name="can-i-customize-retention-policies"></a>Můžete přizpůsobit zásady uchovávání informací?
Ano, máte vlastní nastavení zásad. Můžete například nakonfigurovat každý týden a každý den uchování požadavky, ale není roční a měsíční.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Můžete použít různé časy pro plánování zálohování a zásady uchovávání informací?
Ne. Zásady uchovávání informací lze aplikovat pouze na body záloh. Tato Image příkladu zásady uchovávání informací pro zálohy pořízené v 00: 00 a 18: 00.

![Plánování zálohování a uchovávání](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Pokud zálohu, zůstane po dlouhou dobu, trvá obnovení staršího datového bodu déle? <br/>
Ne. Čas obnovení nejstaršího nebo nejnovější bod je stejný. Každý bod obnovení se chová jako úplný bod.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Jestliže se každý bod obnovení chová jako úplný bod, ovlivní to celkové fakturovatelné úložiště zálohování?
Typické produkty s dlouhodobými body uchování ukládají zálohovaná data jako úplné body.

- Úplné body jsou *neefektivní* z hlediska úložiště, ale jejich obnovení je snadnější a rychlejší.
- Přírůstkové kopie jsou úložiště *efektivní* ale vyžadují obnovení řetězu dat, což ovlivňuje dobu obnovení

Architektura úložiště Azure Backup nabízí to nejlepší z obou světů – optimální ukládání dat pro rychlé obnovení a nízké poplatky za úložiště. Tím se zajistí, že je efektivní využití příchozí i odchozí šířky pásma. Velikost úložiště dat a čas potřebný k obnovení dat, jsou omezeny na minimum. Další informace o [přírůstkové zálohování](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Je nějak omezený počet bodů obnovení, které lze vytvořit?
Na jednu chráněnou instanci je možné vytvořit až 9 999 bodů obnovení. Chráněná instance je počítač, server (fyzický nebo virtuální) nebo úloha, která zálohuje do Azure.

- Další informace o [zálohování a uchovávání](./backup-overview.md#backup-and-retention).


### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Jak často můžu obnovit data, která se zálohuje do Azure?
Počet obnovení z Azure Backup není omezený.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Platí se za výchozí přenos z Azure při obnovování dat?
Ne. Obnovení je zdarma a se vám neúčtují poplatky za výchozí přenos.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co se stane, když můj zásady zálohování?
Když se použije nová zásada, plán a uchovávání nové zásady následuje.

- Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu.
- Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.

## <a name="encryption"></a>Šifrování

### <a name="is-the-data-sent-to-azure-encrypted"></a>Jsou data odesílaná do Azure šifrovaná?
Ano. Data se šifrují na místním počítači pomocí AES256. Data se odesílají prostřednictvím zabezpečeného spojení HTTPS. Data přenášená do cloudu je chráněn odkazu HTTPS pouze mezi služby úložiště a obnovení. Protokol iSCSI zabezpečuje data přenášená mezi počítači pro obnovení služby a uživatele. Zabezpečené tunelové propojení se používá k ochraně kanál iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Jsou šifrovaná i zálohovaná data v Azure?
Ano. Data v Azure je zašifrovaná at-rest.

- Pro zálohování místních šifrování v klidovém stavu poskytuje heslem, které poskytnete při zálohování do Azure.
- Pro virtuální počítače Azure data jsou zašifrovaná – v klidovém stavu pomocí šifrování služby Storage (SSE).

Microsoft nikdy nedešifruje zálohovaná data.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Jaká je minimální délka šifrovací klíč používaný k šifrování zálohovaných dat?
Pokud používáte agenta Azure Backup, šifrovací klíč by měl obsahovat alespoň 16 znaků. Pro virtuální počítače Azure neplatí žádné omezení délky klíčů, které používá služba Azure Key Vault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co se stane, když ztratím šifrovací klíč? Můžete obnovit data? Může Microsoft obnovit data?
Klíč používaný k šifrování zálohovaných dat je k dispozici pouze ve vaší lokalitě. Microsoft neudržuje jeho kopii v Azure a nemá ke klíči žádný přístup. Pokud je někam nezaložili klíče, Microsoft nemůže zálohovaná data obnovit.

## <a name="next-steps"></a>Další postup

Přečtěte si další nejčastější dotazy:

- [Běžné otázky](backup-azure-vm-backup-faq.md) o záloh virtuálních počítačů Azure.
- [Běžné otázky](backup-azure-file-folder-backup-faq.md) o agenta Azure Backup
