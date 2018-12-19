---
title: Vytvoření aplikace v Dockeru/Go v Linuxu – služba Azure App Service
description: Postup pro nasazení image Dockeru s aplikací v Go do služby Azure Web App for Containers.
keywords: azure app service, web app, go, docker, kontejner
services: app-service
author: msangapu
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9bf773a9804356dac153dca8730ffe520a250d28
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600307"
---
# <a name="deploy-a-dockergo-web-app-in-web-app-for-containers"></a>Nasazení webové aplikace v Dockeru/Go ve službě Web App for Containers

[App Service Linux](app-service-linux-intro.md) poskytuje předdefinované zásobníky aplikací v Linuxu s podporou jazyků, jako jsou .NET, PHP, Node.js a další. Můžete také použít vlastní image Dockeru a spouštět webovou aplikaci v zásobníku aplikací, který ještě není v Azure definovaný. Tento rychlý start ukazuje, jak vytvořit webovou aplikaci a nasadit image jazyka Go z Docker Hubu. Webovou aplikaci vytvoříte pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

Pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) vytvořte [webovou aplikaci](../overview.md) v plánu služby App Service `myAppServicePlan`. Nezapomeňte nahradit `<app name>` globálně jedinečným názvem aplikace.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

Parametr `--deployment-container-image-name` v předchozím příkazu odkazuje na veřejnou image z Docker Hubu [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Přechod do aplikace

```bash
http://<app_name>.azurewebsites.net/hello
```

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Blahopřejeme!** Do služby Web App for Containers jste nasadili vlastní image Dockeru s aplikací v Go.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití vlastní image Dockeru](tutorial-custom-docker-image.md)
