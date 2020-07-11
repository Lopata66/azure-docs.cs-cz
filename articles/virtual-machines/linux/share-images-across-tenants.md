---
title: Sdílení imagí Galerie napříč klienty v Azure
description: Naučte se sdílet image virtuálních počítačů napříč klienty Azure pomocí galerií sdílených imagí.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 6560bf452f04ae5d88168b8d3fad300feb90b16f
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86220520"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Sdílení imagí virtuálních počítačů galerie v klientech Azure

Galerie sdílených imagí vám umožňují sdílet Image pomocí RBAC. Ke sdílení imagí v rámci tenanta a dokonce i jednotlivcům mimo vašeho tenanta můžete použít RBAC. Další informace o této možnosti jednoduchého sdílení najdete v tématu [sdílení Galerie](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Portál nemůžete použít k nasazení virtuálního počítače z image v jiném tenantovi Azure. Pokud chcete vytvořit virtuální počítač z image sdílené mezi klienty, musíte použít Azure CLI nebo [PowerShell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Vytvoření virtuálního počítače pomocí Azure CLI

Přihlaste se k objektu služby pro tenanta 1 pomocí appID, klíče aplikace a ID tenanta 1. `az account show --query "tenantId"`V případě potřeby můžete použít k získání ID tenanta.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Přihlaste se k instančnímu objektu pro tenanta 2 pomocí appID, klíče aplikace a ID tenanta 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Vytvořte virtuální počítač. Informace v příkladu nahraďte vlastními.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Další kroky

Pokud narazíte na nějaké problémy, můžete [řešit problémy s galerií sdílených imagí](troubleshooting-shared-images.md).