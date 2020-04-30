---
title: Zálohování stavu systému Windows do Azure
description: Naučte se zálohovat stav systému Windows Server nebo počítačů s Windows do Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 4089815f8f76d9868f8fa56f8b2eab3de89541d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128167"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Zálohování stavu systému Windows ve Správce prostředků nasazení

Tento článek vysvětluje, jak zálohovat stav systému Windows Server do Azure. Slouží k tomu, aby vás provedl základy.

Chcete-li se dozvědět více o Azure Backup, přečtěte si tento [přehled](backup-overview.md).

Pokud předplatné Azure nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/), který vám umožní přístup ke službám Azure.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Chcete-li zálohovat stav systému Windows Server, je třeba vytvořit trezor Recovery Services v oblasti, kam chcete data uložit. Musíte také určit způsob replikace úložiště.

### <a name="to-create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

1. Pokud jste to ještě neudělali, přihlaste se na webu [Azure Portal](https://portal.azure.com/) pomocí svého předplatného Azure.
2. V nabídce centra klikněte na **Všechny služby**, v seznamu prostředků zadejte **Recovery Services** a klikněte na **Trezory služby Recovery Services**.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-system-state/open-rs-vault-list.png)

    Pokud předplatné obsahuje trezory služby Recovery Services, jsou tyto trezory uvedené v seznamu.
3. V nabídce **Trezory Recovery Services** klikněte na **Přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Otevře se okno trezoru Recovery Services s výzvou k vyplnění polí **Název**, **Předplatné**, **Skupina prostředků** a **Oblast**.

    ![Vytvoření trezoru Recovery Services – krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný v rámci předplatného Azure. Zadejte název v rozsahu 2 až 50 znaků. Musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.

5. V části **Předplatné** z rozevírací nabídky vyberte předplatné Azure. Pokud používáte jenom jedno předplatné, zobrazí se toto předplatné a můžete přejít k dalšímu kroku. Pokud si nejste jisti, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Více možností je dostupných, jen pokud je váš účet organizace přidružený k více předplatným Azure.

6. V části **Skupina prostředků**:

    * vyberte **Vytvořit novou**, pokud chcete vytvořit skupinu prostředků.
    Nebo
    * vyberte **Použít existující** a kliknutím na rozevírací nabídku zobrazte seznam dostupných skupin prostředků.

   Kompletní informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md).

7. Klikněte na **Oblast** a vyberte zeměpisnou oblast trezoru. Tato volba určuje geografickou oblast, kam jsou zasílaná vaše zálohovaná data.

