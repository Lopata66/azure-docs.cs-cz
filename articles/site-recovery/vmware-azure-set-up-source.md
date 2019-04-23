---
title: Nastavení zdrojového prostředí pro VMware pro replikaci Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit místní prostředí pro replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 075f86b24e2915d9689db8097889a830bade74c5
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149355"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Nastavení zdrojového prostředí pro VMware pro replikaci Azure

Tento článek popisuje, jak nastavit místní vaše zdrojové prostředí pro replikaci virtuálních počítačů VMware do Azure. Její součástí jsou kroky pro výběr vašeho scénáře replikace, nastavení místního počítače jako konfigurační server Site Recovery, a automaticky zjišťování místních virtuálních počítačů. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již:

- Plánované nasazení díky [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). To vám umožní přidělit dostatečnou šířku pásma v závislosti na vaší denní frekvenci změn dat, abyste splňovat váš požadovaný cíl bodu obnovení (RPO).
- [Nastavení prostředků](tutorial-prepare-azure.md) v [webu Azure portal](https://portal.azure.com).
- [Nastavení VMware v místním prostředí](vmware-azure-tutorial-prepare-on-premises.md), včetně vyhrazený účet pro automatické zjišťování.

## <a name="choose-your-protection-goals"></a>Volba cílů ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

## <a name="set-up-the-configuration-server"></a>Nastavení konfigurace serveru

Prostřednictvím šablonu otevřít virtualizace aplikace OVA () můžete nastavit konfigurační server jako VMware v místním prostředí virtuálních počítačů. [Další informace](concepts-vmware-to-azure-architecture.md) o součástech, které se nainstalují na virtuální počítač VMware.

1. Další informace o [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites) pro nasazení konfiguračního serveru.
2. [Zkontrolujte kapacitu čísla](vmware-azure-deploy-configuration-server.md#capacity-planning) pro nasazení.
3. [Stáhněte si](vmware-azure-deploy-configuration-server.md#download-the-template) a [importovat](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) šablony OVA nastavení VMware v místním prostředí virtuálního počítače, na kterém běží konfigurační server. Licence, které jsou poskytovány s šablonou je zkušební licence a je platná po dobu 180 dnů. Po uplynutí této doby zákazník potřebuje aktivovat systém windows s licencí opatřené.
4. Zapněte požadovaný virtuální počítač VMware a [ho zaregistrovat](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) v služby Recovery Services vault.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery vyloučení složek z antivirového programu

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Pokud je aktivní na zdrojovém počítači antivirový software

Pokud zdrojový počítač má aktivní antivirový software, by se měly vyloučit složku instalace. Ano, vyloučit složky *C:\ProgramData\ASR\agent* hladký průběh replikace.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Pokud Software antivirové ochrany v programu je aktivní na konfiguračním serveru

Vyloučit následující složky z antivirového softwaru pro hladký průběh replikace a aby se zabránilo problémům s připojením

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manageru 
- Kolekce chyb nástroj pro C:\Program Files\Microsoft Azure Site Recovery 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Instalační adresář serveru Azure Site Recovery. Příklad: E:\Program soubory (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Pokud Software antivirové ochrany v programu je aktivní na horizontální navýšení kapacity zpracování server nebo hlavní cíl

Vyloučit následující složky z antivirového softwaru

1. Agent C:\Program Files\Microsoft Azure Recovery Services
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery zatížení vyvážené proces serveru instalační adresář, příklad: C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Další postup
[Nastavení cílového prostředí](./vmware-azure-set-up-target.md) 
