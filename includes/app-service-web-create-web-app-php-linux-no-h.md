---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 70d590e8ebbbfb700fc31274b8c0026426d225ea
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531978"
---
<!-- Please keep this file set to PHP 7.2, as that's the highest PHP version Laravel supports (as shown in the PHP+MySQL tutorial) -->

Vytvořte [webovou aplikaci](../articles/app-service/overview.md#app-service-on-linux) v `myAppServicePlan` plánu App Service. 

V Cloud Shell můžete použít [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) příkaz. V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `PHP|7.2`. Pokud chcete zobrazit všechny podporované moduly runtime, spusťte příkaz [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.2" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Vytvořili jste novou prázdnou webovou aplikaci s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>
