---
title: Konfigurace sítě VPN typu Site-to-Site (S2S) pro použití se soubory Azure | Microsoft Docs
description: Jak nakonfigurovat síť VPN typu Site-to-Site (S2S) pro použití se soubory Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7762366f68bee2cd8c44e81bb22366c504ff1a73
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484423"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurace sítě VPN typu Site-to-site pro použití se soubory Azure
Připojení VPN typu Site-to-Site (S2S) můžete použít k připojení sdílených složek Azure přes protokol SMB z místní sítě bez nutnosti otevření portu 445. SÍŤ VPN typu Site-to-site můžete nastavit pomocí [VPN Gateway Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md), což je prostředek Azure, který nabízí služby VPN a který je nasazený ve skupině prostředků spolu s účty úložiště nebo jinými prostředky Azure.

![Graf topologie ilustrující topologii brány Azure VPN, která připojuje sdílenou složku Azure k místní lokalitě pomocí S2S VPN](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Důrazně doporučujeme, abyste si přečetli [Přehled sítě Azure Files](storage-files-networking-overview.md) , než budete pokračovat v tomto článku, abyste mohli získat úplnou diskusi o možnostech sítě dostupných pro soubory Azure.

Tento článek podrobně popisuje kroky pro konfiguraci sítě VPN typu Site-to-site pro připojení sdílených složek Azure přímo v místním prostředí. Pokud chcete směrovat provoz synchronizace pro Azure File Sync přes síť Site-to-Site VPN, přečtěte si téma [Konfigurace nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Požadavky
- Sdílená složka Azure, kterou byste chtěli místně připojit. Pomocí sítě VPN typu Site-to-site můžete použít buď službu Azure [Standard](storage-how-to-create-file-share.md) , nebo [prémiovou sdílenou složku Azure](storage-how-to-create-premium-fileshare.md) .

- Síťové zařízení nebo server v místním datovém centru, které je kompatibilní s Azure VPN Gateway. Soubory Azure jsou nezávislá z místního síťového zařízení, ale Azure VPN Gateway udržuje [seznam testovaných zařízení](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Různá síťová zařízení nabízejí různé funkce, charakteristiky výkonu a funkce správy, proto je zvažte při výběru síťového zařízení.

    Pokud nemáte stávající síťové zařízení, Windows Server obsahuje integrovanou roli serveru, službu Směrování a vzdálený přístup (RRAS), která se dá použít jako místní síťové zařízení. Další informace o tom, jak nakonfigurovat Směrování a vzdálený přístup v systému Windows Server, najdete v tématu [Brána služby RAS](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Přidat účet úložiště do virtuální sítě
V Azure Portal přejděte k účtu úložiště, který obsahuje sdílenou složku Azure, kterou chcete místně připojit. V obsahu účtu úložiště vyberte položku **brány firewall a virtuální sítě** . Pokud jste při vytváření účtu úložiště nepřidali virtuální síť, musí mít výsledné podokno pro **všechny vybrané sítě** přepínač **Povolení přístupu z** .

Pokud chcete přidat svůj účet úložiště do požadované virtuální sítě, vyberte **vybrané sítě**. V části **virtuální sítě** klikněte buď na **+ Přidat existující virtuální síť** , nebo **přidejte novou virtuální síť** v závislosti na požadovaném stavu. Vytvoření nové virtuální sítě bude mít za následek vytvoření nového prostředku Azure. Nový nebo existující prostředek virtuální sítě se nemusí nacházet ve stejné skupině prostředků nebo předplatném jako účet úložiště, ale musí být ve stejné oblasti jako účet úložiště a skupina prostředků a předplatné, ve kterém virtuální síť nasazujete, musí odpovídat tomu, který budete používat.  Nasaďte VPN Gateway do. 

![Snímek obrazovky Azure Portal, který poskytuje možnost Přidat existující nebo novou virtuální síť k účtu úložiště](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Pokud přidáte existující virtuální síť, zobrazí se výzva k výběru jedné nebo více podsítí této virtuální sítě, do které se má účet úložiště přidat. Pokud vyberete novou virtuální síť, vytvoříte v rámci vytváření virtuální sítě podsíť a později ji můžete přidat prostřednictvím výsledného prostředku Azure pro virtuální síť.

Pokud jste ještě nepřidali účet úložiště do předplatného, bude nutné do virtuální sítě přidat koncový bod služby Microsoft. Storage. To může nějakou dobu trvat a až do dokončení této operace nebudete mít přístup ke sdíleným složkám Azure v rámci tohoto účtu úložiště, včetně prostřednictvím připojení VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Nasazení VPN Gateway Azure
V obsahu pro Azure Portal vyberte **vytvořit nový prostředek** a vyhledejte *bránu virtuální sítě*. Brána virtuální sítě musí být ve stejném předplatném, oblasti Azure a skupině prostředků jako virtuální síť, kterou jste nasadili v předchozím kroku (Všimněte si, že skupina prostředků se automaticky vybere při vyzvednutí virtuální sítě). 

Pro účely nasazení VPN Gateway Azure je nutné vyplnit následující pole:

- **Name (název**): název prostředku Azure pro VPN Gateway. Může se jednat o libovolný název, který najdete v případě správy.
- **Oblast**: oblast, do které bude VPN Gateway nasazena.
- **Typ brány**: pro účely nasazení sítě VPN typu Site-to-site musíte vybrat **VPN**.
- **Typ sítě VPN**: v závislosti na vašem zařízení VPN můžete zvolit buď na základě *trasy**, nebo **na základě zásad** . Sítě VPN založené na směrování podporují IKEv2, zatímco sítě VPN založené na zásadách podporují jenom IKEv1. Další informace o těchto dvou typech bran sítě VPN najdete v tématu [informace o bránách sítě VPN založených na zásadách a směrování](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about) .
- **SKU**: SKU řídí počet povolených tunelů typu Site-to-site a požadovaný výkon sítě VPN. Pokud chcete vybrat odpovídající SKLADOVOU položku pro váš případ použití, Projděte si seznam [SKU brány](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) . SKU VPN Gateway může v případě potřeby změnit později.
- **Virtuální síť**: virtuální síť, kterou jste vytvořili v předchozím kroku.
- **Veřejná IP adresa**: ip adresa VPN Gateway, která bude zpřístupněna pro Internet. Je možné, že budete muset vytvořit novou IP adresu, ale pokud to bude vhodné, můžete také použít stávající nepoužitou IP adresu. Pokud se rozhodnete **vytvořit nový**, vytvoří se nová IP adresa prostředek Azure ve stejné skupině prostředků jako VPN Gateway a **název veřejné IP adresy** bude název nově vytvořené IP adresy. Pokud vyberete možnost **použít existující**, musíte vybrat stávající nepoužitou IP adresu.
- **Povolit režim aktivní – aktivní**: Pokud vytváříte konfiguraci brány aktivní – aktivní, vyberte jenom **povoleno** . v opačném případě nechte zaškrtnuté políčko **zakázáno** . Další informace o režimu aktivní-aktivní najdete v tématu [vysoce dostupné možnosti připojení mezi různými místy a VNET-to-VNet](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Konfigurovat ASN protokolu BGP**: vyberte jenom **Povolit** , pokud konfigurace konkrétně vyžaduje toto nastavení. Další informace o tomto nastavení najdete v tématu [o protokolu BGP s Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Vyberte možnost **zkontrolovat + vytvořit** a vytvořte VPN Gateway. Úplné vytvoření a nasazení VPN Gateway může trvat až 45 minut.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Vytvoření brány místní sítě pro místní bránu 
Brána místní sítě je prostředek Azure, který představuje vaše místní síťové zařízení. V obsahu pro Azure Portal vyberte **vytvořit nový prostředek** a vyhledejte *bránu místní sítě*. Brána místní sítě je prostředek Azure, který se nasadí společně s vaším účtem úložiště, virtuální sítí a VPN Gateway, ale nemusí být ve stejné skupině prostředků nebo předplatném jako účet úložiště. 

Pro účely nasazení prostředku brány místní sítě musíte vyplnit následující pole:

- **Name (název**): název prostředku Azure pro bránu místní sítě. Může se jednat o libovolný název, který najdete v případě správy.
- **IP adresa**: veřejná IP adresa místní brány.
- **Adresní prostor**: rozsahy adres pro síť, kterou tato brána místní sítě představuje. Můžete přidat více rozsahů adresních prostorů, ale ujistěte se, že rozsahy, které zadáte, se nepřekrývají s rozsahy jiných sítí, ke kterým se chcete připojit. 
- **Konfigurovat nastavení protokolu BGP**: Pokud vaše konfigurace vyžaduje toto nastavení, nakonfigurujte jenom nastavení protokolu BGP. Další informace o tomto nastavení najdete v tématu [o protokolu BGP s Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Předplatné**: požadované předplatné. Nemusí se shodovat s předplatným použitým pro VPN Gateway nebo účet úložiště.
- **Skupina prostředků**: požadovaná skupina prostředků. Nemusí se shodovat se skupinou prostředků použitou pro VPN Gateway nebo účet úložiště.
- **Umístění**: oblast Azure, ve které se má prostředek brány místní sítě vytvořit. Tato možnost by se měla shodovat s oblastí, kterou jste vybrali pro VPN Gateway a účet úložiště.

Vyberte **vytvořit** k vytvoření prostředku brány místní sítě.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurace místního síťového zařízení
Konkrétní postup konfigurace místního síťového zařízení závisí na síťovém zařízení, které vaše organizace vybrala. V závislosti na zařízení, které vaše organizace zvolilo, může být v [seznamu testovaných zařízení](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) odkaz na pokyny dodavatele zařízení pro konfiguraci pomocí VPN Gateway Azure.

## <a name="create-private-endpoint-preview"></a>Vytvoření privátního koncového bodu (Preview)
Vytvoření privátního koncového bodu pro váš účet úložiště poskytuje účtu úložiště IP adresu v adresním prostoru IP adres vaší virtuální sítě. Když připojíte sdílenou složku Azure z místní sítě pomocí této privátní IP adresy, pravidla směrování automaticky definovaná při instalaci sítě VPN směrují vaši žádost o připojení k účtu úložiště přes síť VPN. 

V okně účet úložiště vyberte **připojení privátního koncového bodu** v obsahu na levé straně a **+ soukromý koncový bod** pro vytvoření nového privátního koncového bodu. Výsledný Průvodce má několik stránek, které je potřeba provést:

![Snímek obrazovky s oddílem základy oddílu Vytvoření privátního koncového bodu](media/storage-files-configure-s2s-vpn/create-private-endpoint-1.png)

Na kartě **základy** vyberte požadovanou skupinu prostředků, název a oblast pro váš soukromý koncový bod. To může být cokoli, co potřebujete, ale nemusí se shodovat s účtem úložiště, i když musíte vytvořit privátní koncový bod ve stejné oblasti jako virtuální síť, ve které chcete vytvořit privátní koncový bod.

Na kartě **prostředek** vyberte přepínač pro **připojení k prostředku Azure ve složce Můj adresář**. V části **typ prostředku**vyberte pro typ prostředku možnost **Microsoft. Storage/storageAccounts** . Pole **prostředek** je účet úložiště se sdílenou složkou Azure, ke které se chcete připojit. Cílový dílčí prostředek je **soubor**, protože se jedná o soubory Azure.

Karta **Konfigurace** umožňuje vybrat konkrétní virtuální síť a podsíť, do které chcete přidat privátní koncový bod. Vyberte virtuální síť, kterou jste vytvořili výše. Musíte vybrat odlišnou podsíť z podsítě, do které jste přidali koncový bod služby.

Karta **Konfigurace** také umožňuje nastavit privátní zónu DNS. Tato možnost není nutná, ale umožňuje použít místo cesty UNC cestu UNC (například `\\mystorageaccount.privatelink.file.core.windows.net\myshare`), a to v případě, že se má IP adresa připojit ke sdílené složce Azure. Můžete to udělat i s vašimi vlastními servery DNS v rámci vaší virtuální sítě.

Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte privátní koncový bod. Po vytvoření privátního koncového bodu se zobrazí dva nové prostředky: prostředek privátního koncového bodu a spárované virtuální síťové rozhraní. Prostředek rozhraní virtuální sítě bude mít vyhrazenou privátní IP adresu účtu úložiště. 

## <a name="create-the-site-to-site-connection"></a>Vytvoření připojení Site-to-site
K dokončení nasazení S2S VPN je nutné vytvořit připojení mezi místním síťovým zařízením (reprezentovaným prostředkem místní síťové brány) a VPN Gateway. Provedete to tak, že přejdete na VPN Gateway, který jste vytvořili výše. V obsahu pro VPN Gateway vyberte možnost **připojení**a klikněte na tlačítko **Přidat**. Výsledné podokno **Přidat připojení** vyžaduje následující pole:

- **Name**(název): název připojení. VPN Gateway může hostovat několik připojení, proto si vyberte název, který je vhodný pro vaši správu, která bude toto konkrétní připojení rozlišovat.
- **Typ připojení**: vzhledem k tomu, že se jedná o připojení S2S, vyberte v rozevíracím seznamu možnost **site-to-Site (IPSec)** .
- **Brána virtuální sítě**: Toto pole se automaticky vybere VPN Gateway, ke kterému se připojujete, a nedá se změnit.
- **Brána místní sítě**: Jedná se o bránu místní sítě, kterou chcete připojit k vašemu VPN Gateway. Výsledné podokno výběru by mělo mít název brány místní sítě, kterou jste vytvořili výše.
- **Shared Key (PSK)** : kombinace písmen a číslic, která se používá k navázání šifrování pro připojení. Stejný sdílený klíč musí být použit jak v bráně virtuální sítě, tak v bráně místní sítě. Pokud vaše zařízení brány žádné neposkytuje, můžete si ho udělat tady a poskytnout zařízení.

Vyberte **OK** a vytvořte připojení. Pomocí stránky **připojení** můžete ověřit, že se připojení úspěšně provedlo.

## <a name="mount-azure-file-share"></a>Připojení sdílené složky Azure 
Posledním krokem při konfiguraci S2S VPN je ověření, že funguje pro soubory Azure. To můžete provést tak, že svou sdílenou složku Azure nasdílíte do místního prostředí s vaším preferovaným operačním systémem. Pokyny k připojení pomocí operačního systému najdete tady:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Viz také
- [Přehled sítě Azure Files](storage-files-networking-overview.md)
- [Konfigurace sítě VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurace sítě VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md)
