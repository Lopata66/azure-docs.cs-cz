---
title: Filtrování příchozího provozu pomocí DNAT služby Azure Firewall a webu Azure Portal
description: V tomto kurzu zjistíte, jak nasadit a konfigurovat DNAT služby Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/28/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: df57faad770b252228b6c55d4caff775acfe3594
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531160"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Kurz: Filtrování příchozího provozu pomocí DNAT služby Azure Firewall a webu Azure Portal

Ve službě Azure Firewall můžete nakonfigurovat překlad adres na základě zdroje (DNAT) pro překlad a filtrování příchozího provozu do vašich podsítí. Při konfiguraci DNAT akce kolekce pravidel NAT je nastavena na **Dnat**. Každé pravidlo v kolekci pravidel NAT pak lze použít k překladu veřejné IP adresy a portu brány firewall na privátní IP adresu a port. Pravidla DNAT implicitně přidávají odpovídající pravidlo sítě, které povoluje přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Další informace najdete v článku, který pojednává o [logice zpracování pravidel služby Azure Firewall](rule-processing.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvořit výchozí trasu
> * Konfigurace pravidla DNAT
> * Otestovat bránu firewall

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

V tomto kurzu vytvoříte dvě partnerské virtuální sítě:

- **VN-Hub** – v této virtuální síti bude brána firewall.
- **VN-Spoke** – v této virtuální síti bude server úloh.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na domovské stránce webu Azure Portal klikněte na **Skupiny prostředků** a potom klikněte na **Přidat**.
3. Jako **Název skupiny prostředků** zadejte **RG-DNAT-Test**.
4. V části **Předplatné** vyberte své předplatné.
5. V části **Umístění skupiny prostředků** vyberte umístění. Všechny další prostředky, které vytvoříte, musí být ve stejném umístění.
6. Klikněte na možnost **Vytvořit**.

## <a name="set-up-the-network-environment"></a>Nastavení síťového prostředí

Nejprve vytvořte virtuální sítě a pak mezi nimi vytvořte partnerský vztah.

### <a name="create-the-hub-vnet"></a>Vytvoření virtuální sítě centra

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Virtuální sítě**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **VN-Hub**.
5. V části **Adresní prostor** zadejte **10.0.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **AzureFirewallSubnet**.

     Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
     > [!NOTE]
     > Minimální velikost podsítě AzureFirewallSubnet je /26.
10. V části **Rozsah adres** zadejte **10.0.1.0/24**.
11. Ostatní výchozí nastavení ponechte a potom klikněte na **Vytvořit**.

### <a name="create-a-spoke-vnet"></a>Vytvoření virtuální sítě paprsku

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Virtuální sítě**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **VN-Spoke**.
5. Jako **Adresní prostor** zadejte **192.168.0.0/16**.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. V části **Podsíť** jako **Název** zadejte **SN-Workload**.

    V této podsíti bude server.
10. Jako **Rozsah adres** zadejte **192.168.1.0/24**.
11. Ostatní výchozí nastavení ponechte a potom klikněte na **Vytvořit**.

### <a name="peer-the-vnets"></a>Vytvoření partnerského vztahu virtuálních sítí

Teď mezi dvěma virtuálními sítěmi vytvořte partnerský vztah.

#### <a name="hub-to-spoke"></a>Od centra k paprsku

1. Klikněte na virtuální síť **VN-Hub**.
2. V části **Nastavení** klikněte na **Partnerské vztahy**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako název zadejte **Peer-HubSpoke**.
5. Jako virtuální síť vyberte **VN-Spoke**.
6. Klikněte na **OK**.

#### <a name="spoke-to-hub"></a>Od paprsku k centru

1. Klikněte na virtuální síť **VN-Spoke**.
2. V části **Nastavení** klikněte na **Partnerské vztahy**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako název zadejte **Peer-SpokeHub**.
5. Jako virtuální síť vyberte **VN-Hub**.
6. Klikněte na **Povolit přesměrovaný provoz**.
7. Klikněte na **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač úloh a umístěte ho do podsítě **SN-Workload**.

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Compute** klikněte na **Virtuální počítače**.
3. Klikněte na **Přidat**, potom na **Windows Server**, dále na **Windows Server 2016 Datacenter** a nakonec klikněte na **Vytvořit**.

**Základy**

1. Jako **Název** zadejte **Srv-Workload**.
5. Zadejte uživatelské jméno a heslo.
6. V části **Předplatné** vyberte své předplatné.
7. V části **Skupina prostředků** klikněte na **Použít existující** a pak vyberte **RG-DNAT-Test**.
8. V části **Umístění** vyberte dříve použité umístění.
9. Klikněte na **OK**.

**Velikost**

1. Zvolte vhodnou velikost pro testovací virtuální počítač s Windows Serverem. Například **B2ms** (8 GB paměti RAM, 16 GB úložiště).
2. Klikněte na **Vybrat**.

**Nastavení**

1. V části **Síť** jako **Virtuální síť** vyberte **VN-Spoke**.
2. Jako **Podsíť** vyberte **SN-Workload**.
3. Klikněte na **Veřejná IP adresa** a pak na **Žádná**.
4. V části **Vyberte veřejné příchozí porty** vyberte **Žádné veřejné příchozí porty**. 
2. Ostatní výchozí nastavení ponechte a klikněte na **OK**.

**Souhrn**

Zkontrolujte souhrn a potom klikněte na **Vytvořit**. Dokončení tohoto procesu může několik minut trvat.

Po dokončení nasazení si poznamenejte privátní IP adresu virtuálního počítače. Použijete ji později při konfiguraci brány firewall. Klikněte na název virtuálního počítače, v části **Nastavení** klikněte na **Sítě** a vyhledejte privátní IP adresu.

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

1. Na domovské stránce webu Azure Portal klikněte na **Vytvořit prostředek**.
2. Klikněte na **Sítě** a vedle **Doporučené** klikněte na **Zobrazit vše**.
3. Klikněte na bránu **Firewall** a potom vyberte **Vytvořit**. 
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Název     |FW-DNAT-test|
   |Předplatné     |\<Vaše předplatné\>|
   |Skupina prostředků     |**Použít existující**: RG-DNAT-Test |
   |Umístění     |Vyberte dříve použité umístění.|
   |Volba virtuální sítě     |**Použít existující**: VN-Hub|
   |Veřejná IP adresa     |**Vytvořte novou**. Veřejná IP adresa musí být typu Standardní SKU.|

5. Klikněte na **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a potom kliknutím na **Vytvořit** vytvořte bránu firewall.

   Nasazení může několik minut trvat.
7. Po dokončení nasazení přejděte do skupiny prostředků **RG-DNAT-Test** a klikněte na bránu firewall **FW-DNAT-test**.
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvořit výchozí trasu

U podsítě **SN-Workload** nakonfigurujete výchozí trasu v odchozím směru, která půjde přes bránu firewall.

1. Na domovské stránce webu Azure Portal klikněte na **Všechny služby**.
2. V části **Sítě** klikněte na **Směrovací tabulky**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Jako **Název** zadejte **RT-FWroute**.
5. V části **Předplatné** vyberte své předplatné.
6. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **RG-DNAT-Test**.
7. V části **Umístění** vyberte dříve použité umístění.
8. Klikněte na možnost **Vytvořit**.
9. Klikněte na **Aktualizovat** a pak klikněte na směrovací tabulku **RT-FWroute**.
10. Klikněte na **Podsítě** a pak na **Přidružit**.
11. Klikněte na **Virtuální síť** a pak vyberte **VN-Spoke**.
12. V části **Podsíť** klikněte na **SN-Workload**.
13. Klikněte na **OK**.
14. Klikněte na **Trasy** a pak na **Přidat**.
15. Jako **Název trasy** zadejte **FW-DG**.
16. V části **Předpona IP adresy** zadejte **0.0.0.0/0**.
17. V části **Typ dalšího směrování** vyberte **Virtuální zařízení**.

    Brána Azure Firewall je ve skutečnosti spravovaná služba, ale v tomto případě bude virtuální zařízení fungovat.
18. V části **Adresa dalšího směrování** zadejte dříve poznamenanou privátní IP adresu brány firewall.
19. Klikněte na **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurace pravidla NAT

1. Otevřete skupinu prostředků **RG-DNAT-Test** a klikněte na bránu firewall **FW-DNAT-test**. 
2. Na stránce **FW-DNAT-test** v části **Nastavení** klikněte na **Pravidla**. 
3. Klikněte na tlačítko **kolekce pravidel NAT přidat**. 
4. Jako **Název** zadejte **RC-DNAT-01**. 
5. V části **Priorita** zadejte **200**. 
6. V části **Pravidla** jako **Název** zadejte **RL-01**.
7. V části **Protokol** vyberte **TCP**.
8. Do pole **Zdrojové adresy** zadejte *. 
9. Do pole **Cílové adresy** zadejte veřejnou IP adresu brány firewall. 
10. Do pole **Cílové porty** zadejte **3389**. 
11. Do pole **Přeložená adresa** zadejte privátní IP adresu virtuálního počítače Srv-Workload. 
12. Do pole **Přeložený port** zadejte **3389**. 
13. Klikněte na tlačítko **Add** (Přidat). 

## <a name="test-the-firewall"></a>Otestovat bránu firewall

1. Připojte k veřejné IP adrese brány firewall vzdálenou plochu. Měli byste se připojit k virtuálnímu počítači **Srv-Workload**.
2. Zavřete vzdálenou plochu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **RG-DNAT-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvořit výchozí trasu
> * Konfigurace pravidla DNAT
> * Otestovat bránu firewall

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
