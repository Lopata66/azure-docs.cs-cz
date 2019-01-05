---
title: Zálohování virtuálních počítačů Azure pomocí služby Azure Backup
description: Zjistěte, jak zálohovat virtuální počítače Azure pomocí služby Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: c1bd92b9c4611465b680f195e4881a447f4bb701
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044395"
---
# <a name="back-up-azure-vms-with-the-azure-backup-service"></a>Zálohování virtuálních počítačů Azure pomocí služby Azure Backup

Tento článek vysvětluje, jak nakonfigurovat ochranu pro virtuální počítač z nabídky operace virtuálních počítačů nebo do trezoru služby Recovery Services. Trezory Služeb zotavení chrání:

* Virtuální počítače nasazené Azure Resource Managerem
* Klasické virtuální počítače
* Virtuální počítače služby Storage úrovně Standard
* Virtuální počítače služby Premium Storage
* Virtuální počítače spuštěné na spravovaných discích
* Virtuální počítače šifrované službou Azure Disk Encryption
* Zálohování konzistentní vzhledem k aplikacím virtuálních počítačů s Windows pomocí služby Stínová kopie svazku (VSS) a virtuálních počítačů s Linuxem pomocí vlastních předsnímkových a posnímkových skriptů

Další informace o ochraně virtuálních počítačů služby Premium Storage najdete v článku [Zálohování a obnovení virtuálních počítačů služby Premium Storage](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Další informace o podpoře pro virtuální počítače se spravovanými disky najdete v tématu věnovaném [zálohování a obnovení virtuálních počítačů na spravovaných discích](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Další informace o rozhraní s předsnímkovými a posnímkovými skripty pro zálohování virtuálních počítačů s Linuxem najdete v tématu [Zálohování virtuálních počítačů s Linuxem konzistentní s aplikacemi pomocí předsnímkových a posnímkových skriptů](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Další informace o tom, co lze a nelze zálohovat, najdete v tématu [Příprava prostředí pro zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md#before-you-start).

> [!NOTE]
> Služba Backup vytvoří samostatné skupiny prostředků než skupina prostředků virtuálního počítače k uložení kolekci bodů obnovení. Zákazníkům doporučujeme není zamknout skupiny prostředků vytvořené pro použití službou Backup.
Formát názvu skupiny prostředků vytvořené pomocí služby Backup je: AzureBackupRG_`<Geo>`_`<number>`
<br>Například: AzureBackupRG_northeurope_1
>
>

V závislosti na počtu virtuálních počítačů, které chcete ochránit, můžete začít z několika různých počátečních bodů. Pokud chcete v rámci jedné operace zálohovat více virtuálních počítačů, přejděte do trezoru služby Recovery Services a [spusťte úlohu zálohování z řídicího panelu trezoru](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Pokud chcete zálohovat jeden virtuální počítač, [spusťte úlohu zálohování z nabídky operace virtuálního počítače](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>Konfigurace úlohy zálohování z nabídky operace virtuálního počítače

Konfigurace úlohy zálohování z nabídky operace virtuálního počítače pomocí následujících kroků. Postup se vztahuje pouze na virtuální počítače na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra klikněte na **Všechny služby** a v dialogovém okně Filtr zadejte **Virtuální počítače**. Při psaní se seznam prostředků bude filtrovat. Až uvidíte položku Virtuální počítače, vyberte ji.

  ![Snímek obrazovky ukazující přechod k virtuálním počítačům z části Všechny služby](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  Zobrazí se seznam virtuálních počítačů (VM) v rámci předplatného.

  ![Zobrazí se seznam virtuálních počítačů v rámci předplatného.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. V seznamu vyberte virtuální počítač, který chcete zálohovat.

  ![Zobrazí se seznam virtuálních počítačů v rámci předplatného.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Když vyberete virtuální počítač, otevřete seznam virtuálních počítačů posune doleva a v nabídce Správa virtuálního počítače a řídicí panel virtuální počítač.

4. V nabídce Správa virtuálních počítačů v **operace** klikněte na tlačítko **zálohování**. </br>

  ![Možnost zálohování v nabídce Správa virtuálních počítačů](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  Otevře se nabídka zálohování povolit.

  ![Možnost zálohování v nabídce Správa virtuálních počítačů](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. V oblasti trezoru služby Recovery Services klikněte na tlačítko **vybrat existující** a z rozevíracího seznamu zvolte trezor.

  ![Průvodce povolením zálohování](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  Pokud neexistuje žádný trezor služby Recovery Services, nebo pokud chcete použít nový trezor, klikněte na **Vytvořit nový** a zadejte název nového trezoru. Nový trezor se vytvoří ve stejné skupině prostředků a oblasti jako virtuální počítač. Pokud chcete vytvořit trezor služby Recovery Services s použitím jiných hodnot, přečtěte si část popisující [vytvoření trezoru služby Recovery Services](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. Z nabídky vyberte zásady zálohování vyberte zásadu. Pod rozevírací nabídce se zobrazí podrobnosti vybrané zásady.

  Pokud chcete vytvořit novou zásadu nebo upravte existující zásady, klikněte na **vytvořit (nebo upravit) nové zásady** otevřete editor zásad zálohování. Pokyny k definování zásad zálohování naleznete v tématu [Definování zásad zálohování](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Pokud chcete uložit změny zásady zálohování a vraťte se do nabídky povolit zálohování, klikněte na tlačítko **OK**.

  ![Výběr zásady zálohování](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Použít zásady služby Recovery Services vault a zálohování pro virtuální počítač, klikněte na tlačítko **povolit zálohování** nasaďte zásadu. Nasazení zásadu přidruží k trezoru a virtuálním počítačům.

  ![Tlačítko Povolit zálohování](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Průběh konfigurace můžete sledovat prostřednictvím oznámení, která se zobrazují na portálu. Následující příklad ukazuje spuštění nasazení.

  ![Oznámení Povolení zálohování](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Po dokončení konfigurace průběh v nabídce Správa virtuálních počítačů, klikněte na tlačítko **zálohování** otevřete nabídku zálohování a zobrazte dostupné podrobnosti.

  ![Zobrazení Zálohovaná položka virtuálního počítače](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  Než se dokončí prvotní zálohování, bude **Stav poslední zálohy** ukazovat **Upozornění (nedokončené prvotní zálohování)**. Pokud chcete zobrazit další plánovaná úloha zálohování dojde, v části **Souhrn** klikněte na název zásady. V nabídce zásady zálohování se otevře a zobrazí čas plánovaného zálohování.

10. K ochraně virtuálního počítače, klikněte na tlačítko **zálohovat nyní**.

  ![klikněte na Zálohovat nyní a spusťte prvotní zálohování](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Otevře se nabídka Zálohovat nyní.

  ![ukazuje okno Zálohovat nyní](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. V nabídce Zálohovat nyní klikněte na ikonu kalendáře, pomocí ovládacího prvku kalendáře vyberte poslední den tohoto bodu obnovení se uchovávají a klikněte na tlačítko **OK**.

  ![nastavte poslední den uchování bodu obnovení vytvořeného pomocí možnosti Zálohovat nyní](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Oznámení nasazení vás budou informovat o aktivaci úlohy zálohování a možnosti sledovat průběh úlohy na stránce Úlohy zálohování.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Konfigurace úlohy zálohování z trezoru služby Recovery Services
Pokud chcete konfigurovat úlohu zálohování, dokončete následující kroky.

1. Vytvoření trezoru služby Recovery Services pro virtuální počítač.
2. Použití webu Azure Portal k výběru scénáře, nastavení zásady zálohování a určení položek, které mají být chráněné.
3. Spuštění prvotního zálohování.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Vytvoření trezoru Recovery Services pro virtuální počítač
Trezor záloh Služeb zotavení je entita, která ukládá všechny vytvořené zálohy a body obnovení. Trezor Služeb zotavení obsahuje také zásadu zálohování, která se používá pro chráněné virtuální počítače.

> [!NOTE]
> Zálohování virtuálního počítače je místní proces. Virtuální počítače z jedné oblasti není možné zálohovat do trezoru služby Recovery Services v jiné oblasti. Pro každou oblast Azure s virtuálními počítači, které se mají zálohovat, tedy musí existovat alespoň jeden trezor služby Recovery Services.
>
>

Chcete-li vytvořit trezor Služeb zotavení:

1. Pokud jste to ještě neudělali, přihlaste se na webu [Azure Portal](https://portal.azure.com/) pomocí svého předplatného Azure.
2. V nabídce centra klikněte na **Všechny služby** a v dialogovém okně Filtr zadejte **Recovery Services**. Při psaní se seznam prostředků bude filtrovat. Až v seznamu uvidíte položku Trezory služby Recovery Services, klikněte na ni.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Pokud předplatné obsahuje trezory služby Recovery Services, jsou tyto trezory uvedené v seznamu.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. V nabídce **Trezory Recovery Services** klikněte na **Přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Trezor služby Recovery Services otevře nabídka výzvou k vyplnění polí **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Vytvoření trezoru Recovery Services – krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný v rámci předplatného Azure. Zadejte název v rozsahu 2 až 50 znaků. Musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.

5. V části **Předplatné** z rozevírací nabídky vyberte předplatné Azure. Pokud používáte jenom jedno předplatné, zobrazí se toto předplatné a můžete přejít k dalšímu kroku. Pokud si nejste jisti, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Více možností je dostupných, jen pokud je váš účet organizace přidružený k více předplatným Azure.

6. V části **Skupina prostředků**:

    * vyberte **Vytvořit novou**, pokud chcete vytvořit skupinu prostředků.
    Nebo
    * vyberte **Použít existující** a kliknutím na rozevírací nabídku zobrazte seznam dostupných skupin prostředků.

  Kompletní informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

7. Klikněte na **Oblast** a vyberte zeměpisnou oblast trezoru. Tato volba určuje geografickou oblast, kam jsou zasílaná vaše zálohovaná data.

  > [!IMPORTANT]
  > Pokud si nejste jisti oblastí, ve které jsou vaše virtuální počítače, zavřete dialogové okno vytvoření trezoru a na portálu přejděte na seznam virtuálních počítačů. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé z nich. Vytvořte trezor nejprve v první oblasti, poté přejděte k další oblasti. Není potřeba specifikovat účty úložiště používané k ukládání zálohovaných dat – trezor služby Recovery Services a služba Azure Backup se o úložiště postarají automaticky.
  >

8. V dolní části v nabídce trezoru služby Recovery Services, klikněte na tlačítko **vytvořit**.

    Vytvoření trezoru služby Recovery Services může trvat několik minut. Sledujte oznámení o stavu v pravé horní části portálu. Když je trezor vytvořený, zobrazí se v seznamu trezorů Služeb zotavení. Pokud se trezor nezobrazí ani po několika minutách, klikněte na **Obnovit**.

    ![Kliknutí na tlačítko Obnovit](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Jakmile se trezor zobrazí v seznamu trezorů služby Recovery Services, jste připraveni nastavit redundanci úložiště.

Teď, když jste vytvořili trezor, se naučte, jak nastavit replikaci úložiště.

### <a name="set-storage-replication"></a>Nastavení replikace úložiště
Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud je trezor služby Recovery Services vaší primární zálohou, ponechte možnost replikace úložiště nastavenou na geograficky redundantní úložiště. Pokud chcete levnější možnost, která není tak trvanlivá, vyberte místně redundantní úložiště. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. Z **trezory služby Recovery Services** nabídce vyberte nový trezor.

  ![Výběr nového trezoru ze seznamu trezorů služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Po výběru trezoru, v nabídce nastavení (*obsahující názvem trezoru v horní části*) a řídicím panelu trezoru otevřete.

  ![Zobrazení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. V nabídce Správa nového trezoru pomocí vertikálního a posuňte se dolů do části Správa, klikněte na tlačítko **infrastruktura zálohování** otevřete nabídku zálohování infrastruktury.

   ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. V nabídce infrastruktura zálohování klikněte na tlačítko **konfigurace zálohování** otevřít **konfigurace zálohování** nabídky.

    ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-open-infra.png)
4. Zvolte vhodnou možnost replikace pro svůj trezor.

    ![volby konfigurace úložiště](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud používáte Azure jako primární koncový bod úložiště záloh, pokračujte v používání **geograficky redundantního** úložiště. Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště najdete v tomto [přehledu redundance úložiště](../storage/common/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Výběr cíle zálohování, nastavení zásad a určení položek k ochraně
Před registrací virtuálních počítačů k trezoru spusťte proces vyhledávání, abyste se ujistili, že byly identifikované všechny nové virtuální počítače přidané k předplatnému. Proces se dotáže Azure na seznam virtuálních počítačů v rámci předplatného společně s dalšími informacemi, jako například název cloudové služby a oblast. Na webu Azure Portal výraz scénář vyjadřuje, co chcete vložit do trezoru Recovery Services. Zásada je plán, jak často a kdy jsou pořizovány body obnovení. Zásada také obsahuje rozsah uchování bodů obnovení.

1. Pokud již máte otevřený trezor Recovery Services, pokračujte ke kroku 2. Jinak klikněte na **Všechny služby**. Zadejte **Recovery Services** a klikněte na **Trezory služby Recovery Services**.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Objeví se seznam trezorů Recovery Services.

    ![Zobrazení seznamu trezorů služby Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    V seznamu trezorů služby Recovery Services výběrem trezoru otevřete jeho řídicí panel.

     ![Otevření nabídky trezoru](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Kliknutím na **Zálohování** v nabídce řídicího panelu trezoru otevřete nabídku Zálohování.

    ![Otevřete nabídku zálohování](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Otevření nabídky zálohování a cíl zálohování.

    ![Otevření nabídky Scénář](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. V nabídce Cíl zálohování z **ve kterém je spuštěná vaše úloha** rozevírací nabídky vyberte Azure. V rozevírací nabídce **Co chcete zálohovat?** zvolte Virtuální počítač a potom klikněte na **OK**.

    Tyto akce v trezoru zaregistrují rozšíření virtuálního počítače. V nabídce Cíl zálohování se zavře a **zásady zálohování** otevře se nabídka.

    ![Otevření nabídky Scénář](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. V nabídce zásady zálohování vyberte zásadu zálohování, které chcete použít pro trezor.

    ![Výběr zásady zálohování](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou. Pokud chcete vytvořit zásadu, vyberte v rozevírací nabídce **Vytvořit novou**. Pokyny k definování zásad zálohování naleznete v tématu [Definování zásad zálohování](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Kliknutím na **OK** přidružte zásadu zálohování k trezoru.

    Nabídka zásady zálohování se zavře a **výběr virtuálních počítačů** otevře se nabídka.
5. V **výběr virtuálních počítačů** nabídku, vyberte virtuální počítače, které chcete přidružit k určené zásadě a klikněte na tlačítko **OK**.

    ![Výběr úlohy](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Vybraný virtuální počítač se ověří. Pokud se nezobrazí virtuální počítače, které jste očekávali, zkontrolujte, že existují ve stejném umístění Azure jako trezor služby Recovery Services a že ještě nejsou chráněné. Umístění trezoru služby Recovery Services je uvedené na řídicím panelu trezoru.

6. Teď, když jste definovali všechna nastavení trezoru, v nabídce Backup, klikněte na tlačítko **povolit zálohování** nasaďte zásadu do trezoru a virtuálních počítačů. Nasazením zásady zálohování se nevytvoří prvotní bod obnovení pro virtuální počítač.

    ![Povolení zálohování](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Po úspěšném povolení zálohování se vaše zásada zálohování spustí podle plánu. Přesto pokračujte a spusťte první úlohu zálohování.

## <a name="initial-backup"></a>Prvotní zálohování
Nasazení zásady zálohování na virtuální počítač neznamená, že jsou data zálohovaná. Ve výchozím nastavení je prvním plánovaným zálohováním (definovaným v zásadě zálohování) prvotní zálohování. Dokud proběhne prvotní zálohování, bude stav poslední zálohy na **úlohy zálohování** nabídce se zobrazuje jako **upozornění (nedokončené prvotní zálohování)**.

![Zálohování čeká na zpracování](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Pokud nemá prvotní zálohování brzy začít, doporučujeme spustit **Zálohovat nyní**.

Spuštění úlohy prvotního zálohování:

1. Na řídicím panelu trezoru klikněte na číslo pod záznamem **Zálohování položek** nebo klikněte na dlaždici **Zálohování položek**. <br/>
  ![Ikona nastavení](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Otevře se nabídka **Zálohování položek**.

  ![Zálohování položek](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Na **zálohování položek** nabídku, vyberte položku.

  ![Ikona nastavení](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Otevře se seznam **Zálohování položek**. <br/>

  ![Aktivovaná úloha zálohování.](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. V seznamu **Zálohování položek** klikněte na **...** (tři tečky) a otevřete místní nabídku.

  ![Místní nabídka](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Zobrazí se místní nabídka.

  ![Místní nabídka](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. V místní nabídce klikněte na **Zálohovat nyní**.

  ![Místní nabídka](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Otevře se nabídka Zálohovat nyní.

  ![zobrazí v nabídce Zálohovat nyní](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. V nabídce Zálohovat nyní klikněte na ikonu kalendáře, pomocí ovládacího prvku kalendáře vyberte poslední den tohoto bodu obnovení se uchovávají a klikněte na tlačítko **zálohování**.

  ![nastavte poslední den uchování bodu obnovení vytvořeného pomocí možnosti Zálohovat nyní](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Oznámení nasazení vás budou informovat o aktivaci úlohy zálohování a možnosti sledovat průběh úlohy na stránce Úlohy zálohování. V závislosti na velikosti virtuálního počítače může vytváření prvotní zálohy chvíli trvat.

  > [!NOTE]
  > - Všechna data zálohovaných službou Azure Backup se šifrují při nečinnosti pomocí [šifrování služby Storage (SSE)](../storage/common/storage-service-encryption.md).
  >
  >

6. Pokud chcete zobrazit nebo sledovat stav prvotní zálohy, na řídicím panelu trezoru na dlaždici **Úlohy zálohování** klikněte na **Probíhající**.

  ![Dlaždice Úlohy zálohování](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Otevře se nabídka úlohy zálohování.

  ![Dlaždice Úlohy zálohování](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  V **úlohy zálohování** nabídky, se zobrazí stav všech úloh. Zkontrolujte, jestli úloha zálohování vašeho virtuálního počítače stále probíhá, nebo se dokončila. Po dokončení úlohy zálohování se stav změní na *Dokončeno*.

  > [!NOTE]
  > Jako součást operace zálohování vydá služba Azure Backup rozšířením zálohování v každém virtuálním počítači příkaz k vyprázdnění všech zápisů a pořízení konzistentního snímku.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalace agenta virtuálního počítače na virtuální počítač
Tyto informace jsou poskytnuté pro případ potřeby. Pro fungování rozšíření Backup musí být na virtuálním počítači Azure nainstalovaný agent virtuálního počítače Azure. Pokud byl váš virtuální počítač vytvořen z galerie Azure, je na něm agent virtuálního počítače již nainstalován. Virtuální počítače migrované z místních datových center nebudou mít agenta virtuálního počítače nainstalovaného. V takovém případě je potřeba agenta virtuálního počítače nainstalovat. Pokud máte problémy se zálohováním virtuálního počítače Azure, zkontrolujte, zda je agent virtuálního počítače na virtuálním počítači správně nainstalovaný (viz následující tabulka). Pokud vytváříte vlastní virtuální počítač, před jeho zřízením nainstalujte agenta virtuálního počítače.

Další informace o [Agentu virtuálního počítače](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) a [jak ho nainstalovat](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Následující tabulka poskytuje další informace o agentu virtuálního počítače pro virtuální počítače s Windows nebo Linuxem.

| **Operace** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalace agenta virtuálního počítače |<li>Stáhněte si a nainstalujte [MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace je potřeba oprávnění správce. <li>[Aktualizujte vlastnost virtuálního počítače](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), aby indikovala, že je agent nainstalovaný. |<li> Nainstalujte nejnovějšího [agenta systému Linux](https://github.com/Azure/WALinuxAgent) z Githubu. K dokončení instalace je potřeba oprávnění správce. <li> [Aktualizujte vlastnost virtuálního počítače](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), aby indikovala, že je agent nainstalovaný. |
| Aktualizace agenta virtuálního počítače |Aktualizace agenta virtuálního počítače je stejně jednoduchá, jako přeinstalace [binárních souborů agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |Postupujte podle pokynů v tématu [Aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |
| Ověření instalace agenta virtuálního počítače |<li>Ve virtuálním počítači Azure přejděte do složky *C:\WindowsAzure\Packages*. <li>Měl by být přítomný soubor WaAppAgent.exe.<li> Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. Pole Verze produktu by mělo být 2.6.1198.718 nebo vyšší. |neuvedeno |

### <a name="backup-extension"></a>Rozšíření zálohování
Když je agent virtuálního počítače nainstalovaný na virtuálním počítači, služba Azure Backup nainstaluje do agenta virtuálního počítače rozšíření zálohování. Služba Azure Backup bezproblémově upgraduje a opravuje rozšíření zálohování bez dalšího zásahu uživatele.

Služba Backup nainstaluje rozšíření zálohování i v případě, že virtuální počítač není spuštěný. Spuštěný virtuální počítač poskytuje největší šanci získání bodu obnovení, který je konzistentní v rámci aplikace. Služba Azure Backup nicméně bude pokračovat v zálohování virtuálního počítače, i když je vypnutý a rozšíření nebylo možné nainstalovat. Tento typ zálohování se označuje jako Virtuální počítač v režimu offline a bod obnovení je *konzistentní pro případ chyby*.

## <a name="troubleshooting-information"></a>Informace o řešení potíží
Pokud máte problémy s plněním některých úkolů v tomto článku, obraťte se na [Pokyny při řešení potíží](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Ceny
Náklady na zálohování virtuálních počítačů Azure závisí na počtu chráněných instancí. Definici chráněné instance najdete v tématu [Co je chráněná instance](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Informace o [cenách služby Backup](https://azure.microsoft.com/pricing/details/backup/) najdete na stránce Ceny služby Azure Backup.

## <a name="next-steps"></a>Další postup

[Správa](backup-azure-manage-vms.md) zálohy.
