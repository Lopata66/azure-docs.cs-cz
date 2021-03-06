---
title: Vytváření a odstraňování privátních koncových bodů v clusteru Azure Stream Analytics
description: Naučte se spravovat privátní koncové body v clusteru Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: cff02bb4b7d0f7e969589a9977a89f89e2674ffc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019410"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Vytváření a odstraňování privátních koncových bodů v clusteru Azure Stream Analytics

Úlohy Azure Stream Analytics spuštěné v clusteru můžete propojit se vstupními a výstupními prostředky, které se nacházejí za bránou firewall nebo virtuální sítí Azure. Nejprve v clusteru Stream Analytics vytvoříte privátní koncový bod pro prostředek, jako je centrum událostí Azure nebo služba Azure SQL Database. Pak ve vstupním nebo výstupním prostředku schválíte připojení privátního koncového bodu.

Jakmile připojení schválíte, budou mít všechny úlohy spuštěné v clusteru Stream Analytics k tomuto prostředku přístup prostřednictvím privátního koncového bodu. V tomto článku se dozvíte, jak vytvořit a odstranit privátní koncové body v clusteru Stream Analytics. Můžete vytvořit soukromé koncové body pro Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, centrum událostí Azure a Azure Service Bus. Brzy se přidají soukromé koncové body pro jiné služby. 

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Vytvoření privátního koncového bodu v clusteru Stream Analytics

V této části se dozvíte, jak vytvořit privátní koncový bod ve Stream Analyticsm clusteru.

1. V Azure Portal Najděte cluster Stream Analytics a vyberte ho.

1. V části **Nastavení** vyberte **soukromé koncové body**.

1. Vyberte **Přidat privátní koncový bod** a zadejte následující informace pro výběr prostředku, ke kterému chcete bezpečně přistupovat prostřednictvím privátního koncového bodu.

   |Nastavení|Hodnota|
   |---|---|
   |Název|Zadejte libovolný název vašeho privátního koncového bodu. Pokud se tento název povede, vytvořte jedinečný název.|
   |Způsob připojení|**V adresáři vyberte připojit k prostředku Azure**.<br><br>Můžete zvolit jeden z vašich prostředků pro bezpečné připojení pomocí privátního koncového bodu nebo se můžete připojit k prostředku jiné osoby pomocí ID prostředku nebo aliasu, který s vámi sdílí.|
   |Předplatné|Vyberte své předplatné.|
   |Typ prostředku|Vyberte [typ prostředku, který se mapuje k vašemu prostředku](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Prostředek|Vyberte prostředek, ke kterému se chcete připojit pomocí privátního koncového bodu.|
   |Cílový podprostředek|Typ prostředku pro prostředek vybraný výše, ke kterému bude mít přístup váš privátní koncový bod.|

   ![Prostředí pro vytvoření privátního koncového bodu](./media/private-endpoints/create-private-endpoint.png)

1. Schvalte připojení z cílového prostředku. Pokud jste například vytvořili privátní koncový bod pro instanci Azure SQL Database v předchozím kroku, měli byste přejít na tuto instanci SQL Database a zobrazit nedokončené připojení, které by mělo být schváleno. Zobrazení žádosti o připojení může trvat několik minut.

    ![schválit soukromý koncový bod](./media/private-endpoints/approve-private-endpoint.png)

1. Vraťte se do clusteru Stream Analytics, abyste viděli změnu stavu z **nedokončeného schválení zákazníka** na čeká se na **dokončení** nastavení **DNS** během několika minut.

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Odstranění privátního koncového bodu v clusteru Stream Analytics

1. V Azure Portal Najděte cluster Stream Analytics a vyberte ho.

1. V části **Nastavení** vyberte **soukromé koncové body**.

1. Zvolte privátní koncový bod, který chcete odstranit, a vyberte **Odstranit**.

   ![Odstranit privátní koncový bod](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Další kroky

Nyní máte přehled o správě privátních koncových bodů v clusteru Azure Stream Analytics. V dalším kroku se dozvíte, jak škálovat clustery a spouštět úlohy ve vašem clusteru:

* [Škálování Azure Stream Analytics clusteru](scale-cluster.md)
* [Správa úloh Stream Analytics v clusteru Stream Analytics](manage-jobs-cluster.md)
