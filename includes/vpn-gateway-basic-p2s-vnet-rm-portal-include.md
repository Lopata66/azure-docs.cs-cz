---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5975f334eae543ea0f6ddc182170ae185ac5397a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467912"
---
Pokud chcete vytvořit virtuální síť v modelu nasazení Resource Manageru pomocí webu Azure Portal, postupujte podle následujících kroků. Snímky obrazovek slouží pouze jako příklady. Hodnoty na obrázcích nahraďte vlastními hodnotami. Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Pokud chcete, aby se tato virtuální síť připojila k místnímu umístění (kromě vytvoření konfigurace P2S), budete se muset domluvit se správcem vaší místní sítě a vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se nemusí směrovat očekávaným způsobem. Pokud navíc chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nesmí překrývat s jinou virtuální sítí. Podle toho pečlivě naplánujte konfiguraci sítě.
>
>

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).  V nabídce Azure Portal nebo na **domovské** stránce a vyberte **vytvořit prostředek**. **Nový** otevře se stránka.

2. V **části Hledat na Marketplace**zadejte *virtuální síť* a z výsledků vyberte **Virtual Network** .

   ![Stránka pro vyhledání prostředku Virtual Network](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Stránka pro vyhledání prostředku virtuální sítě")

3. U dolního okraje stránky Virtuální síť v seznamu **Vybrat model nasazení** vyberte **Resource Manager** a potom klikněte na **Vytvořit**.

   ![Vyberte Správce prostředků](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Výběr správce prostředků")
4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní na zelenou značku zaškrtnutí, pokud jsou znaky zadané do pole platné. Některé hodnoty mohou být vyplněny automaticky. V takovém případě je nahraďte vlastními. Stránka **Vytvořit virtuální síť** vypadá podobně jako v následujícím příkladu:

   ![Ověřování polí](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Ověřování polí")
5. **Název**: Zadejte název vaší virtuální sítě.
6. **Adresní prostor**: Zadejte adresní prostor. Pokud chcete přidat více adresních prostorů, přidejte první adresní prostor. Další adresní prostory můžete přidat později po vytvoření virtuální sítě.
7. **Předplatné**: Zkontrolujte, jestli je uvedeno správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
8. **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou zadáním názvu nové skupiny prostředků. Pokud vytváříte novou skupinu, nazvěte skupinu prostředků v souladu s hodnotami plánované konfigurace. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../articles/azure-resource-manager/management/overview.md#resource-groups).
9. **Umístění**: Vyberte umístění sítě VNet. Umístění určuje, kde budou uloženy prostředky nasazené v této síti VNet.
10. **Podsíť:** Přidejte název podsítě a rozsah adres podsítě. Další podsítě můžete přidat později po vytvoření virtuální sítě.
11. Pokud chcete mít k síti VNet snadný přístup na řídicím panelu, vyberte možnost **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

    ![Připnout na řídicí panel](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "připnout na řídicí panel")
12. Po kliknutí na **Vytvořit** se na řídicím panelu zobrazí dlaždice s informacemi o průběhu vytváření sítě VNet. Obsah dlaždice se v průběhu vytváření sítě VNet mění.

    ![Dlaždice vytváření virtuální sítě](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "dlaždice Vytváří se virtuální síť")
