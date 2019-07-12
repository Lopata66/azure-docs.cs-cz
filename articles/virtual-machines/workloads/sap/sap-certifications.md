---
title: Certifikace Microsoft Azure pro SAP | Dokumentace Microsoftu
description: Aktualizovaný seznam aktuální konfigurace a certifikací SAP na platformě Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: e64e2386611060b1393a330695a4729fe9490e54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709984"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certifikace SAP a konfigurace běžící v Microsoft Azure

SAP a Microsoft má dlouhou historii týmovou práci v silné partnerství, která má oboustranně pro své zákazníky. Microsoft neustále aktualizuje svou platformu a odesílání nové podrobnosti certifikaci pro SAP, aby se zajistilo Microsoft Azure je nejlepší platformou pro úlohy SAP spouštěli na. Následující tabulky osnovy Azure podporované konfigurace a seznam stále se rozšiřující certifikací SAP. 

## <a name="sap-hana-certifications"></a>Certifikace SAP HANA
Odkazy:

- [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) for SAP HANA – podpora pro nativní virtuální počítače Azure a velké instance HANA.

| Produkt SAP | Podporovaný operační systém | Nabídky Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (včetně klientského softwaru HANA sestává z SQLDBC, ODBO – Windows pouze, ODBC a ovladače JDBC, HANA studio a HANA database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řady virtuálních počítačů řady D-Series |
| Obchodní jeden on HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA s certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Řízená dostupnost pro GS5. Plná podpora pro M64s M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA v Azure (velké instance) [platformách IaaS certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, SAP HANA v Azure (velké instance) <br /> [SAP HANA s certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise for BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA v Azure (velké instance) [platformách IaaS certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA v Azure (velké instance) <br /> [SAP HANA s certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Mějte na paměti, že SAP používá termín "slučování" v [platformách IaaS certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako synonymum pro "škálování" a není pro zajištění vysoké dostupnosti "clustering.

## <a name="sap-netweaver-certifications"></a>Certifikace SAP NetWeaver
Microsoft Azure má certifikaci pro následující produkty SAP, se zárukou plné podpory od Microsoftu i SAPu.
Odkazy:

- [1928533 – SAP aplikace v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533) u všech aplikací využívajících SAP NetWeaver, jako je SAP TREX, SAP LiveCache a SAP obsahu. A všechny databáze, s výjimkou SAP HANA.


| Produkt SAP | Hostovaný operační systém | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business Suite Software | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows a Oracle Linux pouze), DB2, SAP ASE |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 k DS15_v2, GS1 až GS5, D2s_v3 k D64s_v3, E2s_v3 E64s_v3 M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows a Oracle Linux pouze), DB2, SAP ASE |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 k DS15_v2, GS1 až GS5, D2s_v3 k D64s_v3, E2s_v3 E64s_v3 M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP BusinessObjects BI | Windows |Není k dispozici |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 k DS15_v2, GS1 až GS5, D2s_v3 k D64s_v3, E2s_v3 E64s_v3 M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (Windows a Oracle Linux pouze), DB2, SAP ASE |A5 až A11, D11 až D14, DS11 až DS14, DS11_v2 k DS15_v2, GS1 až GS5, D2s_v3 k D64s_v3, E2s_v3 E64s_v3 M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Další úlohy SAP v Azure nepodporuje

| Produkt SAP | Hostovaný operační systém | RDBMS | Typy virtuálních počítačů |
| --- | --- | --- | --- |
| SAP Business jeden na SQL Server | Windows  | SQL Server | Všechny NetWeaver certified typy virtuálních počítačů<br /> [Poznámka SAP #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows a Linux | | Všechny typy NetWeaver certifikace virtuálního počítače<br /> Poznámka SAP #2451795 |
| Platformy BI obchodní objekty SAP | Windows a Linux | | Poznámka SAP #2145537 |
| SAP Data Services 4.2 | | | Poznámka SAP #2288344 |
| SAP Hybris Commerce Platform 5.x a 6.x | Windows | SQL Server, Oracle | Všechny NetWeaver certified typy virtuálních počítačů<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
