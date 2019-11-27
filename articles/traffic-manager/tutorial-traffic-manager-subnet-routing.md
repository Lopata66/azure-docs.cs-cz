---
title: Kurz – konfigurace směrování provozu v podsíti pomocí Azure Traffic Manager
description: V tomto kurzu se dozvíte, jak nakonfigurovat Traffic Manager pro směrování provozu z podsítí uživatelů do konkrétních koncových bodů.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: 00bc453ebb0e467f48bd886fc7c6b6c422693864
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420256"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Kurz: směrování provozu do konkrétních koncových bodů na základě podsítě uživatele pomocí Traffic Manager

Tento článek popisuje, jak nakonfigurovat metodu směrování provozu podsítě. Metoda směrování provozu **podsítě** umožňuje namapovat sadu rozsahů IP adres na konkrétní koncové body. Když Traffic Manager přijme požadavek, prozkoumá se zdrojová IP adresa požadavku a vrátí se koncový bod, který je k ní přidružený.

V tomto kurzu se s využitím směrování podsítě bude provoz směrovat na interní nebo produkční web v závislosti na IP adrese dotazu uživatele.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu služby Traffic Manager pro směrování provozu na základě podsítě uživatele
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:

- dva základní weby spuštěné v různých oblastech Azure – **USA – východ** (slouží jako interní web) a **Západní Evropa** (slouží jako produkční web)
- dva testovací virtuální počítače pro testování služby Traffic Manager – jeden virtuální počítač v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**

Testovací virtuální počítače slouží k ilustraci způsobu, jakým Traffic Manager směruje provoz uživatelů na interní nebo produkční web v závislosti na podsíti, ze které dotaz uživatele pochází.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:

1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

V této části vytvoříte dva virtuální počítače *myIISVMEastUS* a *myIISVMWestEurope* v oblastech **východní USA** a **západní Evropa** Azure.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** > **výpočetní** > **Windows Server 2019 Datacenter**.
2. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :

   - **Předplatné** > **skupinu prostředků**: vyberte **vytvořit novou** a potom zadejte **myResourceGroupTM1**.
   - **Podrobnosti Instance** > **název virtuálního počítače**: zadejte *myIISVMEastUS*.
   - **Podrobnosti Instance** > **oblasti**: vyberte **východní USA**.
   - **Účet správce** > **uživatelské jméno**: zadejte uživatelské jméno podle vašeho výběru.
   - **Účet správce** > **heslo**: zadejte heslo, které jste si zvolili. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Pravidla portů pro příchozí** > **veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - **Pravidla portů pro příchozí** > **vybrat příchozí porty**: v rozevíracím seznamu vyberte možnost **RDP** a **http** .

3. Vyberte kartu **Správa** nebo vyberte **Další: disky**, **Další: sítě**a **Další: Správa**. V části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a klikněte na **vytvořit**.  
6. Postupujte podle kroků a vytvořte druhý virtuální počítač s názvem *myIISVMWestEurope*s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístěním** *západní Evropa*a všechna ostatní nastavení stejné jako *myIISVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server IIS na dva virtuální počítače – *myIISVMEastUS* & *myIISVMWestEurope*a pak aktualizujete výchozí stránku webu. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
7. Spusťte Windows PowerShell na virtuálním počítači *myIISVMEastUS*a pomocí následujících příkazů nainstalujte server IIS a aktualizujte výchozí soubor htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zavřete připojení RDP k virtuálnímu počítači *myIISVMEastUS* .
9. Opakujte kroky 1-6 s vytvořením připojení RDP s *myIISVMWestEurope* virtuálního počítače ve skupině prostředků *myResourceGroupTM2* pro instalaci služby IIS a přizpůsobení její výchozí webové stránky.
10. Spusťte Windows PowerShell na virtuálním počítači s *myIISVMWestEurope* a pomocí následujících příkazů nainstalujte server IIS a aktualizujte výchozí soubor htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery IIS – *myIISVMEastUS* a *myIISVMWestEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální počítač s názvem *myIISVMWestEurope* , který je umístěný ve skupině prostředků *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač (*myVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**východní USA** a **západní Evropa**). Tyto virtuální počítače použijete k otestování, jak Traffic Manager směrují přenosy uživatelů na základě podsítě dotazu uživatele.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** > **výpočetní** > **Windows Server 2019 Datacenter**.
2. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :

   - **Předplatné** > **skupinu prostředků**: vyberte **myResourceGroupTM1**.
   - **Podrobnosti Instance** > **název virtuálního počítače**: zadejte *myVMEastUS*.
   - **Podrobnosti Instance** > **oblasti**: vyberte **východní USA**.
   - **Účet správce** > **uživatelské jméno**: zadejte uživatelské jméno podle vašeho výběru.
   - **Účet správce** > **heslo**: zadejte heslo, které jste si zvolili. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Pravidla portů pro příchozí** > **veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - **Pravidla portů pro příchozí** > **vybrat příchozí porty**: v rozevíracím seznamu pro stažení vyberte **RDP** .

