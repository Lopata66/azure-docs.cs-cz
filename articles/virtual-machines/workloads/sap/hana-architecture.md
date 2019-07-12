---
title: Architektura SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Architektura nasazení SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f322ddab19a8c8635009d2e2b7e7e748fb1e73ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709758"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektura SAP HANA (velké instance) v Azure

Na vysoké úrovni SAP HANA v Azure (velké instance) řešení má aplikační vrstvě SAP, které se nacházejí ve virtuálních počítačích. Databázové vrstvě se nachází na hardwaru SAP TDI nakonfigurované v razítku velké Instance ve stejné oblasti Azure, která je připojená k Azure IaaS.

> [!NOTE]
> Nasaďte aplikační vrstvě SAP ve stejné oblasti Azure jako vrstvě SAP DBMS. Toto pravidlo je dobře zdokumentovaná v publikované informace o úloh SAP v Azure. 

Celková architektura SAP HANA v Azure (velké instance) poskytuje certifikací SAP TDI hardwarová konfigurace, což je nevirtualizovaných, holých počítačů, vysoce výkonné server pro databázi SAP HANA. Poskytuje také schopnosti a flexibilitě Azure pro škálování prostředků pro aplikační vrstvě SAP podle svých potřeb.

![Přehled architektury systému SAP HANA v Azure (velké instance)](./media/hana-overview-architecture/image1-architecture.png)

Architektuře, která je rozdělen na tři části:

- **správné**: Ukazuje na místní infrastrukturu, na kterém běží jiné aplikace v datech centra tak, aby koncoví uživatelé měli přístup k obchodní aplikace, jako je SAP. V ideálním případě by tento místní infrastruktury je připojená k Azure s využitím [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **System Center**: Ukazuje Azure IaaS a virtuálních počítačů v tomto případě použijte k hostování SAP nebo k jiným aplikacím, které používají SAP HANA jako systém DBMS. Menší instance HANA, které fungují s pamětí, které poskytují virtuálních počítačů nasazených ve virtuálních počítačích spolu s jejich aplikační vrstvu. Další informace o virtuálních počítačích najdete v tématu [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/).

   Síť Azure služby slouží k seskupení systémů SAP vyhrazené společně s další aplikace do virtuální sítě. Tyto virtuální sítě připojit k místním systémům a jde o SAP HANA v Azure (velké instance).

   Aplikace SAP NetWeaver a databází, které podporují spouštění v Azure najdete v tématu [SAP Support Poznámka #1928533 – aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure](https://launchpad.support.sap.com/#/notes/1928533). Dokumentaci o tom, jak nasadit řešení SAP v Azure najdete v tématu:

  -  [Použití SAP na virtuálních počítačích Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Pomocí řešení SAP na virtuálních počítačích Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **levé**: Zobrazí hardware s certifikací SAP HANA TDI v razítku velkých instancí Azure. Velká Instance HANA jednotky jsou připojené k virtuálním sítím vaše předplatné Azure s využitím stejnou technologii jako připojení z místního do Azure. Od května 2019 máte zavedená optimalizace, které umožňuje komunikaci mezi jednotkami velká Instance HANA a virtuálních počítačů Azure bez zapojení bránu ExpressRoute. V této architektuře (červené čáry) se zobrazí tato optimalizace se označuje jako ExpressRoute rychlé cesty. 

Razítko velkých instancí Azure samotné kombinují následující komponenty:

- **Výpočetní**: Servery, které jsou založeny na jinou generaci procesorů Intel Xeon, které poskytují nezbytné výpočetní funkce a jsou certifikací SAP HANA.
- **Síť**: Sjednocené vysokorychlostní síťové infrastruktury, která propojuje výpočty, úložiště a sítě LAN součásti.
- **Úložiště**: Infrastrukturu úložiště, který je přístupný prostřednictvím sjednocené síťové infrastruktury. Konkrétní úložnou kapacitu, která je k dispozici, závisí na konkrétní SAP HANA v Azure (velké instance) konfigurace, která je nasazena. Větší kapacitu úložiště je k dispozici na měsíční poplatek.

V infrastruktuře víceklientské velká Instance razítka jsou zákazníci nasadit jako izolovaných tenantů. Při nasazení klienta název předplatného Azure v rámci vaší registrace v Azure. Toto předplatné Azure je ten, který je velká Instance HANA účtovat na vrub. Tyto klienti mají vztah 1:1 k předplatnému Azure. Pro síť je možné pro přístup k celku velká Instance HANA nasazené do jednoho tenanta v jedné oblasti Azure v různých virtuálních sítích, které patří do různých předplatných Azure. Předplatná Azure, musíte patřit do stejné registrace v Azure. 

Stejně jako u virtuálních počítačů, SAP HANA v Azure (velké instance) se nabízí v několika oblastech Azure. Nabízí možnosti zotavení po havárii, můžete vyjádřit výslovný souhlas. Jiné velké Instance razítka v rámci jedné politické geografické oblasti jsou připojené k sobě navzájem. Například HANA velké Instance razítka v oblasti USA – západ a USA – východ jsou propojeny pomocí propojení vyhrazenou síť pro replikaci pro zotavení po havárii. 

Stejně jako, můžete si vybrat mezi různé typy virtuálních počítačů s Azure Virtual Machines, můžete vybrat z různých skladových položek z velká Instance HANA, které jsou přizpůsobené pro typy různých úloh SAP Hana. SAP platí poměry paměti k procesoru soket pro různé úlohy založené na generace procesorů Intel. V následující tabulce jsou uvedeny typy SKU, které nabízejí.

Můžete najít dostupné skladové položky [dostupných skladových položek pro HLI](hana-available-skus.md).

**Další postup**
- Přečtěte si [Síťová architektura SAP HANA (velké instance)](hana-network-architecture.md)