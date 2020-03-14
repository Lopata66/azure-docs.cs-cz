---
title: 'Kurz: Konfigurace skupiny dostupnosti'
description: V tomto kurzu se dozvíte, jak ve službě Azure Virtual Machines vytvořit skupinu dostupnosti Always On SQL Server.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: ed5fc923c82fb0d0e4004e18159d943564c6f55e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249851"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Kurz: Konfigurace skupiny dostupnosti na Azure SQL Server VM ručně

V tomto kurzu se dozvíte, jak ve službě Azure Virtual Machines vytvořit skupinu dostupnosti Always On SQL Server. Úplný kurz vytvoří skupinu dostupnosti s replikou databáze na dvou serverech SQL.

**Časový odhad**: po dokončení splnění požadavků zabere asi 30 minut.

Diagram znázorňuje, co sestavíte v tomto kurzu.

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá základní znalost skupin dostupnosti Always On SQL Server. Pokud potřebujete další informace, přečtěte si téma [Přehled skupin dostupnosti Always On (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

V následující tabulce jsou uvedeny předpoklady, které je třeba provést před zahájením tohoto kurzu:

|  |Požadavek |Popis |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dva servery SQL | – V sadě dostupnosti Azure <br/> – V jedné doméně <br/> – Je nainstalovaná funkce clusteringu s podporou převzetí služeb při selhání |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Sdílená složka pro disk s kopií clusteru |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby SQL Server | Účet domény |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Účet služby agenta SQL Server | Účet domény |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Otevřené porty brány firewall | -SQL Server: **1433** pro výchozí instanci <br/> -Koncový bod zrcadlení databáze: **5022** nebo libovolný dostupný port <br/> – Sonda stavu IP adres služby Vyrovnávání zatížení skupiny dostupnosti: **59999** nebo jakýkoli dostupný port <br/> – Sonda stavu IP jádra pro vyrovnávání zatížení clusteru: **58888** nebo jakýkoli dostupný port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Přidat funkci clusteringu s podporou převzetí služeb při selhání | Tato funkce vyžaduje SQL Server. |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Instalační účet domény | – Místní správce na každé SQL Server <br/> – Člen pevné role serveru sysadmin SQL Server pro každou instanci SQL Server  |


Než začnete tento kurz, musíte [Dokončit požadavky na vytváření skupin dostupnosti Always On v Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Pokud jsou tyto požadavky již dokončeny, můžete přejít na příkaz [vytvořit cluster](#CreateCluster).

  >[!NOTE]
  > Mnohé z kroků uvedených v tomto kurzu teď můžete automatizovat pomocí šablon [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) a [Azure pro rychlý Start](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Vytvoření clusteru

Po dokončení požadovaných součástí je prvním krokem vytvoření clusteru s podporou převzetí služeb při selhání se systémem Windows Server, který obsahuje dva servery SQL a monitorovací Server.

1. RDP na první SQL Server pomocí doménového účtu, který je správcem na serverech SQL Server a monitorovacím serverem.

   >[!TIP]
   >Pokud jste postupovali podle [požadavků dokumentu](virtual-machines-windows-portal-sql-availability-group-prereq.md), vytvořili jste účet s názvem **CORP\Install**. Použijte tento účet.

2. Na řídicím panelu **Správce serveru** vyberte **nástroje**a potom klikněte na **Správce clusteru s podporou převzetí služeb při selhání**.
3. V levém podokně klikněte pravým tlačítkem na **Správce clusteru s podporou převzetí služeb při selhání**a pak klikněte na **vytvořit cluster**.
   ![vytvoření](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png) clusteru
4. V Průvodci vytvořením clusteru vytvořte cluster s jedním uzlem procházením stránek s nastavením v následující tabulce:

   | Stránka | Nastavení |
   | --- | --- |
   | Než začnete |Použít výchozí |
   | Vybrat servery |Do pole **Zadejte název serveru** zadejte název prvního SQL Server a klikněte na **Přidat**. |
   | Upozornění ověření |Vyberte **ne. pro tento cluster nepotřebujete podporu od Microsoftu, a proto nechcete spouštět ověřovací testy. Po kliknutí na tlačítko Další pokračovat v vytváření clusteru**. |
   | Přístupový bod pro správu clusteru |Zadejte název clusteru, například **SQLAGCluster1** v **názvu clusteru**.|
   | Potvrzení |Použijte výchozí hodnoty, pokud nepoužíváte prostory úložiště. Podívejte se na poznámku za touto tabulkou. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Nastavit IP adresu clusteru s podporou převzetí služeb při selhání Windows serveru

  > [!NOTE]
  > V systému Windows Server 2019 vytvoří cluster místo **názvu sítě clusteru** **název distribuovaného serveru** . Pokud používáte Windows Server 2019, přeskočte všechny kroky, které v tomto kurzu odkazují na základní název clusteru. Název sítě clusteru můžete vytvořit pomocí [PowerShellu](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Další informace najdete v [clusteru s podporou převzetí služeb při selhání na blogu: objekt sítě clusteru](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. V **Správce clusteru s podporou převzetí služeb při selhání**přejděte dolů na **základní prostředky clusteru** a rozbalte podrobnosti o clusteru. Měl by se zobrazit **název** a prostředky **IP adresy** ve stavu **selhání** . Prostředek IP adresy nelze uvést do režimu online, protože cluster má přiřazenou stejnou IP adresu jako samotný počítač, proto je duplicitní adresa.

2. Klikněte pravým tlačítkem na prostředek neúspěšné **IP adresy** a pak klikněte na **vlastnosti**.

   ![Vlastnosti clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Vyberte možnost **statická IP adresa** a zadejte dostupnou adresu ze stejné podsítě jako vaše virtuální počítače.

4. V části **základní prostředky clusteru** klikněte pravým tlačítkem na název clusteru a klikněte na **převést do režimu online**. Pak počkejte, dokud oba prostředky nebudou online. Když prostředek názvu clusteru přepne do online režimu, aktualizuje řadič domény pomocí nového účtu počítače služby AD. Pomocí tohoto účtu AD můžete později spustit Clusterovou službu skupiny dostupnosti.

### <a name="addNode"></a>Přidání druhého SQL Server do clusteru

Přidejte ostatní SQL Server do clusteru.

1. Ve stromové struktuře prohlížeče klikněte pravým tlačítkem na cluster a pak klikněte na **přidat uzel**.

    ![Přidat uzel do clusteru](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. V **Průvodci přidat uzel**klikněte na **Další**. Na stránce **Vybrat servery** přidejte druhý SQL Server. Do pole **Zadejte název serveru** zadejte název serveru a pak klikněte na **Přidat**. Až skončíte, klikněte na **Další**.

1. Na stránce **Upozornění na ověření** klikněte na **ne** (v produkčním scénáři byste měli provádět ověřovací testy). Pak klikněte na **Další**.

8. Pokud používáte prostory úložiště, na stránce **potvrzení** zrušte zaškrtnutí políčka **Přidat do clusteru veškeré oprávněné úložiště.**

   ![Přidat potvrzení uzlu](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Pokud používáte prostory úložiště a nechcete zrušit kontrolu **Přidat do clusteru všechny opravňující úložiště**, systém Windows virtuální disky během procesu clusteringu odpojí. V důsledku toho se neobjeví ve Správci disků nebo v Průzkumníkovi, dokud se prostory úložiště z clusteru neodstraní a znovu nepřipojí přes PowerShell. Prostory úložiště seskupují více disků do fondů úložiště. Další informace najdete v tématu [prostory úložiště](https://technet.microsoft.com/library/hh831739).

1. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

   Správce clusteru s podporou převzetí služeb při selhání ukazuje, že váš cluster má nový uzel a zobrazí ho v kontejneru **uzly** .

10. Odhlaste se z relace vzdálené plochy.

### <a name="add-a-cluster-quorum-file-share"></a>Přidat sdílenou složku kvora clusteru

V tomto příkladu používá cluster Windows ke sdílení souborů sdílenou složku k vytvoření kvora clusteru. V tomto kurzu se používá kvorum Většina uzlů a sdílených souborů. Další informace najdete v článku [Principy konfigurací kvora v clusteru s podporou převzetí služeb při selhání](https://technet.microsoft.com/library/cc731739.aspx).

1. Připojte se k členskému serveru určující sdílené složky s relací vzdálené plochy.

1. V **Správce serveru**klikněte na **nástroje**. Otevřete **správu počítače**.

1. Klikněte na **sdílené složky**.

1. Klikněte pravým tlačítkem na **sdílené složky**a pak klikněte na **Nová sdílená složka...** .

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Pomocí **Průvodce vytvořením sdílené složky** vytvořte sdílenou složku.

1. V části **cesta ke složce**klikněte na **Procházet** a vyhledejte nebo vytvořte cestu pro sdílenou složku. Klikněte na **Další**.

1. V části **název, popis a nastavení** ověřte název a cestu ke sdílené složce. Klikněte na **Další**.

1. V nastavení **oprávnění ke sdílené složce** nastavte **oprávnění přizpůsobit**. Klikněte na **vlastní...** .

1. V nabídce **přizpůsobit oprávnění**klikněte na **Přidat...** .

1. Ujistěte se, že účet použitý k vytvoření clusteru má úplné řízení.

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klikněte na tlačítko **OK**.

1. V **oprávněních ke sdílené složce**klikněte na **Dokončit**. Znovu klikněte na tlačítko **Dokončit** .  

1. Odhlášení ze serveru

### <a name="configure-cluster-quorum"></a>Konfigurace kvora clusteru

V dalším kroku nastavte kvorum clusteru.

1. Připojte se k prvnímu uzlu clusteru pomocí vzdálené plochy.

1. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte pravým tlačítkem na cluster, přejděte na **Další akce**a klikněte na **Konfigurovat nastavení kvora clusteru..** .

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. V **Průvodci konfigurací kvora clusteru**klikněte na **Další**.

1. V **možnosti vybrat konfiguraci kvora**zvolte **Vybrat určující disk kvora**a klikněte na **Další**.

1. Na **výběru vybrat určující disk kvora**klikněte na **Konfigurovat určující sdílenou složku**.

   >[!TIP]
   >Windows Server 2016 podporuje cloudový disk s kopií clusteru. Pokud zvolíte tento typ určujícího umístění, nepotřebujete určující sdílenou složku. Další informace najdete v tématu [nasazení určujícího cloudu pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). V tomto kurzu se používá určující sdílená složka, která je podporovaná v předchozích operačních systémech.

1. V části **Konfigurovat určující sdílenou složku**zadejte cestu pro sdílenou složku, kterou jste vytvořili. Klikněte na **Další**.

1. Ověřte nastavení při **potvrzení**. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

Základní prostředky clusteru jsou nakonfigurovány s určující sdílenou složkou.

## <a name="enable-availability-groups"></a>Povolit skupiny dostupnosti

Potom povolte funkci **skupiny dostupnosti AlwaysOn** . Proveďte tyto kroky na serverech SQL.

1. Z obrazovky **Start** spusťte **SQL Server Configuration Manager**.
2. Ve stromové struktuře prohlížeče klikněte **SQL Server Services**, klikněte pravým tlačítkem na službu **SQL Server (MSSQLSERVER)** a klikněte na **vlastnosti**.
3. Klikněte na kartu **Vysoká dostupnost AlwaysOn** a pak vyberte **Povolit skupiny dostupnosti AlwaysOn**, a to následujícím způsobem:

    ![Povolit Skupiny dostupnosti AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klikněte na **Použít**. V automaticky otevíraném okně klikněte na **OK** .

5. Restartujte službu SQL Server.

Opakujte tyto kroky na ostatních SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Vytvořit databázi v prvním SQL Server

1. Spusťte soubor RDP na první SQL Server s účtem domény, který je členem pevné role serveru sysadmin.
1. Otevřete SQL Server Management Studio a připojte se k první SQL Server.
7. V **Průzkumník objektů**klikněte pravým tlačítkem na **databáze** a klikněte na **Nová databáze**.
8. Do **název databáze**zadejte **MyDB1**a pak klikněte na **OK**.

### <a name="backupshare"></a>Vytvoření sdílené složky zálohy

1. V prvním SQL Server v **Správce serveru**klikněte na **nástroje**. Otevřete **správu počítače**.

1. Klikněte na **sdílené složky**.

1. Klikněte pravým tlačítkem na **sdílené složky**a pak klikněte na **Nová sdílená složka...** .

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Pomocí **Průvodce vytvořením sdílené složky** vytvořte sdílenou složku.

1. V části **cesta ke složce**klikněte na **Procházet** a vyhledejte nebo vytvořte cestu pro sdílenou složku zálohy databáze. Klikněte na **Další**.

1. V části **název, popis a nastavení** ověřte název a cestu ke sdílené složce. Klikněte na **Další**.

1. V nastavení **oprávnění ke sdílené složce** nastavte **oprávnění přizpůsobit**. Klikněte na **vlastní...** .

1. V nabídce **přizpůsobit oprávnění**klikněte na **Přidat...** .

1. Ujistěte se, že účty služby Agent SQL Server a SQL Server pro oba servery mají úplné řízení.

   ![Nová sdílená složka](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klikněte na tlačítko **OK**.

1. V **oprávněních ke sdílené složce**klikněte na **Dokončit**. Znovu klikněte na tlačítko **Dokončit** .  

### <a name="take-a-full-backup-of-the-database"></a>Proveďte úplnou zálohu databáze.

Pro inicializaci řetězce protokolu je nutné zálohovat novou databázi. Pokud neprovedete zálohu nové databáze, nelze ji zahrnout do skupiny dostupnosti.

1. V **Průzkumník objektů**klikněte pravým tlačítkem na databázi, přejděte na **úlohy...** , klikněte na **zálohovat**.

1. Kliknutím na tlačítko **OK** převezmete úplnou zálohu do výchozího umístění zálohy.

## <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti
Nyní jste připraveni ke konfiguraci skupiny dostupnosti pomocí následujících kroků:

* Vytvořte databázi na prvním SQL Server.
* Proveďte úplnou zálohu a zálohu protokolu transakcí databáze.
* Obnovení úplných záloh a zaprotokolování do druhé SQL Server pomocí možnosti **NORECOVERY**
* Vytvoření skupiny dostupnosti (**AG1**) s synchronním potvrzením, automatickým převzetím služeb při selhání a čitelnými sekundárními replikami

### <a name="create-the-availability-group"></a>Vytvořte skupinu dostupnosti:

1. V relaci vzdálené plochy k prvnímu SQL Server. V **Průzkumník objektů** v SSMS klikněte pravým tlačítkem na možnost **AlwaysOn High Availability** a klikněte na **Průvodce novou skupinou dostupnosti**.

    ![Spustit Průvodce novou skupinou dostupnosti](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na stránce **Úvod** klikněte na **Další**. Na stránce **zadat název skupiny dostupnosti** zadejte název skupiny dostupnosti, například **AG1**, do pole **název skupiny dostupnosti**. Klikněte na **Další**.

    ![Průvodce novým AG, zadání názvu AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na stránce **Vybrat databáze** vyberte svou databázi a klikněte na **Další**.

   >[!NOTE]
   >Databáze splňuje předpoklady pro skupinu dostupnosti, protože jste převzali alespoň jednu úplnou zálohu na zamýšlené primární replice.

   ![Průvodce novým AG, výběr databází](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na stránce **zadat repliky** klikněte na **Přidat repliku**.

   ![Průvodce novým AG, zadání replik](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Zobrazí se dialogové okno **připojit k serveru** . Do pole **název serveru**zadejte název druhého serveru. Klikněte na **Připojit**.

   Zpátky na stránce **zadat repliky** by se teď měl zobrazit druhý server uvedený v **replikách dostupnosti**. Repliky nakonfigurujte následujícím způsobem.

   ![Průvodce novým AG, zadání replik (dokončeno)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Kliknutím na **koncové body** zobrazíte koncový bod zrcadlení databáze pro tuto skupinu dostupnosti. Použijte stejný port, který jste použili při nastavování [pravidla brány firewall pro koncové body zrcadlení databáze](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Průvodce novým AG, výběr synchronizace počátečních dat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na stránce **Vyberte počáteční synchronizaci dat** vyberte možnost **úplné** a zadejte sdílené síťové umístění. Pro toto umístění použijte [sdílenou složku zálohy, kterou jste vytvořili](#backupshare). V tomto příkladu **\\\\\<první SQL Server\>** \\. Klikněte na **Další**.

   >[!NOTE]
   >Úplná synchronizace provede úplnou zálohu databáze na první instanci SQL Server a obnoví ji do druhé instance. U rozsáhlých databází se Úplná synchronizace nedoporučuje, protože může trvat dlouhou dobu. Tuto dobu můžete zkrátit ručním zálohováním databáze a jejím obnovením pomocí `NO RECOVERY`. Pokud je databáze již obnovena pomocí `NO RECOVERY` druhé SQL Server před konfigurací skupiny dostupnosti, vyberte možnost **pouze připojit**. Pokud chcete zálohování provést po konfiguraci skupiny dostupnosti, vyberte možnost **Přeskočit počáteční synchronizaci dat**.

    ![Průvodce novým AG, výběr synchronizace počátečních dat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na stránce **ověřování** klikněte na **Další**. Tato stránka by měla vypadat podobně jako na následujícím obrázku:

    ![Průvodce novým AG, ověřování](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Pro konfiguraci naslouchacího procesu existuje upozornění, protože jste nenakonfigurovali naslouchací proces skupiny dostupnosti. Toto upozornění můžete ignorovat, protože na virtuálních počítačích Azure vytváříte naslouchací proces po vytvoření nástroje pro vyrovnávání zatížení Azure.

10. Na stránce **Souhrn** klikněte na **Dokončit**a potom počkejte, než průvodce nakonfiguruje novou skupinu dostupnosti. Na stránce **průběh** můžete kliknutím na **Další podrobnosti** zobrazit podrobný průběh. Po dokončení průvodce Zkontrolujte stránku **výsledků** a ověřte, jestli je skupina dostupnosti úspěšně vytvořená.

     ![Průvodce novým AG, výsledky](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Kliknutím na **Zavřít** průvodce ukončíte.

### <a name="check-the-availability-group"></a>Ověřit skupinu dostupnosti

1. V **Průzkumník objektů**rozbalte možnost **vysoce dostupná dostupnost AlwaysOn**a pak rozbalte položku **skupiny dostupnosti**. Nyní by se měla zobrazit nová skupina dostupnosti v tomto kontejneru. Klikněte pravým tlačítkem na skupinu dostupnosti a pak klikněte na **zobrazit řídicí panel**.

   ![Zobrazit řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   **Řídicí panel AlwaysOn** by měl vypadat nějak takto.

   ![Řídicí panel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Můžete zobrazit repliky, režim převzetí služeb při selhání každé repliky a stav synchronizace.

2. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte na svůj cluster. Vyberte **role**. Název skupiny dostupnosti, který jste použili, je role v clusteru. Tato skupina dostupnosti nemá IP adresu pro připojení klienta, protože jste nenakonfigurovali naslouchací proces. Naslouchací proces budete konfigurovat po vytvoření nástroje pro vyrovnávání zatížení Azure.

   ![AG v Správce clusteru s podporou převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nepokoušejte se převzít služby při selhání skupiny dostupnosti z Správce clusteru s podporou převzetí služeb při selhání. Všechny operace převzetí služeb při selhání by se měly provádět v rámci **řídicího panelu AlwaysOn** v SSMS. Další informace najdete v tématu [omezení používání Správce clusteru s podporou převzetí služeb při selhání se skupinami dostupnosti](https://msdn.microsoft.com/library/ff929171.aspx).
    >

V tomto okamžiku máte skupinu dostupnosti s replikami na dvou instancích SQL Server. Skupinu dostupnosti můžete přesunout mezi instancemi. Nemůžete se připojit ke skupině dostupnosti, protože nemáte naslouchací proces. Naslouchací proces vyžaduje nástroj pro vyrovnávání zatížení ve virtuálních počítačích Azure. Dalším krokem je vytvoření nástroje pro vyrovnávání zatížení v Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Na virtuálních počítačích Azure vyžaduje Skupina dostupnosti SQL Server službu pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení uchovává IP adresy pro naslouchací procesy skupiny dostupnosti a cluster s podporou převzetí služeb při selhání systému Windows Server. Tato část shrnuje, jak vytvořit nástroj pro vyrovnávání zatížení v Azure Portal.

Azure Load Balancer může být buď Standard Load Balancer, nebo základní Load Balancer. Standard Load Balancer má více funkcí než základní Load Balancer. V případě skupiny dostupnosti se Standard Load Balancer vyžaduje, pokud použijete zónu dostupnosti (místo skupiny dostupnosti). Podrobnosti o rozdílu mezi typy nástroje pro vyrovnávání zatížení naleznete v tématu [Load Balancer porovnání skladové](../../../load-balancer/concepts-limitations.md#skus)položky.

1. V Azure Portal přejděte do skupiny prostředků, kde jsou vaše servery SQL, a klikněte na **+ Přidat**.
1. Vyhledejte **Load Balancer**. Vyberte nástroj pro vyrovnávání zatížení publikovaný Microsoftem.

   ![AG v Správce clusteru s podporou převzetí služeb při selhání](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klikněte na možnost **Vytvořit**.
1. Pro nástroj pro vyrovnávání zatížení nakonfigurujte následující parametry.

   | Nastavení | Pole |
   | --- | --- |
   | **Název** |Použijte textový název nástroje pro vyrovnávání zatížení, například **sqlLB**. |
   | **Typ** |Interní |
   | **Virtuální síť** |Použijte název virtuální sítě Azure. |
   | **Podsíť** |Použijte název podsítě, ve které se nachází virtuální počítač.  |
   | **Přiřazení IP adresy** |Statické |
   | **IP adresa** |Použijte dostupnou adresu z podsítě. Tuto adresu použijte pro naslouchací proces skupiny dostupnosti. Všimněte si, že se liší od IP adresy vašeho clusteru.  |
   | **Předplatné** |Použijte stejné předplatné jako virtuální počítač. |
   | **Umístění** |Použijte stejné umístění jako virtuální počítač. |

   Okno Azure Portal by mělo vypadat takto:

   ![Vytvoření nástroje pro vyrovnávání zatížení](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Kliknutím na **vytvořit**vytvořte Nástroj pro vyrovnávání zatížení.

Pokud chcete nakonfigurovat nástroj pro vyrovnávání zatížení, musíte vytvořit back-end fond, test a nastavit pravidla vyrovnávání zatížení. Udělejte to Azure Portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Přidat back-end fond pro naslouchací proces skupiny dostupnosti

1. V Azure Portal přejdete do skupiny dostupnosti. Možná budete muset zobrazení aktualizovat, aby se zobrazil nově vytvořený Nástroj pro vyrovnávání zatížení.

   ![Najít Load Balancer ve skupině prostředků](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klikněte na nástroj pro vyrovnávání zatížení, klikněte na **back-endové fondy**a pak klikněte na **+ Přidat**.

1. Zadejte název back-end fondu.

1. Přidružte back-end fond ke skupině dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové síťové konfigurace IP adresy**zaškrtněte **virtuální počítač** a vyberte oba virtuální počítače, které budou hostovat repliky skupin dostupnosti. Nezahrnujte souborový server sdílené složky.

   >[!NOTE]
   >Pokud nejsou zadány oba virtuální počítače, připojení budou úspěšná pouze pro primární repliku.

1. Kliknutím na tlačítko **OK** vytvořte fond back-end.

### <a name="set-the-probe"></a>Nastavení testu paměti

1. Klikněte na nástroj pro vyrovnávání zatížení, klikněte na **sondy stavu**a potom klikněte na **+ Přidat**.

1. Nastavte sondu stavu naslouchacího procesu následujícím způsobem:

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokol** | Zvolit TCP | TCP |
   | **Port** | Libovolný nepoužitý port | 59999 |
   | **Interval**  | Doba mezi pokusy o sondu v sekundách |5 |
   | **Prahová hodnota špatného stavu** | Počet po sobě jdoucích selhání testu, které se musí vyskytnout, když se virtuální počítač považuje za poškozený  | 2 |

1. Kliknutím na **OK** nastavte sondu stavu.

### <a name="set-the-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

1. Klikněte na nástroj pro vyrovnávání zatížení, klikněte na tlačítko **pravidla vyrovnávání zatížení**a klikněte na **+ Přidat**.

1. Nastavte pravidla vyrovnávání zatížení naslouchacího procesu následujícím způsobem.

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | SQLAlwaysOnEndPointListener |
   | **IP adresa front-endu** | Zvolit adresu |Použijte adresu, kterou jste vytvořili při vytváření nástroje pro vyrovnávání zatížení. |
   | **Protokol** | Zvolit TCP |TCP |
   | **Port** | Použijte port pro naslouchací proces skupiny dostupnosti. | 1433 |
   | **Port back-endu** | Toto pole se nepoužívá, je-li pro přímé vrácení serveru nastavená plovoucí IP adresa. | 1433 |
   | **Testu** |Název, který jste zadali pro test paměti | SQLAlwaysOnEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **NTato** |
   | **Časový limit nečinnosti** | Počet minut, po který se má připojení TCP nechat otevřené | 4 |
   | **Plovoucí IP adresa (přímá návrat ze serveru)** | |Povolit |

   > [!WARNING]
   > Při vytváření se nastaví přímá návrat serveru. Název není možné změnit.

1. Kliknutím na **OK** nastavte pravidla vyrovnávání zatížení naslouchacího procesu.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Přidání základní IP adresy clusteru pro cluster s podporou převzetí služeb při selhání systému Windows Server (WSFC)

IP adresa služby WSFC také musí být v nástroji pro vyrovnávání zatížení.

1. Na portálu na stejném nástroji pro vyrovnávání zatížení Azure klikněte na **front-end konfigurace IP adresy** a klikněte na **+ Přidat**. Použijte IP adresu, kterou jste nakonfigurovali pro služby WSFC v základních prostředcích clusteru. Nastavte IP adresu jako statickou.

1. V nástroji pro vyrovnávání zatížení klikněte na **sondy stavu**a pak klikněte na **+ Přidat**.

1. Následujícím způsobem nastavte test stavu hlavní IP adresy clusteru služby WSFC:

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | WSFCEndPointProbe |
   | **Protokol** | Zvolit TCP | TCP |
   | **Port** | Libovolný nepoužitý port | 58888 |
   | **Interval**  | Doba mezi pokusy o sondu v sekundách |5 |
   | **Prahová hodnota špatného stavu** | Počet po sobě jdoucích selhání testu, které se musí vyskytnout, když se virtuální počítač považuje za poškozený  | 2 |

1. Kliknutím na **OK** nastavte sondu stavu.

1. Nastavte pravidla vyrovnávání zatížení. Klikněte na tlačítko **pravidla vyrovnávání zatížení**a klikněte na **+ Přidat**.

1. Následujícím způsobem nastavte pravidla vyrovnávání zatížení základních IP adres clusteru.

   | Nastavení | Popis | Příklad
   | --- | --- |---
   | **Název** | Text | WSFCEndPoint |
   | **IP adresa front-endu** | Zvolit adresu |Použijte adresu, kterou jste vytvořili při konfiguraci IP adresy služby WSFC. To se liší od IP adresy naslouchacího procesu. |
   | **Protokol** | Zvolit TCP |TCP |
   | **Port** | Použijte port pro IP adresu clusteru. Toto je dostupný port, který se nepoužívá pro port testu naslouchacího procesu. | 58888 |
   | **Port back-endu** | Toto pole se nepoužívá, je-li pro přímé vrácení serveru nastavená plovoucí IP adresa. | 58888 |
   | **Testu** |Název, který jste zadali pro test paměti | WSFCEndPointProbe |
   | **Trvalost relace** | Rozevírací seznam | **NTato** |
   | **Časový limit nečinnosti** | Počet minut, po který se má připojení TCP nechat otevřené | 4 |
   | **Plovoucí IP adresa (přímá návrat ze serveru)** | |Povolit |

   > [!WARNING]
   > Při vytváření se nastaví přímá návrat serveru. Název není možné změnit.

1. Kliknutím na tlačítko **OK** nastavte pravidla vyrovnávání zatížení.

## <a name="configure-listener"></a>Konfigurace naslouchacího procesu

Dalším krokem je konfigurace naslouchacího procesu skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání.

> [!NOTE]
> V tomto kurzu se dozvíte, jak vytvořit jeden naslouchací proces – s jednou IP adresou interního nástroje. Pokud chcete vytvořit jeden nebo více posluchačů pomocí jedné nebo více IP adres, přečtěte si téma [Vytvoření naslouchacího procesu skupiny dostupnosti a nástroje pro vyrovnávání zatížení | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Nastavit port naslouchacího procesu

V SQL Server Management Studio nastavte port naslouchacího procesu.

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

1. Přejděte do **skupin dostupnosti** **AlwaysOn s vysokou dostupností** |  | **naslouchací procesy skupiny dostupnosti**.

1. Nyní byste měli vidět název naslouchacího procesu, který jste vytvořili v Správce clusteru s podporou převzetí služeb při selhání. Klikněte pravým tlačítkem myši na název naslouchacího procesu a klikněte na **vlastnosti**.

1. Do pole **port** zadejte číslo portu pro naslouchací proces skupiny dostupnosti. 1433 je výchozí hodnota a pak klikněte na **OK**.

Teď máte SQL Server skupinu dostupnosti ve virtuálních počítačích Azure, které běží v režimu Správce prostředků.

## <a name="test-connection-to-listener"></a>Testovat připojení ke službě Listener

Otestování připojení:

1. Protokol RDP na SQL Server, který je ve stejné virtuální síti, ale nevlastní repliku. V clusteru můžete použít jiné SQL Server.

1. K otestování připojení použijte nástroj **Sqlcmd** . Například následující skript vytvoří připojení **Sqlcmd** k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Pokud naslouchací proces používá jiný port než výchozí port (1433), zadejte port v připojovacím řetězci. Například následující příkaz Sqlcmd se připojí k naslouchacího procesu na portu 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Připojení SQLCMD se automaticky připojí k jakékoli instanci SQL Server hostuje primární repliku.

> [!TIP]
> Ujistěte se, že port, který zadáte, je otevřený v bráně firewall obou serverů SQL. Oba servery vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [Přidání nebo úprava pravidla brány firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Další kroky

- [Přidejte IP adresu do nástroje pro vyrovnávání zatížení pro druhou skupinu dostupnosti](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
