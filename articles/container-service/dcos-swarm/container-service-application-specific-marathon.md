---
title: (ZASTARALÉ) Služba Marathon pro aplikaci nebo uživatelem
description: Vytvoření služby Marathon specifické pro aplikaci nebo uživatele
author: rgardler
ms.service: container-service
ms.topic: conceptual
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 423dc7f62806f774a5ec4855faa8be9001292773
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277721"
---
# <a name="deprecated-create-an-application-or-user-specific-marathon-service"></a>(ZASTARALÉ) Vytvoření aplikace nebo služby Marathon specifické pro uživatele

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Azure Container Service poskytuje sadu hlavních serverů, na kterých předem konfigurujeme Apache Mesos a Marathon. Ty je možné použít k orchestrování aplikací v clusteru, ale vhodnější je hlavní servery k tomuto účelu nepoužívat. Například při úpravách konfigurace Marathonu je nutné se k přihlásit k samotným hlavním serverům a provést změny přímo na nich – to může vést ke vzniku jedinečných hlavních serverů, které se jen málo liší od těch standardních a je třeba o ně nezávisle pečovat a spravovat je. Navíc konfigurace, kterou vyžaduje jeden tým, nemusí být optimální pro jiný tým.

V tomto článku si vysvětlíme, jak přidat službu Marathon specifickou pro uživatele nebo aplikaci.

Protože tato služba bude patřit jedinému uživateli nebo týmu, je možné ji nakonfigurovat, jakkoli si budoucí uživatelé přejí. Služba Azure Container Service zajistí, že bude služba nadále v provozu. V případě selhání služby ji služba Azure Container Service restartuje za vás. Ve většině případů si ani nevšimnete, že došlo k výpadku.

## <a name="prerequisites"></a>Požadavky
[Nasaďte instanci služby Azure Container Service](container-service-deployment.md) pomocí orchestrator typu DC/OS a [ujistěte se, že se váš klient může připojit k vašemu clusteru](../container-service-connect.md). Také proveďte následující kroky.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Vytvoření služby Marathon specifické pro aplikaci nebo uživatele
Začněte tím, že vytvoříte konfigurační soubor JSON, který definuje název vytvářené aplikační služby. Zde jako název rozhraní používáme `marathon-alice`. Uložte soubor s názvem, jako je `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Pak pomocí rozhraní příkazového řádku DC/OS nainstalujte instanci Marathonu s možnostmi nastavenými v konfiguračním souboru:

```bash
dcos package install --options=marathon-alice.json marathon
```

Nyní byste svou službu `marathon-alice` měli vidět spuštěnou na kartě služeb v uživatelském rozhraní DC/OS. Pokud chcete k uživatelskému rozhraní přistupovat přímo, bude na adrese `http://<hostname>/service/marathon-alice/`.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Nastavte rozhraní příkazového řádku DC/OS pro přístup ke službě
Volitelně je možné následujícím způsobem nakonfigurovat rozhraní příkazového řádku DC/OS pro přístup k této nové službě, a to tím, že nastavíte vlastnost `marathon.url`, aby ukazovala na instanci `marathon-alice`:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Pomocí příkazu `dcos config show` můžete ověřit, proti které instanci Marathon pracuje vaše CLI. Můžete se vrátit k hlavní službě Marathon pomocí příkazu `dcos config unset marathon.url`.

