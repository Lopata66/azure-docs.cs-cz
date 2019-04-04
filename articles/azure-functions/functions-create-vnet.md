---
title: Integrace Azure Functions s Azure Virtual Network
description: Podrobný kurz ukazuje, jak se připojit ke službě Azure virtual network funkce
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 4f27bbeb43bbf373b621d151d68583f0041378b3
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894197"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrace aplikace function app s Azure virtual network

Tento podrobný kurz ukazuje, jak pomocí služby Azure Functions pro připojení k prostředkům ve virtuální síti Azure.

Pro účely tohoto kurzu jsme se nasazení webu z Wordpressu na virtuální počítač do privátního, bez – přístup k Internetu, virtuální sítě. Pak nasadíme funkce s přístupem k Internetu a virtuální síť. Tato funkce použije pro přístup k prostředkům z webu WordPress nasadit uvnitř virtuální sítě.

Další informace o tom, jak systém funguje, řešení potíží a pokročilou konfiguraci naleznete v dokumentu [integrujte svou aplikaci s Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Azure Functions v plánu Premium mají stejné možnosti hostování jako webové aplikace, takže všechny funkce a omezení v tomto dokumentu platí pro funkce také.

## <a name="topology"></a>Topologie

 ![Integrace virtuální sítě uživatelského rozhraní][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Vytvoření virtuálního počítače v rámci virtuální sítě

Chcete-li začít, vytvoříme předem nakonfigurované virtuální počítač s Wordpressem uvnitř virtuální sítě. 

WordPress na virtuálním počítači byla vybrána, protože je jeden z nejlevnější prostředků, které je možné nasadit uvnitř virtuální sítě. Všimněte si, že tento scénář může také pracovat s jakýmikoli prostředky ve virtuální síti, jako je například rozhraní REST API služby App Service Environment a dalšími službami Azure.

1. Přejít na web Azure Portal
2. Přidat nový prostředek tak, že otevřete okno "Vytvořit prostředek"
3. Vyhledejte "[WordPress LEMP7 Max Performance na CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" a otevřete její okno pro vytvoření 
4. V okně vytvoření konfigurace virtuálního počítače s následujícími informacemi:
    1. Vytvořte novou skupinu prostředků pro tento virtuální počítač, aby vymazání prostředků jednodušší na konci tohoto kurzu. Můžu s názvem Moje skupina prostředků "Kurzu funkce virtuální sítě"
    1. Zadejte jedinečný název virtuálního počítače. Můžu s názvem "Připojení typu VNET-Wordpress" dolování
    1. Vyberte oblast co nejblíže k vám
    1. Vyberte velikost jako B1s (1 virtuální procesor, 1 GB paměti)
    1. Pro účet správce vyberte ověřování pomocí hesla a zadejte jedinečné uživatelské jméno a heslo. V tomto kurzu nebudete potřebovat k přihlášení k virtuálnímu počítači, pokud potřebujete řešit.
    
        ![Vytvoření virtuálního počítače základní informace o kartě](./media/functions-create-vnet/create-vm-1.png)

1. Přejděte na síťové kartě a zadejte následující informace:
    1.  Vytvořit novou virtuální síť
    1.  Zadejte rozsah požadované privátních adres a podsítí v rámci tohoto rozsahu adres. Velikost podsítě určí, kolik virtuálních počítačů můžete použít v plánu služby App Service. Pokud IP adresování a zapojení podsítě jsou pro vás nová, je [dokumentu zaměřenou na Základy](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Přidělování IP adres a podsítí jsou důležité v tomto scénáři, takže by doporučuji čtení několika článcích a sledování online několik videí, dokud to dává smysl. 
        1. V tomto příkladu se můžu rozhodli používat 10.10.0.0/16 síť s podsítí 10.10.1.0/24. Volba za cenu značných a používat /16 podsítě vzhledem k tomu, že je snadno spočítat podsítě, které jsou k dispozici v síti 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Zpět na síťové kartě, nastavte veřejnou IP adresu na "None". To nasadí virtuální počítač s přístupem na pouze virtuální sítě.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Vytvoření virtuálního počítače. Bude to trvat přibližně 5 minut.
8. Po vytvoření virtuálního počítače najdete jeho síťové karty a poznamenejte si privátní IP adresu pro pozdější. Virtuální počítač by neměl mít veřejnou IP adresu.

    ![14]

Nyní máte webu z wordpressu nasazené výhradně ve vaší virtuální síti. Tento web není přístupný z veřejného Internetu.

## <a name="create-a-premium-plan-function-app"></a>Vytvoření plánu Premium aplikace Function App

Dalším krokem je vytvoření aplikace funkcí v plánu premium. Plán premium je nová nabídka, která přináší bez serveru škálovat na všechny výhody vyhrazený plán služby App Service. Aplikace funkcí plánu consumption nepodporují integrace virtuální sítě.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>Připojit vaši aplikaci Function App k virtuální síti

Pomocí webu z Wordpressu hostování souborů z v rámci vaší virtuální sítě můžete nyní připojit aplikace function app k virtuální síti.

1.  Na portálu pro danou aplikaci funkcí z předchozího kroku vyberte **funkce platformy**a pak vyberte **sítě**

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Vyberte **kliknout pro konfiguraci** v části Integrace virtuální sítě

    ![Konfigurovat stav síťových funkcí](./media/functions-create-vnet/Networking-1.png)

1. Na stránce integrace virtuální sítě vyberte **přidání virtuální sítě (preview)**

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Vytvořte novou podsíť pro vaše funkce a služby App Service plánujete používat. Všimněte si, že velikost podsítě omezí celkový počet virtuálních počítačů můžete přidat do plánu služby app service. Virtuální síť bude automaticky směrovat provoz mezi podsítěmi ve virtuální síti, takže není důležité, že funkce je v jiné podsíti, z vašeho virtuálního počítače. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Vytvořit funkci, která přistupuje k prostředkům ve vaší virtuální síti

Aplikace Function App teď přístup k virtuální síti s náš web wordpress, tedy budeme používat funkce pro přístup k souboru a obsluhovat zpět uživateli. V tomto příkladu použijeme webu z wordpressu jako rozhraní API a funkce Proxy jako volání funkce vzhledem k tomu, že jsou oba usnadňuje nastavení a vizualizace. Stejně snadno můžete použít jiné v rámci virtuální sítě nasazené rozhraní API a další funkce s kódem, vytváření rozhraní API volá rozhraní API nasazené v rámci vaší virtuální sítě. SQL server nasazený v rámci vaší virtuální sítě je ideální příklad.

1. Na portálu otevřete aplikaci Function App z předchozího kroku
1. Vytvořit Proxy tak, že vyberete **proxy servery** > **+**

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Nakonfigurujte název proxy serveru a směrování. Volba /plant jako Moje trasy.
1. Vyplňte vaše lokalita wordpress IP z předchozí a nastavte adresu URL back-endu na `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Nyní pokud se pokusíte navštivte adresu URL back-end přímo zadáním nebo vložením do na nové kartě prohlížeče, na stránce by vypršení časového limitu. Jedná se o očekávané chování, jak je vaše lokalita wordpress je připojen k pouze virtuální sítě a nikoli po Internetu. Pokud vložíte adresu URL vašeho proxy serveru do prohlížeče by se měla zobrazit obrázek krásné zařízení stáhli z webu Wordpress uvnitř virtuální sítě. 

Aplikace Function App je připojený k Internetu a virtuální síť. Proxy server je přijímání žádosti přes veřejný internet a pak funguje jako jednoduchý proxy server HTTP pro předávání tohoto požadavku podél do virtuální sítě. Proxy server potom předává požadavky odpověď zpět do prostřednictvím veřejného Internetu. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Další kroky

Funkce běžící v plánu Premium sdílet stejnou základní infrastruktury služby App Service jako webové aplikace. To znamená, že všechny dokumentace k Web Apps platí pro vaše funkce plánu Premium.

1. [Další informace o integraci virtuální sítě pomocí služby App Service / funkce zde](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [Další informace o virtuálních sítí v Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [Povolit pro síťové funkce a ovládacím prvkem služby App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [Připojení k jednotlivým místních prostředků bez nutnosti změn brány firewall pomocí hybridních připojení](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [Další informace o proxy serverů funkcí](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
