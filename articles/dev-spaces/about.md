---
title: Co je služba Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Přečtěte si, jak Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy ve clusterech služby Azure Kubernetes.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8b22181bcddda9e4156c0e0dbe61d7d813498d96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80529730"
---
# <a name="what-is-azure-dev-spaces"></a>Co je služba Azure Dev Spaces?

Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy v clusterech Azure Kubernetes Service (AKS). Azure Dev Spaces také umožňuje ladit a testovat všechny součásti aplikace v AKS s minimálním nastavením vývojového počítače, aniž by se musely replikovat nebo napodobovat závislosti.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Jak Azure Dev Spaces zjednodušuje vývoj na platformě Kubernetes

Azure Dev Spaces pomáhá týmům soustředit se na vývoj a rychlou iteraci své aplikace mikroslužeb tím, že týmům umožní pracovat přímo s celou architekturou mikroslužeb nebo aplikací spuštěnou v AKS. Azure Dev Spaces taky poskytuje způsob, jak nezávisle aktualizovat části architektury mikroslužeb v izolaci, aniž by to ovlivnilo zbytek clusteru AKS nebo jiných vývojářů. Azure Dev Spaces slouží pro vývoj a testování ve vývojových a testovacích prostředích nižší úrovně a není určené ke spouštění v produkčních clusterech AKS.

Vzhledem k tomu, že týmy můžou pracovat s celou aplikací a spolupracovat přímo v AKS, Azure Dev Spaces:

* Minimalizuje nastavení místního počítače.
* Snižuje čas nastavení pro nové vývojáře v týmu.
* Zvyšuje rychlost týmu prostřednictvím rychlejší iterace.
* Snižuje počet redundantních prostředí pro vývoj a integraci, protože členové týmu můžou sdílet cluster.
* Odstraní nutnost replikace nebo napodobování závislostí.
* Vylepšuje spolupráci mezi vývojovými týmy a týmy, se kterými pracují, například DevOps týmy.

Azure Dev Spaces poskytuje nástroje pro generování Docker a Kubernetes prostředků pro vaše projekty. Pomocí tohoto nástroje můžete snadno přidávat nové a existující aplikace do vývojového prostoru i dalších clusterů AKS.

Další informace o tom, jak Azure Dev Spaces funguje, najdete v tématu [Jak funguje Azure dev Spaces a která je nakonfigurovaná][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Podporované oblasti a konfigurace

Azure Dev Spaces podporuje jenom clustery AKS v [některých oblastech][supported-regions]. Azure Dev Spaces podporuje použití [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Visual Studio Code](https://code.visualstudio.com/download) s [rozšířením Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) nainstalovaným v systémech Linux, MacOS nebo Windows 8 nebo novějším pro sestavování a spouštění aplikací na AKS. Podporuje také používání sady [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) nainstalované ve Windows 8 nebo novějším. V případě sady Visual Studio 2019 budete potřebovat úlohu vývoj pro Azure. V případě sady Visual Studio 2017 budete potřebovat úlohu vývoje webu a [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o rychlém a iterativním vývoji týmů s Azure Dev Spaces s [rychlým startem pro vývoj týmu][team-development-quickstart].

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
