---
title: Vytvoření svazku s duálním protokolem (NFSv3 a SMB) pro Azure NetApp Files | Microsoft Docs
description: Popisuje, jak vytvořit svazek, který používá duální protokol NFSv3 a SMB s podporou mapování uživatelů LDAP.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/28/2020
ms.author: b-juche
ms.openlocfilehash: 70b42fb5522b31b5e7e15b5715717f0d010d56dc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052409"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Vytvoření svazku s duálním protokolem (NFSv3 a protokolu SMB) pro Azure NetApp Files

Azure NetApp Files podporuje vytváření svazků pomocí systému souborů NFS (NFSv3 a NFSv 4.1), SMB3 nebo duálního protokolu. V tomto článku se dozvíte, jak vytvořit svazek, který využívá duální protokol NFSv3 a SMB s podporou mapování uživatelů LDAP.  


## <a name="before-you-begin"></a>Než začnete 

* Je potřeba, abyste už vytvořili fond kapacit.  
    Viz [nastavení fondu kapacit](azure-netapp-files-set-up-capacity-pool.md).   
* Podsíť musí být delegovaná na Azure NetApp Files.  
    Viz [delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Požadavky

* Ujistěte se, že splňujete [požadavky pro připojení ke službě Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Na serveru DNS vytvořte zónu zpětného vyhledávání a přidejte do této zóny zpětného vyhledávání záznam ukazatele (PTR) hostitelského počítače služby AD. V opačném případě se vytvoření svazku se dvěma protokoly nezdaří.
* Ujistěte se, že je klient NFS aktuální a že používá nejnovější aktualizace pro daný operační systém.
* Ujistěte se, že je server služby Active Directory (AD) LDAP v provozu a funguje ve službě AD. Můžete to udělat tak, že nainstalujete a nakonfigurujete roli [Služba AD LDS (Active Directory Lightweight Directory Services) (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na počítači AD.
* Svazky s duálním protokolem momentálně nepodporují Azure Active Directory Domain Services (AADDS).  
* Verze systému souborů NFS používaná svazkem s duálním protokolem je NFSv3. V takovém případě platí následující požadavky:
    * Duální protokol nepodporuje rozšířené atributy seznamů ACL systému Windows `set/get` z klientů systému souborů NFS.
    * Klienti NFS nemohou měnit oprávnění pro styl zabezpečení systému souborů NTFS a klienti systému Windows nemohou měnit oprávnění pro svazky s duálním protokolem ve stylu UNIX.   

    Následující tabulka popisuje styly zabezpečení a jejich účinky:  
    
    | Styl zabezpečení    | Klienti, kteří mohou měnit oprávnění   | Oprávnění, která mohou klienti používat  | Výsledný efektivní styl zabezpečení    | Klienti, kteří mají přístup k souborům     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | Bity režimu NFSv3   | UNIX  | Systém souborů NFS a Windows   |
    | `Ntfs`    | Windows   | Seznamy řízení přístupu NTFS     | NTFS  |Systém souborů NFS a Windows|
* Uživatelé systému UNIX, kteří přidávají svazek se zabezpečením NTFS pomocí systému souborů NFS, budou ověřeni jako uživatel systému Windows `root` pro systém UNIX `root` a `pcuser` pro všechny ostatní uživatele. Před připojením svazku při použití systému souborů NFS se ujistěte, že tyto uživatelské účty existují ve službě Active Directory. 
* Pokud máte velké topologie a používáte `Unix` styl zabezpečení se svazkem se dvěma protokoly nebo LDAP s rozšířenými skupinami, Azure NetApp Files pravděpodobně nepůjde získat přístup ke všem serverům v topologiích.  Pokud k této situaci dojde, požádejte o pomoc tým svého účtu.  <!-- NFSAAS-15123 --> 
* Pro vytvoření svazku se dvěma protokoly nepotřebujete certifikát kořenové certifikační autority serveru. Je vyžadován pouze v případě, že je povolen protokol LDAP over TLS.


## <a name="create-a-dual-protocol-volume"></a>Vytvoření svazku se dvěma protokoly

1.  V okně fondy kapacit klikněte na okno **svazky** . Kliknutím na **+ Přidat svazek** vytvořte svazek. 

    ![Přejít na svazky](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  V okně vytvořit svazek klikněte na **vytvořit** a zadejte informace pro následující pole na kartě základy:   
    * **Název svazku**      
        Zadejte název svazku, který vytváříte.   

        Název svazku musí být v rámci každého fondu kapacity jedinečný. Musí mít aspoň tři znaky dlouhé. Můžete použít jakékoli alfanumerické znaky.   

        Nemůžete použít `default` nebo `bin` jako název svazku.

    * **Fond kapacit**  
        Zadejte fond kapacit, ve kterém chcete vytvořit svazek.

    * **Kvóta**  
        Určuje velikost logického úložiště, které je přidělené svazku.  

        Pole **Dostupná kvóta** zobrazuje množství nevyužitého místa ve zvoleném fondu kapacity, které můžete použít k vytvoření nového svazku. Velikost nového svazku nesmí překročit dostupnou kvótu.  

    * **Propustnost (MiB/S)**   
        Pokud je svazek vytvořený v manuálním fondu kapacity QoS, určete propustnost, kterou pro svazek požadujete.   

        Pokud se svazek vytvoří ve fondu kapacity auto QoS, hodnota zobrazená v tomto poli je (propustnost × úroveň služby).   

    * **Virtuální síť**  
        Zadejte službu Azure Virtual Network (VNet), ze které chcete získat přístup ke svazku.  

        Virtuální síť, kterou zadáte, musí mít přidělenou podsíť Azure NetApp Files. Služba Azure NetApp Files se dá použít jenom ze stejné virtuální sítě nebo z virtuální sítě, která se nachází ve stejné oblasti jako svazek prostřednictvím partnerského vztahu virtuálních sítí. Ke svazku z místní sítě se můžete dostat i přes Express Route.   

    * **Podsíť**  
        Zadejte podsíť, kterou chcete použít pro svazek.  
        Podsíť, kterou zadáte, musí být delegovaná na Azure NetApp Files. 
        
        Pokud jste nedelegovanou podsíť, můžete na stránce vytvořit svazek kliknout na **vytvořit novou** . Pak na stránce vytvořit podsíť zadejte informace o podsíti a vyberte možnost **Microsoft. NetApp/** Volumes pro delegování podsítě pro Azure NetApp Files. V každé virtuální síti je možné delegovat jenom jednu podsíť na Azure NetApp Files.   
 
        ![Vytvoření svazku](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Vytvoření podsítě](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Pokud chcete pro svazek použít existující zásadu snímku, rozbalte ji kliknutím na **Zobrazit Upřesnit oddíl** , určete, jestli chcete cestu k snímku skrýt, a v rozevírací nabídce vyberte zásadu snímku. 

        Informace o vytváření zásad snímku najdete v tématu [Správa zásad snímků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Zobrazit rozšířený výběr](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klikněte na **protokol** a pak proveďte následující akce:  
    * Jako typ protokolu pro svazek vyberte **duální protokol (NFSv3 a SMB)** .   

    * Zadejte **cestu svazku** pro svazek.   
    Tato cesta svazku je název sdíleného svazku. Název musí začínat abecedním znakem a musí být jedinečný v rámci každého předplatného a každé oblasti.  

    * Zadejte **styl zabezpečení** , který se má použít: systém souborů NTFS (výchozí) nebo UNIX.

    * Volitelně můžete [nakonfigurovat zásadu exportu pro svazek](azure-netapp-files-configure-export-policy.md).

    ![Zadat duální protokol](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Kliknutím na tlačítko **zkontrolovat + vytvořit** zkontrolujte podrobnosti o svazku. Pak kliknutím na **vytvořit** vytvořte svazek.

    Svazek, který jste vytvořili, se zobrazí na stránce svazky. 
 
    Svazek dědí atributy předplatného, skupiny prostředků a umístění z fondu kapacity. Stav nasazení svazku můžete monitorovat na kartě Oznámení.

## <a name="manage-ldap-posix-attributes"></a>Správa atributů LDAP POSIX

Pomocí modulu snap-in konzoly MMC Uživatelé a počítače služby Active Directory můžete spravovat atributy POSIX, jako je UID, domovský adresář a další hodnoty.  Následující příklad ukazuje Editor atributů služby Active Directory:  

![Editor atributů služby Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Pro uživatele LDAP a skupiny LDAP musíte nastavit následující atributy: 
* Požadované atributy pro uživatele LDAP:   
    `uid`: Alice, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Požadované atributy pro skupiny LDAP:   
    `objectClass`: "POSIX", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>Konfigurace klienta NFS 

Postupujte podle pokynů v části [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md) ke konfiguraci klienta NFS.  

## <a name="next-steps"></a>Další kroky  

* [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md)
* [Řešení potíží se svazky se dvěma protokoly](troubleshoot-dual-protocol-volumes.md)
