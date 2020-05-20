---
title: Automatizace instalace místního prostředí Integration runtime pomocí místních skriptů PowerShellu
description: K automatizaci instalace Integration Runtime v místním počítači do místního počítače.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 068586a96ad3655cb70171266bd58f56ed320fc1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664379"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatizace instalace místního prostředí Integration runtime pomocí místních skriptů PowerShellu
Pokud chcete automatizovat instalaci Integration Runtime v místním prostředí na místních počítačích (kromě virtuálních počítačů Azure, kde můžeme místo toho použít šablonu Správce prostředků), můžete použít místní skripty PowerShellu. Tento článek obsahuje dva skripty, které můžete použít.

## <a name="prerequisites"></a>Požadavky

* Na místním počítači spusťte PowerShell. Chcete-li spustit skripty, je třeba zvolit možnost **Spustit jako správce**.
* [Stáhněte si](https://www.microsoft.com/download/details.aspx?id=39717) místně hostovaný software Integration runtime. Zkopírujte cestu k umístění staženého souboru. 
* K registraci modulu runtime integrace v místním prostředí potřebujete také **ověřovací klíč** .
* Pro automatizaci ručních aktualizací musíte mít předem konfigurovaný modul runtime integrace v místním prostředí.

## <a name="scripts-introduction"></a>Úvod ke skriptům 

> [!NOTE]
> Tyto skripty se vytvářejí pomocí [dokumentovaného nástroje příkazového řádku](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#set-up-an-existing-self-hosted-ir-via-local-powershell) v místním prostředí Integration runtime. Pokud je to potřeba, může tyto skripty přizpůsobit svým potřebám pro automatizaci.
> Skripty je potřeba použít pro jednotlivé uzly, proto je nezapomeňte spustit napříč všemi uzly v případě nastavení vysoké dostupnosti (2 nebo více uzlů).

* Pro automatizaci instalace: Nainstalujte a zaregistrujte nový uzel Integration runtime v místním prostředí pomocí **[InstallGatewayOnLocalMachine. ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** – skript se dá použít k instalaci uzlu místního prostředí Integration runtime a jeho registraci k ověřovacímu klíči. Skript přijímá dva argumenty, **nejdříve** určí umístění místního [prostředí Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717) na místním disku, přičemž **druhý** Určuje **ověřovací klíč** (pro registraci uzlu IR v místním prostředí).

* Pro automatizaci ručních aktualizací: aktualizujte v místním prostředí IR uzel s konkrétní verzí nebo na nejnovější verzi **[Script-Update-Gateway. ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** – to je podporováno i v případě, že jste automatickou aktualizaci vypnuli nebo chcete mít větší kontrolu nad aktualizacemi. Skript lze použít k aktualizaci uzlu místního prostředí Integration runtime na nejnovější verzi nebo na určenou vyšší verzi (downgrade nefunguje). Přijímá argument pro zadání čísla verze (příklad:-Version 3.13.6942.1). Pokud není zadána žádná verze, vždy aktualizuje modul IR v místním prostředí na nejnovější verzi, která se nachází ve [stahování](https://www.microsoft.com/download/details.aspx?id=39717).
    > [!NOTE]
    > Zadat lze pouze poslední 3 verze. V ideálním případě se používá k aktualizaci existujícího uzlu na nejnovější verzi. **předpokládá se, že máte registrovanou technologii IR hostovaná na sebe**. 

## <a name="usage-examples"></a>Příklady použití

### <a name="for-automating-setup"></a>Pro automatizaci instalace
1. Stáhněte si místně hostovaný IR z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=39717). 
1. Zadejte cestu, ve které výše stažený SHIR MSI (instalační soubor). Pokud je cesta například *c:\users\username\downloads\ IntegrationRuntime_4.7.7368.1. msi*, můžete pro tuto úlohu použít níže uvedený příklad příkazového řádku PowerShellu:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Nahraďte [key] klíčem ověřování a zaregistrujte svůj IR.
    > Nahraďte "username" vaším uživatelským jménem.
    > Zadejte umístění souboru InstallGatewayOnLocalMachine. ps1 při spuštění skriptu. V tomto příkladu jsme si ho uložili na Desktop.

1. Pokud je na vašem počítači nainstalovaný modul IR s vlastním prostředím, skript ho automaticky odinstaluje a pak nakonfiguruje nový. Zobrazí se následující okno vyjmuté: ![ Konfigurace prostředí Integration runtime](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Až se instalace a registrace klíče dokončí, uvidíte, že jste *úspěšně nainstalovali bránu* a do svého místního prostředí PowerShell jste *úspěšně zaregistrovali výsledky brány* .
        [![skript 1 – výsledek spuštění](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Pro automatizaci ručních aktualizací
Tento skript se používá k aktualizaci nebo instalaci a registraci nejnovějšího místního prostředí Integration runtime. Spuštění skriptu provede následující kroky:
1. Ověřit aktuálně v místním prostředí IR verze
2. Získat nejnovější verzi nebo zadanou verzi z argumentu
3. Pokud je novější verze než aktuální verze:
    * Stáhnout místní prostředí IR MSI pro místní hostování
    * upgradovat

Pomocí tohoto skriptu můžete sledovat následující příklad příkazového řádku:
* Stáhnout a nainstalovat nejnovější bránu:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Stáhnout a nainstalovat bránu zadané verze:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Pokud je aktuální verze již nejnovější, bude se vám zobrazovat následující výsledek, takže není potřeba žádná aktualizace.   
    [![skript 2 – výsledek spuštění](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)
