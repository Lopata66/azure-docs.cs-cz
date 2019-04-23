---
title: Povolit protokolování diagnostiky v Azure Traffic Manageru
description: Zjistěte, jak povolit protokolování diagnostiky pro váš profil Traffic Manageru a soubory protokolů, které jsou vytvořeny ve výsledku.
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: a7d6893c42028790ec565961f2a2cb54035aefa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60204053"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Povolit protokolování diagnostiky v Azure Traffic Manageru

Tento článek popisuje, jak povolit diagnostické protokolování a přístup data protokolu pro profil Traffic Manageru.

Diagnostické protokoly Azure Traffic Manager může poskytnout přehled o chování prostředků profilu Traffic Manageru. Například můžete použít data protokolu profil, který chcete-li zjistit, proč jednotlivé testy vypršel časový limit pro koncový bod.

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Při spuštění PowerShell z počítače, je nutné modul Azure PowerShell 1.0.0 nebo novějším. Můžete spustit `Get-Module -ListAvailable Az` nainstalovanou verzi zjistíte. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzAccount` pro přihlášení k Azure.

1. **Načtěte profil služby Traffic Manager:**

    Pokud chcete povolit protokolování diagnostiky, budete potřebovat ID profilu služby Traffic Manager. Načíst profil Traffic Manageru, který chcete povolit diagnostické protokolování pomocí [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Výstup obsahuje informace o ID profilu Traffic Manageru.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Povolte protokolování diagnostiky pro profil Traffic Manageru:**

    Povolit protokolování diagnostiky pro profil Traffic Manageru pomocí ID, kterou jste získali v předchozím kroku s [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Následující příkaz uloží podrobné protokoly pro profil Traffic Manageru na zadaný účet Azure Storage. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Ověřte nastavení diagnostiky:**

      Ověřte nastavení diagnostiky pro používání profilu Traffic Manageru [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Následující příkaz zobrazí kategorie, které se protokolují pro prostředek.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Ujistěte se, že všechny protokolu kategorií přidružených k zobrazení prostředků profilu Traffic Manageru jako povolené. Ověřte také, že účet úložiště je správně nastavená.

## <a name="access-log-files"></a>Přístup k souborům protokolu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte do svého účtu Azure Storage na portálu.
2. Na **přehled** stránky vaší služby Azure Storage account, v části **služby** vyberte **objekty BLOB**.
3. Pro **kontejnery**vyberte **přehledy. protokoly probehealthstatusevents**a přejděte dolů soubor PT1H.json a klikněte na tlačítko **Stáhnout** a stáhněte a uložte kopii tohoto protokolu soubor.

    ![Přístup k souborům protokolu z vašeho profilu Traffic Manageru z úložiště objektů blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Schéma protokolu Traffic Manageru

Všechny diagnostické protokoly, které jsou k dispozici prostřednictvím služby Azure Monitor sdílejí společné schéma nejvyšší úrovně, s flexibilitou pro každou službu a vygenerovat jedinečné vlastnosti pro vlastní události. Schéma nejvyšší úrovně diagnostické protokoly, naleznete v tématu [podporované služby, schémat a kategorie pro diagnostické protokoly Azure](../azure-monitor/platform/tutorial-dashboards.md).

Následující tabulka obsahuje schéma protokoly specifické pro prostředek profilu Azure Traffic Manageru.

|||||
|----|----|---|---|
|**Název pole**|**Typ pole**|**Definice**|**Příklad**|
|EndpointName|String|Název sady koncových bodů Traffic Manageru, jejichž stav je zaznamenávány.|*myPrimaryEndpoint*|
|Status|String|Stav koncových bodů Traffic Manageru, který byl zjištěný. Stav může být buď **nahoru** nebo **dolů**.|**Nahoru**|
|||||

## <a name="next-steps"></a>Další postup

* Další informace o [monitorování Traffic Manageru](traffic-manager-monitoring.md)

