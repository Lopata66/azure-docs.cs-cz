---
title: Matice podpory pro zotavení po havárii virtuálních počítačů Hyper-V v nástroji VMM cloudů do sekundární lokality pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Shrnuje podporu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.openlocfilehash: 60ca12e5b362a37eb9f85c9a0d1fc23ca99e9edc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360796"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality

Tento článek shrnuje, co je podporováno při použití [Azure Site Recovery](site-recovery-overview.md) služby pro replikaci virtuálních počítačů Hyper-V spravované v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality. Pokud chcete replikovat virtuální počítače Hyper-V do Azure, přečtěte si [tento systém podpory replikace z](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Můžete pouze replikace do sekundární lokality Pokud jsou vaši hostitelé Hyper-V spravované v cloudech VMM.

  

## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2012 R2 | Servery musí být spuštěná nejnovější aktualizace.
Windows Server 2016 |  Cloudy VMM 2016 s kombinaci systému Windows Server 2016 a 2012 R2 hostitele se momentálně nepodporují.<br/><br/> Nasazení, která upgradu ze System Center 2012 R2 VMM 2012 R2 na System Center 2016 nejsou aktuálně podporovány.


## <a name="replicated-vm-support"></a>Replikované podpora virtuálních počítačů

Následující tabulka shrnuje podporu operačního systému pro počítače pomocí služby Site Recovery replikovat. Jakékoli úlohy můžou běžet na podporovaný operační systém.

**Verze Windows** | **Technologie Hyper-V (s VMM)**
--- | ---
Windows Server 2016 | Žádný hostovaný operační systém [podporuje technologie Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) na Windows serveru 2016 
Windows Server 2012 R2 | Žádný hostovaný operační systém [podporuje technologie Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) v systému Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Úložiště počítače Linux

Je možné replikovat jenom počítače s Linuxem pomocí následující úložiště:

- Systém (EXT3 ETX4, ReiserFS, XFS) souborů.
- Mapování více cest softwaru zařízení.
- Správce svazků (LVM2).
- Fyzické servery s HP CCISS řadič úložiště nejsou podporované.
- Systém souborů ReiserFS je podporován pouze v operačním systémem SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfigurace sítě - hostitele nebo hosta virtuálního počítače

**Konfigurace** | **Podporuje se**  
--- | --- 
Hostitel – seskupování síťových adaptérů | Ano 
Host - VLAN | Ano 
Hostitel – protokolu IPv4 | Ano 
Hostitel – IPv6 | Ne 
Hosta virtuálního počítače – seskupování síťových adaptérů | Ne
Hosta virtuálního počítače – protokolu IPv4 | Ano
Hosta virtuálního počítače – IPv6 | Ne
Hostovaný virtuální počítač – Windows/Linux - statická IP adresa | Ano
Hosta virtuálního počítače – s více síťovými Kartami | Ano


## <a name="storage"></a>Úložiště

### <a name="host-storage"></a>Úložiště hostitele

**Úložiště (hostitel)** | **Podporuje se**
--- | --- 
NFS | neuvedeno
SMB 3.0 |  Ano
SAN (ISCSI) | Ano
Více cest (MPIO) | Ano

### <a name="guest-or-physical-server-storage"></a>Host nebo fyzický server úložiště

**Konfigurace** | **Podporuje se**
--- | --- | 
VMDK |  neuvedeno
VHD/VHDX | Ano (až 16 disků)
Virtuální počítače 2. generace | Ano
Sdílený disk clusteru | Ne
Šifrovaného disku | Ne
ROZHRANÍ UEFI| neuvedeno
NFS | Ne
SMB 3.0 | Ne
RDM | neuvedeno
Disk > 1 TB | Ano
Svazek s prokládané disk > 1 TB<br/><br/> LVM | Ano
Prostory úložiště | Ano
Přidání nebo odebrání horké disku | Ne
Vyloučení disku | Ano
Více cest (MPIO) | Ano

## <a name="vaults"></a>Trezory

**Akce** | **Podporuje se**
--- | --- 
Přesun trezorů služby mezi skupinami prostředků (v rámci předplatného ani mezi předplatnými) |  Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků (v rámci předplatného ani mezi předplatnými) | Ne

## <a name="azure-site-recovery-provider"></a>Zprostředkovatele Azure Site Recovery

Zprostředkovatel koordinuje komunikaci mezi servery VMM. 

**nejnovější** | **Aktualizace**
--- | --- 
5.1.19 ([dostupná z portálu](https://aka.ms/downloaddra) | [Nejnovější funkce a opravy](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Další postup

[Replikace virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)

