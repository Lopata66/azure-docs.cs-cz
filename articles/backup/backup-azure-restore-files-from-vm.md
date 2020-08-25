---
title: Obnovení souborů a složek ze zálohy virtuálního počítače Azure
description: V tomto článku se dozvíte, jak obnovit soubory a složky z bodu obnovení virtuálního počítače Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: ac121195ba46389798acc7f099829fde96da72e1
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827133"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Azure Backup poskytuje možnost obnovení [virtuálních počítačů Azure a disků](./backup-azure-arm-restore-vms.md) ze záloh virtuálních počítačů Azure, označovaných také jako body obnovení. Tento článek vysvětluje, jak obnovit soubory a složky ze zálohy virtuálního počítače Azure. Obnovování souborů a složek je k dispozici pouze pro virtuální počítače Azure nasazené pomocí modelu Správce prostředků a chráněny do trezoru Recovery Services.

> [!NOTE]
> Tato funkce je k dispozici pro virtuální počítače Azure nasazené pomocí modelu Správce prostředků a chráněných do trezoru Recovery Services.
> Obnovení souborů ze zašifrované zálohy virtuálního počítače se nepodporuje.
>

## <a name="mount-the-volume-and-copy-files"></a>Připojení svazku a zkopírování souborů

Chcete-li obnovit soubory nebo složky z bodu obnovení, přejděte na virtuální počítač a vyberte požadovaný bod obnovení.

