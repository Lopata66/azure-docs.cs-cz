---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7b98d3c1febd68a7ee73cf3064f4d8e108ea81fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083844"
---
Než budete moct nasadit virtuální počítače na Azure Stack hraniční zařízení, musíte klienta nakonfigurovat tak, aby se připojil k zařízení přes Azure Resource Manager přes Azure PowerShell. Podrobný postup najdete [v tomto zařízení v Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md), kde se můžete připojit k Azure Resource Manager.


Ujistěte se, že se k přístupu k zařízení z klienta dají použít tyto kroky (Tato konfigurace jste provedli při připojování k Azure Resource Manager, právě ověříte, že konfigurace byla úspěšná.): 

1. Ověřte, zda Azure Resource Manager komunikace funguje. Zadejte:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/
    ```

1. Pro ověření zavolejte rozhraní API místních zařízení. Zadejte: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Zadejte uživatelské jméno – *EdgeARMuser* a heslo pro připojení prostřednictvím Azure Resource Manager.

1. Pokud jste nakonfigurovali **COMPUTE** pro Kubernetes, můžete tento krok přeskočit. Pokračujte a ujistěte se, že jste povolili síťové rozhraní pro výpočetní výkon. V místní uživatelské rozhraní přejít na nastavení **výpočtů** . Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače. Virtuální počítače, které vytvoříte, budou připojené k virtuálnímu přepínači připojenému k tomuto portu a přidružené síti. Ujistěte se, že zvolíte síť, která odpovídá statické IP adrese, kterou použijete pro virtuální počítač.  

    ![Povolit výpočetní nastavení 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Povolte výpočetní prostředky v síťovém rozhraní. Azure Stack Edge vytvoří a bude spravovat virtuální přepínač odpovídající tomuto síťovému rozhraní. Nezadávejte v tuto chvíli konkrétní IP adresy pro Kubernetes. Povolení výpočetní kapacity může trvat několik minut.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

