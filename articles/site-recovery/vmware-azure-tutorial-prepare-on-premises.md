---
title: Příprava místních serverů VMware na zotavení po havárii virtuálních počítačů VMware do Azure | Microsoft Docs
description: Zjistěte, jak připravit místní servery VMware na zotavení po havárii do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1095a80ba05aa3e0ae6dfcd526db7ffd18fb9d4d
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359365"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Příprava místních serverů VMware na zotavení po havárii do Azure

Tento článek popisuje, jak Příprava místních serverů VMware na zotavení po havárii do Azure s využitím [Azure Site Recovery](site-recovery-overview.md) služby. 

Toto je druhý kurz řady, která ukazuje, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. V prvním kurzu jste [nastavili komponenty Azure](tutorial-prepare-azure.md) potřebné pro zotavení po havárii VMware.


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Příprava účtu na vCenter serveru nebo hostiteli vSphere ESXi, pro automatizaci zjišťování virtuálních počítačů.
> * Příprava účtu pro automatickou instalaci služby Mobility na virtuální počítače VMware.
> * Zkontrolujte VMware serveru a požadavky na virtuální počítač a podporu.
> * Připravte se na připojení k virtuálním počítačům Azure po převzetí služeb při selhání.

> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny přečtěte si článek v části How To Site Recovery obsahu.

## <a name="before-you-start"></a>Než začnete

Ujistěte se, že jste připravili Azure jak je popsáno v [prvního kurzu této série](tutorial-prepare-azure.md).

## <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Potřebujete účet, který může spouštět operace, jako jsou vytváření a odebírání disků a práce s virtuálními počítači.

Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Roli pojmenujte například **Azure_Site_Recovery**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

### <a name="vmware-account-permissions"></a>Oprávnění účtu VMware

**Úkol** | **Role nebo oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.
**Úplná replikace, převzetí služeb při selhání, navrácení služeb po obnovení** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

## <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na počítače, které chcete replikovat, bude nutné nainstalovat službu Mobility. Pokud povolíte replikaci počítače, může Site Recovery provést nabízenou instalaci této služby, nebo ji můžete nainstalovat ručně nebo pomocí instalačních nástrojů.

- V tomto kurzu provedeme instalaci služby Mobility pomocí nabízené instalace.
- U nabízené instalace musíte připravit účet, který služba Site Recovery může použít k získání přístupu k virtuálnímu počítači. Tento účet zadáte při nastavování zotavení po havárii v konzole Azure.

Připravte účet následujícím způsobem:

Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.

- **Virtuální počítače s Windows**: Instalace na virtuálních počítačích s Windows, pokud nepoužíváte doménový účet, zakažte vzdálené řízení přístupu uživatele na místním počítači. Provedete to tak, že v registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** přidáte položku DWORD **LocalAccountTokenFilterPolicy** s hodnotou 1.
- **Virtuální počítače s Linuxem**: Pokud chcete instalaci provést na virtuální počítače s Linuxem, připravte na zdrojovém serveru s Linuxem kořenový účet.


## <a name="check-vmware-requirements"></a>Kontrola požadavků na VMware

Ujistěte se, že servery a virtuální počítače VMware splňují požadavky.

1. [Ověřte](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) požadavky na servery VMware.
2. V případě virtuálních počítačů s Linuxem [zkontrolujte](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) požadavky na systém souborů a úložiště. 
3. Zkontrolujte podporu místní [sítě](vmware-physical-azure-support-matrix.md#network) a [úložiště](vmware-physical-azure-support-matrix.md#storage). 
4. Zkontrolujte podporované [sítě Azure](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](vmware-physical-azure-support-matrix.md#azure-storage) a [výpočetní prostředí](vmware-physical-azure-support-matrix.md#azure-compute) po převzetí služeb při selhání.
5. Místní virtuální počítače, které replikujete do Azure, musí splňovat [Požadavky na virtuální počítače Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. Ve virtuálních počítačích s Linuxem musí být jedinečný název zařízení nebo název přípojného bodu. Ujistěte se, že žádné dvě zařízení a přípojné body mají stejné názvy. Všimněte si, že název nejsou malá a velká písmena. Například názvy dvou zařízení pro stejný virtuální počítač jako _zařízení1_ a _zařízení1_ není povolený.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání se můžete k virtuálním počítačům Azure připojit z vaší místní sítě.

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Windows pomocí protokolu RDP, postupujte následovně:

- **Přístup k internetu**. Před převzetím služeb při selhání povolte na místním virtuálním počítači protokol RDP. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.
- **Přístup k S2S (Site-to-site) VPN**:
    - Před převzetím služeb při selhání povolte na místním počítači protokol RDP.
    - Protokol RDP musí být povolený v části **Brána Windows Firewall** -> **Povolené aplikace a funkce** pro **doménovou a privátní** síť.
    - Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135).
- Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. Pokud existují, nebudete moct přihlásit k virtuálnímu počítači, dokud se aktualizace nedokončí.
- Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Linuxem pomocí protokolu SSH, postupujte následovně:

- Před provedením převzetí služeb při selhání zkontrolujte, že je na místním počítači nastavené automatické spuštění služby Secure Shell při spuštění systému.
- Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.
- Po převzetí služeb při selhání na virtuálním počítači Azure povolte příchozí připojení k portu SSH pro pravidla skupiny na virtuálním počítači, u kterého proběhlo převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený.
- [Přidejte veřejnou IP adresu](site-recovery-monitoring-and-troubleshooting.md) pro tento virtuální počítač.
- Můžete zkontrolovat **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače.


## <a name="failback-requirements"></a>Požadavky na navrácení služeb po obnovení
Pokud plánujete navrácení služeb po obnovení v místní lokalitě, jsou celou řadou [předpokladů pro navrácení služeb po obnovení](vmware-azure-reprotect.md##before-you-begin). Tyto teď můžete připravit, ale není nutné. Můžete připravit po převzetí služeb při selhání do Azure.



## <a name="next-steps"></a>Další postup

Nastavení zotavení po havárii. Pokud replikujete více virtuálních počítačů, plánování kapacity.
> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii do Azure pro virtuální počítače VMware](vmware-azure-tutorial.md)
> [plánování kapacit](site-recovery-deployment-planner.md).