1. Přihlaste se k [Azure Portal](https://portal.Azure.com) a v levém podokně vyberte **virtuální počítače**. V seznamu virtuálních počítačů vyberte virtuální počítač a otevřete tak řídicí panel tohoto virtuálního počítače.

2. V nabídce virtuálního počítače vyberte **zálohování** a otevřete řídicí panel zálohování.

    ![Otevřít zálohovanou položku trezoru Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. V nabídce řídicího panelu zálohování vyberte **obnovení souboru**.

    ![Vybrat obnovení souboru](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Otevře se nabídka **obnovení souborů** .

    ![Nabídka obnovení souborů](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. V rozevírací nabídce **Vyberte bod obnovení** vyberte bod obnovení, který obsahuje soubory, které chcete. Ve výchozím nastavení je již vybrán nejnovější bod obnovení.

5. Pokud chcete stáhnout software, který se používá ke kopírování souborů z bodu obnovení, vyberte **stáhnout spustitelný soubor** (pro virtuální počítače s Windows Azure) nebo **stáhnout skript** (pro virtuální počítače se systémem Linux Azure se vygeneruje skript Pythonu).

    ![Stáhnout spustitelný soubor](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure stáhne spustitelný soubor nebo skript do místního počítače.

    ![Stáhnout zprávu pro spustitelný soubor nebo skript](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Ke spuštění spustitelného souboru nebo skriptu jako správce doporučujeme uložit stažený soubor do počítače.

6. Spustitelný soubor nebo skript je chráněn heslem a vyžaduje heslo. V nabídce **obnovení souboru** vyberte tlačítko Kopírovat a načtěte heslo do paměti.

    ![Generované heslo](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Ujistěte se, že [máte správný počítač](#selecting-the-right-machine-to-run-the-script) pro spuštění skriptu. Pokud je pravý počítač stejný jako počítač, do kterého jste stáhli skript, můžete pokračovat do části ke stažení. Z umístění pro stahování (obvykle složky *stažené soubory* ) klikněte pravým tlačítkem na spustitelný soubor nebo skript a spusťte ho s přihlašovacími údaji správce. Po zobrazení výzvy zadejte heslo nebo vložte heslo z paměti a stiskněte klávesu **ENTER**. Po zadání platného hesla se skript připojí k bodu obnovení.

    ![Spustitelný výstup](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Pro počítače se systémem Linux se vygeneruje skript Pythonu. Jeden potřebuje stáhnout skript a zkopírovat ho na relevantní/kompatibilní server Linux. Možná budete muset změnit oprávnění k provedení ```chmod +x <python file name>``` . Pak spusťte soubor Python s nástrojem ```./<python file name>``` .

V části [požadavky na přístup](#access-requirements) se ujistěte, že se skript úspěšně spouští.

### <a name="identifying-volumes"></a>Identifikace svazků

#### <a name="for-windows"></a>Pro Windows

Když spustíte spustitelný soubor, operační systém tyto nové svazky připojí a přiřadí písmena jednotek. K procházení těchto jednotek můžete použít Průzkumníka Windows nebo Průzkumníka souborů. Písmena jednotek přiřazená ke svazkům nemusí být shodná s písmeny, která jsou v původním virtuálním počítači. Název svazku se ale zachová. Pokud je například svazek na původním virtuálním počítači "datový disk (E: `\` )", může být tento svazek připojen v místním počítači jako datový disk (libovolné písmeno ': `\` ). Procházejte všemi svazky uvedenými ve výstupu skriptu, dokud nenajdete soubory nebo složku.  

   ![Připojené svazky pro obnovení](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Pro Linux

V systému Linux jsou svazky bodu obnovení připojeny ke složce, ve které je skript spuštěn. Zobrazí se odpovídající připojené disky, svazky a odpovídající cesty připojení. Tyto cesty připojení jsou viditelné uživatelům, kteří mají přístup na úrovni root. Procházejte svazky uvedené ve výstupu skriptu.

  ![Nabídka obnovení souborů pro Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Uzavírání připojení

Po zjištění souborů a jejich jejich zkopírování do umístění místního úložiště odeberte (nebo odpojte) další jednotky. Chcete-li odpojit jednotky, vyberte v nabídce **obnovení souboru** v Azure Portal možnost **Odpojit disky**.

![Odpojit disky](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Po odpojení disků se zobrazí zpráva. Aktualizace připojení může trvat několik minut, aby bylo možné disky odebrat.

V systému Linux se po navázání připojení k bodu obnovení neodstraní příslušné cesty připojení automaticky. Cesty pro připojení existují jako "osamocené" svazky a jsou viditelné, ale při přístupu k souborům a jejich zapisování vyvolávají chybu. Je možné je odebrat ručně. Skript při spuštění identifikuje všechny takové svazky existující z předchozích bodů obnovení a vyčistí je na základě souhlasu.

> [!NOTE]
> Ujistěte se, že připojení bylo po obnovení požadovaných souborů zavřeno. To je důležité, zejména v případě, kdy je počítač, ve kterém je spuštěný skript, nakonfigurovaný taky pro zálohování. Pokud je připojení stále otevřené, může následné zálohování selhat s chybou "UserErrorUnableToOpenMount". Důvodem je, že připojené jednotky nebo svazky se považují za dostupné a když k nim dojde, můžou selhat, protože základní úložiště, které je cílový server iSCSI, nemusí být k dispozici. Vymazáním připojení dojde k odebrání těchto jednotek a svazků, takže nebudou během zálohování k dispozici.

## <a name="selecting-the-right-machine-to-run-the-script"></a>Výběr správného počítače ke spuštění skriptu

Pokud byl skript úspěšně stažen, je dalším krokem ověření, zda je počítač, na kterém chcete skript spustit, pravý počítač. Níže jsou uvedené požadavky, které je třeba splnit v počítači.

### <a name="original-backed-up-machine-versus-another-machine"></a>Původní zálohovaný počítač versus jiný počítač

1. Pokud je zálohovaným počítačem velký virtuální počítač s velkým diskem – to znamená, že počet disků je větší než 16 disků nebo je každý disk větší než 4 TB, je **nutné skript spustit na jiném počítači** a [tyto požadavky](#file-recovery-from-virtual-machine-backups-having-large-disks) musí být splněny.
1. I v případě, že zálohovaný počítač není virtuálním počítačem s velkým diskem, v [těchto scénářích](#special-configurations) se skript nedá spustit na stejném ZÁLOHOVANém virtuálním počítači.

### <a name="os-requirements-on-the-machine"></a>Požadavky na operační systém v počítači

Počítač, ve kterém se skript musí spustit, musí splňovat [tyto požadavky na operační systém](#system-requirements).

### <a name="access-requirements-for-the-machine"></a>Požadavky na přístup k počítači

Počítač, ve kterém musí být spuštěn skript, musí splňovat [tyto požadavky na přístup](#access-requirements).

## <a name="special-configurations"></a>Speciální konfigurace

### <a name="dynamic-disks"></a>Dynamické disky

Pokud má chráněný virtuální počítač Azure svazky s jedním nebo oběma následujícími charakteristikami, nemůžete spustit spustitelný skript na stejném virtuálním počítači.

- Svazky, které jsou rozloženy na více discích (rozložené a prokládané svazky)
- Svazky odolné proti chybám (zrcadlené svazky a svazky RAID-5) na dynamických discích

Místo toho spusťte spustitelný skript na jakémkoli jiném počítači s kompatibilním operačním systémem.

### <a name="windows-storage-spaces"></a>Prostory úložiště ve Windows

Prostory úložiště Windows jsou technologie systému Windows, která umožňuje Virtualizovat úložiště. Prostory úložiště Windows umožňují seskupit standardní disky do fondů úložiště. Pak použijete volné místo v těchto fondech úložiště k vytvoření virtuálních disků nazývaných prostory úložiště.

Pokud chráněný virtuální počítač Azure používá prostory úložiště Windows, nemůžete spustit spustitelný skript na stejném virtuálním počítači. Místo toho spusťte spustitelný skript na jakémkoli jiném počítači s kompatibilním operačním systémem.

### <a name="lvmraid-arrays"></a>LVM/pole RAID

V systému Linux se ke správě logických svazků na více discích používají pole LVM (Logical Volume Manager) nebo software RAID. Pokud chráněný virtuální počítač se systémem Linux používá LVM a/nebo pole RAID, nemůžete skript spustit na stejném virtuálním počítači. Místo toho spusťte skript na jakémkoli jiném počítači s kompatibilním operačním systémem, který podporuje systém souborů chráněného virtuálního počítače.

Následující výstup skriptu zobrazí disková pole LVM nebo RAID a svazky s typem oddílu.

   ![Nabídka výstup pro Linux LVM](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Chcete-li tyto oddíly převést do režimu online, spusťte příkazy v následujících částech.

#### <a name="for-lvm-partitions"></a>Pro LVM oddíly

Po spuštění skriptu jsou oddíly LVM připojeny ve fyzických nebo/diskch fyzických svazcích určených ve výstupu skriptu. Proces je

1. Získat jedinečný seznam názvů skupin svazků z fyzických svazků nebo disků
2. Pak vytvořte seznam logických svazků v těchto skupinách svazků.
3. Pak připojte logické svazky k požadované cestě.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>Výpis názvů skupin svazků z fyzických svazků

Seznam názvů skupin svazků:

```bash
pvs -o +vguuid
```

Tento příkaz zobrazí seznam všech fyzických svazků (včetně těch, které jsou k dispozici před spuštěním skriptu), jejich odpovídající názvy skupin svazků a jedinečné identifikátory uživatelů (UUID) skupiny svazků. Příklad výstupu příkazu je uveden níže.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

První sloupec (PV) zobrazuje fyzický svazek. následující sloupce zobrazují relevantní název skupiny svazků, formát, atributy, velikost, volné místo a jedinečné ID skupiny svazků. Výstup příkazu zobrazuje všechny fyzické svazky. Podívejte se na výstup skriptu a Identifikujte svazky související se zálohováním. Ve výše uvedeném příkladu by byl výstup skriptu zobrazený jako/dev/SDF a/dev/SDD.. A takže skupina svazků *datavg_db* patří do skriptu a skupina svazků *Appvg_new* patří do tohoto počítače. Posledním účelem je zajistit, aby měl jedinečný název skupiny svazků jedno jedinečné ID.

###### <a name="duplicate-volume-groups"></a>Duplicitní skupiny svazků

K dispozici jsou situace, kdy názvy skupin svazků můžou po spuštění skriptu obsahovat 2 identifikátory UUID. Znamená to, že názvy skupin svazků v počítači, kde je skript spuštěný, a v zálohovaném virtuálním počítači jsou stejné. Pak musíme přejmenovat skupiny svazků zálohovaných virtuálních počítačů. Podívejte se na příklad níže.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

Výstup skriptu by zobrazil/dev/SDG,/dev/SDH,/dev/sdm2 jako připojený. Proto jsou odpovídající názvy VG Appvg_new a rootvg. Ale v seznamu VG počítače se taky nacházejí stejné názvy. Můžeme ověřit, že jeden VG název má dva identifikátory UUID.

Nyní musíme přejmenovat VG názvy pro svazky založené na skriptech, například:/dev/SDG,/dev/SDH,/dev/sdm2. Pokud chcete přejmenovat skupinu svazků, použijte následující příkaz.

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

Teď máme všechna VG jména s jedinečnými identifikátory.

###### <a name="active-volume-groups"></a>Aktivní skupiny svazků

Ujistěte se, že jsou aktivní skupiny svazků odpovídající svazkům skriptu. Následující příkaz slouží k zobrazení aktivních skupin svazků. Ověřte, zda se v tomto seznamu vyskytují skupiny svazků související s tímto skriptem.

```bash
vgdisplay -a
```  

Jinak aktivujte skupinu svazků pomocí následujícího příkazu.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>Výpis logických svazků v rámci skupin svazků

Jakmile získáme jedinečný a aktivní seznam VGs týkajících se skriptu, logické svazky přítomné v těchto skupinách svazků můžou být uvedené pomocí následujícího příkazu.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

Tento příkaz zobrazí cestu k jednotlivým logickým svazkům jako "LV Path".

##### <a name="mounting-logical-volumes"></a>Připojování logických svazků

Připojení logických svazků k vybrané cestě:

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> Nepoužívejte příkaz "Mount-a". Tento příkaz připojí všechna zařízení popsaná v '/etc/fstab '. To může znamenat, že se může připojit k duplicitním zařízením. Data je možné přesměrovat na zařízení vytvořená skriptem, která data neuchovávají, takže by mohlo dojít ke ztrátě dat.

#### <a name="for-raid-arrays"></a>Pro pole RAID

Následující příkaz zobrazí podrobnosti o všech discích RAID:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Příslušný disk RAID se zobrazí jako `/dev/mdm/<RAID array name in the protected VM>`

Použijte příkaz Mount, pokud má disk RAID fyzické svazky:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Pokud je v disku RAID nakonfigurovaný jiný LVM, použijte předchozí postup pro oddíly LVM, ale místo názvu disku RAID použijte název svazku.

## <a name="system-requirements"></a>Požadavky na systém

### <a name="for-windows-os"></a>OPERAČNÍ systém Windows

V následující tabulce je uvedena kompatibilita mezi operačním systémem serveru a počítače. Při obnovování souborů nemůžete obnovit soubory na předchozí nebo budoucí verzi operačního systému. Například nemůžete obnovit soubor z virtuálního počítače s Windows serverem 2016 na počítač se systémem Windows Server 2012 nebo Windows 8. Můžete obnovit soubory z virtuálního počítače na stejný serverový operační systém nebo do kompatibilního klientského operačního systému.

|Operační systém serveru | Kompatibilní klientský operační systém  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Pro Linux OS

V systému Linux musí operační systém počítače používaného k obnovení souborů podporovat systém souborů chráněného virtuálního počítače. Když vyberete počítač pro spuštění skriptu, ujistěte se, že počítač má kompatibilní operační systém a používá jednu z verzí identifikovanou v následující tabulce:

|Operační systém Linux | Verze  |
| --------------- | ---- |
| Ubuntu | 12,04 a vyšší |
| CentOS | 6,5 a vyšší  |
| RHEL | 6,7 a vyšší |
| Debian | 7 a vyšší |
| Oracle Linux | 6,4 a vyšší |
| SLES | 12 a vyšší |
| openSUSE | 42,2 a vyšší |

> [!NOTE]
> Zjistili jsme některé problémy s spuštěním skriptu pro obnovení souboru na počítačích s operačním systémem SLES 12 SP4 a prozkoumáme ho s týmem SLES.
> V současné době spouštění skriptu pro obnovení souborů pracuje na počítačích s verzemi operačních systémů SLES 12 SP2 a SP3.
>

Skript také vyžaduje, aby byly součásti Python a bash spouštěny a bezpečně připojeny k bodu obnovení.

|Součást | Verze  |
| --------------- | ---- |
| bash | 4 a vyšší |
| python | 2.6.6 a vyšší  |
| TLS | 1,2 by měla být podporovaná.  |

## <a name="access-requirements"></a>Požadavky na přístup

Pokud skript spustíte na počítači s omezeným přístupem, ujistěte se, že máte přístup k těmto akcím:

- `download.microsoft.com`
- Adresy URL služby obnovení (GEO-Name) odkazují na oblast, ve které se nachází trezor Recovery Services.)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (Pro veřejné oblasti Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn` (Pro Azure Čína 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us` (Pro státní správu USA Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de` (Pro Azure Německo)
- Odchozí porty 53 (DNS), 443, 3260

> [!NOTE]
>
> - Název souboru staženého skriptu bude mít v adrese URL zadán **geografickou příponu** . Příklad: název staženého skriptu začíná řetězcem \' VMname \' \_ \' name \' _ \' GUID, například \' *ContosoVM_wcus_12345678*
> - Adresa URL bude <https://pod01-rec2.wcus.backup.windowsazure.com> "
>

Pro Linux skript vyžaduje pro připojení k bodu obnovení komponenty "Open-iSCSI" a "lshw". Pokud komponenty v počítači, na kterém je spuštěn skript, neexistují, skript si vyžádá oprávnění k instalaci součástí. Poskytněte souhlas pro instalaci nezbytných součástí.

Přístup ke službě `download.microsoft.com` je vyžadován ke stažení komponent používaných k vytvoření zabezpečeného kanálu mezi počítačem, na kterém je skript spuštěn, a daty v bodu obnovení.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Obnovení souborů ze záloh virtuálních počítačů s velkými disky

V této části se dozvíte, jak provést obnovení souborů ze záloh virtuálních počítačů Azure s více než 16 disky nebo pokud je velikost každého disku větší než 4 TB.

Vzhledem k tomu, že proces obnovy souborů připojí všechny disky ze zálohy, když je použit velký počet disků (>16) nebo velké disky (> 4 TB), jsou doporučeny následující body akcí:

- Pro obnovení souborů ponechte samostatný server pro obnovení (virtuální počítače Azure s D2v3). Tuto možnost můžete použít pouze pro obnovení souboru a pak ji vypnout, pokud to není nutné. Obnovení původního počítače se nedoporučuje, protože bude mít významný dopad na samotný virtuální počítač.
- Pak skript spusťte jednou, abyste zkontrolovali, jestli je operace obnovení souborů úspěšná.
- Pokud proces obnovení souboru přestane reagovat (disky se nikdy nepřipojují nebo jsou připojené, ale svazky se neobjeví), proveďte následující kroky.
  - Pokud je server pro obnovení virtuální počítač s Windows:
    - Ujistěte se, že operační systém je WS 2012 nebo vyšší.
    - Zajistěte, aby byly na serveru pro obnovení nastaveny klíče registru, jak je navrženo, a nezapomeňte restartovat server. Číslo vedle identifikátoru GUID může být v rozsahu od 0001-0005. V následujícím příkladu je to 0,0004. Procházejte cestou klíče registru do části Parameters (parametry).

    ![Změny klíčů registru](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Pokud je server pro obnovení virtuálním počítačem se systémem Linux:
  - V souboru/etc/iSCSI/iscsid.conf změňte nastavení z:
    - `node.conn[0].timeo.noop_out_timeout = 5`  schopn `node.conn[0].timeo.noop_out_timeout = 30`
- Po provedení změny výše spusťte skript znovu. U těchto změn je vysoce pravděpodobné, že obnovení souboru bude úspěšné.
- Pokaždé, když uživatel stáhne skript, Azure Backup zahájí proces přípravy bodu obnovení ke stažení. U velkých disků bude tento proces trvat značnou dobu. Pokud dojde k následným nárůstům požadavků, cílová Příprava přejde ke stažení spirály. Proto se doporučuje stáhnout skript z portálu/PowerShell/CLI, počkat po 20-30 minut (Heuristická) a pak ji spustit. V tuto chvíli se očekává, že cíl bude připravený pro připojení ze skriptu.
- Po obnovení souboru se vraťte na portál a vyberte Odpojit **disky** pro body obnovení, ve kterých jste nedokázali připojit svazky. V podstatě tento krok vyčistí všechny existující procesy a relace a zvýší pravděpodobnost obnovení.

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte při obnovování souborů z virtuálních počítačů problémy, další informace najdete v následující tabulce.

| Chybová zpráva/scénář | Pravděpodobná příčina | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Výstup exe: *zachytila se výjimka při připojování k cíli* . | Skript nemůže získat přístup k bodu obnovení.    | Ověřte, zda počítač splňuje [předchozí požadavky na přístup](#access-requirements). |  
| Výstup exe: *cíl již byl přihlášen prostřednictvím relace iSCSI.* | Skript již byl spuštěn na stejném počítači a jednotky byly připojeny. | Svazky bodu obnovení již byly připojeny. Nemusí být připojené se stejnými písmeny jednotky původního virtuálního počítače. Procházejte všemi dostupnými svazky v Průzkumníkovi souborů pro váš soubor. |
| Výstup z exe: *Tento skript je neplatný, protože disky byly odpojeny přes portál nebo překročily limit 12 hodin. Stáhněte si nový skript z portálu.* |    Disky byly odpojeny z portálu nebo byl překročen limit 12 hodin. | Tento konkrétní exe je teď neplatný a nedá se spustit. Pokud chcete získat přístup k souborům tohoto bodu obnovení v čase, přejděte na portál pro nový soubor exe.|
| V počítači, kde je spuštěný exe: po kliknutí na tlačítko Odpojit se nové svazky nepřipojí. | Iniciátor iSCSI na počítači neodpovídá/nereaguje na aktualizace připojení k cíli a údržbě mezipaměti. |  Po kliknutí na **Odpojit**počkejte několik minut. Pokud se nové svazky odpojeny, procházejte všemi svazky. Procházení všech svazků vynutí, aby iniciátor aktualizoval připojení, a svazek je odpojený s chybovou zprávou, že disk není k dispozici.|
| Výstup z exe: skript se úspěšně spustí, ale ve výstupu skriptu se nezobrazí "nové svazky připojené". |    Toto je přechodná chyba    | Svazky budou již připojeny. Otevřete Průzkumníka, který chcete procházet. Pokud používáte stejný počítač ke spouštění skriptů pokaždé, zvažte restartování počítače a seznam by se měl zobrazit v dalších spuštěních exe. |
| Specifické pro Linux: nemůžete zobrazit požadované svazky | OPERAČNÍ systém počítače, ve kterém je spuštěný skript, nemusí rozpoznat podkladový systém souborů chráněného virtuálního počítače. | Ověřte, zda je bod obnovení konzistentní nebo konzistentní se souborem. Pokud je konzistentní soubor, spusťte skript na jiném počítači, jehož operační systém rozpozná chráněný virtuální počítač. |
| Specifické pro systém Windows: nemůžete zobrazit požadované svazky | Disky jsou možná připojené, ale svazky nejsou nakonfigurované. | Na obrazovce Správa disků Identifikujte další disky týkající se bodu obnovení. Pokud je některý z těchto disků v režimu offline, zkuste je převést do režimu online tak, že kliknete pravým tlačítkem na disk a vyberete **online**.|

## <a name="security"></a>Zabezpečení

Tato část popisuje různé bezpečnostní opatření, která se provádí při implementaci obnovení souborů ze záloh virtuálních počítačů Azure.

### <a name="feature-flow"></a>Tok funkcí

Tato funkce byla sestavena pro přístup k datům virtuálních počítačů, aniž by bylo nutné obnovovat celý virtuální počítač nebo disky virtuálních počítačů a minimální počet kroků. Přístup k datům virtuálních počítačů je poskytován skriptem (který připojí svazek pro obnovení, jak je vidět níže) a tvoří základ všech implementací zabezpečení:

  ![Tok funkcí zabezpečení](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementace zabezpečení

#### <a name="select-recovery-point-who-can-generate-script"></a>Vybrat bod obnovení (který může vygenerovat skript)

Skript poskytuje přístup k datům virtuálních počítačů, takže je důležité regulovat, kdo ho může vygenerovat na prvním místě. Musíte se přihlásit do Azure Portal a mít k vygenerování skriptu [autorizaci RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) .

Obnovení souborů vyžaduje stejnou úroveň autorizace, jakou vyžaduje obnovení virtuálních počítačů a obnovení disků. Jinými slovy, data virtuálních počítačů mohou být vygenerována pouze oprávnění uživatelé.

Vygenerovaný skript je podepsaný oficiálním certifikátem společnosti Microsoft pro službu Azure Backup. Jakékoli manipulace se skriptem znamená, že signatura je poškozená a všechny pokusy o spuštění skriptu jsou v operačním systému zvýrazněné jako potenciální riziko.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Připojit svazek pro obnovení (který může spustit skript)

Pouze správce může spustit skript a měl by být spuštěn v režimu zvýšeného oprávnění. Skript spustí pouze předem vygenerovanou sadu kroků a nepřijímá vstup z žádného externího zdroje.

Chcete-li spustit skript, je vyžadováno heslo, které se zobrazí pouze oprávněnému uživateli v době generování skriptu v Azure Portal nebo PowerShellu/CLI. K tomu je potřeba zajistit, aby autorizovaný uživatel, který stáhl skript, byl zodpovědný také za spuštění skriptu.

#### <a name="browse-files-and-folders"></a>Procházet soubory a složky

Pokud chcete procházet soubory a složky, používá skript iniciátor iSCSI v počítači a připojuje se k bodu obnovení, který je nakonfigurovaný jako cíl iSCSI. Tady si můžete představit scénáře, kdy se jeden z nich snaží napodobovat nebo napodobovat falešnou hodnotu.

Používáme mechanismus vzájemného ověřování CHAP, aby každá součást ověřovala druhou. To znamená, že u falešného iniciátoru se připojíte k cíli iSCSI a napsání falešného cíle se připojí k počítači, ve kterém se skript spouští.

Tok dat mezi službou obnovení a počítačem je chráněný vytvořením zabezpečeného tunelu TLS přes TCP (v počítači, na kterém je spuštěný skript,[by se měl podporovat protokol tls 1,2](#system-requirements) ).

Jakýkoli seznam Access Control souborů (ACL), který se nachází v nadřazeném nebo zálohovaném virtuálním počítači, se zachová i v připojeném systému souborů.

Skript poskytuje přístup jen pro čtení k bodu obnovení a je platný jenom 12 hodin. Pokud chcete přístup odebrat dříve, přihlaste se Azure Portal/PowerShell/CLI a proveďte **odpojování disků** pro konkrétní bod obnovení. Tento skript se okamžitě zruší.

## <a name="next-steps"></a>Další kroky

- Jakékoli problémy při obnovování souborů najdete v části [věnované řešení potíží](#troubleshooting) .
- Informace o tom, jak [obnovit soubory přes PowerShell](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)
- Informace o tom, jak [obnovit soubory prostřednictvím](./tutorial-restore-files.md) rozhraní příkazového řádku Azure
- Po obnovení virtuálního počítače se dozvíte, jak [Spravovat zálohy](./backup-azure-manage-vms.md) .
