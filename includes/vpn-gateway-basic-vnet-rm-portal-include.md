---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301932"
---
Pomocí následujících kroků můžete vytvořit virtuální síť pomocí modelu nasazení Resource Manager a webu Azure portal. Další informace o virtuálních sítích najdete v tématu [přehled Virtual Network](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Pokud používáte virtuální síť jako součást architektury mezi různými místy, ujistěte se, že jste ve Správci místní sítě vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se bude směrovat neočekávaným způsobem. Kromě toho, pokud chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nemůže překrývat s jinou virtuální sítí. Podle toho naplánujte konfiguraci sítě.
>
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Do **vyhledávacích prostředků, služeb a dokumentů (G +/)** zadejte *Virtual Network*.

   ![Stránka pro vyhledání prostředku Virtual Network](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Stránka pro vyhledání prostředku virtuální sítě")
1. Z výsledků **Marketplace** vyberte **Virtual Network** .

   ![Vybrat virtuální síť](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Stránka pro vyhledání prostředku virtuální sítě")
1. Na stránce **Virtual Network** vyberte **vytvořit**.

   ![Stránka virtuální síť](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Výběr možnosti Vytvořit")
1. Jakmile vyberete **vytvořit**, otevře se stránka **vytvořit virtuální síť** .
1. Na kartě **základy** nakonfigurujte nastavení sítě VNet **a podrobnosti o** **instanci** .

   ![Karta základy](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Karta základy") Po vyplnění polí se zobrazí zelená značka zaškrtnutí, pokud jsou znaky, které zadáte do pole, ověřovány. Některé hodnoty jsou autofilled, můžete nahradit vlastními hodnotami:

   - **Předplatné**: Zkontrolujte, jestli je uvedeno správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou kliknutím na **vytvořit novou** . Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Název**: Zadejte název své virtuální sítě.
   - **Oblast**: vyberte umístění pro virtuální síť. Umístění Určuje, kde bude za prostředky, které nasadíte do této virtuální síti.

1. Na kartě **IP adresy** nakonfigurujte hodnoty. Hodnoty uvedené v následujících příkladech jsou pro demonstrační účely. Upravte tyto hodnoty podle nastavení, které požadujete.

   ![Karta IP adresy](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Karta IP adresy")  
   - **Adresní prostor IPv4**: ve výchozím nastavení se automaticky vytvoří adresní prostor. Kliknutím na adresní prostor se můžete přizpůsobit tak, aby odrážel vaše vlastní hodnoty. Můžete také přidat další adresní prostory.
   - **IPv6**: Pokud vaše konfigurace vyžaduje adresní prostor IPv6, vyberte pole **Přidat adresní prostor IPv6** k zadání těchto informací.
   - **Podsíť**: Pokud použijete výchozí adresní prostor, automaticky se vytvoří výchozí podsíť. Pokud změníte adresní prostor, budete muset přidat podsíť. Vyberte **+ Přidat podsíť** a otevřete tak okno **Přidat podsíť** . Nakonfigurujte následující nastavení a pak vyberte **Přidat** a přidejte hodnoty:
      - **Název podsítě**: v tomto příkladu jsme jmenovali podsíť "front".
      - **Rozsah adres podsítě**: rozsah adres pro tuto podsíť.

1. Na kartě **zabezpečení** nechte v tuto chvíli výchozí hodnoty:

   - **DDos Protection**: Basic
   - **Brána firewall**: zakázaná
1. Vyberte **zkontrolovat + vytvořit** a ověřte nastavení virtuální sítě.
1. Po ověření nastavení vyberte **vytvořit**.
