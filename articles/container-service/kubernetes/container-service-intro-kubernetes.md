---
title: ZASTARALÉ Úvod do Azure Container Service pro Kubernetes
description: Azure Container Service pro Kubernetes zjednodušuje nasazování a správu aplikací založených na kontejnerech v Azure.
author: gabrtv
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: c0ef7255a087dd5dc26532316deab337f9eff715
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76271573"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>ZASTARALÉ Úvod do Azure Container Service pro Kubernetes

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v tématu [Přehled služby Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Azure Container Service pro Kubernetes zjednodušuje vytvoření, konfiguraci a správu clusteru virtuálních počítačů, ve kterých je předem nakonfigurované spouštění kontejnerizovaných aplikací. Díky tomu můžete při nasazování a správě kontejnerových aplikací v Microsoft Azure využívat své stávající dovednosti nebo stavět na rozsáhlých a stále se rozšiřujících odborných znalostech komunity.

Díky používání služby Azure Container Service můžete využívat výhody funkcí Azure na podnikové úrovni, a přitom zachovávat přenositelnost aplikací prostřednictvím Kubernetes a formátu image Dockeru.

## <a name="using-azure-container-service-for-kubernetes"></a>Použití služby Azure Container Service pro Kubernetes
Naším cílem u služby Azure Container Service je poskytnout hostitelské prostředí pro kontejnery díky používání nástrojů a technologií open source, které jsou dnes mezi našimi zákazníky oblíbené. Za tímto účelem zveřejňujeme standardní koncové body rozhraní Kubernetes API. S použitím těchto standardních koncových bodů můžete využívat veškerý software, který dokáže komunikovat s clusterem Kubernetes. Můžete například zvolit [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) nebo [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Vytvoření clusteru Kubernetes pomocí služby Azure Container Service
Pokud chcete začít používat službu Azure Container Service, nasaďte cluster Azure Container Service pomocí [Azure CLI](container-service-kubernetes-walkthrough.md) nebo přes portál (na webu Azure Marketplace vyhledejte **Azure Container Service**). Pokud jste pokročilý uživatel a potřebujete větší kontrolu nad šablonami Azure Resource Manageru, můžete pomocí open source projektu [acs-engine](https://github.com/Azure/acs-engine) sestavit vlastní cluster Kubernetes a nasadit ho přes rozhraní příkazového řádku `az`.

### <a name="using-kubernetes"></a>S použitím Kubernetes
Kubernetes automatizuje nasazení, škálování a správu kontejnerizovaných aplikací. Má bohatou výbavu funkcí, například:
* Automatické balení do kontejnerů
* Samoopravení
* Horizontální škálování
* Zjišťování služeb a vyrovnávání zatížení
* Automatická uvádění a vracení zpět
* Správa tajných kódů a konfigurací
* Orchestrace úložiště
* Spouštění dávek

Diagram architektury systému Kubernetes nasazeného prostřednictvím služby Azure Container Service:

![Služba Azure Container Service nakonfigurovaná pro používání Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Videa

Podpora pro Kubernetes ve službě Azure Container Service (Azure Friday, leden 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Nástroje pro vývoj a nasazování aplikací v Kubernetes (Azure OpenDev, červen 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Další kroky

Přečtěte si [Rychlý start s Kubernetes](container-service-kubernetes-walkthrough.md) a začněte prozkoumávat službu Azure Container Service ještě dnes.
