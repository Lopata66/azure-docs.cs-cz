---
title: Analýza zabezpečení sítě v Azure Network Watcher zobrazení skupin zabezpečení – PowerShell | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí prostředí PowerShell pro analýzu zabezpečení virtuálních počítačů pomocí zobrazení skupin zabezpečení.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 389eab13193b313d8609bfa54d5e0dc42329f5ad
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720763"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analyzovat zabezpečení vašich virtuálních počítačů se zobrazením skupin zabezpečení pomocí Powershellu

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Zobrazení skupin zabezpečení vrátí pravidla zabezpečení sítě nakonfigurovaná a efektivní, které se použijí k virtuálnímu počítači. Tato možnost je užitečná k auditování a diagnostice skupiny zabezpečení sítě a pravidel, které jsou nakonfigurované na virtuálním počítači k zajištění provozu se správně povolený nebo zakázaný. V tomto článku ukážeme, jak načíst pravidla nakonfigurovaná a efektivní zabezpečení k virtuálnímu počítači pomocí Powershellu


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři můžete spustit `Get-AzNetworkWatcherSecurityGroupView` rutina pro načtení informací o pravidlo zabezpečení.

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher.

## <a name="scenario"></a>Scénář

Scénáře popsané v tomto článku načte pravidla nakonfigurovaná a efektivní zabezpečení pro daný virtuální počítač.

## <a name="retrieve-network-watcher"></a>Načíst Network Watcher

Prvním krokem je načtení instance Network Watcheru. Tato proměnná je předána `Get-AzNetworkWatcherSecurityGroupView` rutiny.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Získání virtuálního počítače

Virtuální počítač se vyžaduje pro spuštění `Get-AzNetworkWatcherSecurityGroupView` rutiny proti. Následující příklad načte objekt virtuálního počítače.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Načtení zobrazení skupin zabezpečení

Dalším krokem je načtení výsledný objekt zobrazení skupiny zabezpečení.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

Následující příklad je zkrácený odpovědi vrácené výsledky. Ve výsledcích zobrazí všechna pravidla zabezpečení efektivní a použité na virtuálním počítači rozdělené ve skupinách **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, a  **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Další postup

Navštivte [auditování skupiny zabezpečení sítě (NSG) pomocí služby Network Watcher](network-watcher-nsg-auditing-powershell.md) postup automatizace ověření skupiny zabezpečení sítě.


