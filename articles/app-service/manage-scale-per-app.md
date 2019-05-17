---
title: Hostování s vysokou hustotou pro aplikaci pomocí škálování – Azure App Service | Dokumentace Microsoftu
description: Hostování s vysokou hustotou ve službě Azure App Service
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602328"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hostování s vysokou hustotou ve službě Azure App Service pomocí škálování pro aplikaci

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při používání služby App Service, je možné škálovat vašich aplikací díky škálování [plán služby App Service](overview-hosting-plans.md) jsou spuštěné na. Při spuštění více aplikací v rámci stejného plánu služby App Service, každá instance s horizontálním navýšením kapacity spustí všechny aplikace v plánu.

*Škálování pro aplikaci* se dá nastavit na úrovni plánu služby App Service umožňuje škálování aplikace nezávisle na plán služby App Service, který je hostitelem ho. Tímto způsobem, plán služby App Service je možné škálovat na 10 instancí, ale lze nastavit aplikaci používat pouze pět.

> [!NOTE]
> Škálování pro aplikaci je k dispozici pouze pro **standardní**, **Premium**, **Premium V2** a **izolované** cenové úrovně.
>

Aplikace jsou přiděleny k dispozici plán služby App Service pomocí nejlepším řešením úsilí pro rovnoměrnou distribuci napříč instancemi. Když není zaručená rovnoměrná distribuce, platformu budete mít jistotu, že dvě instance stejné aplikace nebude možné hostovat na stejné instance plánu služby App Service.

Platforma není závislý na metriky, které při rozhodování o přidělování pracovních procesů. Aplikace se znovu vyrovnána pouze v případě, že instance jsou přidány nebo odebrány z plánu služby App Service.

## <a name="per-app-scaling-using-powershell"></a>Na aplikaci škálování s využitím Powershellu

Vytvoření plánu s předáváním škálování za aplikací ```-PerSiteScaling $true``` parametr ```New-AzAppServicePlan``` rutiny.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Povolit aplikaci škálování s existující plán služby App Service předáním `-PerSiteScaling $true` parametr ```Set-AzAppServicePlan``` rutiny.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na úrovni aplikace nakonfigurujte počet instancí, které aplikace můžete používat v plánu služby App Service.

V následujícím příkladu je omezená na dvě instance bez ohledu na to, kolik instancí základní plán služby app service škálovat na aplikaci.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` se liší od `$newapp.MaxNumberOfWorkers`. Škálování používá aplikaci `$newapp.SiteConfig.NumberOfWorkers` určit vlastnosti škálování aplikace.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Jednotlivé aplikace škálování s využitím Azure Resource Manageru

Vytvoří následující šablony Azure Resource Manageru:

- Plán služby App Service, který je škálovat na 10 instancí
- aplikace, který je nakonfigurovaný tak, aby mít délku maximálně pět instancí umožňovala.

Nastavení plánu služby App Service **PerSiteScaling** vlastnost na hodnotu true `"perSiteScaling": true`. Nastavení aplikace **počet pracovních procesů** používat až 5 `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Doporučenou konfiguraci pro hostování s vysokou hustotou

Za škálování aplikace je funkce, která je povolena v obou globálními oblastmi Azure a [App Service Environment](environment/app-service-app-service-environment-intro.md). Doporučenou strategii je však využijte pokročilé funkce a větší kapacitu plánu služby App Service pomocí služby App Service Environment.  

Postupujte podle těchto kroků a nakonfigurujte hostování s vysokou hustotou pro vaše aplikace:

1. Určení plánu služby App Service s vysokou hustotou plánu a navýšit její kapacitu na požadovanou kapacitu.
1. Nastavte `PerSiteScaling` příznak na hodnotu true na plán služby App Service.
1. Nové aplikace jsou vytvořeny a přiřazeny do tohoto plánu služby App Service s **numberOfWorkers** vlastnost nastavena na hodnotu **1**.
   - Pomocí této konfigurace provede hustotu nejvyšší možné.
1. Počet pracovních procesů můžete nakonfigurovat nezávisle na aplikaci udělit další prostředky podle potřeby. Příklad:
   - Můžete nastavit aplikaci intenzivně využívaných **numberOfWorkers** k **3** mít větší kapacitu zpracování pro tuto aplikaci.
   - Nastaví nízkou použití aplikace **numberOfWorkers** k **1**.

## <a name="next-steps"></a>Další postup

- [Podrobný přehled plánů služby Azure App Service](overview-hosting-plans.md)
- [Úvod do prostředí App Service](environment/app-service-app-service-environment-intro.md)
