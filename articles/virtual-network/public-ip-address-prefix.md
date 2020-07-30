---
title: Předpona veřejných IP adres Azure
description: Přečtěte si, co je předpona veřejných IP adres Azure a jak vám může pomáhat s přiřazením předvídatelných veřejných IP adres vašim prostředkům.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 53dd6d2dda762b3cbf53f4aaec6cd3692a9656e9
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432578"
---
# <a name="public-ip-address-prefix"></a>Předpona veřejné IP adresy

Předpona veřejných IP adres je rezervovaný rozsah IP adres v Azure. Azure nabízí souvislý rozsah adres pro vaše předplatné na základě toho, kolik zadáte. 

Pokud nejste obeznámeni s veřejnými adresami, přečtěte si téma [veřejné IP adresy.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Veřejné IP adresy se přiřazují z fondu adres v každé oblasti Azure. Můžete [si stáhnout](https://www.microsoft.com/download/details.aspx?id=56519) Seznam rozsahů, které Azure používá pro každou oblast. Například 40.121.0.0/16 je jedním z více než 100 rozsahů, které Azure používá v oblasti Východní USA. Rozsah zahrnuje použitelné adresy 40.121.0.1-40.121.255.254.

Předponu veřejné IP adresy vytvoříte v oblasti a předplatném Azure zadáním názvu a počtu adres, které chcete zahrnout do předpony. 

Rozsahy veřejných IP adres se přiřazují s vámi zvolenou předponou. Pokud vytvoříte předponu/28, Azure poskytne 16 IP adres z jednoho z jeho rozsahů.

Nevíte, který rozsah Azure bude přiřazovat, dokud nevytvoříte rozsah, ale adresy budou souvislé. 

Předpony veřejných IP adres obsahují poplatky. Další informace najdete v tématu [ceny veřejných IP adres](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Proč vytvořit předponu veřejné IP adresy?

Když vytvoříte prostředky veřejné IP adresy, Azure přiřadí dostupnou veřejnou IP adresu z libovolného rozsahu používaného v této oblasti. 

Až Azure IP adresu přiřadí, nebudete znát přesnou IP adresu. Tento proces může být problematický, když vytváříte pravidla brány firewall, která povolují konkrétní IP adresy. Pro každou přidanou IP adresu je nutné přidat odpovídající pravidlo brány firewall.

Když přiřazujete adresy k prostředkům z předpony veřejné IP adresy, aktualizace pravidla brány firewall se nevyžadují. Do pravidla se přidá celý rozsah.

## <a name="benefits"></a>Výhody

- Vytváření prostředků veřejné IP adresy ze známého rozsahu.
- Konfigurace pravidla brány firewall s rozsahy, které zahrnují veřejné IP adresy, které jste právě přiřadili, a adresy, které jste ještě nepřiřadili. Tato konfigurace eliminuje nutnost měnit pravidla brány firewall při přiřazování IP adres novým prostředkům.
- Výchozí velikost rozsahu, který můžete vytvořit, je/28 nebo 16 IP adres.
- Neexistují omezení pro počet rozsahů, které můžete vytvořit. Existují omezení maximálního počtu statických veřejných IP adres, které můžete mít v rámci předplatného Azure. Počet rozsahů, které vytvoříte, nemůže zahrnovat víc statických veřejných IP adres, než kolik můžete mít ve svém předplatném. Další informace najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy, které vytvoříte pomocí adres z předpony, se dají přiřadit k libovolnému prostředku Azure, ke kterému můžete přiřadit veřejnou IP adresu.
- Můžete snadno zjistit, které IP adresy jsou uvedené a nejsou v rámci tohoto rozsahu uvedené.

## <a name="scenarios"></a>Scénáře
K statické veřejné IP adrese můžete přidružit následující prostředky z předpony:

|Prostředek|Scénář|Kroky|
|---|---|---|
|Virtuální počítače| Přidružení veřejných IP adres od předpony k virtuálním počítačům v Azure snižuje režijní náklady na správu při přidávání IP adres do seznamu povolených adres v bráně firewall. Můžete přidat celou předponu s jedním pravidlem brány firewall. Při škálování s virtuálními počítači v Azure můžete přidružit IP adresy ze stejné předpony ukládání nákladů, času a režijních nákladů na správu.| Přidružení IP adres k virtuálnímu počítači z předpony: </br> 1. [vytvořte předponu.](manage-public-ip-address-prefix.md) </br> 2. [vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) </br> 3. [přiřaďte IP adresu k síťovému rozhraní virtuálního počítače.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> [IP adresy můžete taky přidružit k sadě škálování virtuálního počítače](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standardní nástroje pro vyrovnávání zatížení | Přidružení veřejných IP adres z předpony na konfiguraci IP adresy front-endu nebo odchozí pravidlo pro nástroj pro vyrovnávání zatížení zajišťuje zjednodušení vašeho prostoru veřejných IP adres Azure. Zjednodušte svůj scénář vymazáním odchozích připojení z rozsahu souvislých IP adres. | K přidružení IP adresy z předpony k vašemu nástroji pro vyrovnávání zatížení: </br> 1. [vytvořte předponu.](manage-public-ip-address-prefix.md) </br> 2. [vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) </br> 3. při vytváření nástroje pro vyrovnávání zatížení vyberte nebo aktualizujte IP adresu vytvořenou v kroku 2 výše jako front-endové IP adresu vašeho nástroje pro vyrovnávání zatížení. |
| Brána Azure Firewall | Veřejnou IP adresu můžete použít z předpony pro odchozí SNAT. Všechny odchozí přenosy virtuální sítě se překládají na [Azure firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veřejnou IP adresu. | Postup při přidružení IP adresy z předpony k bráně firewall: </br> 1. [vytvořte předponu.](manage-public-ip-address-prefix.md) </br> 2. [vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) </br> 3. když [nasadíte bránu Azure firewall](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), nezapomeňte vybrat IP adresu, kterou jste dříve zadali z předpony.|
| Application Gateway v2 | Veřejnou IP adresu můžete použít z předpony pro vaše automatické škálování a redundantní Aplikační bránu v2. | Postup při přidružení IP adresy z předpony k bráně: </br> 1. [vytvořte předponu.](manage-public-ip-address-prefix.md) </br> 2. [vytvořte IP adresu z předpony.](manage-public-ip-address-prefix.md) </br> 3. když [nasadíte Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway), nezapomeňte vybrat IP adresu, kterou jste dříve zadali z předpony.|

## <a name="constraints"></a>Omezení

- Nemůžete zadat IP adresy pro předponu. Azure poskytuje IP adresy pro předponu na základě zadané velikosti.
- Ve výchozím nastavení můžete vytvořit předponu s až 16 IP adresami nebo/28. Přečtěte si o [omezeních sítě zvýšení požadavků](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) a [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) , kde najdete další informace.
- Po vytvoření předpony nemůžete rozsah změnit.
- Z rozsahu předpony lze přiřadit pouze statické veřejné IP adresy, které byly vytvořeny pomocí standardní SKU. Další informace o SKU veřejných IP adres najdete v tématu [Veřejná IP adresa](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z rozsahu lze přiřadit pouze k prostředkům Azure Resource Manager. Adresy se nedají přiřadit k prostředkům v modelu nasazení Classic.
- Všechny veřejné IP adresy vytvořené z předpony musí existovat ve stejné oblasti a předplatném Azure jako předpona. Adresy musí být přiřazené k prostředkům ve stejné oblasti a předplatném.
- Pokud jsou adresy v rámci této adresy přiřazené k prostředkům veřejné IP adresy, které jsou přidružené k prostředku, nemůžete předponu odstranit. Zrušte přidružení všech prostředků veřejné IP adresy, kterým jsou nejprve přiřazeny IP adresy z předpony.


## <a name="next-steps"></a>Další kroky

- [Vytvoření](manage-public-ip-address-prefix.md) předpony veřejné IP adresy
