---
title: Vytvoření image kontejnerů Azure technických prostředků | Azure Marketplace
description: Vytvoření technických prostředků pro kontejner Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 32002f8b88a2fbeba29f15b3b9289ecf3d7560d0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942734"
---
# <a name="prepare-your-container-technical-assets"></a>Příprava kontejneru technické prostředky

Tento článek popisuje kroky a požadavky na konfiguraci kontejneru nabídky na webu Azure Marketplace.

## <a name="before-you-begin"></a>Než začnete

Zkontrolujte [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) dokumentaci, která poskytuje rychlé starty, kurzy a ukázky.

## <a name="fundamental-technical-knowledge"></a>Základní znalosti

Navrhování, sestavování a testování tyto prostředky dobu trvat a vyžaduje technické znalosti platformy Azure a s technologiemi použitými k vytvoření nabídky.
 
Kromě vaší domény řešení váš technický tým by měl mít znalosti následující technologie společnosti Microsoft:

-   Základní znalosti o [služeb Azure](https://azure.microsoft.com/services/) 
-   Jak [návrhu a architektury aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
-   Praktické znalosti [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [služby Azure Storage](https://azure.microsoft.com/services/?filter=storage) a [sítě Azure](https://azure.microsoft.com/services/?filter=networking)
-   Praktické znalosti [Azure Resource Manageru](https://azure.microsoft.com/features/resource-manager/)
-   Praktické znalosti [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Doporučené nástroje

Zvolte jednu nebo obě z následujících skriptovací prostředí ke správě svou image kontejneru:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme přidat následující nástroje do svého vývojového prostředí:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Linka: [Nástroje Azure Resource Manageru](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Linka: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Linka: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také kontrola nástroje dostupné v [Azure Developer Tools](https://azure.microsoft.com/tools/) stránky a pokud používáte Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Vytvořte image kontejneru

- Vytvoření a konfigurace virtuálního pevného disku (VHD) pro váš kontejner virtuální počítač (VM). Tento virtuální pevný disk obsahuje operační systém (Windows, Linux a Ubuntu) pro kontejner. Další datové disky se může vyžadovat.
- Konfigurace operačního systému virtuálního počítače, velikost virtuálních počítačů, portech a jakýchkoliv připojených datových disků.
- Instalace aplikace a další software, který je nezbytný pro vaši nabídku. Příklad: databázový software, software třetích stran nebo vlastních aplikací.

## <a name="next-steps"></a>Další postup

[Vytvoření nabídky kontejneru](./cpp-create-offer.md)
