---
title: ZASTARALÉ Kurz Azure Container Service – škálování aplikace
description: Kurz Azure Container Service – Škálování aplikace
author: dlepow
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 2ea8a5428c1fabdfda4f2298c0559792537df481
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273992"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>ZASTARALÉ Škálování Kubernetes lusků a Kubernetes infrastruktury

> [!TIP]
> Aktualizovanou verzi v tomto kurzu, který používá službu Azure Kubernetes, najdete v tématu [kurz: škálování aplikací ve službě Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Pokud jste postupovali podle kurzů, máte funkční cluster Kubernetes ve službě Azure Container Service a nasadili jste aplikaci Azure Vote. 

V tomto kurzu, který je pátou částí sedmidílné série, budete škálovat pody v této aplikaci a vyzkoušíte si jejich automatické škálování. Naučíte se také, jak škálovat počet uzlů agentů virtuálního počítače Azure, aby se změnila kapacita clusteru pro hostování úloh. Mezi dokončené úlohy patří:

> [!div class="checklist"]
> * Ruční škálování podů Kubernetes
> * Konfigurace automatického škálování podů, ve kterých běží front-end aplikace
> * Škálování uzlů agentů Azure Kubernetes

V dalších kurzech se aktualizuje aplikace Azure Vote a nakonfiguruje se služba Log Analytics pro monitorování clusteru Kubernetes.

## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes. 

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Ruční škálování podů

Doposud máme nasazený front-end Azure Vote a instanci Redis, oboje s jedinou replikou. K ověření spusťte příkaz [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

[https://shell.azure.com](https://shell.azure.com) V prohlížeči otevřete Cloud Shell.

```console
kubectl get pods
```

Výstup:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ručně změňte počet podů v nasazení `azure-vote-front` pomocí příkazu [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale). Tento příklad zvýší jejich počet na 5.

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Spuštěním příkazu [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) ověřte, že Kubernetes vytváří tyto pody. Asi za minutu budou další pody spuštěné:

```console
kubectl get pods
```

Výstup:

```output
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatické škálování podů

Kubernetes podporuje [horizontální automatické škálování podů](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) pro úpravu počtu podů v nasazení v závislosti na využití procesoru nebo jiné vybrané metrice. 

Aby bylo možné použít modul automatického škálování, pody musí mít definovaná omezení a požadavky na procesor. V nasazení `azure-vote-front` kontejner front-endu vyžaduje 0,25 CPU s limitem 0,5 CPU. Nastavení vypadá takto:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Následující příklad využívá příkaz [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) k automatickému škálování počtu podů v nasazení `azure-vote-front`. Pokud tady využití procesoru přesáhne 50 %, modul automatického škálování zvýší počet podů na maximálně 10.


```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Spuštěním následujícího příkazu zobrazíte stav modulu automatického škálování:

```console
kubectl get hpa
```

Výstup:

```output
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po několika minutách se díky minimálnímu zatížení aplikace Azure Vote počet replik podů automaticky sníží na 3.

## <a name="scale-the-agents"></a>Škálování agentů

Pokud jste cluster Kubernetes vytvořili pomocí výchozích příkazů v předchozím kurzu, má tři uzly agentů. Pokud ve vašem clusteru plánujete více nebo méně úloh kontejneru, můžete počet agentů upravit ručně. Použijte příkaz [az acs scale](/cli/azure/acs#az-acs-scale) a zadejte počet agentů pomocí parametru `--new-agent-count`.

Následující příklad zvýší počet uzlů agentů v clusteru Kubernetes s názvem *myK8sCluster* na 4. Dokončení tohoto příkazu trvá několik minut.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

Výstup tohoto příkazu ukazuje počet uzlů agentů v hodnotě `agentPoolProfiles:count`:

```output
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste v clusteru Kubernetes využili různé funkce škálování. Mezi probírané úlohy patří:

> [!div class="checklist"]
> * Ruční škálování podů Kubernetes
> * Konfigurace automatického škálování podů, ve kterých běží front-end aplikace
> * Škálování uzlů agentů Azure Kubernetes

Přejděte k dalšímu kurzu, kde se seznámíte s aktualizací aplikace v Kubernetes.

> [!div class="nextstepaction"]
> [Aktualizace aplikace v Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

