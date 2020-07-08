---
title: Integrace Azure Container Registry se službou Azure Kubernetes
description: Naučte se integrovat službu Azure Kubernetes Service (AKS) s využitím Azure Container Registry (ACR).
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: aa2b82e70b1a1372076483c7405c32b66da377af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974410"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověření pomocí Azure Container Registry ze služby Azure Kubernetes Service

Pokud používáte Azure Container Registry (ACR) se službou Azure Kubernetes Service (AKS), je nutné vytvořit ověřovací mechanismus. Tato operace je implementovaná v rámci prostředí CLI a portálu tím, že uděluje požadovaná oprávnění vašemu ACR. Tento článek popisuje příklady konfigurace ověřování mezi těmito dvěma službami Azure. 

AKS můžete nastavit na integraci ACR v několika jednoduchých příkazech pomocí Azure CLI. Tato integrace přiřadí roli AcrPull instančnímu objektu přidruženému ke clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tyto příklady vyžadují:

* Role **vlastníka** nebo **správce účtu Azure** v **předplatném Azure**
* Azure CLI verze 2.7.0 nebo novější

Abyste se vyhnuli nutnosti potřebovat roli **vlastníka** nebo **správce účtu Azure** , můžete instanční objekt nakonfigurovat ručně nebo použít existující INSTANČNÍ objekt k ověření ACR z AKS. Další informace najdete v tématech [ověřování ACR pomocí instančních objektů](../container-registry/container-registry-auth-service-principal.md) nebo [ověřování z Kubernetes s tajným klíčem pro vyžádání](../container-registry/container-registry-auth-kubernetes.md)obsahu.

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Vytvoření nového clusteru AKS s integrací ACR

Během počátečního vytváření clusteru AKS můžete nastavit integraci AKS a ACR.  Pokud chcete, aby cluster AKS spolupracoval s ACR, použije se Azure Active Directory **instanční objekt** . Následující příkaz rozhraní příkazového řádku umožňuje autorizovat stávající ACR ve vašem předplatném a nakonfiguruje příslušnou roli **ACRPull** pro instanční objekt. Zadejte platné hodnoty pro následující parametry.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Případně můžete zadat název ACR pomocí ID prostředku ACR, který má následující formát:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Dokončení tohoto kroku může trvat několik minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurace integrace ACR pro existující clustery AKS

Integrujte stávající ACR s existujícími clustery AKS zadáním platných hodnot pro **ACR-Name** nebo **ACR-Resource-ID** , jak je uvedeno níže.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

ani

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Integraci mezi ACR a clusterem AKS taky můžete odebrat pomocí následujících kroků:

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

nebo

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Práce s ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Import obrázku do ACR

Naimportujte image z Docker Hub do svého ACR spuštěním následujícího postupu:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Nasazení ukázkové image z ACR do AKS

Ujistěte se, že máte správné přihlašovací údaje AKS.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Vytvořte soubor s názvem **ACR-Nginx. yaml** , který obsahuje následující:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

V dalším kroku spusťte toto nasazení v clusteru AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Nasazení můžete monitorovat spuštěním:

```console
kubectl get pods
```

Měli byste mít dvě běžící lusky.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Řešení potíží
* Další informace o [diagnostice ACR](../container-registry/container-registry-diagnostics-audit-logs.md)
* Další informace o [stavu ACR](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
