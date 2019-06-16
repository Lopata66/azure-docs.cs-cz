---
title: Analýza zabezpečení sítě v Azure Network Watcher zobrazení skupin zabezpečení – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat rozhraní příkazového řádku Azure pro analýzu zabezpečení virtuálních počítačů pomocí zobrazení skupin zabezpečení.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 68222a90eb60ab4f84a34b5e46833128ea081ec1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724434"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analyzovat zabezpečení vašich virtuálních počítačů se zobrazením skupin zabezpečení pomocí Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Zobrazení skupin zabezpečení vrátí pravidla zabezpečení sítě nakonfigurovaná a efektivní, které se použijí k virtuálnímu počítači. Tato možnost je užitečná k auditování a diagnostice skupiny zabezpečení sítě a pravidel, které jsou nakonfigurované na virtuálním počítači k zajištění provozu se správně povolený nebo zakázaný. V tomto článku ukážeme, jak načíst pravidla nakonfigurovaná a efektivní zabezpečení k virtuálnímu počítači pomocí rozhraní příkazového řádku Azure

K provedení kroků v tomto článku, budete muset [instalace rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher.

## <a name="scenario"></a>Scénář

Scénáře popsané v tomto článku načte pravidla nakonfigurovaná a efektivní zabezpečení pro daný virtuální počítač.

## <a name="get-a-vm"></a>Získání virtuálního počítače

Virtuální počítač se vyžaduje pro spuštění `vm list` rutiny. Následující příkaz zobrazí seznam virtuálních počítačů ve skupině prostředků:

```azurecli
az vm list -resource-group resourceGroupName
```

Když víte, virtuální počítač, můžete použít `vm show` rutiny pro získání jeho Id prostředku:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Načtení zobrazení skupin zabezpečení

Dalším krokem je načtení výsledný objekt zobrazení skupiny zabezpečení.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

Následující příklad je zkrácený odpovědi vrácené výsledky. Ve výsledcích zobrazí všechna pravidla zabezpečení efektivní a použité na virtuálním počítači rozdělené ve skupinách **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, a  **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

Navštivte [auditování skupiny zabezpečení sítě (NSG) pomocí služby Network Watcher](network-watcher-nsg-auditing-powershell.md) postup automatizace ověření skupiny zabezpečení sítě.

Další informace o pravidlech zabezpečení, které se použijí k síťovým prostředkům návštěvou [přehled zobrazení skupin zabezpečení](network-watcher-security-group-view-overview.md)
