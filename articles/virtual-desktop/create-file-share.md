---
title: Vytvoření sdílené složky souborů Azure s řadičem domény – Azure
description: Nastavte kontejner profilu FSLogix ve sdílené složce Azure ve stávajícím fondu hostitele virtuálních počítačů s Windows s doménou Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4723c2a8fa66e4ed2c4b40975179d7d4d2b281d6
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484658"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Vytvoření kontejneru profilu se soubory Azure a služba AD DS

V tomto článku se dozvíte, jak vytvořit sdílenou složku Azure ověřenou řadičem domény ve stávajícím fondu hostitelů virtuálních počítačů s Windows. Tuto sdílenou složku můžete použít k ukládání profilů úložiště.

Tento proces používá Active Directory Domain Services (služba AD DS), což je adresářová služba Prem. Pokud hledáte informace o tom, jak vytvořit kontejner profilu FSLogix pomocí Azure služba AD DS, přečtěte si téma [vytvoření kontejneru profilu FSLogix se soubory Azure](create-profile-container-adds.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že je váš řadič domény synchronizovaný do Azure a je možné ho přeložit ze služby Azure Virtual Network (VNET), ke které jsou hostitelé relace připojení.

## <a name="set-up-a-storage-account"></a>Nastavení účtu úložiště

Nejdřív budete muset nastavit účet úložiště Azure Files.

Nastavení účtu úložiště:

1. Přihlaste se k portálu Azure.

2. Na panelu hledání vyhledejte **účet úložiště** .

3. Vyberte **+ Přidat**.

4. Do stránky **vytvořit účet úložiště** zadejte následující informace:

    - Vytvořte novou skupinu prostředků.
    - Zadejte jedinečný název účtu úložiště.
    - Pro **umístění**doporučujeme vybrat stejné umístění jako fond hostitelů virtuálních počítačů s Windows.
    - **Výkon** – vyberte **Standard**. (V závislosti na požadavcích na IOPS. Další informace najdete v tématu [Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows](store-fslogix-profile.md).)
    - Jako **typ účtu**vyberte **StorageV2** nebo **úložiště**.
    - V případě **replikace**vyberte **místně redundantní úložiště (LRS)**.

5. Až budete hotovi, vyberte **zkontrolovat + vytvořit**a pak vybrat **vytvořit**.

Pokud potřebujete podrobnější pokyny ke konfiguraci, přečtěte si téma věnované [místní dostupnosti](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability).

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

V dalším kroku budete muset vytvořit sdílenou složku Azure.

Vytvoření sdílené složky:

1. Vyberte **Přejít k prostředku**.

2. Na stránce Přehled vyberte **sdílení souborů**.

3. Vyberte **+ sdílené složky**, vytvořte novou sdílenou složku s názvem **Profiles**a pak zadejte vhodnou kvótu nebo nechte pole prázdné pro možnost žádná kvóta.

4. Vyberte **Vytvořit**.

## <a name="enable-active-directory-authentication"></a>Povolit ověřování služby Active Directory

V dalším kroku budete muset povolit ověřování služby Active Directory (AD). Pokud chcete tuto zásadu povolit, musíte postupovat podle pokynů v počítači, který je už připojený k doméně. Pokud chcete povolit ověřování, postupujte podle těchto pokynů na virtuálním počítači, na kterém běží řadič domény:

1. Protokol RDP (Remote Desktop Protocol) do virtuálního počítače připojeného k doméně.

2. Podle pokynů v části [Povolení ověřování azure služba AD DS pro sdílené složky Azure](../storage/files/storage-files-identity-ad-ds-enable.md) nainstalujte modul AzFilesHybrid a povolte ověřování.

3.  Otevřete Azure Portal, otevřete svůj účet úložiště, vyberte **Konfigurace**a potvrďte, že **Služba Active Directory (AD)** je nastavená na **povoleno**.

     ![Snímek obrazovky konfigurační stránky s povoleným Azure Active Directory (AD).](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Přiřazení oprávnění Azure RBAC uživatelům pro virtuální počítače s Windows

Všem uživatelům, kteří musí mít v účtu úložiště uložené profily FSLogix, musí mít přiřazenou roli Přispěvatel sdílené složky SMB pro data souborů úložiště. 

Uživatelé přihlášeni k hostitelům relace virtuálních počítačů s Windows potřebují přístupová oprávnění pro přístup ke sdílené složce souborů. Udělení přístupu ke sdílené složce Azure zahrnuje konfiguraci oprávnění na úrovni sdílené složky i na úrovni NTFS, podobně jako tradiční sdílená složka systému Windows.

Pokud chcete nakonfigurovat oprávnění na úrovni sdílené složky, přiřaďte každému uživateli roli s příslušnými přístupovými oprávněními. Oprávnění lze přiřadit buď k jednotlivým uživatelům, nebo ke skupině Azure AD. Další informace najdete v tématu [Přiřazení přístupových oprávnění k identitě](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>Účty nebo skupiny, kterým přiřadíte oprávnění, by měly být vytvořeny v doméně a synchronizovány se službou Azure AD. Účty vytvořené ve službě Azure AD nebudou fungovat.

Přiřazení oprávnění řízení přístupu na základě role (RBAC):

1. Otevřete web Azure Portal.

2. Otevřete účet úložiště, který jste vytvořili v části [Nastavení účtu úložiště](#set-up-a-storage-account).

3. Vyberte **Access Control (IAM)**.

4. Vyberte **Přidat přiřazení role**.

5. Na kartě **Přidat přiřazení role** vyberte **úložiště souborová data SMB sdílet oprávnění Přispěvatel** pro účet správce.
   
     Pokud chcete přiřadit oprávnění uživatele k profilům FSLogix, postupujte podle těchto pokynů. Když se ale dostanete ke kroku 5, vyberte místo toho možnost **soubor úložiště SMB sdílení** .

6. Vyberte **Uložit**.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Přiřazení oprávnění uživatelů ke sdílené složce Azure

Jakmile uživatelům přiřadíte oprávnění RBAC, budete muset nakonfigurovat oprávnění systému souborů NTFS.

Abyste mohli začít, musíte si od Azure Portal víc věcí:

- Cesta UNC.
- Klíč účtu úložiště.

### <a name="get-the-unc-path"></a>Získat cestu UNC

Tady je postup, jak získat cestu UNC:

1. Otevřete web Azure Portal.

2. Otevřete účet úložiště, který jste vytvořili v části [Nastavení účtu úložiště](#set-up-a-storage-account).

3. Vyberte **Nastavení**a pak vyberte **vlastnosti**.

4. Zkopírujte identifikátor URI **koncového bodu služby primární souborové služby** do textového editoru dle vašeho výběru.

5. Po zkopírování identifikátoru URI proveďte následující kroky a změňte je na cestu UNC:

    - Odebrat `https://`
    - Místo lomítka nahraďte zpětným `/` lomítkem `\` .
    - Přidejte název sdílené složky, kterou jste vytvořili v části [Vytvoření sdílené složky Azure](#create-an-azure-file-share) , na konec názvu UNC.

        Příklad: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Získání klíče účtu úložiště

Získání klíče účtu úložiště:

1. Otevřete web Azure Portal.

2. Otevřete účet úložiště, který jste vytvořili v části [Nastavení účtu úložiště](#set-up-a-storage-account).

3. Na kartě **účet úložiště** vyberte **přístupové klíče**.

4. Zkopírujte **klíč1** nebo **key2** do souboru na místním počítači.

### <a name="configure-ntfs-permissions"></a>Konfigurace oprávnění systému souborů NTFS

Konfigurace oprávnění systému souborů NTFS:

1. Otevřete příkazový řádek na virtuálním počítači připojeném k doméně.

2. Spusťte následující rutinu pro připojení sdílené složky Azure a přiřazení písmene jednotky:

     ```powershell
     net use <desired-drive-letter>: <UNC-pat> <SA-key> /user:Azure\<SA-name>
     ```

3. Spuštěním následující rutiny zkontrolujte přístupová oprávnění ke sdílené složce Azure:
    
    ```powershell
    icacls <mounted-drive-letter>:
    ```

    Nahraďte `<mounted-drive-letter>` písmenem jednotky, na kterou jste namapovali.

    Pro *uživatele NT AUTHORITY\Authenticated Users* a *BUILTIN\Users* mají ve výchozím nastavení určitá oprávnění. Tato výchozí oprávnění umožňují těmto uživatelům číst kontejnery profilů jiných uživatelů. Oprávnění popsaná v tématu [Konfigurace oprávnění pro úložiště pro použití s kontejnery profilů a kontejnery Office](/fslogix/fslogix-storage-config-ht) ale neumožňují uživatelům číst kontejnery profilů pro všechny ostatní.

4. Spuštěním následujících rutin umožníte uživatelům virtuálních počítačů s Windows vytvářet vlastní kontejnery profilů a zablokovat přístup ke svému kontejneru profilů z jiných uživatelů.
     
     ```powershell
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Nahraďte <připojeného písmene> písmenem jednotky, kterou jste použili k namapování jednotky.
     - Nahraďte <uživatelem-e-mailem> pomocí hlavního názvu uživatele (UPN) uživatele nebo skupiny Active Directory, který obsahuje uživatele, kteří budou vyžadovat přístup ke sdílené složce.

     Příklad:

     ```powershell
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

5. Vyberte **Apply** (Použít).

## <a name="configure-fslogix-on-session-host-vms"></a>Konfigurace FSLogix na virtuálních počítačích hostitele relací

V této části se dozvíte, jak nakonfigurovat virtuální počítač pomocí FSLogix. Při každé konfiguraci hostitele relace budete muset postupovat podle těchto pokynů. Než začnete s konfigurací, postupujte podle pokynů v tématu [Stažení a instalace FSLogix](/fslogix/install-ht). K dispozici je několik možností, které zajišťují, aby byly klíče registru nastaveny na všech hostitelích relací. Tyto možnosti můžete nastavit v imagi nebo nakonfigurovat zásady skupiny.

Konfigurace FSLogix na VIRTUÁLNÍm počítači hostitele relace:

1. RDP na virtuální počítač hostitele relace fondu hostitelů virtuálních ploch Windows.

2. [Stáhněte a nainstalujte FSLogix](/fslogix/install-ht).

5. Postupujte podle pokynů v části [Konfigurace nastavení registru kontejneru profilu](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Přejděte na **počítač**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**.

    - Vytvořte klíč **profilů** .

    - Vytvořit **povoleno, DWORD** s hodnotou 1.

    - Vytvořte **VHDLocations MULTI_SZ**.

    - Nastavte hodnotu **VHDLocations** na cestu UNC, kterou jste vygenerovali, a [Získejte cestu UNC](#get-the-unc-path).

6. Restartujte virtuální počítač.

## <a name="testing"></a>Testování

Jakmile nainstalujete a nakonfigurujete FSLogix, můžete otestovat nasazení přihlášením pomocí uživatelského účtu, kterému byla přiřazena skupina aplikací nebo plocha ve fondu hostitelů. Ujistěte se, že uživatelský účet, ke kterému se přihlašujete, má oprávnění ke sdílení souborů.

Pokud se uživatel přihlásil dřív, bude mít existující místní profil, který bude použit během této relace. Abyste se vyhnuli vytváření místního profilu, buď vytvořte nový uživatelský účet, který se má použít pro testy, nebo použijte metody konfigurace popsané v tématu [kurz: Konfigurace kontejneru profilů pro přesměrování uživatelských profilů](/fslogix/configure-profile-container-tutorial/).

Chcete-li ověřit vaše oprávnění v relaci:

1. Spusťte relaci na virtuálním počítači s Windows.

2. Otevřete web Azure Portal.

3. Otevřete účet úložiště, který jste vytvořili v části [Nastavení účtu úložiště](#set-up-a-storage-account).

4. Na stránce vytvoření sdílené složky Azure vyberte **vytvořit sdílenou složku** .

5. Ujistěte se, že složka obsahující profil uživatele teď ve vašich souborech existuje.

Pokud chcete provést další testování, postupujte podle pokynů v tématu a ujistěte se, [že váš profil funguje](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Další kroky

Řešení potíží s FSLogix najdete v [tomto průvodci odstraňováním potíží](/fslogix/fslogix-trouble-shooting-ht).
