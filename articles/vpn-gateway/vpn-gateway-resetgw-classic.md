---
title: Resetování služby Azure VPN Gateway pro opětovné vytvoření tunelu IPsec
description: Tento článek vás provede resetováním VPN Gateway Azure, aby se znovu navázaly tunely IPsec. Tento článek se týká bran VPN v modelu nasazení Classic i Správce prostředků.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: 90a01e4ee3ddf7b100ef51e55b9917c709a05864
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84982879"
---
# <a name="reset-a-vpn-gateway"></a>Resetování brány VPN Gateway

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Tento článek vám pomůže resetovat bránu VPN.

### <a name="what-happens-during-a-reset"></a>Co se stane během resetování?

Brána sítě VPN se skládá ze dvou instancí virtuálních počítačů spuštěných v konfiguraci s aktivním pohotovostním režimem. Když resetujete bránu, restartuje bránu a pak na ni znovu použije konfigurace pro více míst. Brána si ponechá stejnou veřejnou IP adresu. To znamená, že nebudete muset aktualizovat konfiguraci směrovače VPN na novou veřejnou IP adresu brány Azure VPN.

Při vystavení příkazu k resetování brány se okamžitě spustí aktuální aktivní instance služby Azure VPN Gateway. V případě převzetí služeb při selhání z aktivní instance (restartování) bude v pohotovostním stavu k dispozici krátká mezera. Prodleva by neměla být delší než jedna minuta.

Pokud po prvním resetování nedojde k obnovení připojení, znovu vydejte stejný příkaz pro resetování druhé instance virtuálního počítače (nové aktivní brány). Pokud se vyžadují dvě restartování za sebou, bude doba, po kterou se obě instance virtuálního počítače (aktivní a pohotovostní) restartují, trochu delší. To způsobí delší dobu připojení VPN, maximálně 30 až 45 minut, než virtuální počítače dokončí restartování.

Pokud stále dochází k problémům s připojením mezi různými místy, otevřete prosím žádost o podporu z Azure Portal po dvou restartováních.

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Před restartováním brány ověřte pro každé tunelové propojení VPN typu Site-to-Site (S2S) s protokolem IPsec klíčové body následujícího seznamu. Jakákoli neshoda v těchto bodech způsobí odpojení tunelových propojení VPN typu S2S. Ověření a opravování konfigurací místních a Azure VPN Gateway vám ušetříte z zbytečných restartování a přerušení pro ostatní funkční připojení v bráně.

Před resetováním brány ověřte následující položky:

* Internetové IP adresy (virtuální IP adresy) pro bránu Azure VPN a bránu místní VPN jsou správně nakonfigurovány v zásadách Azure VPN i v zásadách místní VPN.
* Předsdílený klíč musí být stejný v bráně Azure VPN i v bráně místní VPN.
* Pokud použijete určitou konfiguraci protokolu IPsec/IKE, jako například šifrování, algoritmy hash nebo metodu Perfect Forward Secrecy (PFS), ujistěte se, že je stejně nakonfigurovaná brána Azure VPN i brána místní sítě.

## <a name="azure-portal"></a><a name="portal"></a>portál Azure

Správce prostředků VPN Gateway můžete resetovat pomocí Azure Portal. Pokud chcete resetovat klasickou bránu, přečtěte si postup [PowerShellu](#resetclassic) .

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

1. Otevřete [Azure Portal](https://portal.azure.com) a přejděte do brány správce prostředků virtuální sítě, kterou chcete resetovat.
2. V okně pro bránu virtuální sítě klikněte na resetovat.

   ![Resetovat okno VPN Gateway](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. V okně Reset klikněte na tlačítko **obnovit** .

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rutina pro **resetování brány je resetována – AzVirtualNetworkGateway**. Před provedením resetu se ujistěte, že máte nejnovější verzi [PowerShellu AZ rutins](https://docs.microsoft.com/powershell/module/az.network). Následující příklad obnoví bránu virtuální sítě s názvem VNet1GW ve skupině prostředků TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Result:

Když obdržíte výsledek vrácení, můžete předpokládat, že resetování brány bylo úspěšné. Nejedná se však o výsledek návratového výsledku, který indikuje, že bylo resetování úspěšné. Pokud se chcete podívat na historii, abyste viděli přesně, kdy došlo k obnovení brány, můžete tyto informace zobrazit v [Azure Portal](https://portal.azure.com). Na portálu přejděte na **"Gateway"-> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Model nasazení Classic

Rutina pro **resetování brány je resetována – AzureVNetGateway**. Rutiny Azure PowerShell pro správu služeb musí být nainstalované místně na vašem počítači. Nemůžete použít Azure Cloud Shell. Před provedením resetu se ujistěte, že máte nejnovější verzi [rutin PowerShellu pro správu služeb (SM)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Při použití tohoto příkazu se ujistěte, že používáte úplný název virtuální sítě. Klasické virtuální sítě vytvořené pomocí portálu mají dlouhý název, který se vyžaduje pro PowerShell. Dlouhý název můžete zobrazit pomocí příkazu Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml.

Následující příklad obnoví bránu pro virtuální síť s názvem "Group TestRG1 virtuální sítě testvnet1" (která na portálu představuje jednoduše "virtuální sítě testvnet1"):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Result:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Bránu resetujete tak, že použijete příkaz [AZ Network VNet-Gateway Reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) . Následující příklad obnoví bránu virtuální sítě s názvem VNet5GW ve skupině prostředků TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Result:

Když obdržíte výsledek vrácení, můžete předpokládat, že resetování brány bylo úspěšné. Nejedná se však o výsledek návratového výsledku, který indikuje, že bylo resetování úspěšné. Pokud se chcete podívat na historii, abyste viděli přesně, kdy došlo k obnovení brány, můžete tyto informace zobrazit v [Azure Portal](https://portal.azure.com). Na portálu přejděte na **"Gateway"-> Resource Health**.
