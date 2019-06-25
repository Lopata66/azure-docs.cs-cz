---
title: O možnostech sítě v zotavení po havárii Azure do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Poskytuje přehled sítí pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sujayt
ms.openlocfilehash: a6c9c690efe8b75cd1a939de1c68cf4e5bd40d70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789756"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>O možnostech sítě v replikaci z Azure do Azure



Tento článek obsahuje pokyny k síti při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiného, pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Než začnete

Zjistěte, jak Site Recovery poskytuje zotavení po havárii pro [tento scénář](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typickou síťovou infrastrukturu

Následující diagram znázorňuje typickou prostředí Azure pro aplikace běžící na virtuálních počítačích Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pokud používáte Azure ExpressRoute nebo VPN připojení z vaší místní sítě do Azure, prostředí vypadá takto:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Obvykle sítě jsou chráněny pomocí brány firewall a skupin zabezpečení sítě (Nsg). Brány firewall pomocí adresy URL nebo na základě IP adresy na seznam povolených připojení k síti. Skupiny Nsg stanovit pravidla, které používají rozsahy IP adres k řízení síťového připojení.

>[!IMPORTANT]
> Použití ověřeného proxy řízení síťového připojení není podporováno službou Site Recovery a nejde povolit replikaci.


## <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall na základě adresy URL, povolte tyto adresy URL pro obnovení lokality:


**Adresa URL** | **Podrobnosti**  
--- | ---
*.blob.core.windows.net | Vyžaduje, aby data je možné zapisovat na účet úložiště mezipaměti ve zdrojové oblasti z virtuálního počítače. Pokud znáte všechny mezipaměti účty úložiště pro virtuální počítače, můžete seznam povolených adresy URL účtu konkrétní úložiště (Ex: cache1.blob.core.windows.net a cache2.blob.core.windows.net) namísto *. blob.core.windows.net
login.microsoftonline.com | Vyžaduje se pro autorizaci a ověřování k adresám URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Vyžaduje, aby mohla probíhat komunikace služby Site Recovery z virtuálního počítače. Pokud váš proxy server brány firewall podporuje IP adresy, můžete použít odpovídající "Site Recovery IP".
*.servicebus.windows.net | Vyžaduje, aby data monitorování a diagnostiky Site Recovery lze zapsat z virtuálního počítače. Pokud váš proxy server brány firewall podporuje IP adresy, můžete použít odpovídající "Site Recovery monitorování IP".

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud k řízení odchozího připojení používáte proxy server založené na protokolu IP brány firewall nebo pravidla skupiny zabezpečení sítě, musí být povoleny tyto rozsahy IP adres.

- Všechny rozsahy IP adres, které odpovídají účty úložiště ve zdrojové oblasti
    - Vytvoření [značka služby úložiště](../virtual-network/security-overview.md#service-tags) podle pravidla NSG pro zdrojové oblasti.
    - Povolte tyto adresy tak, aby data je možné zapisovat na účet úložiště mezipaměti z virtuálního počítače.
- Vytvoření [značky služby Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na základě pravidel skupiny zabezpečení sítě umožňující přístup ke všem IP adresám v odpovídající službě AAD
    - Pokud do Azure Active Directory (AAD) se přidají nové adresy v budoucnu, musíte vytvořit nová pravidla skupiny zabezpečení sítě.
- Site Recovery service koncový bod IP adresy – k dispozici v [soubor XML](https://aka.ms/site-recovery-public-ips) a závisí na cílovém umístění.
- Doporučujeme vytvořit požadovaná pravidla NSG na testovací skupiny zabezpečení sítě a ověřte, že neexistují žádné problémy, než vytvoříte pravidla v produkčním prostředí skupiny zabezpečení sítě.


Rozsahy adres IP pro obnovení lokality jsou následující:

   **Cíl** | **Site Recovery IP** |  **Site Recovery monitorování IP**
   --- | --- | ---
   Východní Asie | 52.175.17.132 | 13.94.47.61
   Jihovýchodní Asie | 52.187.58.193 | 13.76.179.223
   Střed Indie | 52.172.187.37 | 104.211.98.185
   Indie – jih | 52.172.46.220 | 104.211.224.190
   Středoseverní USA | 23.96.195.247 | 168.62.249.226
   Severní Evropa | 40.69.212.238 | 52.169.18.8
   Západní Evropa | 52.166.13.64 | 40.68.93.145
   USA – východ | 13.82.88.226 | 104.45.147.24
   Západní USA | 40.83.179.48 | 104.40.26.199
   Středojižní USA | 13.84.148.14 | 104.210.146.250
   USA – střed | 40.69.144.231 | 52.165.34.144
   Východní USA 2 | 52.184.158.163 | 40.79.44.59
   Japonsko – východ | 52.185.150.140 | 138.91.1.105
   Japonsko – západ | 52.175.146.69 | 138.91.17.38
   Brazílie – jih | 191.234.185.172 | 23.97.97.36
   Austrálie – východ | 104.210.113.114 | 191.239.64.144
   Austrálie – jihovýchod | 13.70.159.158 | 191.239.160.45
   Kanada – střed | 52.228.36.192 | 40.85.226.62
   Kanada – východ | 52.229.125.98 | 40.86.225.142
   Západní střed USA | 52.161.20.168 | 13.78.149.209
   Západní USA 2 | 52.183.45.166 | 13.66.228.204
   Spojené království – západ | 51.141.3.203 | 51.141.14.113
   Velká Británie – jih | 51.140.43.158 | 51.140.189.52
   Velká Británie – jih 2 | 13.87.37.4| 13.87.34.139
   Velká Británie – sever | 51.142.209.167 | 13.87.102.68
   Jižní Korea – střed | 52.231.28.253 | 52.231.32.85
   Jižní Korea – jih | 52.231.198.185 | 52.231.200.144
   Francie – střed | 52.143.138.106 | 52.143.136.55
   Francie – jih | 52.136.139.227 |52.136.136.62
   Austrálie, střed| 20.36.34.70 | 20.36.46.142
   Austrálie – střed 2| 20.36.69.62 | 20.36.74.130
   Jižní Afrika – západ | 102.133.72.51 | 102.133.26.128
   Jižní Afrika – sever | 102.133.160.44 | 102.133.154.128
   USA (Gov) – Virginia | 52.227.178.114 | 23.97.0.197
   US Gov – Iowa | 13.72.184.23 | 23.97.16.186
   USA (Gov) – Arizona | 52.244.205.45 | 52.244.48.85
   USA (Gov) – Texas | 52.238.119.218 | 52.238.116.60
   US DoD – východ | 52.181.164.103 | 52.181.162.129
   US DoD – střed | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje postup při konfiguraci pravidla skupiny zabezpečení sítě pro virtuální počítač k replikaci.

- Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě, použijte "Povolit HTTPS odchozí" pravidla pro port: 443 pro všechny požadované rozsahy IP adres.
- Tento příklad předpokládá, že umístění zdrojového virtuálního počítače je "Východ USA" a cílové umístění je "USA".

### <a name="nsg-rules---east-us"></a>Pravidla skupiny zabezpečení sítě – USA – východ

1. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "Storage.EastUS" na skupiny zabezpečení sítě, jak je znázorněno v následujícím snímku obrazovky.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "AzureActiveDirectory" na skupiny zabezpečení sítě, jak je znázorněno v následujícím snímku obrazovky.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Vytvořte odchozí pravidla HTTPS (443) pro obnovení lokality IP adres, odpovídající cílové umístění:

   **Umístění** | **Site Recovery IP adresa** |  **Site Recovery monitorování IP adresa**
    --- | --- | ---
   USA – střed | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Pravidla skupiny zabezpečení sítě – USA (střed)

Tato pravidla jsou vyžadována proto, že je možné povolit replikaci z cílové oblasti zdrojové oblasti post-převzetí služeb při selhání:

1. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "Storage.CentralUS" na skupiny zabezpečení sítě.

2. Vytvořte odchozí pravidlo zabezpečení HTTPS (443) pro "AzureActiveDirectory" na skupiny zabezpečení sítě.

3. Vytvořte odchozí pravidla HTTPS (443) pro obnovení lokality IP adresy, které odpovídají umístění zdroje:

   **Umístění** | **Site Recovery IP adresa** |  **Site Recovery monitorování IP adresa**
    --- | --- | ---
   USA – střed | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Konfigurace síťového virtuálního zařízení

Pokud používáte síťových virtuálních zařízení (Nva) k řízení odchozího síťového provozu z virtuálních počítačů, zařízení může získat omezených Pokud všechny replikace provoz prochází přes síťové virtuální zařízení. Doporučujeme vytvořit koncový bod služby sítě ve vaší virtuální síti pro "Úložiště" tak, aby provoz replikace se nepřenáší na síťové virtuální zařízení.

### <a name="create-network-service-endpoint-for-storage"></a>Vytvoření koncového bodu služby sítě pro úložiště
Koncový bod služby sítě ve vaší virtuální síti můžete vytvořit pro "Úložiště" tak, aby provoz replikace, nenechává hranice Azure.

- Vyberte virtuální sítí Azure a klikněte na koncové body služby

    ![koncový bod úložiště](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Klikněte na tlačítko "Přidat" a 'Přidat koncové body služby' Otevře se karta
- Vyberte "Microsoft.Storage" v části "Služba" a požadované podsítě v rámci pole "Podsítě" a klikněte na tlačítko 'Přidat'

>[!NOTE]
>Účty úložiště používané pro Azure Site Recovery neomezují přístup k virtuální síti. Měli byste povolit přístup ".

### <a name="forced-tunneling"></a>Vynucené tunelování

Můžete přepsat Azure výchozí systémovou trasu pro předponu adresy 0.0.0.0/0 s [vlastní trasy](../virtual-network/virtual-networks-udr-overview.md#custom-routes) a přesměrovat provoz virtuálního počítače místní síťové virtuální zařízení (NVA), ale tato konfigurace se nedoporučuje pro Site Recovery replikace. Pokud používáte vlastní trasy, měli byste [vytvořit koncový bod služby virtuální sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální sítě pro "Úložiště" tak, aby provoz replikace, nenechává hranice Azure.

## <a name="next-steps"></a>Další postup
- Začněte chránit svoje úlohy pomocí [replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure.md).
- Další informace o [IP adresu uchování](site-recovery-retain-ip-azure-vm-failover.md) převzetí služeb při selhání virtuálních počítačů Azure.
- Další informace o zotavení po havárii [virtuálních počítačů Azure pomocí ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
