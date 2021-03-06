---
title: 'Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí Azure CLI'
description: V tomto rychlém startu se dozvíte, jak nasadit Azure API pro FHIR v Azure pomocí rozhraní příkazového řádku Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3340cd0a39b0f5311487fec5b05d37e3376d433a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659246"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak nasadit Azure API pro FHIR v Azure pomocí rozhraní příkazového řádku Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>Přidat rozšíření HealthcareAPIs

```azurecli-interactive
az extension add --name healthcareapis
```

Získat seznam příkazů pro HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

Vyberte název skupiny prostředků, která bude obsahovat rozhraní Azure API pro FHIR, a vytvořte ji:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Nasazení rozhraní API Azure pro FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Načíst příkaz schopnosti rozhraní API FHIR

Získejte příkaz schopností z rozhraní API FHIR pomocí:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V této příručce pro rychlý Start jste nasadili rozhraní Azure API pro FHIR do svého předplatného. Pokud chcete nastavit další nastavení v rozhraní API Azure pro FHIR, přejděte k Průvodci dalšími nastaveními. Pokud jste připraveni začít používat rozhraní API Azure pro FHIR, přečtěte si další informace o tom, jak registrovat aplikace.

>[!div class="nextstepaction"]
>[Další nastavení v rozhraní API Azure pro FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Přehled registrace aplikací](fhir-app-registration.md)