3. Vyberte kartu **Správa** nebo vyberte **Další: disky**, **Další: sítě**a **Další: Správa**. V části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a klikněte na **vytvořit**.  
6. Postupujte podle kroků a vytvořte druhý virtuální počítač s názvem *myVMWestEurope*s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístěním** *západní Evropa*a všechna ostatní nastavení stejné jako *myVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil služby Traffic Manager, který vám umožní na základě zdrojové IP adresy požadavku vracet konkrétní koncové body.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Jako metodu směrování vyberte **Podsíť**.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Existující** a zadejte *myResourceGroupTM1*. |
    | |                              |
    |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače, na kterých běží servery IIS – *myIISVMEastUS* & *myIISVMWestEurope* , ke směrování provozu uživatelů na základě podsítě dotazu uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Název           | myInternalWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Nastavení směrování podsítě    |   Přidejte IP adresu testovacího virtuálního počítače *myVMEastUS* . Všechny dotazy uživatelů pocházející z tohoto virtuálního počítače se přesměrují na *myInternalWebSiteEndpoint*.    |

4. Opakujte kroky 2 a 3 a přidejte další koncový bod s názvem *myProdWebsiteEndpoint* pro veřejnou IP adresu *myIISVMWestEurope-IP* , která je přidružená k VIRTUÁLNÍMU počítači serveru služby IIS s názvem *myIISVMWestEurope*. V části **nastavení směrování podsítě**přidejte IP adresu testovacího virtuálního počítače – *myVMWestEurope*. Všechny dotazy uživatelů z tohoto testovacího virtuálního počítače se budou směrovat do koncového bodu *myProdWebsiteEndpoint*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části otestujete, jak Traffic Manager směruje provoz uživatelů z dané podsítě do konkrétního koncového bodu. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v oblasti **východní USA** , přejděte ve webovém prohlížeči na název DNS vašeho profilu Traffic Manager.
    - Z testovacího virtuálního počítače (*myVMWestEurope*), který je umístěný v oblasti **západní Evropa** , přejděte ve webovém prohlížeči na název DNS vašeho profilu Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
2. Klikněte na **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

V této části uvidíte službu Traffic Manager v akci.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že *myVMEastUS* IP adresa virtuálního počítače je přidružená ke koncovému bodu *myInternalWebsiteEndpoint*, webový prohlížeč spustí testovací webový server – *myIISVMEastUS*.

7. V dalším kroku se připojte k virtuálnímu počítači *myVMWestEurope* , který se nachází v **Západní Evropa** pomocí kroků 1-5 a přejděte k názvu domény profilu Traffic Manager z tohoto virtuálního počítače. Vzhledem k tomu, že *myVMWestEurope* IP adresa virtuálního počítače je přidružená ke koncovému bodu *myProductionWebsiteEndpoint*, webový prohlížeč spustí testovací webový server – *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
