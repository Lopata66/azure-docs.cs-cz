---
title: Začínáme s SAP na virtuálních počítačích Azure | Microsoft Docs
description: Přečtěte si o řešeních SAP, která běží na virtuálních počítačích v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 704d4c1b4e4e4229f35526748494aaac2e351f99
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871373"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Použití Azure k hostování a spouštění scénářů pro úlohy SAP

Když použijete Microsoft Azure, můžete spolehlivě spouštět klíčové úlohy a scénáře SAP na škálovatelné, kompatibilní a podnikové prověřené platformě. Získáte škálovatelnost, flexibilitu a úspory nákladů Azure. Díky rozšířenému partnerství mezi společnostmi Microsoft a SAP můžete spouštět aplikace SAP napříč vývojovými a testovacími a provozními scénáři v Azure a plně podporovat. Z SAP NetWeaver do SAP S/4HANA, SAP BI v systému Linux do Windows a SAP HANA k SQL, máme na vás přehled.

Kromě hostování scénářů SAP NetWeaver s různými systémy DBMS v Azure můžete hostovat i další scénáře úloh SAP, jako je například SAP BI v Azure. 

Jedinečnost Azure pro SAP HANA je nabídka, která nastavuje Azure odděleně. Aby bylo možné hostovat víc paměti a scénářů SAP náročných na prostředky procesoru, které zahrnují SAP HANA, Azure nabízí použití holého hardwaru vyhrazeného pro zákazníky. Pomocí tohoto řešení můžete spouštět SAP HANA nasazení, která vyžadují až 24 TB (120 TB škálování) paměti pro S/4HANA nebo jiné úlohy SAP HANA. 

Hostování scénářů pro úlohy SAP v Azure taky může vytvořit požadavky na integraci identit a jednotné přihlašování. K této situaci může dojít, když použijete Azure Active Directory (Azure AD) k propojení různých komponent SAP a SAP software-as-a-Service (SaaS) nebo Platform-as-a-Service (PaaS). Seznam takových scénářů integrace a jednotného přihlašování s entitami služby Azure AD a SAP je popsán v části "AAD SAP Identity Integration and Single přihlašování".

## <a name="changes-to-the-sap-workload-section"></a>Změny v části úlohy SAP
Změny dokumentů v části věnované úlohám SAP v Azure najdete na konci tohoto článku. Záznamy v protokolu změn se uchovávají přibližně po 180 dnech.

## <a name="you-want-to-know"></a>Chcete znát
Pokud máte konkrétní otázky, budeme v této části úvodní stránky ukazovat na konkrétní dokumenty nebo toky. Chcete znát:

- Jaké virtuální počítače Azure a jednotky velkých instancí HANA jsou podporované pro vydávání verzí softwaru SAP a verze operačních systémů. Přečtěte si dokument, [který software SAP podporuje pro nasazení Azure](./sap-supported-product-on-azure.md) pro odpovědi a proces hledání informací.
- Jaké scénáře nasazení SAP jsou podporovány u virtuálních počítačů Azure a velkých instancí HANA. Informace o podporovaných scénářích najdete v dokumentu:
    - [Podporované scénáře pro úlohy SAP na virtuálních počítačích Azure](./sap-planning-supported-configurations.md)
    - [Podporované scénáře pro velkou instanci HANA](./hana-supported-scenario.md)
