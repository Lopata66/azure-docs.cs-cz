---
title: Konfigurace pravidel brány firewall protokolu IP v Azure synapse Analytics
description: Článek, který vás učí, abyste nakonfigurovali pravidla brány firewall protokolu IP v Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 1dfac9a56e3bc299fbb0651ae1cd5644eba83267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83645819"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Pravidla brány firewall protokolu IP pro Azure synapse Analytics (Preview)

Tento článek vysvětluje pravidla brány firewall protokolu IP a naučíte se, jak je nakonfigurovat ve službě Azure synapse Analytics.

## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP

Pravidla brány firewall protokolu IP udělují nebo zamítají přístup k vašemu pracovnímu prostoru synapse na základě zdrojové IP adresy jednotlivých požadavků. Pro svůj pracovní prostor můžete nakonfigurovat pravidla brány firewall protokolu IP. Pravidla brány firewall protokolu IP nakonfigurovaná na úrovni pracovního prostoru se vztahují na všechny veřejné koncové body pracovního prostoru (fondy SQL, SQL na vyžádání a vývoj).

## <a name="create-and-manage-ip-firewall-rules"></a>Vytvoření a Správa pravidel brány firewall protokolu IP

Existují dva způsoby, jak pravidla brány firewall protokolu IP přidat do pracovního prostoru synapse. Pokud chcete do svého pracovního prostoru přidat bránu firewall protokolu IP, vyberte **zabezpečení + sítě** a zaškrtněte políčko během vytváření pracovního prostoru vytvořit **připojení ze všech IP adres** .

![Azure Portal konfiguraci IP adres pracovního prostoru synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure Portal konfiguraci IP adres pracovního prostoru synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Po vytvoření pracovního prostoru můžete také přidat pravidla firewallu protokolu IP do pracovního prostoru synapse. V části **zabezpečení** z Azure Portal vyberte **brány firewall** . Pokud chcete přidat nové pravidlo brány firewall protokolu IP, zadejte jeho název, počáteční IP adresu a koncovou IP adresu. Po dokončení vyberte **Uložit**.

![Konfigurace IP adres pracovních prostorů Azure synapse v Azure Portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Připojení k synapse z vlastní sítě

K pracovnímu prostoru synapse se můžete připojit pomocí synapse studia. Můžete také použít SQL Server Management Studio (SSMS) pro připojení k prostředkům SQL (fondy SQL a SQL na vyžádání) ve vašem pracovním prostoru.

Ujistěte se, že brána firewall v síti a místní počítač umožňují odchozí komunikaci na portech TCP 80, 443 a 1443 pro synapse Studio.

Také je potřeba pro synapse Studio zapnout odchozí komunikaci na portu UDP 53. Pokud se chcete připojit pomocí nástrojů, jako je SSMS a Power BI, musíte na portu TCP 1433 zapnout odchozí komunikaci.

Pokud používáte výchozí nastavení zásad připojení pro přesměrování, možná budete muset pro další porty zapnout odchozí komunikaci. Další informace o zásadách připojení najdete [tady](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy).

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure synapse](../quickstart-create-workspace.md)

Vytvoření pracovního prostoru Azure synapse s [virtuální sítí spravovaného pracovního prostoru](./synapse-workspace-managed-vnet.md)