---
title: Azure brány firewall hostovaného operačního systému virtuálního počítače je špatně nakonfigurovaný. | Dokumentace Microsoftu
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839742"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Brána firewall operačního systému hosta virtuálního počítače Azure je špatně nakonfigurovaný.

V tomto článku představují k vyřešení nesprávně nakonfigurované hostovaného operačního systému brány firewall na virtuálním počítači Azure.

## <a name="symptoms"></a>Příznaky

1.  Na úvodní obrazovce virtuálního počítače (VM) ukazuje, že je virtuální počítač plně načteno.

2.  V závislosti na konfiguraci hostovaného operačního systému může být některé nebo žádné síťový provoz, obraťte se virtuálním počítači.

## <a name="cause"></a>Příčina

Chybná konfigurace brány firewall systému hosta můžete blokovat některé nebo všechny druhy síťový provoz do virtuálního počítače.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte snímek systémový disk ovlivněných virtuálních počítačů jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).

Chcete-li tento problém vyřešit, použijte konzole sériového portu nebo [opravte virtuální počítač v režimu offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) připojením systémový disk virtuálního počítače na virtuální počítač pro obnovení.

## <a name="online-mitigations"></a>Online migrace

Připojte se k [konzoly sériového portu a pak otevřete PowerShell instance](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Pokud konzole sériového portu není povolená na virtuálním počítači, přejděte do části "Opravy virtuální počítač do režimu Offline" v následujícím článku na Azure:

 [Dojde k interní chybě při pokusu o připojení k virtuálnímu počítači Azure přes vzdálenou plochu](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Následující pravidla lze upravovat, buď umožňuje přístup k virtuálnímu počítači (prostřednictvím protokolu RDP) nebo poskytnout prostředí pro snazší řešení problémů:

*   Vzdálená plocha (TCP-In): Toto je standardní pravidlo, které poskytuje primární přístup do virtuálního počítače tím, že protokol RDP v Azure.

*   Vzdálená správa Windows (HTTP-In): Toto pravidlo umožňuje vám umožní připojit se k virtuálnímu počítači pomocí Powershellu. V Azure, tento typ přístupu vám umožní používat skriptovací aspekt vzdálené skriptování a řešení potíží.

*   Sdílení souborů a tiskáren (SMB-In): Toto pravidlo aktivuje přístup ke sdílené složce sítě jako možnost řešení potíží.

*   Sdílení souborů a tiskáren (žádost o odezvu – ICMPv4-In): Toto pravidlo umožňuje příkaz ping virtuálnímu počítači.

V případě sériový přístup ke konzole může dotazovat aktuální stav pravidla brány firewall.

*   Dotazování s použitím zobrazovaný název jako parametr:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Dotazování s použitím místní Port, který aplikace používá:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Dotazování s použitím místní IP adresu, která aplikace používá:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Pokud se zobrazí, že pravidlo je zakázaná, můžete ji povolit spuštěním následujícího příkazu:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   S řešením problémů, můžete vypnout profilů brány firewall:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Pokud jste to správně nastavit bránu firewall, znovu povolte bránu firewall po dokončení odstraňování problémů.

    > [!Note]
    > Není nutné restartovat virtuální počítač, aby tuto změnu použít.

*   Připojení k virtuálnímu počítači pomocí RDP, zkuste to znovu.

### <a name="offline-mitigations"></a>Offline migrace

1.  Povolení nebo zakázání pravidla brány firewall, najdete v tématu [povolit nebo zakázat pravidlo brány firewall na operační systém hosta virtuálního počítače Azure](enable-disable-firewall-rule-guest-os.md).

2.  Zkontrolujte, zda jsou v [blokuje příchozí provoz scénáře s branou firewall hostovaného operačního systému](guest-os-firewall-blocking-inbound-traffic.md).

3.  Pokud jste pořád máte pochybnosti o Určuje, zda brána firewall blokuje přístup, podívejte se na [vypnout hostovaný operační systém brány Firewall na virtuálním počítači Azure](disable-guest-os-firewall-windows.md)a potom znovu povolit bránu firewall systému hosta s použitím správné pravidla.

