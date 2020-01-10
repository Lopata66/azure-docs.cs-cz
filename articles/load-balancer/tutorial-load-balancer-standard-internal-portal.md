---
title: 'Kurz: vytvoření interního nástroje pro vyrovnávání zatížení – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto kurzu se dozvíte, jak vytvořit interní standardní nástroj pro vyrovnávání zatížení pomocí Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 918a7700df6b5be3ebca7949875127e42f8d3a91
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780378"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>Kurz: Vyrovnávání zatížení interního provozu pomocí standardního nástroje pro vyrovnávání zatížení v Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky ve virtuálních počítačích (VM). Pomocí Azure Portal můžete vytvořit standardní nástroj pro vyrovnávání zatížení a vyrovnávat interní provoz mezi virtuálními počítači. V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat interní nástroj pro vyrovnávání zatížení, back-endové servery a síťové prostředky na cenové úrovni Standard.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Pokud dáváte přednost, můžete provést tento postup pomocí [rozhraní příkazového řádku Azure](load-balancer-get-started-ilb-arm-cli.md) nebo [prostředí Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) místo na portálu.

Postup použití v tomto kurzu, přihlaste se k webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Vytvoření virtuální sítě, back-end serverů a testovacího virtuálního počítače

Nejprve vytvořte virtuální síť (VNet). Ve virtuální síti vytvořte dva virtuální počítače, které se mají použít pro back-end fond standardního nástroje pro vyrovnávání zatížení, a třetí virtuální počítač, který se má použít k otestování nástroje pro vyrovnávání zatížení. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
   
1. V **vytvořit virtuální síť** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: typ **MyVNet**.
   - **Skupina prostředků**: vyberte **vytvořit nový**, zadejte **MyResourceGroupLB**a vyberte **OK**. 
   - **Podsíť** > **název**: typ **MyBackendSubnet**.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   - **Předplatné** > **skupiny prostředků**: odkládací dolů a vyberte možnost **MyResourceGroupLB**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: typ **MyVM1**.
   - **Podrobnosti Instance** > **oblasti**: vyberte **východní USA 2**.
  
   
1. Vyberte **sítě** kartě nebo vyberte **Další: disky**, pak **Další: sítě**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   - **Skupina zabezpečení sítě síťové karty**: vyberte **základní**.
   - **Veřejná IP adresa** > vyberte **vytvořit novou** a zadejte následující hodnoty a vyberte **OK**:
       - **Název**: **MyVM1-IP**
       - **SKU**: vyberte **Standard** .
   - **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - **Vyberte příchozí porty**: rozevírací seznam a vyberte **RDP (3389)** .

   
   
1. Vyberte **správu** kartě nebo vyberte **Další** > **správu**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální počítač s názvem **MyVM2**, se všemi nastaveními stejný jako MyVM1. 

1. Podle pokynů znovu vytvořte třetí virtuální počítač s názvem **MyTestVM**. 

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Pomocí portálu vytvořte standardní interní nástroj pro vyrovnávání zatížení. Název a IP adresu, kterou vytvoříte, se automaticky nakonfiguruje jako front-endu nástroje pro vyrovnávání zatížení.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
   
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte *MyResourceGroupLB* .|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Region (Oblast)         | Vyberte **Východní USA 2**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | Skladová položka           | Vyberte **Standard**.                          |
    | Virtuální síť           | Vyberte *MyVNet*.                          |    
    | Přiřazení IP adresy              | Vyberte **Statické**.   |
    | Privátní IP adresa|Zadejte adresu, která se nachází v adresním prostoru virtuální sítě a podsítě, například *10.3.0.7*.  |

3. Na kartě **Revize + vytvořit** klikněte na **vytvořit**. 
   

## <a name="create-standard-load-balancer-resources"></a>Vytvoření prostředků standardního nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení.

### <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

Účelem distribuce provozu do virtuálních počítačů, nástroje pro vyrovnávání zatížení používá fond back endových adres. Fond back endových adres obsahuje IP adresy virtuálních síťových rozhraní (NIC), které jsou připojené k nástroji pro vyrovnávání zatížení. 

**Vytvoření fondu back endových adres, který obsahuje VM1 a VM2:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
   
1. Na **přidat back-endový fond** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: typ **MyBackendPool**.
   
1. V části **virtuální počítače**. 
   1. Přidat **MyVM1** a **MyVM2** do back endového fondu.
   2. Jakmile přidáte každý počítač, rozevírací seznam a vyberte jeho **konfigurace protokolu IP sítě**. 
     
