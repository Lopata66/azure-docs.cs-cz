---
title: Jak vytvořit cluster Kubernetes povolené prostory vývoj Azure pomocí Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Zjistěte, jak rychle vytvořit cluster Kubernetes povolené prostory vývoj Azure přímo z prohlížeče bez nutnosti instalace cokoli.
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s
ms.openlocfilehash: 1217b2cce03c36e4fd354d5cb9a58348b655a314
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193836"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Vytvoření clusteru Kubernetes pomocí Azure Cloud Shell

Můžete použít [Azure Cloud Shell](/azure/cloud-shell) k vytvoření clusteru prostory vývoj Azure s použitím **vyzkoušet** tlačítko z této stránky. Pokud nejste přihlášení, postupujte podle pokynů se přihlásit pomocí účtu Azure a pak zadejte příkazy příkazového řádku Azure Cloud Shell, když se objeví.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Nejprve vytvořte skupinu prostředků. Použijte některou z aktuálně podporovaných oblastí (USA – východ, USA – východ 2, USA – střed, USA – západ 2, Západní Evropa, Jihovýchodní Asie, Kanada – střed nebo Kanada – východ).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Pomocí následujícího příkazu vytvořte cluster Kubernetes:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9
```

Vytvoření clusteru bude trvat několik minut.  Po dokončení se zobrazí výstup ve formátu JSON. Vyhledejte `provisioningState` a ověřte ho má `Succeeded`.

## <a name="next-steps"></a>Další postup

Naleznete v tématu [Azure Dev prostory](/azure/dev-spaces/) odkazy na úplné kurzy.
