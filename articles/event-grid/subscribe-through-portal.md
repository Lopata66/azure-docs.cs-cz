---
title: Předplatná Azure Event Grid prostřednictvím portálu
description: Tento článek popisuje, jak vytvořit předplatná Event Grid pro podporované zdroje, jako je Azure Blob Storage, pomocí Azure Portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 599f48ed241010d8551bd110c7f778c9ef508eac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81393175"
---
# <a name="subscribe-to-events-through-portal"></a>Přihlášení k odběru událostí prostřednictvím portálu

Tento článek popisuje, jak vytvořit předplatná Event Grid prostřednictvím portálu.

## <a name="create-event-subscriptions"></a>Vytvoření odběrů událostí

Chcete-li vytvořit předplatné Event Grid pro některý z podporovaných [zdrojů událostí](overview.md#event-sources), použijte následující postup. Tento článek ukazuje, jak vytvořit předplatné služby Event Grid pro předplatné Azure.

1. Vyberte **Všechny služby**.

   ![Vybrat všechny služby](./media/subscribe-through-portal/select-all-services.png)

1. Vyhledejte **Event Grid odběry** a vyberte ji z dostupných možností.

   ![Hledat](./media/subscribe-through-portal/search.png)

1. Vyberte **+ Odběr události**.

   ![Přidat předplatné](./media/subscribe-through-portal/add-subscription.png)

1. Vyberte typ předplatného, které chcete vytvořit. Pokud se například chcete přihlásit k odběru událostí předplatného Azure, vyberte **předplatná Azure** a cílové předplatné.

   ![Výběr předplatného Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Chcete-li se přihlásit k odběru všech typů událostí pro tento zdroj události, zachovejte možnost přihlásit se k **odběru všech typů událostí** . V opačném případě vyberte typy událostí pro toto předplatné.

   ![Výběr typů událostí](./media/subscribe-through-portal/select-event-types.png)

1. Zadejte další podrobnosti o odběru události, například koncový bod pro zpracování událostí a název předplatného.

   ![Zadat podrobnosti předplatného](./media/subscribe-through-portal/provide-subscription-details.png)

1. Pokud chcete povolit nedoručené dopisy a přizpůsobovat zásady opakování, vyberte **Další funkce**.

   ![Výběr dalších funkcí](./media/subscribe-through-portal/select-additional-features.png)

1. Vyberte kontejner, který se má použít pro ukládání nedoručených událostí, a nastavte způsob, jakým se odesílají pokusy o opakování.

   ![Povolit nedoručené dopisy a opakovat akci](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Až budete hotovi, vyberte **Vytvořit**.

## <a name="create-subscription-on-resource"></a>Vytvořit předplatné u prostředku

Některé zdroje událostí podporují vytvoření odběru událostí prostřednictvím rozhraní portálu pro daný prostředek. Vyberte zdroj události a vyhledejte **události** v levém podokně.

![Zadat podrobnosti předplatného](./media/subscribe-through-portal/resource-events.png)

Portál nabízí možnosti pro vytvoření odběru událostí, které je relevantní pro daný zdroj.

## <a name="next-steps"></a>Další kroky

* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
