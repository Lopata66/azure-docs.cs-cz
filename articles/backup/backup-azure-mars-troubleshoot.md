---
title: Řešení potíží s agentem Azure Backup
description: Řešení potíží instalace a registrace agenta Azure Backup
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: saurse
ms.openlocfilehash: 4bad788156b2068f24484d3b248f2091409752ad
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621613"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Řešení potíží s agentem Microsoft Azure Recovery Services (MARS)

Tady je postup pro řešení chyb můžete setkat během konfigurace, registraci, zálohování a obnovení.

## <a name="invalid-vault-credentials-provided"></a>Neplatné přihlašovací údaje úložiště k dispozici

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** </br> *Zadané neplatné přihlašovací údaje trezoru. Soubor je poškozený nebo nemá mít nejnovější přihlašovací údaje přidružené ke službě obnovení. (ID: 34513)* | <ul><li> Přihlašovací údaje trezoru jsou neplatné (to znamená, že byly staženy víc než 48 hodin, než čas registrace).<li>MARS Agent se nemůže stahovat soubory do svého adresáře Windows Temp. <li>Přihlašovací údaje trezoru jsou v umístění v síti. <li>Protokol TLS 1.0 je zakázaný.<li> Nakonfigurovaný proxy server blokuje připojení. <br> |  <ul><li>Stáhněte si nové přihlašovací údaje trezoru. (**Poznámka**: Pokud více souborů přihlašovacích údajů trezoru se stáhnou předtím, jenom nejnovější stažený soubor je platný do 48 hodin.) <li>Spuštění **IE** > **nastavení** > **Možnosti Internetu** > **zabezpečení**  >  **Internet**. V dalším kroku vyberte **vlastní úroveň**a posuňte, dokud se nezobrazí stahování části souboru. Potom vyberte **povolit**.<li>Budete také muset tyto weby přidat do aplikace IE [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Změňte nastavení pro použití proxy serveru. Zadejte proxy server podrobnosti. <li> Datum a čas dodržovat váš počítač.<li>Pokud obdržíte chybu s informacemi o tom, že stahování souborů nejsou povoleny, je pravděpodobné, že existuje velký počet souborů v adresáři C:/Windows/Temp.<li>Přejděte na C:/Windows/Temp a zkontrolujte, zda jsou více než 60 000 nebo než 65 000 vývojáři soubory s příponou TMP. Pokud existuje, odstraňte tyto soubory.<li>Ujistěte se, že máte nainstalované rozhraní .NET framework 4.6.2. <li>Pokud jste zakázali protokol TLS 1.0 z důvodu dodržování PCI, podívejte se na to [stránka o řešení problémů](https://support.microsoft.com/help/4022913). <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte z antivirová kontrola následující soubory: <ul><li>CBengine.exe<li>CSC.exe, který má vztah k rozhraní .NET Framework. Existuje CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou vázané na všechny verze rozhraní .NET Framework na příslušném serveru. <li>Dočasné umístění složky nebo mezipaměť. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>Složky bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin

## <a name="unable-to-download-vault-credential-file"></a>Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru

| Podrobnosti o chybě | Doporučené akce |
| ---     | ---    |
|Nepovedlo se stáhnout soubor s přihlašovacími údaji trezoru. (ID: 403) | <ul><li> Zkusit stáhnout přihlašovací údaje trezoru pomocí jiný prohlížeč nebo provádět následujících kroků: <ul><li> Spusťte Internet Exploreru, stiskněte klávesu F12. </li><li> Přejděte na **sítě** kartu k vymazání mezipaměti aplikace Internet Explorer a soubory cookie </li> <li> Aktualizujte stránku<br>(NEBO)</li></ul> <li> Zkontrolujte, jestli předplatné je zakázané nebo vypršela platnost<br>(NEBO)</li> <li> Zaškrtněte, pokud jakékoli pravidlo brány firewall blokuje stahování souborů přihlašovacích údajů trezoru <br>(NEBO)</li> <li> Ujistěte se, že nebyly vyčerpali limit pro trezor (50 počítačů pro každý trezor)<br>(NEBO)</li>  <li> Zajištění uživatel má požadované oprávnění Azure Backup pro stažení přihlašovacích údajů trezoru a server zaregistrujete pomocí úložiště, přečtěte si [článku](backup-rbac-rs-vault.md)</li></ul> | 

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent Microsoft Azure Recovery Services se nemohl připojit ke službě Microsoft Azure Backup

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** <br /><ol><li>*Agent služby Microsoft Azure Recovery se nepodařilo připojit ke službě Microsoft Azure Backup. (ID: 100050) zkontrolujte nastavení sítě a ujistěte se, že se můžete připojit k Internetu*<li>*Vyžadováno ověřování proxy serveru (407)* |Blokuje připojení proxy server. |  <ul><li>Spuštění **IE** > **nastavení** > **Možnosti Internetu** > **zabezpečení**  >  **Internet**. Potom vyberte **vlastní úroveň** a posuňte, dokud se nezobrazí stahování části souboru. Vyberte **Povolit**.<li>Budete také muset tyto weby přidat do aplikace IE [Důvěryhodné servery](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Změňte nastavení pro použití proxy serveru. Zadejte proxy server podrobnosti. <li>Pokud máte antivirový software nainstalovaný na serveru, vylučte následující soubory z antivirová kontrola. <ul><li>CBEngine.exe (namísto dpmra.exe).<li>CSC.exe (související s rozhraní .NET Framework). Existuje CSC.exe pro každou verzi rozhraní .NET, který je nainstalován na serveru. Vylučte soubory CSC.exe, které jsou vázané na všechny verze rozhraní .NET framework na příslušném serveru. <li>Dočasné umístění složky nebo mezipaměť. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>Složky bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
| ---     | ---     | ---    |
| **Chyba** <br />*Nepovedlo se nastavit šifrovací klíč pro bezpečné zálohy. Aktivace nebylo zcela úspěšné, ale šifrovací heslo se uložila do následujícího souboru*. |<li>Server je už zaregistrovaný s jiný trezor.<li>Během konfigurace byla poškozena přístupové heslo.| Zrušit registraci serveru v trezoru a znovu zaregistrujte novou heslu.

## <a name="the-activation-did-not-complete-successfully"></a>Aktivace nebyla úspěšně dokončena.

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|**Chyba** <br /><ol>*Aktivace nebyla úspěšně dokončena. Aktuální operace selhala kvůli vnitřní chybě služby [0x1FC07]. Po nějaké době zkuste operaci zopakovat. Pokud se problém nevyřeší, kontaktujte prosím podporu Microsoftu*     | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Pomocnou složku byl přesunut nesprávně do jiného umístění. <li> Chybí soubor OnlineBackup.KEK.         | <li>Upgrade na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocné umístění složky nebo mezipaměti na svazek s volné místo odpovídající 5 až 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Šifrovací heslo není správně nakonfigurované

| Podrobnosti o chybě | Možné příčiny | Doporučené akce |
|---------|---------|---------|
|**Chyba** <br /><ol>*Došlo k chybě 34506. Šifrovací heslo uložené na tomto počítači není správně nakonfigurovaný*.    | <li> Odkládací složka se nachází na svazku, který nemá dostatek místa. <li> Pomocnou složku byl přesunut nesprávně do jiného umístění. <li> Chybí soubor OnlineBackup.KEK.        | <li>Upgrade na [nejnovější verzi](https://aka.ms/azurebackup_agent) agenta MARS.<li>Přesuňte pomocnou složku nebo umístění mezipaměti na svazek s volné místo odpovídající 5 – 10 % celkové velikosti dat zálohy. Správně přesuňte umístění mezipaměti, použijte postup v [dotazy týkající se Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Ujistěte se, že soubor OnlineBackup.KEK je k dispozici. <br>*Výchozí umístění pro odkládací složce nebo cestu k umístění mezipaměti je C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Zálohování nelze spustit podle plánu
Pokud naplánovaných záloh není aktivují automaticky, zatímco ruční zálohy budou fungovat bez problémů, zkuste následující akce:

- Ujistěte se, že plán zálohování Windows serveru nejsou v konfliktu se soubory a složky, plán zálohování Azure.
- Přejděte na **ovládací panely** > **nástroje pro správu** > **Plánovač úloh**. Rozbalte **Microsoft**a vyberte **Online zálohování**. Dvakrát klikněte na panel **Microsoft OnlineBackup**a přejděte na **triggery** kartu. Ujistěte se, že je stav nastaven **povoleno**. Pokud tomu tak není, vyberte **upravit**a vyberte **povoleno** zaškrtávací políčko a klikněte na tlačítko **OK**. Na **Obecné** kartu, přejděte na **možnosti zabezpečení** a ujistěte se, že uživatelský účet vybrané ke spuštění úkolu je buď **systému** nebo **místní Skupina správců** na serveru.

- Podívejte se, jestli je na serveru nainstalovaný PowerShell 3.0 nebo novější. Pokud chcete zkontrolovat verzi prostředí PowerShell, spusťte následující příkaz a ověřte, zda *hlavní* číslo verze je roven nebo větší než 3.

  `$PSVersionTable.PSVersion`

- Zjistit, zda následující cesta je součástí *PSMODULEPATH* proměnné prostředí.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Pokud zásady spouštění prostředí PowerShell pro *LocalMachine* je nastavena na s omezeným přístupem, rutina prostředí PowerShell, který se aktivuje úloha zálohování může selhat. Spuštěním následujících příkazů v režimu se zvýšenými oprávněními ke kontrole a nastavte zásady spouštění buď *Unrestricted* nebo *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

> [!TIP]
> Aby bylo zajištěno, že změny jsou konzistentní, restartujte server po provedení výše uvedených kroků.


## <a name="troubleshoot-restore-issues"></a>Řešení potíží s obnovení

Azure Backup nemusí úspěšně připojit svazek pro obnovení, dokonce i po několika minutách. Během procesu může také zobrazit chybové zprávy. Pokud chcete začít, obvykle obnovení, postupujte takto:

1.  Zrušte proces probíhající připojení v případě, že je spuštěné pro několik minut.

2.  Podívejte se, pokud jste na nejnovější verzi agenta zálohování. Chcete-li zjistit, na verzi, **akce** podokně konzoly MARS, vyberte **o Microsoft Azure Recovery Services Agent**. Ujistěte se, že **verze** počet se rovná nebo je vyšší než verze uvedená v [v tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Můžete stáhnout nejnovější verzi z [tady](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Přejděte na **Správce zařízení** > **řadiče úložiště**a vyhledejte **iniciátoru iSCSI od Microsoftu**. Pokud ji mohli najít, přejděte přímo ke kroku 7.

4.  Pokud nemůžete najít služba iniciátoru iSCSI společnosti Microsoft, pokuste se najít položku **Správce zařízení** > **řadiče úložiště** volá **neznámé zařízení**, s ID hardwaru **ROOT\ISCSIPRT**.

5.  Klikněte pravým tlačítkem na **neznámé zařízení**a vyberte **aktualizace ovladače**.

6.  Aktualizovat ovladač tak, že vyberete možnost **vyhledat automaticky aktualizovaný ovladač**. Dokončení aktualizace by se měla změnit **neznámé zařízení** k **iniciátoru iSCSI od Microsoftu**, jak je znázorněno níže.

    ![Snímek obrazovky s Azure Backup Správce zařízení, se zvýrazněným řadiče úložiště](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Přejděte na **Správce úloh** > **služby (místní počítač)** > **služba iniciátoru iSCSI společnosti Microsoft**.

    ![Snímek obrazovky z Azure Backup Správce úloh, se zvýrazněným služby (místní)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Restartujte službu iniciátoru iSCSI společnosti Microsoft. Chcete-li to provést, klikněte pravým tlačítkem na službu, vyberte **Zastavit**znovu kliknete pravým tlačítkem a vyberte **Start**.

9.  Opakujte obnovení pomocí [ **rychlé obnovení**](backup-instant-restore-capability.md).

Pokud obnovení pořád selže, restartujte server nebo klienta. Pokud nechcete, aby k restartování nebo obnovení selže i i po restartování serveru, zkuste obnovení v jiném počítači. Postupujte podle kroků v [v tomto článku](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup
* Přečtěte si víc o [zálohování Windows serveru pomocí agenta Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
