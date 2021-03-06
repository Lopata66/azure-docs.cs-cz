---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení Cloud Shell a rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: c1d1efce656bea02385cb20ac11ab90ca5664e60
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026760"
---
Aby bylo možné začít pracovat s digitálními událostmi Azure v otevřeném [Azure Cloud Shell](https://shell.azure.com) , první věc, kterou je třeba provést, se přihlaste a nastavte kontext prostředí na vaše předplatné pro tuto relaci. Spusťte tyto příkazy v Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Místo ID ve výše uvedeném příkazu můžete také použít svůj název vašeho předplatného. 

Pokud jste toto předplatné použili u digitálních vláken Azure jako první, spusťte tento příkaz a zaregistrujte se do oboru názvů digitálních vláken Azure. (Pokud si nejste jistí, můžete ji znovu spustit i v případě, že jste to udělali za nějakou dobu v minulosti.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Dále přidáte [**Microsoft Azure rozšíření IoT pro Azure CLI**](/cli/azure/ext/azure-iot/iot?preserve-view=true&view=azure-cli-latest) do vašeho Cloud Shell, abyste povolili příkazy pro interakci s digitálními interakcemi Azure a dalšími službami IoT. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Nyní jste připraveni pracovat s digitálními úkoly Azure v Cloud Shell.

Můžete to ověřit tak, že je spustíte kdykoli `az dt -h` a zobrazí se seznam dostupných příkazů digitálního vlákna Azure na nejvyšší úrovni.