---
title: Načtení párů klíč-hodnota z určitého bodu v čase
titleSuffix: Azure App Configuration
description: Načíst staré páry klíč-hodnota pomocí snímků v čase v konfiguraci aplikace Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523649"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Konfigurace aplikace Azure udržuje záznam změn provedených u párů klíč-hodnota. Tento záznam poskytuje časovou osu pro změny klíč-hodnota. Historii jakékoli hodnoty klíč-hodnota můžete kdykoli znovu sestavit a zadat její dřívější hodnotu během posledních sedmi dnů. Pomocí této funkce můžete "čas-cestování" zpět a načíst starou hodnotu klíč-hodnota. Můžete například obnovit nastavení konfigurace používané před nejnovějším nasazením, aby bylo možné aplikaci vrátit do předchozí konfigurace.

## <a name="key-value-retrieval"></a>Načtení hodnoty klíče

Pomocí Azure PowerShell můžete načíst dřívější hodnoty klíčů.  Použijte `az appconfig revision list` , pokud chcete načíst požadované hodnoty, přidejte příslušné parametry.  Zadáním názvu úložiště ( `--name {app-config-store-name}` ) nebo pomocí připojovacího řetězce () zadejte instanci Azure App Configuration `--connection-string {your-connection-string}` . Omezte výstup zadáním určitého bodu v čase ( `--datetime` ) a zadáním maximálního počtu položek, které se mají vrátit ( `--top` ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Načte všechny zaznamenané změny hodnot vašich klíčů.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Načte všechny zaznamenané změny pro klíč `environment` a popisky `test` a `prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Načte všechny zaznamenané změny v hierarchickém klíčovém prostoru `environment:prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Načte všechny zaznamenané změny pro klíč v `color` určitém časovém okamžiku.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Načte posledních 10 zaznamenaných změn hodnot klíč-hodnota a vrátí pouze hodnoty pro `key` , `label` a `last-modified` časové razítko.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET Core](./quickstart-aspnet-core-app.md)  
