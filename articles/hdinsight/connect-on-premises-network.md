---
title: Připojení Azure HDInsight k místní síti
description: Zjistěte, jak vytvořit HDInsight cluster ve službě Azure Virtual Network a pak ho připojíte k místní síti. Zjistěte, jak nakonfigurovat překlad mezi HDInsight a v místní síti pomocí vlastního serveru DNS.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 52fe8c05101f9647549acec276f0bdb9fa52d1c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256800"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Připojení HDInsightu k místní síti

Zjistěte, jak připojit HDInsight k místní síti pomocí virtuální sítě Azure a bránu VPN. Tento dokument obsahuje informace o plánování na:

* Pomocí HDInsight ve virtuální síti Azure, která se připojuje k vaší místní síti.
* Konfigurace překladu názvů DNS mezi virtuální sítí a místní sítí.
* Konfigurace skupin zabezpečení sítě pro omezení přístupu k Internetu k HDInsight.
* Porty, které poskytuje HDInsight ve virtuální síti.

## <a name="overview"></a>Přehled

Povolit HDInsight a prostředky v připojeném k síti komunikovat podle názvu, je třeba provést následující akce:

* Vytvoření virtuální sítě Azure.
* Vytvoření vlastního serveru DNS ve virtuální síti Azure.
* Konfigurace virtuální sítě pro použití vlastního serveru DNS. místo výchozího rekurzivní překladače Azure.
* Konfigurace předávání mezi vlastního serveru DNS a místním serverem DNS.

Tato konfigurace umožňuje toto chování:

* Požadavky pro plně kvalifikované názvy domén, které mají příponu DNS __pro virtuální síť__ se předávají do vlastního serveru DNS. Tyto požadavky vlastního serveru DNS. potom předá do rekurzivní překladače Azure, které vrací IP adresu.
* Všechny ostatní žádosti jsou předávány na místním serveru DNS. Dokonce i požadavky na veřejné internetové prostředky, třeba microsoft.com jsou předány na místním serveru DNS pro překlad názvů.

V následujícím diagramu zelenou řádky jsou požadavky na prostředky, které končí příponou DNS ve virtuální síti. Modré čáry jsou požadavky na prostředky v místní síti nebo na veřejný internet.

