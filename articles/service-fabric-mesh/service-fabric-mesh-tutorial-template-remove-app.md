---
title: Kurz – Odebrání aplikace spuštěné ve službě Azure Service Fabric Mesh | Microsoft Docs
description: V tomto kurzu se naučíte odebrat aplikaci spuštěnou ve službě Service Fabric Mesh a odstranit prostředky.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: adc5b96f29f610c63bcfa24a3b5f761c04d41d5b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339665"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Kurz: Odebrat aplikace a prostředky

Tento kurz je čtvrtou částí série. Dozvíte se, jak odebrat spuštěnou aplikaci, která se [předtím nasadila do služby Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Odstranění aplikace spuštěné ve službě Service Fabric Mesh
> * Odstranění prostředků aplikace

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Nasazení aplikace do služby Service Fabric Mesh pomocí šablony](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Škálování aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Upgrade aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Odebrání aplikace

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Otevřete [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) nebo si [nainstalujte Azure CLI a Service Fabric Mesh CLI místně](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Odstranění skupiny prostředků a všech prostředků

Pokud už vytvořené prostředky nepotřebujete, všechny je odstraňte. Dříve jste [vytvořili novou skupinu prostředků](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) pro hostování instance služby Azure Container Registry a prostředků aplikace Service Fabric Mesh.  Tuto skupinu prostředků můžete odstranit a tím se odstraní i všechny přidružené prostředky.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Odstranění jednotlivých prostředků
Instanci služby ACR, aplikaci Service Fabric Mesh a síťové prostředky můžete odstranit také jednotlivě.

Odstranění instance služby ACR:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Odstranění aplikace Service Fabric Mesh:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Odstranění sítě:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Odstranění aplikace spuštěné ve službě Service Fabric Mesh
> * Odstranění prostředků aplikace