---
title: Postup upgradu nástroje Azure Dev prostorů | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 07/03/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: 61e59abde2c3a06b8cf650306b67ac5142729a8c
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50976932"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Postup upgradu nástroje Azure Dev mezery

Pokud už používáte Azure Dev mezery, je nová verze potřebujete upgradovat vaše klientské nástroje Azure Dev mezery.

## <a name="update-the-azure-cli"></a>Aktualizace rozhraní příkazového řádku Azure

Při aktualizaci nejnovější rozhraní příkazového řádku Azure získáte také nejnovější verze rozšíření rozhraní příkazového řádku vývojáře mezery.

Není nutné odinstalovat předchozí verzi, stačí najít odpovídající stáhnout na [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aktualizace rozšíření rozhraní příkazového řádku vývojáře mezery a nástroje příkazového řádku

Spusťte následující příkaz:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aktualizovat rozšíření VS Code

Po instalaci rozšíření se automaticky aktualizuje. Možná budete muset znovu načíst rozšíření používat nové funkce. Ve VS Code, Otevřít **rozšíření** podokně, vyberte **prostory Azure Dev** rozšíření a zvolte **znovu načíst**.

## <a name="update-the-visual-studio-extension"></a>Aktualizace rozšíření sady Visual Studio

Stejně jako ostatní rozšíření a aktualizace, Visual Studio vás upozorní na aktualizace je k dispozici pro Visual Studio Tools pro systém Kubernetes, který obsahuje mezery vývoj Azure. Vyhledejte ikonou vlajky v horní části obrazovky.

Chcete-li aktualizovat nástroje v sadě Visual Studio, zvolte **nástroje > rozšíření a aktualizace** nabídku položky a na levé straně zvolte **aktualizuje**. Najít **Visual Studio Tools pro systém Kubernetes** a zvolte **aktualizace** tlačítko.

## <a name="next-steps"></a>Další postup

Otestování nových nástrojů tak, že vytvoříte nový cluster. Zkuste rychlých startů a kurzů v [Azure Dev prostory](/azure/dev-spaces).

> [!WARNING]
> Azure Dev mezery na stávajících clusterů nebude opravit hned, proto ujistěte se, že používáte nejnovější verzi u všech nasazení v Azure, vytvořte nový cluster po upgradu nástroje.
