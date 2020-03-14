---
title: Přenos dat pomocí služby Azure Data Box Gateway | Microsoft Docs
description: Zjistěte, jak na zařízení Data Box Gateway přidat sdílené složky a připojit se k nim.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 32466cc0a1ab9b86fc2fb8eb791c232ae13f1c01
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213560"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Kurz: přenos dat pomocí Azure Data Box Gateway


## <a name="introduction"></a>Úvod

Tento článek popisuje, jak přidat sdílené složky a připojit se k nim na Data Box Gateway. Po přidání sdílených složek může Data Box Gateway zařízení přenášet data do Azure.

Dokončení tohoto postupu může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Přidání sdílené složky
> * Připojení ke sdílené složce

## <a name="prerequisites"></a>Požadavky

Před přidáním sdílených složek do služby Data Box Gateway se ujistěte, že:

- Zřídili jste virtuální zařízení a připojili se k němu, jak je popsáno v části [zřízení data box Gateway v Hyper-v](data-box-gateway-deploy-provision-hyperv.md) , nebo [zřízení data box Gateway ve VMware](data-box-gateway-deploy-provision-vmware.md).

- Aktivovali jste virtuální zařízení popsané v tématu [připojit a aktivovat Azure Data box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Zařízení je připravené pro vytvoření sdílených složek a přenos dat.

## <a name="add-a-share"></a>Přidání sdílené složky

Chcete-li vytvořit sdílenou složku, postupujte podle následujících pokynů:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj prostředek data box Gateway a pak přejít na **Přehled**. Vaše zařízení by mělo být online. Na panelu příkazů zařízení vyberte **+ Přidat sdílenou složku** .
   
   ![Přidání sdílené složky](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. V části **Přidat sdílenou složku**proveďte následující postup:

    1. Zadejte jedinečný název sdílené složky. Názvy sdílených složek můžou obsahovat jenom malá písmena, číslice a spojovníky. Název sdílené složky musí mít délku 3 až 63 znaků a musí začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník.
    
    2. Vyberte **Typ** sdílené složky. Typ může být SMB nebo NFS, přičemž výchozí typ je SMB. Protokol SMB je standardem pro klienty Windows a systém souborů NFS se používá pro klienty Linuxu. V závislosti na tom, jestli vyberete sdílené složky SMB nebo systému souborů NFS, se budou mírně lišit zobrazené možnosti.

    3. Zadejte účet úložiště, ve kterém se sdílená složka bude nacházet. Pokud kontejner ještě neexistuje, vytvoří se v účtu úložiště s nově vytvořeným názvem sdílené složky. Pokud kontejner již existuje, bude tento kontejner použit.
       > [!IMPORTANT]
       > Ujistěte se, že účet Azure Storage, který používáte, nemá nastavené zásady neměnnosti, pokud ho používáte s Azure Stack Edge nebo Data Box Gateway zařízení. Další informace najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).
    
    4. V části **Služba úložiště** zvolte Objekt blob bloku, Objekt blob stránky nebo Soubory. Zvolený typ služby závisí na tom, v jakém formátu chcete ukládat data v Azure. Například v tomto případě chceme data v Azure ukládat jako objekty blob bloku, proto vybereme Objekt blob bloku. Pokud zvolíte Objekt blob stránky, musíte zajistit, aby vaše data byla zarovnaná na 512 bajtů. Formát VHDX je například vždy zarovnaný na 512 bajtů.
   
    5. Postup v tomto kroku závisí na tom, jestli vytváříte sdílenou složku SMB nebo systému souborů NFS.
     
    - **Sdílená složka SMB** – v části **všechny oprávnění místního uživatele**vyberte **vytvořit novou** nebo **použít existující**. Pokud vytvoříte nového místního uživatele, zadejte **uživatelské jméno** a **heslo**a **potvrďte heslo**. Tato akce přiřadí oprávnění místnímu uživateli. Úprava oprávnění na úrovni sdílené složky se v tuto chvíli nepodporuje.
    
        ![Přidání sdílené složky SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Pokud pro tato sdílená data vyberete zaškrtávací políčko **pro oprávnění jen pro čtení** , můžete zadat uživatele jen pro čtení.
        
    - **Sdílená složka NFS** – zadejte IP adresy povolených klientů, kteří mají přístup ke sdílené složce.

        ![Přidání sdílené složky systému souborů NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Vyberte **vytvořit** a vytvořte sdílenou složku.
    
    Zobrazí se oznámení o tom, že probíhá vytváření sdílené složky. Po vytvoření sdílené složky se zadaným nastavením se **sdílené** složky budou aktualizovat, aby odrážely novou sdílenou složku.
    
    ![Dlaždice aktualizovaných sdílených složek](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Připojení ke sdílené složce

Nyní se můžete připojit k jedné nebo více sdíleným složkám, které jste vytvořili v posledním kroku. Postup se může lišit v závislosti na tom, jestli máte sdílenou složku SMB nebo NFS.

### <a name="connect-to-an-smb-share"></a>Připojení ke sdílené složce SMB

Na klientovi Windows serveru připojeném k vašemu Data Box Gateway se připojte ke sdílené složce SMB zadáním příkazů:


1. V příkazovém okně zadejte:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Po zobrazení výzvy zadejte heslo ke sdílené složce. Tady je ukázkový výstup tohoto příkazu.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Na klávesnici vyberte Windows + R. 
3. V okně **Spustit** zadejte `\\<device IP address>` a pak vyberte **OK**. Otevře se Průzkumník souborů. Nyní byste měli mít přehled o sdílených složkách, které jste vytvořili jako složky. V Průzkumníku souborů poklikejte na sdílenou složku (složku) a zobrazte obsah.
 
    ![Připojení ke sdílené složce SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Do těchto sdílených složek se zapisují generovaná data a zařízení odesílá tato data do cloudu.

### <a name="connect-to-an-nfs-share"></a>Připojení ke sdílené složce systému souborů NFS

V klientském počítači se systémem Linux připojeném k zařízení Data Box Edge proveďte následující postup:

1. Ujistěte se, že klient má nainstalovaného klienta názvů NFSv4. Klienta systému souborů NFS nainstalujete následujícím příkazem:

   `sudo apt-get install nfs-common`

    Další informace najdete v článku věnovaném [instalaci klienta NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po nainstalování klienta systému souborů NFS pomocí následujícího příkazu připojte sdílenou složku systému souborů NFS, kterou jste vytvořili, ke svému zařízení Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Před nastavením připojení se ujistěte, že adresáře, které budou fungovat jako přípojné body na vašem místním počítači, už jsou vytvořené a neobsahují žádné soubory ani podsložky.

    Následující příklad ukazuje připojení ke sdílené složce na zařízení Gateway přes systém souborů NFS. IP adresa virtuálního zařízení je `10.10.10.60`, sdílená složka `mylinuxshare2` se připojí k virtuálnímu počítači s Ubuntu a přípojný bod je `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> V této verzi se vztahují následující omezení:
> - Po vytvoření souboru ve sdílených složkách se přejmenování souboru nepodporuje.
> - Odstraněním souboru ze sdílené složky se neodstraní příslušná položka v účtu úložiště.
> - Pokud ke kopírování dat používáte `rsync`, možnost `rsync -a` není podporovaná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Přidání sdílené složky
> * Připojení ke sdílené složce


V dalším kurzu se dozvíte, jak službu Data Box Gateway spravovat.

> [!div class="nextstepaction"]
> [Správa služby Data Box Gateway pomocí místního webového uživatelského rozhraní](https://aka.ms/dbg-docs)


