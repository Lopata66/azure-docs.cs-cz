---
title: Přehled značek plně kvalifikovaného názvu domény pro Azure Firewall
description: Značka plně kvalifikovaného názvu domény představuje skupinu plně kvalifikovaných názvů domén (FQDN) přidružených k dobře známým službám Microsoftu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/24/2020
ms.author: victorh
ms.openlocfilehash: f4fcadb6d930f7863c733dad105432a64127b59b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232645"
---
# <a name="fqdn-tags-overview"></a>Přehled značek plně kvalifikovaného názvu domény

Značka plně kvalifikovaného názvu domény představuje skupinu plně kvalifikovaných názvů domén (FQDN) přidružených k dobře známým službám Microsoftu. V pravidlech aplikací můžete použít značku plně kvalifikovaného názvu domény, která umožní požadovaný odchozí síťový provoz přes bránu firewall.

Chcete-li například ručně web Windows Update síťový provoz přes bránu firewall, je třeba vytvořit více pravidel aplikací pro každou dokumentaci společnosti Microsoft. Pomocí značek plně kvalifikovaného názvu domény můžete vytvořit pravidlo aplikace, zahrňte značku **aktualizací Windows** a teď můžete směrovat síťový provoz do Microsoft Web Windows Update koncových bodů přes bránu firewall.

Nemůžete vytvořit vlastní značky plně kvalifikovaného názvu domény, ani nemůžete určit, které plně kvalifikované názvy domény jsou zahrnuty v rámci značky. Společnost Microsoft spravuje plně kvalifikované názvy domén, které jsou obsaženy ve značce plně kvalifikovaného názvu domény, a aktualizuje značku jako plně kvalifikované názvy domén. 

<!--- screenshot of application rule with a FQDN tag.-->

Následující tabulka uvádí aktuální plně kvalifikované názvy domén, které můžete použít. Společnost Microsoft tyto značky udržuje a je možné očekávat, že další značky budou pravidelně přidávány.

## <a name="current-fqdn-tags"></a>Aktuální značky plně kvalifikovaného názvu domény

|FQDN – značka  |Popis  |
|---------|---------|
|Windows Update     |Povolte odchozí přístup k Microsoft Update, jak je popsáno v tématu [jak nakonfigurovat bránu firewall pro aktualizace softwaru](https://technet.microsoft.com/library/bb693717.aspx).|
|Program Diagnostika|Povolí odchozí přístup ke všem [koncovým bodům diagnostiky Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Služba Microsoft Active Protection Service (MAPS)|Povolí odchozí přístup ke [mapám](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (pomocného mechanismu)|Umožňuje odchozí přístup k provozu platformy pomocného mechanismu. Tato značka nepokrývá úložiště pro konkrétní zákazníky a koncové body SQL vytvořené pomocí pomocného mechanismu služby. Ty by měly být povolené prostřednictvím [koncových bodů služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo je přidat ručně.<br><br>Další informace o integraci Azure Firewall s pomocným mechanismem řízení přístupu najdete v tématu [uzamčení App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Povolí odchozí přístup ke službě Azure Backup Services.|
|Azure HDInsight|Povolí odchozí přístup pro přenosy platformy HDInsight. Tato značka nepokrývá úložiště pro konkrétní zákazníka ani data SQL ze služby HDInsight. Povolte je pomocí [koncových bodů služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo je přidejte ručně.|
|WindowsVirtualDesktop (WVD)|Umožňuje odchozí přenosy platformy virtuálních počítačů s Windows. Tato značka nepokrývá úložiště specifické pro nasazení a koncové body Service Bus vytvořené nástrojem WVD. Navíc se vyžadují pravidla sítě DNS a služby správy klíčů. Další informace o integraci Azure Firewall s WVD najdete v tématu [použití Azure firewall k ochraně nasazení virtuálních ploch](protect-windows-virtual-desktop.md)v systému Windows. 

> [!NOTE]
> Při výběru značky plně kvalifikovaného názvu domény v pravidle aplikace musí být pole protokol: port nastavené na **https**.

## <a name="next-steps"></a>Další kroky

Další informace o nasazení Azure Firewall najdete v tématu [kurz: nasazení a konfigurace Azure firewall použití Azure Portal](tutorial-firewall-deploy-portal.md).
