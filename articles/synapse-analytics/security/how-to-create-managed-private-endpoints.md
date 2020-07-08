---
title: Vytvoření spravovaného privátního koncového bodu pro připojení k vašim výsledkům zdroje dat.
description: Tento článek vás seznámí s postupem vytvoření spravovaného privátního koncového bodu pro vaše zdroje dat z pracovního prostoru Azure synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: c6f0054364e5bbfb80fc4c1b9db6a4c15f365a81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193885"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Vytvoření spravovaného privátního koncového bodu ke zdroji dat (Preview)

Tento článek vás seznámí s postupem vytvoření spravovaného privátního koncového bodu pro zdroj dat v Azure. Další informace najdete v tématu [spravované privátní koncové body](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1: otevřete pracovní prostor Azure synapse v Azure Portal

Můžete vytvořit spravovaný privátní koncový bod ke zdroji dat z Azure synapse studia. Vyberte kartu **Přehled** v Azure Portal a vyberte **Spustit synapse Studio**.
![Spustit Azure synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Krok 2: přechod na kartu spravované virtuální sítě v synapse studiu

V Azure synapse studiu v levém navigačním panelu vyberte kartu **Spravovat** . Vyberte **spravované virtuální sítě** a pak vyberte **+ Nová**.
![Vytvoření nového spravovaného privátního koncového bodu](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Krok 3: Vyberte typ zdroje dat

Vyberte typ zdroje dat. V tomto případě je cílovým zdrojem dat účet ADLS Gen2. Vyberte **Pokračovat**.
![Vyberte typ cílového zdroje dat.](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Krok 4: zadání informací o zdroji dat

V dalším okně zadejte informace o zdroji dat. V tomto příkladu vytváříme spravovaný privátní koncový bod na účet ADLS Gen2. Zadejte **název** spravovaného privátního koncového bodu. Zadejte **předplatné Azure** a **název účtu úložiště**. Vyberte **Vytvořit**.
![Zadejte podrobnosti o cílovém zdroji dat.](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Krok 5: ověření úspěšného vytvoření spravovaného privátního koncového bodu

Po odeslání žádosti se zobrazí její stav. Chcete-li ověřit úspěšné vytvoření spravovaného privátního koncového bodu, zkontrolujte jeho *stav zřizování*. Možná budete muset počkat 1 minutu a kliknutím na **aktualizovat** aktualizovat stav zřizování. Můžete vidět, že spravovaný privátní koncový bod pro účet ADLS Gen2 byl úspěšně vytvořen.

Můžete také zjistit, že *stav schválení* čeká na *vyřízení*. Vlastník cílového prostředku může schválit nebo zamítnout požadavek na připojení privátního koncového bodu. Pokud vlastník schválí požadavek na připojení privátního koncového bodu, naváže se privátní odkaz. Pokud je odepřený, privátní odkaz se nevytvoří.
![Stav žádosti o vytvoření spravovaného privátního koncového bodu](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Další kroky

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)