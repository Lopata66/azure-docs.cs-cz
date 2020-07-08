---
title: Nejde se vzdáleně připojit k Azure Virtual Machines, protože síťová karta je zakázaná | Microsoft Docs
description: Naučte se řešit potíže s tím, že se protokol RDP nepovede, protože síťová karta je zakázaná ve virtuálním počítači Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77918236"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nejde o vzdálenou plochu k virtuálnímu počítači, protože síťové rozhraní je zakázané.

Tento článek vysvětluje, jak vyřešit problém, ve kterém nemůžete vytvořit připojení ke vzdálené ploše do Azure Windows Virtual Machines (virtuální počítače), pokud je síťové rozhraní zakázané.


## <a name="symptoms"></a>Příznaky

Nemůžete vytvořit připojení RDP ani jiný typ připojení k virtuálnímu počítači v Azure, protože síťové rozhraní je ve virtuálním počítači zakázané.

## <a name="solution"></a>Řešení

Než budete postupovat podle těchto kroků, pořiďte si snímek disku s operačním systémem ovlivněného virtuálního počítače jako záložního. Další informace najdete v tématu [vytvoření snímku disku](../windows/snapshot-copy-managed-disk.md).

Pokud chcete povolit rozhraní pro virtuální počítač, použijte pro virtuální počítač sériové řízení nebo [resetování síťového rozhraní](#reset-network-interface) .

### <a name="use-serial-control"></a>Použití ovládacího prvku sériového portu

1. Připojte se ke [konzole sériového prostředí a otevřete instanci cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Pokud není na vašem VIRTUÁLNÍm počítači povolená síťová konzola, přečtěte si téma [resetování síťového rozhraní](#reset-network-interface).
2. Ověřte stav síťového rozhraní:

        netsh interface show interface

    Poznamenejte si název zakázaného síťového rozhraní.

3. Povolit síťové rozhraní:

        netsh interface set interface name="interface Name" admin=enabled

    Pokud je třeba rozhraní pro spolupráci s názvem "Ethernet 2", spusťte následující příkaz:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Zkontrolujte stav síťového rozhraní znovu a ujistěte se, že je síťové rozhraní povolené.

        netsh interface show interface

    V tuto chvíli nemusíte restartovat virtuální počítač. Virtuální počítač bude dostupný zpátky.

5.  Připojte se k virtuálnímu počítači a podívejte se, jestli se problém vyřeší.

## <a name="reset-network-interface"></a>Resetovat síťové rozhraní

Pokud chcete resetovat síťové rozhraní, změňte IP adresu na jinou IP adresu, která je k dispozici v podsíti. K tomu použijte Azure Portal nebo Azure PowerShell. Další informace najdete v tématu [resetování síťového rozhraní](reset-network-interface.md).
