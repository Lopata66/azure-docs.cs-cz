---
title: Vytvoření virtuálního počítače s Linuxem v Azure ze šablony | Dokumentace Microsoftu
description: Jak používat rozhraní příkazového řádku Azure k vytvoření virtuálního počítače s Linuxem ze šablony Resource Manageru
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62ef6cad2f1c8f8f871043a8d1f70cbd08ccd65f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729383"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak vytvořit virtuální počítač s Linuxem pomocí šablony Azure Resource Manageru

V tomto článku se dozvíte, jak rychle nasadit virtuální počítač (VM) s Linuxem pomocí šablony Azure Resource Manageru a Azure CLI. Můžete také provádět tyto kroky [klasické rozhraní příkazového řádku Azure](create-ssh-secured-vm-from-template-nodejs.md).


V tomto článku se dozvíte, jak rychle nasadit virtuální počítač (VM) s Linuxem pomocí šablony Azure Resource Manageru a Azure CLI. 

## <a name="templates-overview"></a>Přehled šablon
Šablony Azure Resource Manageru jsou soubory JSON, které definují infrastrukturu a konfiguraci vašeho řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o formátu šablony a způsobu jejího sestavení najdete v tématu [vytvoření první šablony Azure Resource Manageru](../../azure-resource-manager/resource-manager-create-first-template.md). Syntaxi JSON pro typy prostředků najdete v tématu [Definování prostředků v šablonách Azure Resource Manageru](/azure/templates/).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupVM* v *eastus* oblasti:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Následující příklad vytvoří virtuální počítač z [tuto šablonu Azure Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment). Je povolené jenom ověřování SSH. Po zobrazení výzvy zadejte hodnotu vlastní veřejný klíč SSH, jako je například obsah *~/.ssh/id_rsa.pub*. Pokud potřebujete k vytvoření páru klíčů SSH, přečtěte si [postup vytvoření a použití páru klíčů SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

V předchozím příkladu jste zadali šablony uložené na Githubu. Můžete také stáhnout nebo vytvořit šablonu a zadejte do místní cesty `--template-file` parametru.


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači
Pro připojení SSH k virtuálnímu počítači, získejte veřejnou IP adresu pomocí [az vm show](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

Pak můžete SSH k virtuálnímu počítači jako za normálních okolností. Zadejte vlastní veřejnou IP adresu z předchozího příkazu:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili základní virtuální počítač s Linuxem. Další šablony Resource Manageru, které zahrnují aplikačních architektur nebo vytvářet složitější prostředí, přejděte [galerii šablon rychlý start Azure](https://azure.microsoft.com/documentation/templates/).

Další informace o vytváření šablon, zobrazí se syntaxe JSON a vlastnosti pro typy prostředků, které jste nasadili:

* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
