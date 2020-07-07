---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67509092"
---
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **všechny služby** a v části **mobilní zařízení** vyberte **Notification Hubs** . Výběrem ikony hvězdičky vedle názvu služby přidáte službu do oddílu **Oblíbené položky** v levé nabídce. Po přidání **Notification Hubs** k **oblíbeným položkám**ji vyberte v nabídce vlevo.

      ![Azure Portal – výběr Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na stránce **Notification Hubs** vyberte **Přidat** na panelu nástrojů.

      ![Notification Hubs – tlačítko Přidat panel nástrojů](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na stránce **Centrum oznámení** proveďte následující kroky:

    1. Zadejte název do **centra oznámení**.  

    1. Zadejte název do v **vytvořit nový obor názvů**. Obor názvů obsahuje nejméně jedno rozbočovače.

    1. V rozevíracím seznamu **umístění** vyberte hodnotu. Tato hodnota určuje umístění, ve kterém chcete vytvořit centrum.

    1. Vyberte existující skupinu prostředků ve **skupině prostředků**nebo vytvořte název nové skupiny prostředků.

    1. Vyberte **Vytvořit**.

        ![Azure Portal – nastavení vlastností centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Vyberte **oznámení** (ikona zvonku) a pak vyberte **Přejít k prostředku**. Můžete také aktualizovat seznam na stránce **Notification Hubs** a vybrat své centrum.

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Vyberte ze seznamu **Zásady přístupu**. Všimněte si, že jsou k dispozici dva připojovací řetězce. Budete je potřebovat později pro zpracování nabízených oznámení.

      >[!IMPORTANT]
      >V aplikaci *nepoužívejte zásady* **DefaultFullSharedAccessSignature** . To je určeno jenom pro použití v back-endu.
      >

      ![Azure Portal – připojovací řetězce centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
