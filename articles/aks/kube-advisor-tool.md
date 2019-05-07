---
title: Zkontrolujte vaše nasazení Kubernetes v Azure pro implementaci osvědčených postupů
description: Zjistěte, jak vyhledat implementaci osvědčených postupů v nasazeních ve službě Azure Kubernetes Service pomocí kube advisor
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 03c5eb2e32a0a8ec51844511276d9efba5651068
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073759"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Kontrola osvědčených postupů Kubernetes v clusteru

Existuje několik osvědčených postupů, které byste měli postupovat na vaše nasazení Kubernetes a zajistit nejlepší výkon a odolnost vašich aplikací. Můžete použít nástroj kube – Poradce pro vás pod rouškou pro nasazení, která nejsou po těchto návrzích.

## <a name="about-kube-advisor"></a>O kube advisor

[Nástroj Poradce kube] [ kube-advisor-github] je navržený pro běh ve vašem clusteru jeden kontejner. Dotazy na serveru Kubernetes API informace o nasazení a vrátí sadu navržených vylepšení.

Nástroj kube advisor může podávat požadavkem na prostředky a omezení v aplikacích PodSpecs pro Windows, jakož i Linuxové aplikace chybí, ale vlastního nástroje kube advisor musí být naplánováno na Linuxu pod. Můžete naplánovat podu spustit na fond uzlů s konkrétním použití operačního systému [uzlu selektoru] [ k8s-node-selector] v konfiguraci pod.

> [!NOTE]
> Společnost Microsoft podporuje nástroj Poradce kube na základě best effort. Problémech a návrhy by měl být zaznamenaná na Githubu.

## <a name="running-kube-advisor"></a>Spuštění kube advisor

Ke spuštění nástroje na cluster, který je nakonfigurovaný pro [řízení přístupu na základě role (RBAC)](azure-ad-integration.md), pomocí následujících příkazů. První příkaz vytvoří účet služby Kubernetes. Druhý příkaz spustí nástroj v podu pomocí tohoto účtu služby a nakonfiguruje pod k odstranění po jeho ukončení. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Pokud nepoužíváte RBAC, je spustit příkaz takto:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Během několika sekund byste měli vidět tabulku popisující potenciálních zlepšení vašich nasazení.

![Výstup Kube advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Provádí kontroly

Nástroj ověří několik Kubernetes osvědčených postupů, každý s vlastní navrhované nápravné.

### <a name="resource-requests-and-limits"></a>Omezení a požadavky na zdroje

Kubernetes podporuje definování [prostředků požadavky a omezení na specifikacích pod][kube-cpumem]. Požadavek definuje minimální procesoru a paměti potřebných ke spuštění kontejneru. Limit definuje maximální využití procesoru a paměti, která má být povolený.

Ve výchozím nastavení jsou nastaveny žádné požadavky nebo omezení na specifikacích pod. To může vést k uzly se overscheduled a kontejnerů se vyčerpaná. Nástroj Poradce kube zvýrazní podů bez požadavků a omezení sady.

## <a name="cleaning-up"></a>Čištění

Pokud váš cluster má RBAC povolená, můžete vyčistit `ClusterRoleBinding` po spuštění nástroje, pomocí následujícího příkazu:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Pokud používáte nástroj proti clusteru, který není povolen RBAC, vyžaduje se žádné čištění.

## <a name="next-steps"></a>Další postup

- [Řešení potíží se službou Azure Kubernetes Service](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors