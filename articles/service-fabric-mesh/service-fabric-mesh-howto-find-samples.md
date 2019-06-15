---
title: Najít ukázky pro Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak různé ukázkové aplikace Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: db8c68bf5f9aeb8069044c1344be9f69e498b1b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419556"
---
# <a name="find-service-fabric-mesh-samples"></a>Najít ukázky pro Service Fabric sítě

Tato tabulka popisuje dostupné ukázkové aplikace Service Fabric mřížky. Zdrojový kód v těchto příkladech ukazuje, jak dosáhnout určitého scénáře pomocí modelu prostředků služby Service Fabric.

Další informace o nasazení šablony přímo do Azure, najdete v článku [stránka Githubu ukázkové šablony.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)


|Ukázková šablona|Popis scénáře|Zdrojový kód|Vývojářské nástroje|
|------------|--------------------|----------|----------------------|
| [Aplikace Hello World](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statické webové stránky hostované v kontejneru. Pro Linux využívá nginx, pro Windows služby IIS | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Žádné požadavky |
| [Čítač aplikace pro svazky souborů Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Stav Store tak, že připojí soubory Azure na základě svazku uvnitř kontejneru. <br><br> **Poznámka:** Tato šablona vyžaduje sdílenou soubory Azure k už být zřízeno [pokyny](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio sítě nástroje |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Vytvořte aplikaci s front-endu a back-endová služba, která používá překladu názvů DNS. Použít jako návod [zde](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio sítě nástroje |
| [Vizuální objekty aplikací](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Škálování a upgrade mikroslužeb v rámci aplikace. | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio sítě nástroje |
| [Hlasovací aplikace](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Vytvořte aplikaci s front-endu a back-endová služba, která používá překladu názvů DNS | [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio sítě nástroje pro Windows verze VS Code nebo rozhraní příkazového řádku dotnet lze použít pro Linux verze |
| [Čítač aplikace Service Fabric Reliable svazků](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Store stavu tím, že připojení Service Fabric Reliable disku na základě objemu uvnitř kontejneru.| [Zdrojový kód](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio sítě nástroje |
