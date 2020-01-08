---
title: Kurz – vytvoření vazby mezipaměti Azure pro Redis do vaší aplikace založené na jarním cloudu Azure
description: V tomto kurzu se dozvíte, jak vytvořit vazby Azure cache pro Redis do vaší aplikace pro jarní Cloud v Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 662d36f8a25f2f0a21d800b7b1a25e94b13908a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461506"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Vytvoření vazby mezipaměti Azure pro Redis do vaší aplikace pro jarní Cloud v Azure 

Místo ruční konfigurace aplikací pro spouštění pružiny můžete automaticky navazovat výběr služeb Azure na své aplikace s využitím jarního cloudu Azure. Tento článek ukazuje, jak vytvořit propojení aplikace s mezipamětí Azure cache pro Redis.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance cloudu Azure pro jaře
* Služba Azure cache pro instanci služby Redis
* Rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure

Pokud nemáte nasazenou instanci Azure jaře cloudu, postupujte podle kroků v [rychlém startu o nasazení aplikace pro jarní Cloud v Azure](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Vytvoření vazby mezipaměti Azure pro Redis

1. Do souboru pom. XML projektu přidejte následující závislost:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Odebrat všechny vlastnosti `spring.redis.*` ze souboru `application.properties`

1. Aktualizujte aktuální nasazení pomocí `az spring-cloud app update` nebo vytvořte nové nasazení pomocí `az spring-cloud app deployment create`.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Přejít na **řídicí panel aplikace** a vybrat aplikaci, která se váže k Azure cache pro Redis. Tato aplikace je stejná jako ta, kterou jste aktualizovali nebo nasadili v předchozím kroku.

1. Vyberte možnost **vazba služby** a vyberte **vytvořit vazbu služby**. Vyplňte formulář, ujistěte se, že jste vybrali hodnotu **typu vazby** **Azure cache pro Redis**, mezipaměť Azure pro server Redis a možnost **primárního** klíče.

1. Restartujte aplikaci. Vazba by teď měla fungovat.

1. Chcete-li zajistit, aby vazba služby byla správná, vyberte název vazby a ověřte jeho podrobnosti. Pole `property` by mělo vypadat takto:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit datovou aplikaci v Azure jaře pro službu Azure cache pro Redis. Další informace o vázání služeb pro vaši aplikaci najdete v kurzu týkajícím se vazby aplikace na instanci Azure Database for MySQL.

> [!div class="nextstepaction"]
> [Naučte se vytvořit instanci Azure Database for MySQL instance.](spring-cloud-tutorial-bind-mysql.md)