![Diagram způsob řešení žádosti DNS v konfiguraci v tomto dokumentu](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Pokud používáte PowerShell, budete potřebovat [AZ modulu](https://docs.microsoft.com/powershell/azure/overview).
* Pokud chtějí používat rozhraní příkazového řádku Azure a je zatím ho ještě nenainstalovali, naleznete v tématu [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Vytvoření konfigurace virtuální sítě

Zjistěte, jak vytvořit virtuální síť Azure, který je připojený k vaší místní síti pomocí následujících dokumentech:

* [Pomocí webu Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Použití Azure PowerShellu](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Použití Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Vytvoření vlastního serveru DNS

> [!IMPORTANT]  
> Musíte vytvořit a konfigurovat DNS server před instalací HDInsight do virtuální sítě.

Tyto kroky používají [webu Azure portal](https://portal.azure.com) k vytvoření virtuálního počítače Azure. Další způsoby vytvoření virtuálního počítače, naleznete v tématu [vytvořit virtuální počítač – rozhraní příkazového řádku Azure](../virtual-machines/linux/quick-create-cli.md) a [vytvořit virtuální počítač – Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Vytvoření virtuálního počítače s Linuxem, který používá [svázat](https://www.isc.org/downloads/bind/) DNS software, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
  
2. V nabídce vlevo přejděte na **+ vytvořit prostředek** > **Compute** > **Ubuntu Server 18.04 LTS**.

    ![Vytvoření virtuálního počítače s Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

3. Z __Základy__ kartu, zadejte následující informace:  
  
    | Pole | Hodnota |
    | --- | --- |
    |Předplatné |Vyberte odpovídající předplatné.|
    |Skupina prostředků |Vyberte skupinu prostředků, která obsahuje virtuální síť vytvořili dříve.|
    |Název virtuálního počítače | Zadejte popisný název, který identifikuje tento virtuální počítač. Tento příklad používá **DNSProxy**.|
    |Oblast | Vyberte stejné oblasti jako virtuální síť vytvořili dříve.  Ne všechny velikosti virtuálních počítačů jsou k dispozici ve všech oblastech.  |
    |Možnosti dostupnosti |  Vyberte požadovanou úroveň dostupnosti.  Azure nabízí celou řadu možností pro správu dostupnosti a odolnosti proti chybám pro vaše aplikace.  Architektury řešení použití k ochraně aplikací a dat v případě výpadku datacentra nebo události údržby replikované virtuální počítače v zónách dostupnosti nebo skupinami dostupnosti. Tento příklad používá **žádné redundance infrastruktury požadované**. |
    |Image | Ponechte **Ubuntu Server 18.04 LTS**. |
    |Typ ověřování | __Heslo__ nebo __veřejný klíč SSH__: Metoda ověřování pro účet SSH. Doporučujeme pomocí veřejných klíčů, protože jde o zabezpečení. Tento příklad používá **heslo**.  Další informace najdete v tématu [vytvoření a použití klíčů SSH pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.|
    |Uživatelské jméno |Zadejte uživatelské jméno správce pro virtuální počítač.  Tento příklad používá **sshuser**.|
    |Heslo nebo SSH veřejný klíč | Dostupná pole se určuje podle vaší volby **typ ověřování**.  Zadejte odpovídající hodnotu.|
    |Veřejné příchozí porty|Vyberte **povolit vybrané porty**. Potom vyberte **SSH (22)** z **vyberte příchozí porty** rozevíracího seznamu.|

    ![Základní konfigurace virtuálního počítače](./media/connect-on-premises-network/vm-basics.png)

    Další položky ponechte výchozí hodnoty a pak vyberte **sítě** kartu.

4. Z **sítě** kartu, zadejte následující informace:

    | Pole | Hodnota |
    | --- | --- |
    |Virtuální síť | Vyberte virtuální síť, kterou jste vytvořili dříve.|
    |Podsíť | Vyberte výchozí podsíť pro virtuální síť, kterou jste vytvořili dříve. Proveďte __není__ vyberte podsíť používá bránu sítě VPN.|
    |Veřejná IP adresa | Použijte hodnotu vyplní automaticky.  |

    ![Nastavení virtuální sítě](./media/connect-on-premises-network/virtual-network-settings.png)

    Další položky ponechte výchozí hodnoty a pak vyberte **revize + vytvořit**.

5. Z **revize + vytvořit** kartu, vyberte možnost **vytvořit** k vytvoření virtuálního počítače.

### <a name="review-ip-addresses"></a>Zkontrolujte IP adresy
Po vytvoření virtuálního počítače, zobrazí se **nasazení bylo úspěšné** oznámení **přejít k prostředku** tlačítko.  Vyberte **přejít k prostředku** přejděte k novému virtuálnímu počítači.  Výchozí zobrazení pro nový virtuální počítač použijte následující postup identifikovat přidružené IP adresy:

1. Z **nastavení**vyberte **vlastnosti**.

2. Poznamenejte si hodnoty pro **veřejné IP adresy nebo DNS název popisek** a **PRIVÁTNÍ IP adresa** pro pozdější použití.

   ![Veřejných a privátních IP adres](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalace a konfigurace vazby (DNS software)

1. Pomocí SSH se připojte k __veřejnou IP adresu__ virtuálního počítače. Nahraďte `sshuser` pomocí uživatelského účtu SSH, jste zadali při vytváření virtuálního počítače. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. K instalaci vazby, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ke konfiguraci vazby pro předávání název řešení požadavků na serveru DNS v místním prostředí, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]  
    > Nahraďte hodnoty v `goodclients` část s rozsahem IP adres virtuální sítě a v místní síti. Tento oddíl definuje adresy, které přijímá požadavky od tento server DNS.
    >
    > Nahraďte `192.168.0.1` položku `forwarders` část s IP adresou vašeho serveru DNS v místním. Tato položka směruje žádosti DNS na místní server DNS pro překlad.

    K úpravě tohoto souboru použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobné následujícímu textu:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Text je __přípona DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, bude se hodit později.

5. Ke konfiguraci vazby k překladu názvů DNS pro prostředky v rámci virtuální sítě, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Je třeba nahradit `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` s příponou DNS, který jste získali dříve.

    K úpravě tohoto souboru použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

6. Spuštění vazby, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, že vazby dokáže přeložit názvy prostředků ve vaší místní síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Nahraďte `dns.mynetwork.net` s plně kvalifikovaný název domény (FQDN) prostředků ve vaší místní síti.
    >
    > Nahraďte `10.0.0.4` s __interní IP adresa__ vašeho vlastního serveru DNS ve virtuální síti.

    Odpověď se zobrazí podobný následujícímu textu:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS.

Ke konfiguraci virtuální sítě, abyste místo Azure rekurzivní překladač použít vlastní server DNS, použijte následující kroky v [webu Azure portal](https://portal.azure.com):

1. V nabídce vlevo přejděte na **všechny služby** > **sítě** > **virtuální sítě**.

2. Vyberte virtuální síť v seznamu se otevře zobrazení výchozí pro vaši virtuální síť.  

3. Výchozí zobrazení v části **nastavení**vyberte **servery DNS**.  

4. Vyberte __vlastní__a zadejte **PRIVÁTNÍ IP adresa** vlastního serveru DNS.   

5. Vyberte __Uložit__.  <br />  

    ![Nastavení vlastního serveru DNS pro síť](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurace serveru DNS na místě

V předchozí části jste nakonfigurovali vlastní server DNS ke směrování žádostí na server DNS místní. Dále musíte nakonfigurovat na místním serveru DNS ke směrování žádostí na vlastní server DNS.

Konkrétní kroky pro konfiguraci serveru DNS najdete v dokumentaci pro váš software serveru DNS. Hledat podle pokynů na tom, jak nakonfigurovat __podmíněné předávání__.

Podmíněné předávání pouze předává požadavky pro konkrétní příponu DNS. V takovém případě musíte nakonfigurovat server pro předávání pro příponu DNS virtuální sítě. Požadavky pro tuto příponu mají předávat IP adresu serveru DNS. 

Následující text je příklad konfigurace pro podmíněné předávání **svázat** DNS software:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Informace o použití DNS na **Windows serveru 2016**, najdete v článku [přidat DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentace...

Jakmile jste nakonfigurovali na místním serveru DNS, můžete použít `nslookup` z místní sítě k ověření, aby dokázaly vyhledat názvy virtuální sítě. Následující příklad 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Tento příklad používá na místním serveru DNS na 196.168.0.4 názvu vlastního serveru DNS. Nahraďte IP adresu pro místní server DNS. Nahradit `dnsproxy` adresu se plně kvalifikovaný název vlastního serveru DNS.

## <a name="optional-control-network-traffic"></a>Volitelné: Řízení síťového provozu

Skupiny zabezpečení sítě (NSG) nebo trasy definované uživatelem (UDR) můžete použít k řízení síťového provozu. Skupiny zabezpečení sítě povolují filtrování příchozího a odchozího provozu a povolit nebo zakázat provoz. Trasy definované uživatelem umožňují řídit tok provozu mezi prostředky virtuální sítě, Internetu a v místní síti.

> [!WARNING]  
> HDInsight vyžaduje příchozí přístup z konkrétních IP adres v cloudu Azure a neomezený odchozí přístup. Při použití skupin zabezpečení sítě nebo udr pro řízení provozu, je třeba provést následující kroky:

1. Najdete IP adresy pro umístění, která obsahuje virtuální síť. Seznam požadované IP adresy podle umístění najdete v tématu [požadované IP adresy](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Pro IP adresy, určenou v kroku 1, povolí příchozí provoz z této IP adresy.

   * Pokud používáte __NSG__: Povolit __příchozí__ přenosy na portu __443__ pro IP adresy.
   * Pokud používáte __uživatelem definovaná TRASA__: Nastavte __dalšího segmentu směrování__ trasy, která má typ __Internet__ pro IP adresy.

Příklad použití Azure Powershellu nebo rozhraní příkazového řádku Azure k vytvoření skupin zabezpečení sítě, najdete v článku [rozšířit HDInsight s Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

> [!WARNING]  
> Před instalací HDInsight ve virtuální síti musíte nakonfigurovat vlastní server DNS.

Postupujte podle kroků v [vytvoření clusteru HDInsight pomocí webu Azure portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu k vytvoření clusteru HDInsight.

> [!WARNING]  
> * Při vytváření clusteru musíte zvolit umístění, která obsahuje virtuální síť.
> * V __upřesňující nastavení__ část konfigurace, je nutné vybrat virtuální síť a podsíť, která jste vytvořili dříve.

## <a name="connecting-to-hdinsight"></a>Připojení k HDInsight

Většina dokumentace na HDInsight předpokládá, že máte přístup ke clusteru přes internet. To znamená, že se ke clusteru můžete připojit třeba na `https://CLUSTERNAME.azurehdinsight.net`. Tato adresa se používá veřejnou brány, která není k dispozici, pokud jste použili skupiny zabezpečení sítě nebo udr pro omezení přístupu z Internetu.

Také odkazuje na některé dokumentaci `headnodehost` při připojování ke clusteru z relace SSH. Tato adresa je dostupná pouze z uzlů v rámci clusteru a není použitelný na klientů připojených přes virtuální síť.

K přímému připojení k HDInsight prostřednictvím virtuální sítě, použijte následující postup:

1. Chcete-li zjistit, interní plně kvalifikované názvy domény uzlů clusteru HDInsight, použijte jednu z následujících metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Pokud chcete určit port, který je k dispozici na službu, naleznete v tématu [portů používaných služeb Apache Hadoop v HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

    > [!IMPORTANT]  
    > Některé služby hostované na hlavní uzly jsou aktivní na jednom uzlu jenom po jednom. Pokud pokusu o přístup k službě na jeden hlavní uzel a selže, přejděte k hlavnímu uzlu.
    >
    > Například Apache Ambari slouží pouze na jeden hlavní uzel aktivní najednou. Pokud se pokusíte přístup k Ambari na jeden hlavní uzel a vrátí chybu 404, je spuštěna na k hlavnímu uzlu.

## <a name="next-steps"></a>Další postup

* Další informace o používání HDInsight ve virtuální síti najdete v tématu [rozšířit HDInsight pomocí Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md).

* Další informace o virtuálních sítích Azure najdete v článku [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/security-overview.md).

* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).
