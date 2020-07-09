---
title: Řešení potíží s připojením pro zotavení po havárii Azure do Azure pomocí Azure Site Recovery
description: Řešení potíží s připojením v zotavení po havárii virtuálních počítačů Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 71176c87ee805eb4a634dd6c2f344922fc13c4f3
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86132712"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Řešení potíží s připojením k síti virtuálních počítačů z Azure do Azure

Tento článek popisuje běžné problémy související s připojením k síti při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti. Další informace o požadavcích na síť najdete v tématu [požadavky na připojení pro replikaci virtuálních počítačů Azure](azure-to-azure-about-networking.md).

Aby mohla replikace Site Recovery fungovat, z virtuálního počítače se vyžaduje odchozí připojení ke konkrétním adresám URL nebo rozsahům IP adres. Pokud je váš virtuální počítač za bránou firewall nebo používá pravidla skupiny zabezpečení sítě (NSG) k řízení odchozího připojení, můžete se setkat s jedním z těchto problémů.

| URL | Podrobnosti |
|---|---|
| `*.blob.core.windows.net` | Vyžaduje se, aby se data mohla zapsat do účtu úložiště mezipaměti ve zdrojové oblasti z virtuálního počítače. Pokud znáte všechny účty úložiště mezipaměti pro vaše virtuální počítače, můžete použít seznam povolených adres pro konkrétní adresy URL účtu úložiště. Například `cache1.blob.core.windows.net` a `cache2.blob.core.windows.net` místo `*.blob.core.windows.net` . |
| `login.microsoftonline.com` | Vyžaduje se pro autorizaci a ověřování adres URL služby Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Vyžaduje se, aby na virtuálním počítači mohla probíhat komunikace služby Site Recovery. Pokud proxy server firewall podporuje IP adresy, můžete použít odpovídající _Site Recovery IP adresu_ . |
| `*.servicebus.windows.net` | Požadováno, aby se z virtuálního počítače mohla zapisovat data monitorování Site Recovery a diagnostická data. Pokud proxy server firewall podporuje IP adresy, můžete použít odpovídající _IP adresu monitorování Site Recovery_ . |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Odchozí připojení pro adresy URL Site Recovery nebo rozsahy IP adres (kód chyby 151037 nebo 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problém 1: registrace virtuálního počítače Azure pomocí Site Recovery se nezdařila (151195)

#### <a name="possible-cause"></a>Možná příčina

Nelze navázat připojení k Site Recovery koncovým bodům z důvodu chyby rozlišení DNS (Domain Name System). Tento problém je častější během ochrany při převzetí služeb při selhání virtuálního počítače, ale server DNS není dosažitelný z oblasti zotavení po havárii (DR).

#### <a name="resolution"></a>Řešení

Pokud používáte vlastní server DNS, ujistěte se, že je server DNS přístupný z oblasti zotavení po havárii.

Pokud chcete zjistit, jestli virtuální počítač používá vlastní nastavení DNS:

1. Otevřete **virtuální počítače** a vyberte virtuální počítač.
1. Přejděte do **Nastavení** virtuálních počítačů a vyberte **sítě**.
1. V části **virtuální síť/podsíť**vyberte odkaz a otevřete stránku prostředku virtuální sítě.
1. Přejít na **Nastavení** a vyberte **servery DNS**.

Pokuste se získat přístup k serveru DNS z virtuálního počítače. Pokud server DNS není přístupný, zpřístupněte ho buď při selhání přes server DNS, nebo při vytváření sítě mezi sítí DR a DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com – chyba":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problém 2: Konfigurace Site Recovery se nezdařila (151196)

> [!NOTE]
> Pokud jsou virtuální počítače za **standardním** interním nástrojem pro vyrovnávání zatížení, má ve výchozím nastavení přístup k IP adresám Office 365, jako je například `login.microsoftonline.com` . Buď změňte na **základní** typ interního nástroje pro vyrovnávání zatížení, nebo vytvořte odchozí přístup, jak je uvedeno v článku [konfigurace vyrovnávání zatížení a odchozích pravidel v Standard Load Balancer pomocí](../load-balancer/configure-load-balancer-outbound-cli.md)rozhraní příkazového řádku Azure.

#### <a name="possible-cause"></a>Možná příčina

Nejde navázat připojení k ověřování Office 365 a koncovým bodům IP4 identity.

#### <a name="resolution"></a>Řešení

- Azure Site Recovery vyžaduje přístup k rozsahům IP adres Office 365 pro ověřování.
- Pokud k řízení odchozího připojení k síti na virtuálním počítači používáte pravidla skupiny zabezpečení sítě Azure (NSG) nebo proxy serveru brány firewall, ujistěte se, že jste povolili komunikaci s rozsahy IP adres Office 365. Azure Active Directory vytvořte pravidlo NSG založené na [značkách služby Azure AD](../virtual-network/security-overview.md#service-tags) , které umožňuje přístup ke všem IP adresám, které odpovídají službě Azure AD.
- Pokud v budoucnu přidáte do Azure AD nové adresy, budete muset vytvořit nová pravidla NSG.

### <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje, jak nakonfigurovat NSG pravidla pro replikaci virtuálního počítače.

- Pokud používáte pravidla NSG k řízení odchozího připojení, použijte možnost **Povolení odchozích pravidel https** pro všechny požadované rozsahy IP adres portu 443.
- V příkladu se předpokládá, že umístění zdroje virtuálního počítače je **východní USA** a cílové umístění je **střed USA**.

#### <a name="nsg-rules---east-us"></a>Pravidla NSG – Východní USA

1. Vytvořte odchozí pravidlo zabezpečení HTTPS pro NSG, jak je znázorněno na následujícím snímku obrazovky. Tento příklad používá **cílovou značku služby**: _Storage. EastUS_ a **rozsahy cílových portů**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="úložiště – značka":::

1. Vytvořte odchozí pravidlo zabezpečení HTTPS pro NSG, jak je znázorněno na následujícím snímku obrazovky. V tomto příkladu se používá **označení cílové služby**: rozsahy _azureactivedirectory selhala_ a **cílové porty**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="AAD – značka":::

1. Vytvořte odchozí pravidla portu HTTPS 443 pro IP adresy Site Recovery, které odpovídají cílovému umístění:

   | Umístění | Site Recovery IP adresa | IP adresa monitorování Site Recovery |
   | --- | --- | --- |
   | USA – střed | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>Pravidla NSG – Střed USA

V tomto příkladu jsou potřeba tato pravidla NSG, aby bylo možné replikaci z cílové oblasti do zdrojové oblasti po převzetí služeb při selhání povolit.

1. Vytvořte odchozí pravidlo zabezpečení HTTPS pro _Storage. CentralUS_:

   - **Značka cílové služby**: _Storage. CentralUS_
   - **Rozsahy cílových portů**: _443_

1. Vytvořte odchozí pravidlo zabezpečení HTTPS pro _azureactivedirectory selhala_.

   - **Značka cílové služby**: _azureactivedirectory selhala_
   - **Rozsahy cílových portů**: _443_

1. Vytvořte odchozí pravidla portu HTTPS 443 pro IP adresy Site Recovery, které odpovídají zdrojovému umístění:

   | Umístění | Site Recovery IP adresa | IP adresa monitorování Site Recovery |
   | --- | --- | --- |
   | USA – východ | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problém 3: Konfigurace Site Recovery se nezdařila (151197)

#### <a name="possible-cause"></a>Možná příčina

Nepovedlo se navázat připojení k Azure Site Recovery koncovým bodům služby.

#### <a name="resolution"></a>Řešení

Azure Site Recovery vyžaduje přístup k [rozsahům IP adres služby Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) v závislosti na konkrétní oblasti. Ujistěte se, že virtuální počítač má přístup k požadovaným rozsahům IP adres.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problém 4: replikace z Azure do Azure se nezdařila, když síťový provoz projde místními proxy server (151072)

#### <a name="possible-cause"></a>Možná příčina

Vlastní nastavení proxy serveru je neplatné a agent služby Azure Site Recovery mobility automaticky nerozpoznal nastavení proxy serveru z Internet Exploreru (IE).

#### <a name="resolution"></a>Řešení

1. Agent služby mobility detekuje nastavení proxy serveru z IE ve Windows a `/etc/environment` v systému Linux.
1. Pokud dáváte přednost nastavení proxy serveru jenom pro Azure Site Recovery služby mobility, můžete zadat podrobnosti o proxy serveru v souboru _ProxyInfo. conf_ , který najdete na adrese:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ by měl mít nastavení proxy v následujícím formátu _ini_ :

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent služby mobility Azure Site Recovery podporuje jenom **neověřené proxy servery**.

### <a name="fix-the-problem"></a>Oprava problému

Pokud chcete povolené [adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) nebo požadované [rozsahy IP](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)adres, postupujte podle kroků v [dokumentu s pokyny k síti](./azure-to-azure-about-networking.md).

## <a name="next-steps"></a>Další kroky

[Replikace virtuálních počítačů Azure do jiné oblasti Azure](azure-to-azure-how-to-enable-replication.md)
