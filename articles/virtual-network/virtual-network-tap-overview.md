---
title: Klepněte na Přehled služby Azure virtual network | Dokumentace Microsoftu
description: Další informace o službě virtual network TAP. Virtuální síť TAP poskytuje hluboká kopie virtuálního počítače síťový provoz, který můžete Streamovat do kolekcí paketů.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593195"
---
# <a name="virtual-network-tap"></a>Naslouchací zařízení virtuální sítě

Virtuální síť Azure TAP (terminál přístupový bod) vám umožní průběžně stream vašeho virtuálního počítače síťový provoz do síťových paketů kolekcí nebo analytics nástroj. Nástroj kolekcí nebo analytics poskytuje [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) partnera. Seznam partnerských řešení, kteří jsou ověření pro práci s virtuální sítí TAP najdete v tématu [partnerská řešení](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Virtuální síť TAP je aktuálně ve verzi preview ve všech oblastech Azure. Pokud chcete použít virtuální síť TAP, musíte se zaregistrovat ve verzi preview zasláním e-mailu <azurevnettap@microsoft.com> svým ID předplatného. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením. Nejste schopni používat funkci, kterou, až se zobrazí potvrzovací e-mail. V této verzi preview je k dispozici bez smlouvy o úrovni služeb a nemělo používat pro produkční úlohy. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Zobrazit [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) podrobnosti.

## <a name="virtual-network-tap-partner-solutions"></a>Virtuální síť TAP partnerských řešení

### <a name="network-packet-brokers"></a>Zprostředkovatelé síťových paketů

- [Velké objemy přepínač velké objemy monitorování prostředků infrastruktury](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Pomocí Nubeva prismatu](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Analýzu zabezpečení, správy výkonu sítě/aplikací

- [Vzhůru zabezpečení](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Následující obrázek ukazuje, jak virtuální sítě funguje TAP. Můžete přidat klepnutím na konfiguraci na [síťové rozhraní](virtual-network-network-interface.md) připojená k virtuálním počítači nasazeném ve virtuální síti. Cíl je IP adresa virtuální sítě ve stejné virtuální síti jako monitorovaných síťových rozhraní nebo [partnerské virtuální](virtual-network-peering-overview.md) sítě. Kolekce řešení pro virtuální síť TAP je možné nasadit za [Azure interního nástroje Load balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts) pro zajištění vysoké dostupnosti. Můžete zhodnotit možnosti nasazení pro jednotlivá řešení, najdete v článku [partnerská řešení](#virtual-network-tap-partner-solutions).

![Jak virtuální sítě funguje klepněte na](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Požadavky

Než vytvoříte virtuální síť TAP, musí obdržet e-mail s potvrzením, která jsou zaregistrovaná ve verzi preview a mít jeden nebo více virtuálních počítačů vytvořených pomocí [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) modelu nasazení a partnera řešení pro agregaci klepněte na provoz ve stejné oblasti azure. Pokud nemáte partnerského řešení ve vaší virtuální síti, najdete v článku [partnerská řešení](#virtual-network-tap-partner-solutions) chcete nasadit některé. Můžete použít stejné virtuální síti TAP prostředku k souhrnným přenosům z několika síťovými rozhraními v jednom nebo několika předplatných. Pokud jsou monitorovaná síťová rozhraní v různých předplatných, předplatná musí být přidružený ke stejnému tenantovi Azure Active Directory. Kromě toho monitorovaných síťových rozhraní a cílový koncový bod pro agregaci TAP provozu může být v partnerských virtuálních sítích ve stejné oblasti. Pokud používáte tento model nasazení Ujistěte se, že [partnerský vztah virtuální sítě](virtual-network-peering-overview.md) před tím, než se konfigurace virtuálního síťového ODPOSLOUCHÁVÁNÍ.

## <a name="permissions"></a>Oprávnění

Účty, můžete použít konfiguraci klepnutím na síťových rozhraních musí být přiřazená k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , který je přiřazen nezbytné akce z následující tabulky:

| Akce | Název |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Potřebný k vytvoření, aktualizaci, čtení a odstranění virtuální sítě resource klepněte na |
| Microsoft.Network/networkInterfaces/read | Vyžadovaných k načtení prostředků rozhraní sítě, na které se nakonfigurují vzoru TAP |
| Microsoft.Network/tapConfigurations/* | Potřebný k vytvoření, aktualizaci, čtení a odstranění konfigurace klepnutím na síťovém rozhraní |

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvoření virtuální sítě TAP](tutorial-tap-virtual-network-cli.md).
