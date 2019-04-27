---
title: Přenos dat pomocí Azure Data Boxu Edge | Microsoft Docs
description: Zjistěte, jak do zařízení Data Box Edge přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e902f0c9465f65f31f6e1a5cadc7b6b30cda1a27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647037"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Kurz: Přenos dat pomocí Azure Data Box Edge

Tento kurz popisuje, jak přidat a připojte se ke sdíleným složkám na vašem zařízení Data Box Edge. Po přidání sdílených složek na okraji pole Data můžou přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

 
## <a name="prerequisites"></a>Požadavky

Abyste mohli přidat sdílené složky do hraničních zařízení Data Box, ujistěte se, že:

- Fyzické zařízení jste nainstalovali, jak je popsáno v [nainstalovat Azure Data Box Edge](data-box-edge-deploy-install.md).

- Po dokončení aktivace fyzického zařízení, jak je popsáno v [připojení, nastavení a aktivovat Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Přidání sdílené složky

Pokud chcete vytvořit sdílenou složku, proveďte následující postup:

1. V [webu Azure portal](https://portal.azure.com/), vyberte prostředek okraj pole Data a pokračujte **přehled**. Vaše zařízení by měl být online.

   ![Online zařízení](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Vyberte **+ přidat sdílenou složku** na panelu příkazů zařízení.

   ![Přidání sdílené složky](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. V **přidat sdílenou složku** podokno, proveďte následující postup:

    a. V **název** zadejte jedinečný název pro svou sdílenou složku.  
    Název sdílené složky může mít jenom malá písmena, číslice a pomlčky. Musí být dlouhý 3 až 63 znaků a začínat písmenem nebo číslo. Pomlčky musí být před a následované písmenem nebo číslo.
    
    b. Vyberte **Typ** sdílené složky.  
    Typ může být **SMB** nebo **NFS**, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu.  
    V závislosti na tom, zda zvolíte, že sdílené složky SMB nebo NFS, zbývající možnosti mírně lišit. 

    c. Zadejte účet úložiště, kde se bude nacházet sdílenou složku. 

    
    d. V **službu Storage** rozevíracího seznamu vyberte **objektů Blob bloku**, **objektů Blob stránky**, nebo **soubory**.  
    Typ služby, kterou jste vybrali závisí na to, jaký formát chtějí dat pro použití v Azure. V tomto příkladu, protože chceme, aby k ukládání dat jako objekty BLOB bloku v Azure, vybereme **objektů Blob bloku**. Pokud vyberete **objektů Blob stránky**, ujistěte se, že vaše data jsou 512 bajtů zarovnána. Například formát VHDX je vždy zarovnaný na 512 bajtů.

    e. Vytvořit nový kontejner objektů blob nebo použijte již existující z rozevíracího seznamu. Při vytváření kontejneru objektů blob, zadejte název kontejneru. Pokud kontejner neexistuje, vytvoří se v účtu úložiště s názvem nově vytvořené sdílené složky.
   
    f. V závislosti na tom, zda jste vytvořili sdílenou složku protokolu SMB nebo sdílené složky NFS proveďte jednu z následujících kroků: 
     
    - **Sdílená složka SMB**: V části **všechny místní uživatel s oprávněním**vyberte **vytvořit nový** nebo **použít existující**. Pokud vytváříte novou místní uživatele, zadejte uživatelské jméno a heslo a potvrďte heslo. Tato akce přiřadí oprávnění na místního uživatele. Po přiřazení oprávnění tady, můžete je upravit Průzkumníka souborů.

        Pokud vyberete **Povolit jenom operace čtení** zaškrtávací políčko pro tato data sdílené složky můžete zadat jen pro čtení uživatelů.

        ![Přidání sdílené složky SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Sdílených složek NFS**: Zadejte IP adresy povolené klientů, které můžete přístup ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Vyberte **vytvořit** sdílenou složku vytvořit.
    
    Zobrazí upozornění, že vytvoření sdílené složky se v průběhu. Po vytvoření sdílené složky se zadaným nastavením **sdílené složky** dlaždici aktualizace tak, aby odrážely novou sdílenou složku.
    

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Teď můžete připojit k jednomu nebo více sdílených složek, které jste vytvořili v předchozím kroku. V závislosti na tom, jestli máte SMB nebo sdílené složky NFS, postup se může lišit.

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

V klientu Windows Server připojený do vašeho zařízení Data Box Edge připojení k serveru SMB pro sdílení zadáním příkazů:


1. V příkazovém řádku zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Po zobrazení výzvy k tomu, zadejte heslo pro sdílenou složku.  
   Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Na klávesnici stiskněte Windows + r

4. V **spustit** okno, zadejte `\\<device IP address>`a pak vyberte **OK**.  
   Otevře se Průzkumník souborů. Nyní by se zobrazit sdílené složky, které jste vytvořili jako složky. V Průzkumníku souborů dvakrát klikněte na sdílenou složku (složku) a zobrazit obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

U svého klienta systému Linux, připojení k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má názvů NFSv4 nainstalovaného klienta. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po instalaci klienta systému souborů NFS, připojte sdílenou složku systému souborů NFS, který jste vytvořili v zařízení Data Box Edge pomocí následujícího příkazu:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Použití `sync` možnost při připojení sdílené složky zlepšuje sazby za přenos velkých souborů.
    > Než připojíte sdílenou složku, ujistěte se, že budou vytvořeny již adresáře, které bude fungovat jako přípojné body na místním počítači. Tyto adresáře by neměly obsahovat jakékoli soubory nebo podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Data Box Edge přes systém souborů NFS. IP adresa zařízení je `10.10.10.60`. Sdílená složka `mylinuxshare2` je připojená k virtuálnímu počítači ubuntuVM. Přípojný bod sdílené složky je `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Na tuto verzi platí následující upozornění:
> - Po vytvoření souboru ve sdílené složce přejmenování souboru se nepodporuje. 
> - Při odstranění souboru ze sdílené složky nedojde k odstranění položky v účtu úložiště.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o následujících tématech okraj pole dat:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce

Informace o transformaci dat pomocí okraj pole dat, přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace dat pomocí Data Boxu Edge](./data-box-edge-deploy-configure-compute.md)


