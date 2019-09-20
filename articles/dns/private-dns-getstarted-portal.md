---
title: Rychlý Start – vytvoření privátní zóny DNS Azure pomocí Azure Portal
description: V tomto rychlém startu vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Toto je podrobný Průvodce vytvořením a správou první privátní zóny DNS a záznamu pomocí Azure Portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 1f13a56941a137397fbb849093feaeb19b897131
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71156251"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Rychlý start: Vytvoření privátní zóny DNS Azure pomocí Azure Portal

Tento rychlý Start vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí Azure Portal.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Ty se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS aktualizuje také záznamy zón při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

V tomto rychlém startu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření virtuální sítě
> * Propojit virtuální síť
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud budete chtít, můžete tento rychlý Start dokončit pomocí [Azure PowerShell](private-dns-getstarted-powershell.md) nebo [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Následující příklad vytvoří zónu DNS s názvem **Private.contoso.com** ve skupině prostředků s názvem **MyAzureResourceGroup**.

Zóna DNS obsahuje záznamy DNS pro doménu. Pokud chcete začít hostovat vaši doménu v Azure DNS, vytvořte pro tento název domény zónu DNS.

![Hledání zón Privátní DNS](media/private-dns-portal/search-private-dns.png)

1. Na panelu hledání na portálu zadejte do textového pole Hledat **privátní zóny DNS** a stiskněte klávesu **ENTER**.
1. Vyberte **zónu privátní DNS**.
2. Vyberte **vytvořit privátní zónu DNS**.

1. Na stránce **vytvořit privátní DNS zónu** zadejte nebo vyberte následující hodnoty:

   - **Skupina prostředků**: Vyberte **vytvořit novou**, zadejte *MyAzureResourceGroup*a vyberte **OK**. Název skupiny prostředků musí být v rámci předplatného Azure jedinečný. 
   -  **Název**: V tomto příkladu zadejte *Private.contoso.com* .
1. V případě **umístění skupiny prostředků**vyberte **středozápadní USA**.

1. Vyberte **zkontrolovat + vytvořit**.

1. Vyberte **Vytvořit**.

Vytvoření zóny může trvat několik minut.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na stránce portálu nahoře vlevo vyberte **vytvořit prostředek**, pak **síť**a pak vyberte **virtuální síť**.
2. Jako **název**zadejte **myAzureVNet**.
3. V případě **skupiny prostředků**vyberte **MyAzureResourceGroup**.
4. V **oblasti umístění**vyberte **středozápadní USA**.
5. Přijměte ostatní výchozí hodnoty a vyberte **vytvořit**.

## <a name="link-the-virtual-network"></a>Propojit virtuální síť

Pokud chcete propojit privátní zónu DNS s virtuální sítí, vytvořte odkaz na virtuální síť.

![Přidat propojení virtuální sítě](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otevřete skupinu prostředků **MyAzureResourceGroup** a vyberte privátní zónu **Private.contoso.com** .
2. V levém podokně vyberte **odkazy virtuální sítě**.
3. Vyberte **Přidat**.
4. Jako **název odkazu**zadejte **myLink** .
5. V případě **virtuální sítě**vyberte **myAzureVNet**.
6. Zaškrtněte políčko **Povolit automatickou registraci** .
7. Vyberte **OK**.

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

1. Na stránce portálu nahoře vlevo vyberte **vytvořit prostředek**a pak vyberte **Windows Server 2016 Datacenter**.
1. Jako skupinu prostředků vyberte **MyAzureResourceGroup** .
1. Jako název virtuálního počítače zadejte **myVM01** .
1. Pro **oblast**vyberte **středozápadní USA** .
1. Jako uživatelské jméno správce zadejte **azureadmin** .
2. Zadejte **Azure12345678** pro heslo a potvrďte heslo.

5. U **veřejných příchozích portů**vyberte **Povolit vybrané porty**a pak pro **vybrat příchozí porty**vybrat **RDP (3389)** .
10. Přijměte ostatní výchozí hodnoty stránky a potom klikněte na **další: Disky >** .
11. Na stránce **disky** přijměte výchozí hodnoty a pak klikněte **na další: >** Sítě.
1. Ujistěte se, že pro virtuální síť je vybraná možnost **myAzureVNet** .
1. Přijměte ostatní výchozí hodnoty stránky a potom klikněte na tlačítko **další: >** Správy.
2. Pro **diagnostiku spouštění**vyberte **vypnuto**, Přijměte ostatní výchozí nastavení a pak vyberte **zkontrolovat + vytvořit**.
1. Zkontrolujte nastavení a klikněte na **vytvořit**.

Opakujte tyto kroky a vytvořte další virtuální počítač s názvem **myVM02**.

Dokončení obou virtuálních počítačů bude trvat několik minut.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

 Následující příklad vytvoří záznam s relativní názvem **DB** v zóně DNS **Private.contoso.com**ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **DB.Private.contoso.com**. Typ záznamu je A s IP adresou **myVM01**.

1. Otevřete skupinu prostředků **MyAzureResourceGroup** a vyberte privátní zónu **Private.contoso.com** .
2. Vyberte **+ Sada záznamů**.
3. Jako **název**zadejte **DB**.
4. Do pole **IP adresa**zadejte IP adresu, kterou vidíte pro **myVM01**. Tato volba by měla být automaticky registrována při spuštění virtuálního počítače.
5. Vyberte **OK**.

## <a name="test-the-private-zone"></a>Testování privátní zóny

Teď můžete testovat překlad IP adres pro privátní zónu **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozích přenosů ICMP

Překlad adres můžete otestovat pomocí příkazu ping. Za tím účelem nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety ICMP.

1. Připojte se k počítači myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Totéž zopakujte pro virtuální počítač myVM02.

### <a name="ping-the-vms-by-name"></a>Odeslání příkazu ping na virtuální počítače podle názvu

1. Z příkazového řádku ve Windows PowerShellu virtuálního počítače myVM02 odešlete příkaz ping do virtuálního počítače myVM01 a použijte v něm automaticky zaregistrovaný název hostitele:
   ```
   ping myVM01.private.contoso.com
   ```
   Zobrazený výstup by měl vypadat zhruba takto:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Teď odešlete příkaz ping na název **db**, který jste předtím vytvořili:
   ```
   ping db.private.contoso.com
   ```
   Zobrazený výstup by měl vypadat zhruba takto:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v rámci tohoto rychlého startu.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scénáře](private-dns-scenarios.md)

