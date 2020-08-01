---
title: Podporované oblasti pro propojený pracovní prostor Log Analytics
description: Tento článek popisuje podporovaná mapování oblastí mezi účtem služby Automation a pracovním prostorem Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/12/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: 4e5cad25c80661f9e707f545929e6ffcb00a1e42
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447854"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Podporované oblasti pro propojený pracovní prostor Log Analytics

V Azure Automation můžete pro své virtuální počítače povolit Update Management, Change Tracking a inventář a Start/Stop VMs during off-hours funkce. Propojení Log Analyticsho pracovního prostoru a účtu Automation v rámci předplatného ale podporuje jenom některé oblasti. Mapování oblastí platí pouze pro účet Automation a pracovní prostor Log Analytics. Pracovní prostor Log Analytics a účet Automation musí být ve stejném předplatném, ale můžou být v různých skupinách prostředků nasazených do stejné oblasti. Další informace najdete v tématu [Log Analytics pracovní prostor a účet Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Podporovaná mapování

V následující tabulce jsou uvedena podporovaná mapování:

|**Oblast pracovního prostoru Log Analytics**|**Oblast Azure Automation**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS|WestCentralUS|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Asie a Tichomoří**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>2</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**Evropa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>2</sup>|USGovArizona|

<sup>1</sup> mapování EastUS pro Log Analytics pracovní prostory na účty Automation není přesné mapování oblastí na oblast, ale je to správné mapování.

<sup>2</sup> v této oblasti je podporovaná jenom Update Management a další funkce, jako je Change Tracking a inventář, nejsou v tuto chvíli k dispozici.

## <a name="unlink-a-workspace"></a>Zrušit propojení pracovního prostoru

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejdřív [odebrat](move-account.md#remove-features) Update Management, Change Tracking a inventář a Start/Stop VMS during off-hours, pokud je používáte. Pokud je neodeberete, nemůžete dokončit operaci odpojování. 

Po odebrání funkcí můžete pomocí následujících kroků zrušit propojení svého účtu Automation.

> [!NOTE]
> Některé funkce, včetně dřívějších verzí řešení Azure SQL monitoring, mohly vytvořit assety automatizace, které je potřeba před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation. Na stránce účet Automation vyberte v části **související prostředky**možnost **propojený pracovní prostor** .

2. Na stránce zrušit propojení pracovního prostoru vyberte zrušit **propojení pracovního prostoru**. Zobrazí se výzva, abyste ověřili, jestli chcete pokračovat.

3. I když Azure Automation odpojíte účet z pracovního prostoru Log Analytics, můžete sledovat průběh v nabídce **oznámení** .

4. Pokud jste použili Update Management, můžete taky chtít odebrat následující položky, které už nepotřebujete:

    * Plány aktualizací: každý má název, který odpovídá nasazení aktualizace, kterou jste vytvořili.
    * Skupiny hybridních pracovních procesů vytvořené pro funkci: každý má název podobný `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Pokud jste použili Start/Stop VMs during off-hours, můžete volitelně odebrat následující položky, které už nepotřebujete:

    * Spuštění a zastavení plánů Runbook VM
    * Spuštění a zastavení runbooků virtuálních počítačů
    * Proměnné

Alternativně můžete zrušit propojení pracovního prostoru s účtem Automation v pracovním prostoru.

1. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**.
2. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o Update Management v článku [Update Management přehled](../update-management/update-mgmt-overview.md).
* Přečtěte si o Change Tracking a inventáři v [přehledu Change Tracking a inventáře](../change-tracking.md).
* Přečtěte si o Start/Stop VMs during off-hours v článku [Start/Stop VMS during off-hours přehled](../automation-solution-vm-management.md).