8. V dolní části okna trezoru služby Recovery Services klikněte na **Vytvořit**.

    Vytvoření trezoru služby Recovery Services může trvat několik minut. Sledujte oznámení o stavu v pravé horní části portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Služeb zotavení. Pokud se trezor nezobrazí ani po několika minutách, klikněte na **Obnovit**.

    ![Kliknutí na tlačítko Obnovit](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Jakmile se trezor zobrazí v seznamu trezorů služby Recovery Services, jste připraveni nastavit redundanci úložiště.

### <a name="set-storage-redundancy-for-the-vault"></a>Nastavení redundance úložiště pro trezor

Při vytváření trezoru služby Recovery Services se ujistěte, že je redundance úložiště nakonfigurována požadovaným způsobem.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor.

    ![Výběr nového trezoru ze seznamu trezorů služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Po výběru trezoru se okno **Trezory služby Recovery Services** zúží a otevře se okno Nastavení (*s názvem trezoru v horní části*) a okno s podrobnostmi o trezoru.

    ![Zobrazení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. V okně Nastavení nového trezoru pomocí vertikálního posuvníku přejděte dolů do části Správa a klikněte na **Infrastruktura zálohování**.
    Otevře se okno Infrastruktura zálohování.
3. V okně Infrastruktura zálohování klikněte na **Konfigurace zálohování**. Otevře se okno **Konfigurace zálohování**.

    ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Zvolte vhodnou možnost replikace pro svůj trezor.

    ![volby konfigurace úložiště](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud používáte Azure jako primární koncový bod úložiště záloh, pokračujte v používání **geograficky redundantního** úložiště. Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště najdete v tomto [přehledu redundance úložiště](../storage/common/storage-redundancy.md).

Teď, když jste vytvořili trezor, nakonfigurujte ho pro zálohování stavu systému Windows.

## <a name="configure-the-vault"></a>Konfigurace trezoru

1. V okně trezoru služby Recovery Services (pro trezor, který jste právě vytvořili) klikněte v části Začínáme na **Zálohovat** a potom v okně **Začínáme se zálohováním** vyberte **Cíl zálohování**.

    ![Otevřete okno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Otevře se okno **Cíl zálohování**.

    ![Otevřete okno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. V rozevírací nabídce **Kde běží vaše úlohy?** vyberte **Místní**.

    Možnost **Místní** jste vybrali proto, že počítačem s Windows Serverem nebo Windows je fyzický počítač, který není v Azure.

3. V nabídce **co chcete zálohovat?** vyberte možnost **stav systému**a klikněte na tlačítko **OK**.

    ![Konfigurace souborů a složek](./media/backup-azure-system-state/backup-goal-system-state.png)

    Po kliknutí na OK se vedle položky **Cíl zálohování** zobrazí zaškrtnutí a otevře se okno **Připravit infrastrukturu**.

    ![Cíl zálohování je nakonfigurovaný, teď se připraví infrastruktura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. V okně **Připravit infrastrukturu** klikněte na **Stáhnout agenta pro Windows Server nebo klienta Windows**.

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Pokud používáte Windows Server Essential, vyberte ke stažení agenta pro Windows Server Essential. Místní nabídka zobrazí výzvu ke spuštění nebo uložení MARSAgentInstaller.exe.

    ![Dialogové okno MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. V místní nabídce stahování klikněte na **Uložit**.

    Ve výchozím nastavení se soubor **MARSagentinstaller.exe** uloží do složky Stažené soubory. Po dokončení instalačního programu se zobrazí automaticky otevírané okno s dotazem, jestli chcete spustit instalační program nebo otevřít složku.

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Agenta ještě nemusíte instalovat. Agenta můžete nainstalovat po stažení přihlašovacích údajů trezoru.

6. V okně **Připravit infrastrukturu** klikněte na **Stáhnout**.

    ![stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Přihlašovací údaje trezoru se stáhnou do složky Stažené soubory. Po dokončení stahování přihlašovacích údajů trezoru se zobrazí automaticky otevírané okno s dotazem, jestli chcete přihlašovací údaje otevřít nebo uložit. Klikněte na **Uložit**. Pokud omylem kliknete **Otevřít**, nechte dialogové okno, které se pokusí otevřít přihlašovací údaje trezoru, zobrazit chybu. Přihlašovací údaje trezoru nejde otevřít. Přejděte k dalšímu kroku. Přihlašovací údaje trezoru jsou ve složce Stažené soubory.

    ![dokončené stahování přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Přihlašovací údaje trezoru musí být uloženy pouze do umístění, které je místní pro systém Windows Server, na kterém chcete agenta použít.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

> [!NOTE]
> Povolení zálohování prostřednictvím webu Azure Portal ještě není dostupné. Pro zálohování stavu systému Windows Server použijte agenta Microsoft Azure Recovery Services.
>

1. Ve složce Stažené soubory (nebo ve složce, kterou jste vybrali pro stahování) vyhledejte soubor **MARSagentinstaller.exe** a dvakrát na něj klikněte.

    Instalační program v průběhu extrakce, instalace a registrace agenta Recovery Services zobrazuje řadu zpráv.

    ![spuštění přihlašovacích údajů instalačního programu agenta Recovery Services](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Dokončete Průvodce instalací agenta Služeb zotavení Microsoft Azure. K dokončení průvodce budete muset:

   * Vybrat umístění instalace a složky mezipaměti.
   * Zadat informace o serveru proxy, pokud pro připojování k internetu používáte server proxy.
   * Zadat svoje uživatelské jméno a heslo, pokud používáte ověřený server proxy.
   * Zadat stažené přihlašovací údaje trezoru.
   * Uložit šifrovací heslo na bezpečné místo.

     > [!NOTE]
     > Pokud heslo ztratíte nebo zapomenete, Microsoft vám nemůže pomoci obnovit zálohovaná data. Uložte soubor na bezpečné místo. Je požadováno pro obnovení zálohy.
     >
     >

Agent je nyní nainstalovaný a váš počítač je registrovaný k trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="back-up-windows-server-system-state"></a>Zálohování stavu systému Windows Server

Počáteční záloha zahrnuje dvě úlohy:

* Naplánování zálohování
* První zálohování stavu systému

K dokončení prvotního zálohování použijte agenta Microsoft Azure Recovery Services.

> [!NOTE]
> Stav systému systému Windows Server 2008 R2 můžete zálohovat pomocí systému Windows Server 2016. Zálohování stavu systému není podporováno u klientských SKU klienta. Stav systému se nezobrazuje jako možnost pro klienty Windows nebo pro počítače s Windows Serverem 2008 SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Naplánování úlohy zálohování

1. Otevřete agenta Služeb zotavení Microsoft Azure. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

    ![Spuštění agenta Služeb zotavení Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. V agentu Služeb zotavení klikněte na **Naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na stránce Začínáme v Průvodci plánování zálohování klikněte na **Další**.

4. Na stránce Výběr položek k zálohování klikněte na **Přidat**.

5. Vyberte **stav systému** a pak klikněte na **OK**.

6. Klikněte na **Další**.

7. Na následujících stránkách vyberte požadovanou četnost zálohování a zásady uchovávání informací pro zálohy stavu systému.

8. Na stránce Potvrzení zkontrolujte uvedené informace a pak klikněte na **Dokončit**.

9. Až průvodce dokončí vytváření plánu zálohování, klikněte na **Zavřít**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Postup při prvním zálohování stavu systému Windows Server

1. Ujistěte se, že neexistují žádné nedokončené aktualizace pro Windows Server, které vyžadují restart.

2. Chcete-li dokončit prvotní synchronizaci přes síť, v agentu Služeb zotavení klikněte na **Zálohovat nyní**.

    ![Zálohování Windows serveru hned teď](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Na obrazovce **Vybrat zálohovanou položku** vyberte **stav systému** , který se zobrazí, a klikněte na **Další**.

4. Na stránce Potvrzení zkontrolujte nastavení, které Průvodce Zálohování nyní použije k zálohování počítače. Poté klikněte na **Zálohovat**.

5. Průvodce zavřete kliknutím na **Zavřít**. Pokud průvodce zavřete před dokončením procesu zálohování, průvodce zůstane spuštěný na pozadí.
    > [!NOTE]
    > Agent MARS aktivuje nástroj SFC/VERIFYONLY jako součást předkontrol všech záloh stavu systému. K tomu je potřeba zajistit, aby byly soubory zálohované jako součást stavu systému správné verze odpovídající verzi Windows. Další informace o nástroji pro kontrolu systémových souborů (SFC) najdete v [tomto článku](https://docs.microsoft.com/windows-server/administration/windows-commands/sfc).
    >

Po dokončení prvotní zálohy se v konzole Zálohování zobrazí stav **Úloha byla dokončena**.

  ![Dokončení IR](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Máte dotazy?

Máte-li nějaké dotazy nebo pokud víte o funkci, kterou byste uvítali, [odešlete nám svůj názor](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Další kroky

* Zdroj dalších informací o [zálohování počítačů se systémem Windows](backup-windows-with-mars-agent.md).
* Teď, když jste zálohovali stav systému Windows Server, můžete [Spravovat trezory a servery](backup-azure-manage-windows-server.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
