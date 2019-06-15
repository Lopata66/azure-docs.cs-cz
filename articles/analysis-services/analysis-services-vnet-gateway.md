---
title: Použití na místní bránu dat pro zdroje dat Azure Virtual Network | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat server pro použití brány pro zdroje dat ve virtuální síti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7e97bd50e3d37218e0f88f722387fd1a53167e27
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60534057"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Použití brány pro zdroje dat ve službě Azure Virtual Network (VNet)

Tento článek popisuje **AlwaysUseGateway** vlastnost serveru k použití pro zdroje dat jsou na [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Přístup k virtuální síti zdroje dat serveru

Pokud jsou vaše zdroje dat přístupné prostřednictvím virtuální sítě, musí váš server Azure Analysis Services připojit k příslušným zdrojům dat, jako by šlo místně, ve vašem prostředí. Můžete nakonfigurovat **AlwaysUseGateway** server vlastnosti a určit server pro přístup k datům všechny zdroje dat pomocí [místní brána](analysis-services-gateway.md). 

> [!NOTE]
> Tato vlastnost je účinné pouze tehdy, když [On-premises data gateway](analysis-services-gateway.md) nainstalovaná a nakonfigurovaná. Brána lze ve virtuální síti.

## <a name="configure-alwaysusegateway-property"></a>Konfiguraci AlwaysUseGateway vlastnosti

1. V aplikaci SSMS > server > **vlastnosti** > **Obecné**vyberte **(vše) zobrazit rozšířené vlastnosti**.
2. V **ASPaaS\AlwaysUseGateway**vyberte **true**.

    ![Vždy používejte vlastnost brány](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Další informace najdete v tématech
[Připojení k místním zdrojům dat](analysis-services-gateway.md)   
[Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

