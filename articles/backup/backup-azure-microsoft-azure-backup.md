---
title: Použití Azure Backup serveru k zálohování úloh do Azure
description: Použití Azure Backup serveru k ochraně nebo zálohování úloh na webu Azure portal.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: kasinh
ms.openlocfilehash: f81e7a0008c015c033d30045970fe1bd67597ff9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452183"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalace a upgrade Azure Backup serveru
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Tento článek vysvětluje postup přípravy prostředí pro zálohování úloh pomocí Microsoft Azure Backup Server (MABS). S Azure Backup Server můžete chránit úlohy aplikace, jako jsou virtuální počítače Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange a klientů Windows z jediné konzoly.

> [!NOTE]
> Azure Backup serveru teď mohou chránit virtuální počítače VMware a poskytuje možnosti lepší zabezpečení. Instalace produktu, jak je vysvětleno v níže uvedených částech a nejnovější verzi agenta Azure Backup. Další informace o zálohování serverů VMware pomocí Azure Backup serveru, najdete v článku, [použití Azure Backup serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md). Další informace o funkcích zabezpečení, naleznete [zabezpečení služby Azure backup nabízí dokumentaci](backup-azure-security-feature.md).
>
>

MABS nasazené ve Virtuálním počítači Azure můžete zálohovat virtuální počítače v Azure, ale by měl být ve stejné doméně, abyste umožnili operace zálohování. Proces zálohování virtuálního počítače Azure zůstane stejná jako zálohování virtuálních počítačů v místním prostředí, ale nasazení MABS v Azure má některá omezení. Další informace o omezení najdete v části [DPM jako virtuální počítač Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure nabízí dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek obsahuje informace a postupy pro obnovení virtuální počítače nasazené pomocí modelu Resource Manager.
>
>

Azure Backup serveru dědí většině funkcí úlohy zálohování z Data Protection Manager (DPM). Tento článek obsahuje odkazy na dokumentace k aplikaci DPM k vysvětlení některých funkcí sdílené. I když Azure Backup serveru sdílí řadu stejných funkcí jako aplikace DPM, Azure Backup Server nepodporuje zálohování na pásku, ani zajistíte jejich integraci s nástrojem System Center.

## <a name="choose-an-installation-platform"></a>Zvolte platformu pro instalaci
Prvním krokem k Azure Backup serveru zprovoznění je nastavit systém Windows Server. Váš server může být v Azure nebo místně.

### <a name="using-a-server-in-azure"></a>Použití serveru v Azure
Při výběru serveru ke spuštění Azure Backup Server, doporučujeme začít s Galerie imagí Windows serveru 2012 R2 Datacenter, Windows Server 2016 Datacenter nebo Windows Server. 2019 Datacenter. V článku [vytvořit svůj první virtuální počítač s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), kurz pro zahájení práce s doporučenou virtuálního počítače v Azure, i pokud jste nikdy Azure před. Doporučená minimální požadavky pro virtuální počítač (VM) serveru by měly být: A2 Standard s využitím dvou jader a 3,5 GB paměti RAM.

Ochrana zatížení pomocí Azure Backup serveru má mnoho detailů. V článku [instalace aplikace DPM jako virtuální počítač Azure](https://technet.microsoft.com/library/jj852163.aspx), pomáhá popisují tyto drobné rozdíly. Před nasazením počítače, přečtěte si tento článek úplně.

### <a name="using-an-on-premises-server"></a>Pomocí místního serveru
Pokud nechcete pro spuštění základního serveru v Azure, můžete spustit na serveru na virtuálních počítačů Hyper-V, virtuálních počítačů VMware nebo fyzické hostitele. Doporučená minimální požadavky na hardware serveru jsou dvě jádra a 4 GB paměti RAM. V následující tabulce jsou uvedeny podporované operační systémy:

| Operační systém | Platforma | Skladová jednotka (SKU) |
|:--- | --- |:--- |
| Windows Server 2019 |64bitová verze |Standard, Datacenter, Essentials (MABS V3 a novější) |
| Windows Server 2016 a nejnovější aktualizace Service packu |64bitová verze |Standard, Datacenter, Essentials (MABS V2 a novější) |
| Windows Server 2012 R2 a nejnovější aktualizace Service Packu |64bitová verze |Standard, Datacenter, Foundation |
| Windows Server 2012 a nejnovější aktualizace Service Packu |64bitová verze |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 a nejnovější aktualizace Service Packu |64bitová verze |Standard, Workgroup |
| Windows Storage Server 2012 a nejnovější aktualizace Service Packu |64bitová verze |Standard, Workgroup |

Odstranění duplicit dat úložiště DPM využívající odstranění duplicitních dat Windows serveru. Další informace o tom [aplikace DPM a odstranění duplicit](https://technet.microsoft.com/library/dn891438.aspx) spolupracují při nasazení ve virtuálních počítačích Hyper-V.

> [!NOTE]
> Azure Backup serveru je navržen pro spouštění na samostatném jednoúčelovém serveru. Azure Backup serveru nejde nainstalovat na:
> - Počítač spuštěný jako řadič domény
> - Počítač, na kterém je nainstalovaná role aplikačního serveru
> - Počítač, který je serverem pro správu nástroje System Center Operations Manager
> - Počítač, na kterém je spuštěný server Exchange
> - Počítač, který je uzlem clusteru

Vždy připojte se k Azure Backup serveru k doméně. Pokud budete chtít přesunout server do jiné domény, nejdříve nainstalujte Azure Backup serveru a pak připojte se k serveru na novou doménu. Přesunout existující počítač Azure Backup serveru do nové domény po nasazení *nepodporuje*.

Odesílání zálohovaných dat do Azure, nebo zachovat místně, musí být zaregistrovaný Azure Backup Server pomocí trezoru služby Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště
Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení používají trezory služby Recovery Services geograficky redundantní úložiště. Pokud se tento trezor je primární úložiště, ponechte možnost úložiště pro geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která není tak trvanlivá. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. Vyberte svůj trezor pro otevření řídícího panelu trezoru a nabídky nastavení. Pokud **nastavení** nabídky neotevře, klikněte na tlačítko **všechna nastavení** v řídicím panelu trezoru.
2. Na **nastavení** nabídky, klikněte na tlačítko **infrastruktura zálohování** > **konfigurace zálohování** otevřít **konfigurace zálohování**okno. Na **konfigurace zálohování** nabídek, zvolte pro svůj trezor možnost replikace úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Po výběru možnosti úložiště pro svůj trezor jste připraveni k přidružení virtuálního počítače k trezoru. Chcete-li začít přidružení, měli byste vyhledat a zaregistrovat virtuální počítače Azure.

## <a name="software-package"></a>Balíček softwaru
### <a name="downloading-the-software-package"></a>Stahuje se balíček softwaru
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Pokud již máte otevřený trezor služeb zotavení, pokračujte krokem 3. Pokud nemáte otevřený trezor služeb zotavení, ale jsou na webu Azure Portal, v hlavní nabídce klikněte na tlačítko **Procházet**.

   * V seznamu prostředků zadejte **Recovery Services**.
   * Během zadávání se seznam bude filtrovat podle zadávaného textu. Až uvidíte **Trezory Služeb zotavení**, klikněte na ně.

     ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Objeví se seznam trezorů Služeb zotavení.
   * Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.

     ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. **Nastavení** otevře se okno ve výchozím nastavení. Pokud je zavřená, klikněte na **nastavení** otevře se okno nastavení.

    ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klikněte na tlačítko **zálohování** otevřete Průvodce Začínáme.

    ![Začínáme se službou Backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    V **Začínáme se zálohováním** okno, které se otevře, **zálohovacích cílů** bude automaticky vybrána.

    ![Backup – cíle – výchozí – otevřít](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. V **cíl zálohování** okně z **ve kterém je spuštěná vaše úloha** nabídce vyberte možnost **On-premises**.

    ![místní a úlohy jako cíle](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z **co chcete zálohovat?** rozevírací nabídky vyberte úlohy, které chcete chránit pomocí Azure Backup Server a pak klikněte na **OK**.

    **Začínáme se zálohováním** Průvodce přepínače **připravit infrastrukturu** možnost zálohování úloh do Azure.

   > [!NOTE]
   > Pokud chcete zálohovat soubory a složky, doporučujeme pomocí agenta Azure Backup a pokynů v článku [stručně a přehledně: zálohování souborů a složek](backup-try-azure-backup-in-10-mins.md). Pokud chcete chránit více než soubory a složky, nebo máte v úmyslu rozbalte potřeby ochrany v budoucnosti, vyberte tyto úlohy.
   >
   >

    ![Získávání změnu Průvodce Začínáme](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. V **připravit infrastrukturu** okno, které se otevře, klikněte na tlačítko **Stáhnout** odkazy pro instalaci Azure Backup serveru a přihlašovací údaje trezoru stáhnout. Během registrace Azure Backup serveru k trezoru služby recovery services použít přihlašovací údaje trezoru. Pomocí odkazů můžete přejít na webu Download Center, kde si můžete stáhnout balíček softwaru.

    ![Příprava infrastruktury Azure Backup serveru](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Vyberte všechny soubory a klikněte na tlačítko **Další**. Stahujte přicházející ze stránky pro stažení aplikace Microsoft Azure Backup a umístěte všechny soubory ve stejné složce.

    ![Stažení softwaru 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Protože velikost ke stažení všechny soubory společně > 3G, na 10 MB/s odkaz ke stažení, které může trvat až 60 minut, než se stahování dokončí.

### <a name="extracting-the-software-package"></a>Extrahuje se balíček softwaru
Po stažení všechny soubory, klikněte na tlačítko **MicrosoftAzureBackupInstaller.exe**. Tím se spustí **Průvodce instalací nástroje Microsoft Azure Backup** k extrakci instalačních souborů do umístění, které jste zadali. Pokračujte v průvodci a klikněte na **extrahovat** tlačítko Zahájit proces extrakce.

> [!WARNING]
> Minimálně 4GB volného místa je potřeba extrahovat soubory instalačního programu.
>
>

![Průvodce instalací zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po dokončení procesu extrakce zaškrtněte políčko ke spuštění čerstvě extrahované *setup.exe* k zahájení instalace Microsoft Azure Backup serveru a klikněte na **Dokončit** tlačítko.

### <a name="installing-the-software-package"></a>Instaluje se balíček softwaru
1. Klikněte na tlačítko **Microsoft Azure Backup** spuštění Průvodce instalací.

    ![Průvodce instalací zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na úvodní obrazovce klikněte na tlačítko **Další** tlačítko. Tím přejdete *kontrol požadovaných součástí* oddílu. Na této obrazovce, klikněte na tlačítko **zkontrolujte** k určení, pokud byly splněny hardwarové a softwarové požadavky pro Azure Backup serveru. Pokud jsou úspěšně splněné všechny požadavky, zobrazí se zpráva označující, zda počítač splňuje požadavky. Klikněte na **Další** tlačítko.

    ![Kontrola Azure Backup Server – Vítejte a požadavky](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server vyžaduje SQL Server Enterprise. Dále instalační balíček Azure Backup serveru se dodává jako součást balíčku s odpovídající binární soubory systému SQL Server nutný v případě, že nechcete použít vlastní SQL. Při spuštění s novou instalací Azure Backup Server, měli byste vybrat možnost **nainstalovat nové Instance systému SQL Server s tímto nastavením** a klikněte na tlačítko **zkontrolovat a nainstalovat** tlačítko. Jakmile požadované součásti jsou úspěšně nainstalováni, klikněte na tlačítko **Další**.

    ![Azure Backup Server – kontrola SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Pokud dojde k selhání s doporučením k restartování počítače, Uděláte to tak a klikněte na tlačítko **zkontrolovat znovu**. V případě jakékoli problémy s konfigurací SQL Rekonfigurace SQL podle pokynů SQL a pokusí nainstalovat nebo upgradovat MABS pomocí existující instanci serveru SQL.

   > [!NOTE]
   > Azure Backup serveru nebude fungovat u vzdálené instance systému SQL Server. Instance používá Azure Backup serveru musí být místní. V případě, že používáte existující server SQL pro MABS, instalací MABS podporuje pouze používání *pojmenované instance* systému SQL server.

   **Ruční konfigurace**

   Pokud používáte vlastní instanci serveru SQL, ujistěte se, že přidejte builtin\Administrators do role sysadmin k hlavní databázi.

    **Konfigurace služby SSRS 2017 SQL**

    Pokud používáte vlastní instanci SQL 2017, budete muset ručně konfigurovat služby SSRS. Po konfiguraci služby SSRS, ujistěte se, že *IsInitialized* služby SSRS je nastavena na *True*. Pokud je nastavené na True, MABS předpokládá, že služby SSRS je už nakonfigurovaná a přeskočí konfigurace služby SSRS.

    Použijte následující hodnoty pro konfiguraci služby SSRS:

        - Service Account: ‘Use built-in account’ should be Network Service
        - Web Service URL: ‘Virtual Directory’ should be ReportServer_<SQLInstanceName>
        - Database: DatabaseName should be ReportServer$<SQLInstanceName>
        - Web Portal URL: ‘Virtual Directory’ should be Reports_<SQLInstanceName>

    [Další informace](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguraci služby SSRS.

4. Zadejte umístění pro instalační soubory aplikace Microsoft Azure Backup serveru a klikněte na **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Pomocné umístění představuje požadavek pro zálohy do Azure. Ujistěte se, že pomocné umístění je minimálně 5 % dat plánované zálohování do cloudu. Pro ochranu na disku je potřeba nakonfigurovat po dokončení instalace různých discích. Další informace o fondech úložiště, najdete v části [nakonfigurujte fondy úložiště a diskové úložiště](https://technet.microsoft.com/library/hh758075.aspx).
5. Zadejte silné heslo pro omezené místní uživatelské účty a klikněte na **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Vyberte, zda chcete použít *Microsoft Update* vyhledat aktualizace, a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme mít aktualizaci Windows přesměrována na Microsoft Update, která nabízí zabezpečení a důležité aktualizace pro Windows a dalších produktů, jako je Microsoft Azure Backup serveru.
   >
   >

    ![PreReq2 zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Zkontrolujte *souhrn nastavení* a klikněte na tlačítko **nainstalovat**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalace se stane ve fázích. V první fázi je na serveru nainstalován Microsoft Azure Recovery Services Agent. Průvodce také kontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu může pokračovat v instalaci, v opačném případě je potřeba zadat podrobnosti o proxy serveru pro připojení k Internetu.

    Dalším krokem je konfigurace agenta Microsoft Azure Recovery Services. Jako součást konfigurace budete muset zadejte svoje přihlašovací údaje trezoru se zaregistrovat počítač pro trezor služby recovery services. Bude také zadejte heslo pro šifrování/dešifrování data přenášená mezi Azure a místním. Můžete automaticky generovat přístupové heslo nebo zadat minimální heslo 16 znaků. Dokud agent nakonfigurovaný, pokračujte v průvodci.

    ![Azure Backup Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po úspěšném dokončení registrace Microsoft Azure Backup serveru na instalaci a konfiguraci systému SQL Server a komponenty Azure Backup serveru pokračuje celkové Průvodce instalací. Po dokončení instalace součásti SQL serveru, jsou nainstalované komponenty Azure Backup serveru.

    ![Server Azure Backup](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po dokončení kroku instalace ikony na ploše produkt bude vytvořena také. Pouze dvakrát klikněte na ikonu ke spuštění produktu.

### <a name="add-backup-storage"></a>Přidání úložiště záloh
První zálohy se ukládají na úložiště připojené k počítači Azure Backup Server. Další informace o přidávání disků najdete v tématu [nakonfigurujte fondy úložiště a diskové úložiště](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Budete muset přidat úložiště pro zálohování i v případě, že máte v úmyslu odesílání dat do Azure. V aktuální architektuře Azure Backup Server Azure Backup vault uchovává *druhý* kopii dat při místní úložiště obsahuje záložní kopie prvnímu (zároveň povinná).
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalace a aktualizace agenta ochrany aplikace Data Protection Manager

MABS používá System Center Data Protection Manager agenta ochrany. [Tady jsou kroky](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) k instalaci agenta ochrany na serverech ochrany.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače.

1. V konzole správce zálohování serveru vyberte **správu** > **agentů**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenty ochrany.

  > [!NOTE]
  > **Aktualizací agenta** sloupec znamená, když je k dispozici pro každý chráněný počítač aktualizace agenta ochrany. V **akce** podokně **aktualizace** akce je dostupná, jenom když je vybrán chráněný počítač a jsou k dispozici aktualizace.
  >
  >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybraných počítačích, **akce** vyberte **aktualizace**.

4. Abyste klientskému počítači, který není připojen k síti, dokud je počítač připojen k síti **stav agenta** sloupci se zobrazuje stav **aktualizace čeká na vyřízení**.

  Až klientský počítač je připojený k síti, **aktualizací agenta** sloupec pro klientský počítač zobrazuje stav **aktualizace**.

## <a name="move-mabs-to-a-new-server"></a>Přesunout MABS na nový server

Pokud potřebujete přesunout MABS na nový server, a přitom zachovat úložiště tady jsou kroky. To lze provést pouze v případě, že se všechna data na moderní úložiště záloh.


  > [!IMPORTANT]
  > - Nový název serveru musí být stejný název jako původní instanci Azure Backup serveru. Název nové instance Azure Backup serveru nelze změnit, pokud chcete zachovat body obnovení pomocí předchozího fondu úložiště a databáze aplikace Data Protection Manager.
  > - Musíte mít zálohu databáze aplikace Data Protection Manager. Budete potřebovat k obnovení databáze.

1. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenty ochrany.
2. Vypnutí původní Azure backup server nebo trvat vypnutí přenosu.
3. Obnovit účet počítače ve službě active directory.
4. Nainstalujte Server 2016 na nový počítač a pojmenujte ho stejný název počítače jako původní server Azure Backup.
5. Připojení k doméně
6. Instalace Azure Backup server verze 2 nebo novější (přesunout DPM disky fondu úložiště z původního serveru a import)
7. Obnovení databáze aplikace DPMDB prováděné v kroku 1.
8. Připojte úložiště z původního zálohování serveru na nový server.
9. Obnovení SQL databázi DPMDB
10. Z příkazového řádku správce na nový disk cd serveru do služby Microsoft Azure Backup nainstalujte umístění a složky bin

Příklad cesty: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
do Azure zálohovat spusťte DPMSYNC-SYNC

10) Spusťte nástroj DPMSYNC-SYNC Poznámka: Pokud nové disky, které jste přidali do fondu úložiště DPM namísto přesouvání staré, pak spusťte DPMSYNC - Reallocatereplica

## <a name="network-connectivity"></a>Připojení k síti
Azure Backup Server vyžaduje připojení ke službě Azure Backup pro produkt nemusí fungovat správně. Chcete-li ověřit, zda je počítač připojen k Azure, použijte ```Get-DPMCloudConnection``` rutiny v konzole prostředí PowerShell pro Azure Backup Server. Pokud výstup této rutiny je hodnota TRUE, pak připojení existuje, jinak není žádné připojení.

Předplatné Azure, které ve stejnou dobu, musí být ve stavu v pořádku. Zjistěte stav svého předplatného a spravovat, přihlaste se k [portál předplatného](https://account.windowsazure.com/Subscriptions).

Jakmile budete vědět o stavu možnosti připojení Azure a předplatné Azure, můžete zjistit dopad na zálohování a obnovení funkce nabízené v tabulce níže.

| Stav připojení | předplatné Azure | Zálohování do Azure | Zálohování na disk | Obnovení z Azure | Obnovení z disku |
| --- | --- | --- | --- | --- | --- |
| Připojeno |Aktivní |Povoleno |Povoleno |Povoleno |Povoleno |
| Připojeno |Vypršela platnost |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Připojeno |Zrušení zřízení |Zastaveno |Zastaveno |Body obnovení zastavena a Azure odstranit |Zastaveno |
| Došlo ke ztrátě připojení > 15 dnů |Aktivní |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Došlo ke ztrátě připojení > 15 dnů |Vypršela platnost |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Došlo ke ztrátě připojení > 15 dnů |Zrušení zřízení |Zastaveno |Zastaveno |Body obnovení zastavena a Azure odstranit |Zastaveno |

### <a name="recovering-from-loss-of-connectivity"></a>Obnovit ze ztráty připojení
Pokud máte bránu firewall nebo proxy server, který brání přístupu k Azure, potřebujete na seznam povolených následující domény adresy v profilu nebo proxy server brány firewall:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Po připojení k Azure se obnovila na počítač serveru Azure Backup, operace, které lze provést Určuje stav předplatného Azure. Výše uvedené tabulce obsahuje podrobnosti o operacích povolená, když je počítač se "připojeno".

### <a name="handling-subscription-states"></a>Zpracování – stavy předplatného
Je možné provést z předplatného Azure *konec platnosti* nebo *zajištění zrušeno* do stavu *aktivní* stavu. Ale tato akce nemá vliv na některé na chování produktu stav není *aktivní*:

* A *zajištění zrušeno* předplatné ztratí funkce pro dobu, po kterou je zrušení zřízení. Na zapnutí *aktivní*, obnovená funkce produktu pro zálohování a obnovení. Zálohovaná data na místním disku můžete získat také v případě, že se ukázal jako nedocenitelný s dobou uchování dostatečně velké. Data záloh v Azure je však nenahraditelně ztraceny, jakmile vstoupí předplatného *zajištění zrušeno* stavu.
* *Neplatné* předplatné pouze ztratí funkce pro, dokud se neprovedl *aktivní* znovu. Všechny zálohy naplánovanou dobu, po kterou přihlášení k odběru se *neplatné* se nespustí.

## <a name="upgrade-mabs"></a>Upgrade MABS
Pomocí následujících postupů můžete upgradovat MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade z MABS V2 na V3

> [!NOTE]

> MABS V2 není předpoklady pro instalaci MABS V3. Ale můžete upgradovat na MABS V3 pouze z MABS V2.

Pro upgrade MABS použijte následující kroky:

1. Upgrade MABS V2 na MABS V3, upgradujte svůj operační systém na Windows Server 2016 nebo Windows Server 2019 v případě potřeby.

2.  Upgrade serveru. Kroky jsou podobné [instalace](#install-and-upgrade-azure-backup-server). Pro nastavení SQL, pomocí možnosti pro upgrade na SQL 2017 vaší instanci SQL, nebo použít vlastní instanci systému SQL server 2017 získáte.

  > [!NOTE]

  > Neukončovat se upgraduje vaše instance SQL, ukončí se odinstaluje instance generování sestav SQL a proto se nezdaří pokus o upgrade znovu MABS.

  Co je důležité si uvědomit:

  > [!IMPORTANT]

  >  Jako součást upgradu SQL 2017 jsme zálohovat šifrovací klíče SQL a odinstalaci služby reporting services. Po upgradu SQL server reporting je nainstalován service(14.0.6827.4788) & obnoví šifrovací klíče.

 > Při konfiguraci SQL 2017 ručně, najdete *konfigurace služby SSRS 2017 SQL* části Postup instalace.

3. Aktualizujte agenty ochrany na chráněných serverech.
4. Zálohování by měly pokračovat bez potřeby restartu provozních serverech.
5. Můžete začít nyní chrání vaše data. Pokud provádíte upgrade na moderní úložiště záloh, při ochraně, můžete také svazky, které chcete ukládat zálohy v a vyhledejte v části zřízené místo. [Další informace](backup-mabs-add-storage.md).

> [!NOTE]

> Pokud upgradujete z MABS V1 na V2, ujistěte se, že váš operační systém Windows Server 2016 nebo Windows Server 2012 R2. Abyste mohli využívat nové funkce, jako je systém System Center 2016 Data Protection Manager moderní úložiště záloh, je nutné nainstalovat V2 zálohování serveru ve Windows serveru 2016. Před upgradováním na nebo instalace zálohování serveru V2, přečtěte si informace o [požadavky na instalaci](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) pro MABS.

## <a name="troubleshooting"></a>Řešení potíží
Pokud Microsoft Azure Backup serveru nezdaří s chybami během fáze instalace (nebo zálohování nebo obnovení), podívejte se na to [chybové kódy dokumentu](https://support.microsoft.com/kb/3041338) Další informace.
Můžete také odkazovat na [nejčastější dotazy týkající se Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další postup
Můžete získat podrobné informace o [Příprava prostředí pro aplikaci DPM](https://technet.microsoft.com/library/hh758176.aspx) na webu Microsoft TechNet. Obsahuje také informace o podporovaných konfiguracích, na kterých je možné Azure Backup Server nasadit a používat. Můžete použít řadu [rutinu Powershellu](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) pro provádění různých operací.

Tyto články můžete získat lepší představu o ochranu úloh pomocí Microsoft Azure Backup serveru.

* [Zálohování serveru SQL Server](backup-azure-backup-sql.md)
* [Zálohování serveru SharePoint](backup-azure-backup-sharepoint.md)
* [Alternativní server backup](backup-azure-alternate-dpm-server.md)
