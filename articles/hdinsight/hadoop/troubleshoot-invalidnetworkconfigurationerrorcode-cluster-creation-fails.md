---
title: Chyba InvalidNetworkConfigurationErrorCode – Azure HDInsight
description: Různé důvody pro neúspěšné vytváření clusterů pomocí InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720380"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Vytvoření clusteru selhalo s InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

Pokud se zobrazí kód chyby `InvalidNetworkConfigurationErrorCode` s popisem "Virtual Network konfigurace není kompatibilní s požadavkem HDInsight", obvykle to znamená problém s [konfigurací virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) pro váš cluster. Na základě zbytku popisu chyby použijte následující části k vyřešení vašeho problému.

## <a name="hostname-resolution-failed"></a>"Překlad názvu hostitele se nezdařil"

### <a name="issue"></a>Problém

Popis chyby obsahuje "překlad názvu hostitele se nezdařil".

### <a name="cause"></a>Příčina

Tato chyba odkazuje na problém s vlastní konfigurací DNS. Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS do rekurzivních překladačů Azure za účelem překladu názvů hostitelů v této virtuální síti (podrobnosti najdete [v tématu překlad IP adres ve virtuálních sítích](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ). Přístup k rekurzivním překladačům Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16. Tato IP adresa je dostupná jenom z virtuálních počítačů Azure. Takže nebude fungovat, pokud používáte server DNS OnPrem nebo server DNS je virtuální počítač Azure, který není součástí virtuální sítě clusteru.

### <a name="resolution"></a>Rozlišení

1. Připojte se přes SSH k virtuálnímu počítači, který je součástí clusteru, a spusťte příkaz `hostname -f`. Tím se vrátí plně kvalifikovaný název domény hostitele (v následujících pokynech se označuje jako `<host_fqdn>`).

1. Pak spusťte příkaz `nslookup <host_fqdn>` (například `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Pokud tento příkaz přeloží název na IP adresu, znamená to, že váš server DNS funguje správně. V takovém případě získáte případ podpory pomocí služby HDInsight a my prozkoumáme váš problém. Ve svém případu podpory zahrňte kroky pro řešení potíží, které jste provedli. Tím se nám pomůžete problém vyřešit rychleji.

1. Pokud výše uvedený příkaz nevrátí IP adresu, spusťte `nslookup <host_fqdn> 168.63.129.16` (například `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Pokud je tento příkaz schopný přeložit IP adresu, znamená to, že server DNS nepředává dotaz do DNS Azure, nebo není virtuálním počítačem, který je součástí stejné virtuální sítě jako cluster.

1. Pokud nemáte virtuální počítač Azure, který může fungovat jako vlastní server DNS ve virtuální síti clusteru, musíte ho nejdřív přidat. Vytvořte virtuální počítač ve virtuální síti, který se nakonfiguruje jako služba DNS pro přeposílání.

1. Jakmile ve virtuální síti nasadíte virtuální počítač, nakonfigurujte pravidla předávání DNS na tomto virtuálním počítači. Předejte všechny požadavky na překlad názvů IDN do 168.63.129.16 a zbytek na váš server DNS. [Tady](../hdinsight-plan-virtual-network-deployment.md) je příklad tohoto instalačního programu pro vlastní server DNS.

1. Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro konfiguraci služby Virtual Network DNS.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>Nepovedlo se připojit k Azure Storagemu účtu.

### <a name="issue"></a>Problém

Popis chyby obsahuje "připojení k účtu Azure Storage se nepovedlo nebo se nepovedlo připojit k Azure SQL".

### <a name="cause"></a>Příčina

Azure Storage a SQL nemají pevné IP adresy, proto musíme Povolit odchozí připojení ke všem IP adresám, aby bylo možné získat přístup k těmto službám. Přesný postup řešení závisí na tom, jestli jste nastavili skupinu zabezpečení sítě (NSG) nebo uživatelsky definovaná pravidla (UDR). Podrobnosti o těchto konfiguracích najdete v části [řízení síťového provozu pomocí služby HDInsight se skupinami zabezpečení sítě a uživatelsky definovanými trasami](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) .

### <a name="resolution"></a>Rozlišení

* Pokud váš cluster používá [skupinu zabezpečení sítě (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Přejít na Azure Portal a Identifikujte NSG, která je přidružená k podsíti, ve které se cluster nasazuje. V části **odchozí pravidla zabezpečení** povolte odchozí přístup k Internetu bez omezení (Všimněte si, že tady je **nižší priorita** , což znamená vyšší prioritu). V části **podsítě** také potvrďte, zda je tento NSG použit pro podsíť clusteru.

* Pokud váš cluster používá [trasy definované uživatelem (udr)](../../virtual-network/virtual-networks-udr-overview.md).

    Přejít na Azure Portal a Identifikujte směrovací tabulku, která je přidružená k podsíti, ve které se cluster nasazuje. Po nalezení směrovací tabulky pro podsíť zkontrolujte část **trasy** v ní.

    Pokud jsou definované trasy, ujistěte se, že existují trasy pro IP adresy v oblasti, ve které byl cluster nasazený, a **typem** pro každou trasu je **Internet**. Pro každou požadovanou IP adresu popsanou ve výše uvedeném článku by měla být definována trasa.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Konfigurace virtuální sítě není kompatibilní s požadavkem HDInsight".

### <a name="issue"></a>Problém

Popisy chyb obsahují zprávy podobné následujícímu:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Příčina

Zřejmě došlo k potížím s vlastním nastavením DNS.

### <a name="resolution"></a>Rozlišení

Ověřte, že je 168.63.129.16 ve vlastním řetězci DNS. Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS do rekurzivních překladačů Azure za účelem překladu názvů hostitelů v této virtuální síti. Další informace najdete v tématu [překlad názvů ve virtuálních sítích](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Přístup k rekurzivním překladačům Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16.

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spusťte následující příkaz:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Mělo by se vám zobrazit přibližně toto:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Na základě výsledku vyberte jeden z následujících kroků a postupujte takto:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 není v tomto seznamu.

**Možnost 1**  
Přidejte 168.63.129.16 jako první vlastní DNS pro virtuální síť pomocí postupu popsaného v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Tyto kroky platí jenom v případě, že vlastní server DNS běží na Linux.

**Možnost 2**  
Nasazení virtuálního počítače serveru DNS pro virtuální síť. Zahrnuje následující kroky:

* Vytvořte virtuální počítač ve virtuální síti, který se nakonfiguruje jako služba DNS pro překládání (může to být Linux nebo Windows VM).
* Nakonfigurujte pravidla předávání DNS na tomto virtuálním počítači (předejte všechny požadavky na překlad názvů IDN do 168.63.129.16 a zbývající na váš server DNS).
* Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro Virtual Network konfiguraci DNS.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 je v seznamu

V takovém případě prosím vytvořte případ podpory v HDInsight a my prozkoumáme váš problém. Do případu podpory zahrňte výsledek níže uvedených příkazů. Pomůže nám to prozkoumat a vyřešit problém rychleji.

Z relace SSH na hlavním uzlu upravte a pak spusťte následující:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