1. Vyberte **Přidat**.
   
   ![Přidejte fond back endových adres](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. Na **back-endové fondy** stránce, rozbalte **MyBackendPool** a ujistěte se, že **VM1** a **VM2** jsou uvedeny.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete povolit monitorování stavu virtuálních počítačů nástroje pro vyrovnávání zatížení, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

**K vytvoření sondy stavu pro monitorování stavu virtuálních počítačů:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na **přidat sondu stavu** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: typ **MyHealthProbe**.
   - **Protokol**: odkládací dolů a vyberte možnost **HTTP**. 
   - **Port**: typ **80**. 
   - **Cesta**: přijměte **/** pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiný identifikátor URI. 
   - **Interval**: typ **15**. Interval je počet sekund mezi pokusy o testování.
   - **Prahová hodnota špatného stavu**: typ **2**. Tato hodnota je počet chyb po sobě jdoucích sondování, ke kterým dojde před virtuální počítač považoval za poškozený.
   
1. Vyberte **OK**.
   
   ![Přidat test](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo automaticky odesílá síťový provoz do fondu back endových adres **MyBackendPool**, rovněž na portu 80. 

**Vytvořte pravidlo nástroje pro vyrovnávání zatížení:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na **přidat pravidlo Vyrovnávání zatížení** stránky, zadejte nebo vyberte následující hodnoty, pokud není již k dispozici:
   
   - **Název**: typ **MyLoadBalancerRule**.
   - **Front-endovou IP adresu:** typ **LoadBalancerFrontEnd** Pokud není k dispozici.
   - **Protokol**: vyberte **TCP**.
   - **Port**: typ **80**.
   - **Back-endový port**: typ **80**.
   - **Back-endový fond**: vyberte **MyBackendPool**.
   - **Sonda stavu**: vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
   ![Přidat pravidlo služby load balancer](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Instalace Internetové informační služby (IIS) na back-end serverů a pak použít MyTestVM k otestování nástroje pro vyrovnávání zatížení pomocí jeho privátní IP adresy. Každý back endového virtuálního počítače slouží jinou verzi výchozí webovou stránku IIS, abyste si mohli zobrazit distribuci požadavků mezi dvěma virtuálními počítači nástroje pro vyrovnávání zatížení.

Na portálu na **přehled** stránce **MyLoadBalancer**, vyhledejte jeho IP adresu v rámci **privátní IP adresa**. Podržte ukazatel myši nad adres a vyberte **kopírování** ikonu zkopírujte. V tomto příkladu je **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním počítačům přes RDP

Připojte se nejprve pro všechny tři virtuální počítače pomocí vzdálené plochy (RDP). 

>[!NOTE]
>Ve výchozím nastavení, budou již máte virtuální počítače **RDP** otevřete port (vzdálené plochy) povolit přístup přes vzdálenou plochu. 

**Pro vzdálené plochy (RDP) k virtuálním počítačům:**

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte každý virtuální počítač **MyResourceGroupLB** skupinu prostředků.
   
1. Na **přehled** stránce **připojit**a pak vyberte **soubor stáhnout RDP**. 
   
1. Otevřít protokol RDP jste stáhli a vyberte možnost **připojit**.
   
1. V dialogovém okně zabezpečení Windows vyberte **víc možností** a potom **použít jiný účet**. 
   
   Zadejte uživatelské jméno a heslo a pak vyberte **OK**.
   
1. Reakce **Ano** do libovolného řádku pro certifikát. 
   
   V novém okně se otevře na plochu virtuálního počítače. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalace služby IIS a nahradí výchozí stránku služby IIS na virtuálních počítačích back-end

Na každém serveru back-end pomocí prostředí PowerShell k instalaci IIS a výchozí webovou stránku IIS nahraďte upravené stránky.

>[!NOTE]
>Můžete také použít **Průvodce přidání rolí a funkcí** v **správce serveru** instalace služby IIS. 

**K instalaci IIS a výchozí webové stránky aktualizovat pomocí prostředí PowerShell:**

1. Spustit na MyVM1 a na MyVM2 **prostředí Windows PowerShell** z **Start** nabídky. 

2. Spusťte následující příkazy k instalaci IIS a nahradit výchozí webovou stránku IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Ukončete připojení RDP s MyVM1 a MyVM2 tak, že vyberete **odpojit**. Nevypínejte virtuálních počítačů.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. Na MyTestVM, otevřete **aplikace Internet Explorer**a Odpovědět **OK** všechny konfigurace zobrazí výzvu. 
   
1. Vložte nebo zadejte privátní IP adresu nástroje pro vyrovnávání zatížení (*10.3.0.7*) do adresního řádku prohlížeče. 
   
   Přizpůsobené serveru výchozí stránku webové služby IIS se zobrazí v prohlížeči. Přečte zprávu buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
1. Aktualizujte prohlížeč, pokud chcete zobrazit distribuci provozu mezi virtuální počítače, nástroj pro vyrovnávání zatížení. Také budete muset vymazat mezipaměť prohlížeče mezi pokusy.

   Někdy **MyVM1** se zobrazí stránka a jindy **MyVM2** stránky se zobrazí, jak nástroj pro vyrovnávání zatížení distribuuje požadavky na každý back endového virtuálního počítače. 

   ![Nové výchozí stránka služby IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nástroj pro vyrovnávání zatížení a všech souvisejících prostředků, když je už nepotřebujete, otevřete **MyResourceGroupLB** prostředku, skupiny a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili standardní interní nástroj pro vyrovnávání zatížení. Vytvoření a konfiguraci síťových prostředků, back-end serverů, sondu stavu a pravidel nástroje pro vyrovnávání zatížení. Službu IIS nainstalovali na virtuálních počítačích back-end a použít testovací virtuální počítač k otestování nástroje pro vyrovnávání zatížení v prohlížeči. 

Dále se naučíte vyrovnávat zatížení virtuálních počítačů napříč zónami dostupnosti.

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