- Které služby Azure, typy virtuálních počítačů Azure a služby Azure Storage jsou k dispozici v různých oblastech Azure, Prohlédněte si [Dostupné produkty](https://azure.microsoft.com/global-infrastructure/services/) v lokalitě v oblasti 
- Funguje rámec HA třetí strany, kromě Windows a Pacemaker? Zkontrolujte dolní část [poznámky k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Co je Azure Storage pro můj scénář nejvhodnější? Číst [Azure Storage typy pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA v Azure (velké instance)

Řada dokumentů vás provede SAP HANA v Azure (velké instance) nebo pro krátké instance s vysokou kapacitou HANA. Informace o velkých instancích HANA najdete v dokumentu [Přehled a architektura SAP HANA v Azure (velké instance)](./hana-overview-architecture.md) a Projděte si související dokumentaci v oddílu velká instance Hana.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA na virtuálních počítačích Azure
Tato část dokumentace obsahuje různé aspekty SAP HANA. V rámci předpokladů byste měli být obeznámeni s hlavními službami Azure, které poskytují základní služby Azure IaaS. Proto potřebujete znát výpočetní výkon, úložiště a síť Azure. Mnohé z těchto věcí jsou zpracovávány v [Průvodci plánováním Azure](./planning-guide.md)NetWeaver souvisejících s SAP. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasazené na virtuálních počítačích Azure
Tato část obsahuje dokumentaci pro plánování a nasazení pro SAP NetWeaver, SAP LaMa a Business One v Azure. Dokumentace se zaměřuje na základy a používání databází jiných než HANA s úlohou SAP v Azure. Dokumenty a články pro vysokou dostupnost jsou také základem pro SAP HANA vysoké dostupnosti v Azure.

Informace o vysoké dostupnosti úlohy SAP v Azure najdete v těchto tématech:

- [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver](./sap-high-availability-guide-start.md)



Informace o integraci mezi Azure Active Directory (Azure AD) a službami SAP a jednotným přihlašováním najdete v těchto tématech:

- [Kurz: Azure Active Directory integrace se SAP cloudem pro zákazníky](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s ověřováním identity cloudové platformy SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s cloudovou platformou SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Prostředí S/4HANA: jednotné přihlašování Fiori hlavní panel SAML pomocí Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Informace o integraci služeb Azure do komponent SAP najdete v těchto tématech:

- [Použití SAP HANA v Power BI Desktopu](/power-bi/desktop-sap-hana)
- [DirectQuery a SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [Použití konektoru SAP BW v Power BI Desktopu](/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory nabízí integraci dat pro SAP HANA a Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Protokol změn

- 08/25/2020: Změna v [Průvodci ha pro SAP ASCS/SCS se službou WSFC a sdíleným diskem](./sap-high-availability-guide-wsfc-shared-disk.md), [Příprava infrastruktury Azure pro SAP ASCS/SCS pomocí služby WSFC a sdíleného disku](./sap-high-availability-infrastructure-wsfc-shared-disk.md) a [instalace SAP NW ha s WSFC a sdíleným diskem](./sap-high-availability-guide-wsfc-shared-disk.md) , který přináší možnost použití architektury Azure Shared disk a Document ERS2 pro SAP
- 08/25/2020: vydání [příručky multi-SID ha pro SAP ASCS/SCS s WSFC a sdíleným diskem Azure](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)
- 08/25/2020: Změna v [Průvodci ha pro SAP ASCS/SCS s WSFC a Azure NetApp Files (SMB)](./high-availability-guide-windows-netapp-files-smb.md), [Příprava infrastruktury Azure pro SAP ASCS/SCS se službou WSFC a sdílenými složkami](./sap-high-availability-infrastructure-wsfc-file-share.md), [Průvodce vysokou SID pro SAP ASCS/SCS s WSFC a sdíleným diskem](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) a [průvodcem pro SAP ASCS/SCS se službou WSFC a SOFS sdílenou složkou](./sap-ascs-ha-multi-sid-wsfc-file-share.md) v případě aktualizací obsahu a restrukturalizace v průvodcích ha pro SAP ASCS/SCS pomocí WFC a sdíleného disku 
- 08/21/2020: Přidání nového vydání operačního systému do [kompatibilních operačních systémů pro velké instance Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) jako dostupný operační systém pro jednotky HLI typu I a II
- 08/18/2020: vydání [ha pro SAP HANA navýšení kapacity pomocí ANF na RHEL](./sap-hana-high-availability-netapp-files-red-hat.md)
- 08/17/2020: přidejte informace o použití Azure Site Recovery pro přesun systémů SAP NetWeaver z místního prostředí do Azure v článku [azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md)
- 08/14/2020: Přidání rad konfigurace disku pro Db2 v článku [IBM Db2 Azure Virtual Machines DBMS nasazení pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- 08/11/2020: přidávání RHEL 7,6 do [kompatibilních operačních systémů pro velké instance Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) jako dostupný operační systém pro HLI jednotky typu I
- 08/10/2020: představení nákladů SAP HANA konfigurací úložiště v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) a provádění některých aktualizací pro [úlohy SAP v Azure: kontrolní seznam pro plánování a nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 08/04/2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) a [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro zdůraznění důležitosti spolehlivého překladu názvů pro clustery Pacemaker
- 08/04/2020: Změna v [SAP NW ha v WFCS se sdílenou složkou](./sap-high-availability-installation-wsfc-file-share.md), [SAP NW ha v WFCS se sdíleným diskem](./sap-high-availability-installation-wsfc-shared-disk.md), [ha pro SAP NW na virtuálních počítačích Azure](./high-availability-guide.md), [ha pro SAP NW na virtuálních počítačích Azure v SLES](./high-availability-guide-suse.md), [ha pro SAP NW na virtuálních počítačích Azure v SLES s ANF](./high-availability-guide-suse-netapp-files.md), [ha pro SAP NW na virtuálních počítačích Azure v SLES průvodci pro více SID](./high-availability-guide-suse-multi-sid.md), [Vysoká dostupnost pro SAP NetWeaver na](./high-availability-guide-rhel.md)virtuálních počítačích Azure v RHEL, ha pro SAP NW na virtuálních počítačích Azure v RHEL [s ANF](./high-availability-guide-rhel-netapp-files.md) a [ha pro SAP NW na](./high-availability-guide-rhel-multi-sid.md) virtuálních počítačích Azure v RHEL – s využitím tohoto průvodce pro vysvětlení použití parametru `enque/encni/set_so_keepalive`
- 07/23/2020: přidání [velké instance SAP HANA uložit v pomocí rezervovaného článku Azure](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) – vysvětlení toho, co potřebujete znát, než si koupíte velké instance SAP HANA rezervaci a jak si koupit
- 07/16/2020: Popište, jak použít Azure PowerShell k instalaci rozšíření nového virtuálního počítače pro SAP v [Průvodci nasazením](deployment-guide.md) .
- 7/04/2020: vydání  [služby Azure monitor pro řešení SAP (Preview)](./azure-monitor-overview.md)
- 07/01/2020: Navrhněte levnější konfiguraci úložiště na základě nárůstu funkčnosti Azure Premium Storage v dokumentu [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) . 
- 06/24/2020: Změna v [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) pro vydání nového vylepšeného agenta Azure plot a větší odolné konfigurace STONITH pro zařízení na základě agenta Azure plot 
- 06/24/2020: Změna v [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro uvolnění odolné konfigurace STONITH
- 06/23/2020: změny v [Azure Virtual Machines Planning and Implementation pro SAP NetWeaver](./planning-guide.md) Guide a zavedení [Azure Storage typů pro Průvodce úlohami SAP](./planning-guide-storage.md)
- 06/22/2020: Přidejte kroky instalace pro nové rozšíření virtuálních počítačů pro SAP do [Průvodce nasazením](deployment-guide.md) .
- 06/16/2020: Změna [připojení veřejných koncových bodů pro virtuální počítače s využitím Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md) pro přidání odkazu do dokumentace k infrastruktuře veřejných cloudů SUSE 101 
- 06/10/2020: přidání nových HLI SKU do [dostupných SKU pro](./hana-available-skus.md) [architekturu úložiště HLI a SAP Hana (velké instance)](./hana-storage-architecture.md)
- 05/21/2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) a [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro přidání odkazu na [připojení k veřejnému koncovému bodu pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md)  
- 05/19/2020: přidejte důležitou zprávu, která nepoužívá skupinu kořenových svazků při použití LVM pro svazky související se službou HANA v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) .
- 05/19/2020: přidejte nový podporovaný operační systém pro velký typ instance pro HANA a v části [kompatibilní operační systémy pro velké instance Hana](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) .
- 05/12/2020: Změna [připojení veřejných koncových bodů pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md) k aktualizaci odkazů a přidání informací pro konfiguraci brány firewall třetích stran
- 05/11/2020: změňte [vysokou dostupnost SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md) a nastavte vytrvalost prostředků na hodnotu 0 pro prostředek netcat, protože to vede k efektivnějšímu převzetí služeb při selhání. 
- 05/05/2020: změny v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md) , které vyjadřují, že Gen2 nasazení jsou k dispozici pro rodinu virtuálních počítačů: Mv1
- 04/24/2020: změny ve [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s ANF v SLES](./sap-hana-scale-out-standby-netapp-files-suse.md), ve SAP HANA škálování na více instancí [s pohotovostním uzlem na virtuálních počítačích Azure s ANF na RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)a s [vysokou dostupností](./high-availability-guide-rhel-netapp-files.md) pro SAP NetWeaver na virtuálních počítačích Azure v SLES s [ANF, které](./high-availability-guide-suse-netapp-files.md) umožňují objasnění, že se automaticky přiřazují IP adresy pro NetWeaver svazky
- 04/22/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md) pro odebrání atributu meta `is-managed` z pokynů, protože je v konfliktu s umístěním clusteru do režimu údržby nebo z něj.
- 04/21/2020: Přidání SQL Azure DB jako podporované DBMS pro SAP pro SAP (Hybris) Commerce Platform 1811 a novější v článcích o [tom, jaký software SAP je podporován pro nasazení Azure](./sap-supported-product-on-azure.md) a [certifikace a konfigurace sap běžící v Microsoft Azure](./sap-certifications.md)
- 04/16/2020: Přidání SAP HANA jako podporovaného systému DBMS pro SAP (Hybris) Commerce Platform v článcích o [tom, co je software SAP pro nasazení Azure podporovaný](./sap-supported-product-on-azure.md) a [certifikace a konfigurace sap spuštěné v Microsoft Azure](./sap-certifications.md)
- 04/13/2020: správné číslo vydání verze SAP pomocného protokolu SAP ve [službě SAP POmocného softwaru Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_sapase.md)
- 04/07/2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) pro vysvětlení cloudu – netconfig – pokyny pro Azure
- 04/06/2020: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) a v [SAP HANA škálování s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) pro odebrání odkazů na NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (nahrazeno pomocí [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 31. března 2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na SLES](./sap-hana-high-availability.md) a [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-rhel.md) , abyste přidali pokyny, jak určit velikost pruhu při vytváření prokládaných svazků.
- 27. března 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) pro zarovnávání možností připojení systému souborů k NetApp TR-4746 (odebrání možnosti připojení synchronizace)
- 26. března 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](./high-availability-guide-suse-multi-sid.md) pro přidání odkazu na NetApp TR-4746
- 26. března 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SLES s Azure NETAPP Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md)vysoká dostupnost pro [systém souborů NFS na virtuálních počítačích Azure na SLES](./high-availability-guide-suse-nfs.md), [Vysoká dostupnost pro SAP NetWeaver na](./high-availability-guide-suse-multi-sid.md)virtuálních počítačích Azure v RHEL, s vysokou dostupností pro SAP NetWeaver na virtuálních počítačích Azure v [RHEL pro aplikace](./high-availability-guide-rhel.md) SAP a pro zajištění [vysoké dostupnosti pro SAP NETWEAVER na virtuálních počítačích Azure v RHEL pomocí Azure NetApp Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md) pro aktualizace diagramů a vysvětlení pokynů pro vytváření fondu back-endu Azure Load Balancer
- 19. března 2020: hlavní revize dokumentu [rychlý Start: ruční instalace SAP HANA s jednou instancí v azure Virtual Machines](./hana-get-started.md) na [instalaci SAP HANA na Azure Virtual Machines](./hana-get-started.md)
- 17. března 2020: Změna [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md) pro odebrání nastavení konfigurace SBD, které už není nutné
- Březen 16 2020: vyjasnění scénáře certifikace sloupce v SAP HANA certifikované platformě IaaS, v [jakém softwaru SAP se podporuje nasazení Azure](./sap-supported-product-on-azure.md)
- 03/11/2020: Změna v [úlohách SAP v podporovaných scénářích virtuálních počítačů Azure](./sap-planning-supported-configurations.md) pro vysvětlení podpory více databází na instanci systému DBMS
- 11. března 2020: Změna v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md) s vysvětlením virtuálních počítačů generace 1 a generace 2
- 10. března 2020: Změna v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) pro objasnění stávajících omezení propustnosti v ANF
- 9. března 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NETAPP Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) [Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích Azure v SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md), [Nastavení Pacemaker v SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md), [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích azure v SUSE Linux Enterprise Server s Pacemaker](./dbms-guide-ha-ibm.md), [vysokou dostupností SAP HANA na](./sap-hana-high-availability.md) virtuálních počítačích Azure na SUSE Linux Enterprise Server a [vysoké dostupnosti pro SAP NetWeaver na](./high-availability-guide-suse-multi-sid.md) virtuálních počítačích Azure v SLES – s agentem pro podporu Azure-9,1 
- 05. března 2020: změny struktury a změny obsahu pro oblasti Azure a virtuální počítače Azure v [azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md)
- 03/03/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) umožňující změnu na efektivnější rozložení svazku ANF
- Března 01 2020: [Příručka pro zálohování SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md) , která zahrnuje Azure Backup službu. Zmenšený a zúžený obsah v [SAP HANA Azure Backup na úrovni souborů](./sap-hana-backup-file-level.md) a odstranil třetí dokument, který se bude řešit zálohováním prostřednictvím snímku disku. Obsah se zpracovává v Průvodci zálohováním pro SAP HANA v Azure Virtual Machines 
- 27. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) a [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SLES Průvodce vícenásobnými SID](./high-availability-guide-suse-multi-sid.md) pro úpravu neúspěšného parametru clusteru
- 26. února 2020: Změna v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) pro upřesnění výběru systému souborů pro Hana v Azure
- 26. února 2020: Změna v [architektuře vysoké dostupnosti a scénářích pro SAP](./sap-high-availability-architecture-scenarios.md) , aby zahrnovaly odkaz na ha pro SAP NetWeaver na virtuálních počítačích Azure v průvodci RHEL s více SID
- 26. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md), [virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver na RHEL](./high-availability-guide-rhel.md) a [virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) pro odebrání příkazu, který nepodporují cluster/ASCS.
- 26. února 2020: vydání  [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci RHEL s více SID](./high-availability-guide-rhel-multi-sid.md) pro přidání odkazu na průvodce clusterem s podporou SUSE multi-SID
- 02/25/2020: Změna v [architektuře vysoké dostupnosti a scénářích](./sap-high-availability-architecture-scenarios.md) , aby mohl SAP přidat odkazy na novější články ha
- 25. února 2020: Změna [vysoké dostupnosti IBM DB2 LUW na virtuálních počítačích Azure v SUSE Linux Enterprise Server s Pacemaker](./dbms-guide-ha-ibm.md) , aby odkazovala na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 21. února 2020: kompletní revize článku [SAP POmocného programu sap Virtual Machines DBMS pro úlohy SAP](./dbms_guide_sapase.md)
- 21. února 2020: Změna [SAP HANA konfigurace úložiště virtuálního počítače Azure](./hana-vm-operations-storage.md) tak, aby reprezentovala nové doporučení pro/Hana/data a přidání nastavení v/v Scheduleru
- 21. února 2020: změny ve velkých instancích dokumentů HANA reprezentují nově certifikované SKU S224 a S224m.
- 21. února 2020: Změna [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v RHEL](./high-availability-guide-rhel.md) a [virtuálních počítačích Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) pro úpravu omezení clusteru pro architekturu replikace serveru fronty 2 (ENSA2)
- 20. února 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](./high-availability-guide-suse-multi-sid.md) pro přidání odkazu na průvodce clusterem s podporou SUSE multi-SID
- 13. února 2020: změny [v Azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md) implementují odkazy na nové dokumenty
- 13. února 2020: Přidání nové [úlohy SAP dokumentu SAP ve scénáři podporovaném virtuálním počítačem Azure](./sap-planning-supported-configurations.md)
- 13. února 2020: Přidání nového dokumentu, [který podporuje software SAP pro nasazení Azure](./sap-supported-product-on-azure.md)
- 13. února 2020: Změna [vysoké dostupnosti IBM DB2 LUW na virtuálních počítačích Azure na serveru Red Hat Enterprise Linux](./high-availability-guide-rhel-ibm-db2-luw.md) , aby odkazovala na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 13. února 2020: přidejte nové typy virtuálních počítačů do [certifikace a konfigurací SAP běžících na Microsoft Azure](./sap-certifications.md)
- 13. února 2020: Přidání nové poznámky k podpoře SAP [v Azure: kontrolní seznam pro plánování a nasazení](./sap-deployment-checklist.md)
- 13. února 2020: Změna [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v RHEL](./high-availability-guide-rhel.md) a [virtuálních počítačích Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) pro zarovnávání časových limitů pro časový limit Red Hat
- 11. února 2020: vydání [SAP HANA do migrace velkých instancí Azure do azure Virtual Machines](./hana-large-instance-virtual-machine-migration.md)
- 7. února 2020: Změna [Možnosti připojení veřejných koncových bodů pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md) k aktualizaci ukázkového snímku NSG
- 03. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md) a [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) pro odebrání upozornění na použití pomlčky v názvech hostitelů uzlů clusteru v SLES
- 28. ledna 2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-rhel.md) za účelem zarovnání časových limitů SAP HANA prostředků clusteru do doporučení Red Hat timeout
- 17. ledna 2020: Změna ve [skupinách umístění v blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](./sap-proximity-placement-scenarios.md) pro změnu oddílu přesunutí stávajících virtuálních počítačů do skupiny umístění blízkosti
- 17. ledna 2020: Změna v [konfiguracích úloh SAP pomocí zóny dostupnosti Azure](./sap-ha-availability-zones.md) odkazuje na postup, který automatizuje měření latence mezi zóny dostupnosti
- 16. ledna 2020: Změna [způsobu instalace a konfigurace SAP Hana (velkých instancí) v Azure](./hana-installation.md) pro přizpůsobení verzí operačního systému pro IaaS do adresáře hardwaru Hana
- 16. ledna 2020: změny [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](./high-availability-guide-suse-multi-sid.md) pro přidání pokynů pro systémy SAP s použitím architektury serveru fronty 2 (ENSA2)
- 10. ledna 2020: změny ve [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) a v [SAP HANA škálování s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) pro přidání pokynů k `nfs4_disable_idmapping` trvalému provedení změn.
- 10. ledna 2020: změny [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES s Azure NetApp Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) a v [Azure Virtual Machines vysoké dostupnosti pro SAP NetWeaver v RHEL s Azure NETAPP Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md) pro přidání pokynů k připojení Azure NetApp Filesch svazků názvů NFSv4.
- 23. prosince 2019: vydání [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES průvodce pro více SID](./high-availability-guide-suse-multi-sid.md)
- 18. prosince 2019: vydání [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 21. listopadu 2019: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md) pro zjednodušení konfigurace mapování ID NFS a změna doporučeného primárního síťového rozhraní pro zjednodušení směrování.
- 15. listopadu 2019: drobné změny [vysoké dostupnosti pro SAP NetWeaver v SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](high-availability-guide-suse-netapp-files.md) a [vysokou dostupnost pro sap NetWeaver v Red Hat Enterprise Linux s Azure NETAPP Files pro aplikace SAP](high-availability-guide-rhel-netapp-files.md) pro vysvětlení omezení velikosti fondu kapacity a příkazu Remove, který podporuje jenom NFSv3 verzi.
- 12. listopadu 2019: vydání [vysoké dostupnosti pro SAP NetWeaver v systému Windows s využitím protokolu SMB (Azure NetApp Files)](high-availability-guide-windows-netapp-files-smb.md)
- 8. listopadu 2019: změny [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SUSE Linux Enterprise Server](sap-hana-high-availability.md), [nastavení SAP HANA systémové replikace na virtuálních počítačích Azure (virtuální počítače)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines vysoké dostupnosti pro SAP NetWeaver v SUSE Linux Enterprise Server pro aplikace SAP](high-availability-guide-suse.md), Azure Virtual Machines High pro [SAP NetWeaver on SUSE Linux Enterprise Server s Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines vysoká dostupnost](high-availability-guide-rhel.md)pro systém souborů SAP NetWeaver v Red Hat Enterprise Linux, Azure Virtual Machines vysoká dostupnost pro systém souborů SAP [NetWeaver na Red Hat Enterprise Linux s Azure NetApp Files](high-availability-guide-rhel-netapp-files.md)a [vysokou dostupností pro NFS na virtuálních počítačích Azure](high-availability-guide-suse-nfs.md)v SUSE Linux Enterprise Server, na [virtuálních](high-availability-guide-rhel-glusterfs.md) počítačích Azure v Red Hat Enterprise Linux, které doporučují Azure Load Balancer  
- 8. listopadu 2019: změny v [kontrolním seznamu plánování úloh SAP a nasazení](sap-deployment-checklist.md) k objasnění doporučení pro šifrování  
- 4. listopadu 2019: změny v [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) pro vytvoření clusteru přímo s konfigurací jednosměrového vysílání  
