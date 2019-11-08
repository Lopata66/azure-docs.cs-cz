---
title: Zálohování SQL Server úloh na Azure Stack
description: V tomto článku se dozvíte, jak nakonfigurovat Microsoft Azure Backup Server (MABS) pro ochranu SQL Server databáze na Azure Stack.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: 28d4ad1a94cea6f21d1fe75483357d8788524b88
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747220"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Zálohování SQL Server v Azure Stack

Tento článek slouží ke konfiguraci serveru Microsoft Azure Backup (MABS) k ochraně SQL Server databáze na Azure Stack.

Správa zálohování SQL Server databáze do Azure a obnovení z Azure zahrnuje tři kroky:

1. Vytvoření zásady zálohování pro ochranu SQL Serverch databází
2. Vytváření záložních kopií na vyžádání
3. Obnovte databázi z disků a z Azure

## <a name="before-you-start"></a>Než začnete

[Instalace a příprava Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Vytvoření zásady zálohování pro ochranu SQL Serverch databází do Azure

1. V uživatelském rozhraní Azure Backup Server klikněte na pracovní prostor **ochrana** .

2. Na pásu karet nástroje klikněte na **Nový** a vytvořte novou skupinu ochrany.

    ![Vytvořit skupinu ochrany](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server spustí Průvodce skupinou ochrany, která vás provede vytvořením **skupiny ochrany**. Klikněte na **Další**.

3. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **servery**.

    ![Vybrat typ skupiny ochrany – servery](./media/backup-azure-backup-sql/pg-servers.png)

4. Na obrazovce **Vybrat členy skupiny** se v seznamu dostupné členy zobrazí různé zdroje dat. Kliknutím na **+** rozbalte složku a odhalte podsložky. Pokud chcete vybrat položku, klikněte na zaškrtávací políčko.

    ![Výběr databáze SQL](./media/backup-azure-backup-sql/pg-databases.png)

    Všechny vybrané položky se zobrazí v seznamu vybrané členy. Po výběru serverů nebo databází, které chcete chránit, klikněte na tlačítko **Další**.

5. Na obrazovce **Vybrat způsob ochrany dat** zadejte název skupiny ochrany a zaškrtněte políčko **Chci online ochranu** .

    ![Metoda ochrany dat – krátkodobý & disku online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Na obrazovce **zadat krátkodobé cíle** uveďte nezbytné vstupy pro vytváření záložních bodů na disku a klikněte na **Další**.

    V tomto příkladu je **Rozsah uchování** **5 dní**, **Frekvence synchronizace** je každých **15 minut**, což je četnost zálohování. **Expresní úplné zálohování** je nastaveno na **8:00 P. M**.

    ![Krátkodobé cíle](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > V příkladu zobrazeném v 8:00 PM každý den se vytvoří bod zálohování, a to tak, že se upraví upravená data z bodu zálohy v předchozím dnu na 8:00 odp. Tento proces se nazývá **expresní úplné zálohování**. Protokoly transakcí se synchronizují každých 15 minut. Pokud potřebujete obnovit databázi v 9:00 PM, bod se vytvoří z protokolů z posledního bodu expresního úplného zálohování (v tomto případě 8PM).
   >
   >

7. Na obrazovce **Kontrola přidělení disku** ověřte, zda je k dispozici celkové místo v úložišti, a na možné místo na disku. Klikněte na **Další**.

8. V části **Vybrat způsob vytvoření repliky**vyberte, jak se má vytvořit první bod obnovení. Prvotní zálohu můžete přenést ručně (mimo síť), abyste se vyhnuli zahlcení šířky pásma nebo přes síť. Pokud se rozhodnete počkat na přenos prvního zálohování, můžete zadat čas počátečního přenosu. Klikněte na **Další**.

    ![Metoda počáteční replikace](./media/backup-azure-backup-sql/pg-manual.png)

    Prvotní záložní kopie vyžaduje přenos celého zdroje dat (SQL Server databáze) z provozního serveru (SQL Server počítače) na Azure Backup Server. Tato data můžou být velká a přenos dat přes síť by mohl překročit šířku pásma. Z tohoto důvodu se můžete rozhodnout pro přenos prvotní zálohy: **ručně** (pomocí vyměnitelného média), abyste se vyhnuli zahlcení šířky pásma nebo **automaticky přes síť** (v určitou dobu).

    Po dokončení prvotního zálohování jsou zbývající zálohy přírůstkové zálohy v prvotní záložní kopii. Přírůstkové zálohování je obvykle malé a snadno se přenáší přes síť.

9. Vyberte, kdy chcete spustit kontrolu konzistence, a klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server provede kontrolu konzistence integrity bodu zálohování. Azure Backup Server vypočítá kontrolní součet záložního souboru na provozním serveru (SQL Server počítači v tomto scénáři) a zálohovaných dat pro tento soubor. Pokud dojde ke konfliktu, předpokládá se, že zálohovaný soubor na Azure Backup Server je poškozený. Azure Backup Server opravit zálohovaná data odesláním bloků odpovídajících neshodě kontrolního součtu. Vzhledem k tomu, že kontroly konzistence jsou náročné na výkon, můžete naplánovat kontrolu konzistence nebo ji spustit automaticky.

10. Chcete-li určit online ochranu zdrojů dat, vyberte databáze, které chcete chránit pomocí Azure a klikněte na tlačítko **Další**.

    ![Vybrat zdroje dat](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Vyberte plány zálohování a zásady uchovávání informací, které vyhovují zásadám organizace.

    ![Plán a uchovávání](./media/backup-azure-backup-sql/pg-schedule.png)

    V tomto příkladu se zálohy odebírají jednou denně v 12:00 PM a 8 ODP. (dolní část obrazovky)

    > [!NOTE]
    > Je dobrým zvykem, aby na disku bylo několik krátkodobých bodů obnovení pro rychlé obnovení. Tyto body obnovení se používají pro provozní obnovení. Azure slouží jako dobré umístění mimo pracoviště s vyšší Slaou a zaručenou dostupností.
    >
    >

    **Osvědčený postup**: Pokud naplánujete zálohování do Azure tak, aby se spouštělo po dokončení zálohování místních disků, nejnovější zálohy na disku se vždycky zkopírují do Azure.

12. Vyberte plán zásad uchovávání informací. Podrobnosti o tom, jak fungují zásady uchovávání informací, najdete v [článku použití Azure Backup k nahrazení vaší páskové infrastruktury](backup-azure-backup-cloud-as-tape.md).

    ![Zásady uchovávání informací](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    V tomto příkladu:

    * Zálohy se ponechají jednou denně v 12:00 PM a 8 PM (dolní část obrazovky) a uchovávají se po dobu 180 dnů.
    * Záloha v sobotu v 12:00. odp. je uchováno po dobu 104 týdnů
    * Záloha na poslední sobotu v 12:00. odp. je uchováno po dobu 60 měsíců
    * Záloha na poslední sobotu v březnu v 12:00. odp. je uchováno po dobu 10 let
13. Klikněte na **Další** a vyberte odpovídající možnost pro přenos prvotní záložní kopie do Azure. Můžete vybrat možnost **automaticky přes síť** .

14. Po kontrole podrobností zásad na obrazovce **souhrnu** dokončete pracovní postup kliknutím na **vytvořit skupinu** . Můžete kliknout na **Zavřít** a monitorovat průběh úlohy v pracovním prostoru monitorování.

    ![Probíhá vytváření skupiny ochrany.](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Zálohování databáze SQL Server na vyžádání

Zatímco předchozí kroky vytvořily zásady zálohování, vytvoří se bod obnovení jenom v případě, že dojde k prvnímu zálohování. Místo čekání na vystavení plánovače pak níže uvedené kroky aktivují vytvoření bodu obnovení ručně.

1. Před vytvořením bodu obnovení počkejte, až se stav skupiny ochrany zobrazí v poli **OK** pro databázi.

    ![Členové skupiny ochrany](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klikněte pravým tlačítkem na databázi a vyberte **vytvořit bod obnovení**.

    ![Vytvořit bod obnovení online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. V rozevírací nabídce vyberte možnost **online ochrana** a kliknutím na tlačítko **OK** Začněte vytvářet body obnovení v Azure.

    ![Vytvořit bod obnovení](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Podívejte se na průběh úlohy v pracovním prostoru **monitorování** .

    ![Konzola monitorování](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Obnovení databáze SQL Server z Azure

K obnovení chráněné entity (SQL Server databáze) z Azure se vyžadují následující kroky.

1. Otevřete konzolu pro správu Azure Backup Server. Přejděte do pracovního prostoru **obnovení** , kde můžete zobrazit chráněné servery. Vyhledejte požadovanou databázi (v tomto případě ReportServer $ MSDPM2012). Vyberte **obnovení z** času, který je zadaný jako **online** bod.

    ![Vybrat bod obnovení](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klikněte pravým tlačítkem myši na název databáze a pak klikněte na tlačítko **obnovit**.

    ![Obnovení z Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS zobrazuje podrobnosti bodu obnovení. Klikněte na **Další**. Chcete-li přepsat databázi, vyberte typ obnovení **obnovit do původní instance SQL Server**. Klikněte na **Další**.

    ![Obnovit do původního umístění](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    V tomto příkladu MABS obnoví databázi do jiné instance SQL Server nebo do samostatné síťové složky.

4. Na obrazovce **zadat možnosti obnovení** můžete vybrat možnosti obnovení, jako je omezování využití šířky pásma sítě, a omezit tak šířku pásma použitou obnovením. Klikněte na **Další**.

5. Na obrazovce **Souhrn** uvidíte všechny konfigurace obnovení, které jsou doposud k dispozici. Klikněte na tlačítko **obnovit**.

    Stav obnovení ukazuje obnovenou databázi. Kliknutím na **Zavřít** můžete Průvodce zavřít a zobrazit průběh v pracovním prostoru **monitorování** .

    ![Zahájit proces obnovení](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po dokončení obnovení je obnovená databáze konzistentní vzhledem k aplikacím.

## <a name="next-steps"></a>Další kroky

Viz článek [záložní soubory a aplikace](backup-mabs-files-applications-azure-stack.md) .
Viz článek [zálohování služby SharePoint na Azure Stack](backup-mabs-sharepoint-azure-stack.md) .
