---
title: Architektury řešení pomocí Azure NetApp Files | Microsoft Docs
description: Poskytuje odkazy na osvědčené postupy pro architektury řešení pomocí Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: b-juche
ms.openlocfilehash: 9d39a6d85f878b6d67ffbcc55f468ad6984eecab
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260796"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architektury řešení s využitím Azure NetApp Files
Tento článek poskytuje odkazy na osvědčené postupy, které vám pomůžou pochopit architektury řešení pro použití Azure NetApp Files.  

Následující diagram shrnuje kategorie architektury řešení, které Azure NetApp Files nabízí:

![Kategorie architektury řešení](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplikace a databázová řešení pro Linux OSS

V této části najdete odkazy na řešení pro aplikace a databáze se systémem Linux OSS. 

### <a name="oracle"></a>Oracle

* [Průvodce nasazením Oracle v Azure v praxi pomocí Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Image virtuálních počítačů Oracle a jejich nasazení na Microsoft Azure: možnosti konfigurace sdíleného úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Výhody použití Azure NetApp Files s Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Aplikace pro Windows a SQL Server řešení

Tato část poskytuje odkazy na aplikace systému Windows a SQL Server řešení.

### <a name="file-sharing-and-global-file-caching"></a>Sdílení souborů a globální ukládání souborů do mezipaměti

* [Nasazení Talon/Azure NetApp Files](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Nasazení SQL Server přes protokol SMB pomocí Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Nasazení skupin dostupnosti Always On s Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Řešení SAP v Azure

Tato část poskytuje odkazy na řešení SAP v Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Obecné SAP a SAP NetWeaver 

* [Aplikace SAP na Microsoft Azure s využitím Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure ve Windows pomocí protokolu SMB (Azure NetApp Files) pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v Red Hat Enterprise Linux pro Průvodce pro aplikace SAP s více SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP HANA horizontálního navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [SAP HANA horizontálního navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>Technická komunita SAP a příspěvky na blogu 

* [Azure NetApp Files – zálohování SAP HANA během několika sekund](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – obnovení databáze HANA ze zálohy snímku](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – SAP HANA přesměrování zálohování pomocí synchronizace cloudu](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Urychlení SAP HANA systémových kopií pomocí Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloudové svazky ONTAP a Azure NetApp Files: Snadná migrace systému SAP HANA](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Řešení infrastruktury virtuálních klientských počítačů

V této části najdete odkazy na řešení infrastruktury virtuálních klientských počítačů.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

## <a name="hpc-solutions"></a>Řešení HPC

V této části najdete referenční informace pro řešení s vysokým výkonem (HPC Computing Computing). 

### <a name="generic-hpc"></a>Obecné HPC

* [Azure NetApp Files: využití cloudového úložiště na maximum](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Spouštění úloh MPI s využitím Azure Batch a Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud prostředí HPC na Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Ropný a plynový

* [Výpočty s vysokým výkonem (HPC): ropný a plynový plyn v Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Spuštění simulace softwarového zásobníku v Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automatizace elektronického návrhu (EDA)

* [Výhody použití Azure NetApp Files pro automatizaci elektronických návrhů](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: EDA HPC Lab s Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analýzy

* [Azure NetApp Files: nový sdílený systém souborů, který se má používat s mřížkou SAS v Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Řešení služeb platformy Azure

Tato část poskytuje řešení pro služby platformy Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Služby Azure Kubernetes a Kubernetes

* [Integrace Azure NetApp Files se službou Azure Kubernetes](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Špičkový výkon Kubernetes v Azure s využitím Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident – úložiště Orchestrator pro kontejnery](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Spouštění úloh MPI s využitím Azure Batch a Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 