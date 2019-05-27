---
title: Zásady opakování pro odběry služby Azure Event Grid a nedoručené zprávy
description: Popisuje, jak přizpůsobit možnosti doručování událostí služby Event Grid. Nastavte cíl onta nedoručených zpráv a určit, jak dlouho to chcete zkusit znovu doručování.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: a1b49fd3a2a85377a56c92aefd1b0056f91895b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66119569"
---
# <a name="dead-letter-and-retry-policies"></a>Zásady opakování a nedoručené zprávy

Při vytváření odběru událostí, můžete přizpůsobit nastavení pro doručování událostí. V tomto článku se dozvíte, jak nastavit umístění nedoručených zpráv a přizpůsobit nastavení opakování. Informace o těchto funkcích najdete v tématu [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Nastavit umístění onta nedoručených zpráv

Pokud chcete nastavit umístění nedoručených zpráv, potřebujete účet úložiště pro uchování událostí, které nelze doručit do koncového bodu. V příkladech Získejte ID prostředku pro existující účet úložiště. Vytvoří odběr událostí, který používá kontejner v účtu úložiště pro koncový bod onta nedoručených zpráv.

> [!NOTE]
> Vytvoření účtu úložiště a kontejner objektů blob v úložišti před spuštěním příkazů v tomto článku.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Chcete-li vypnout dead-lettering, spusťte znovu příkaz pro vytvoření odběru událostí, ale neposkytují hodnotu `deadletter-endpoint`. Není nutné odstranit odběr události.

> [!NOTE]
> Pokud používáte rozhraní příkazového řádku Azure v místním počítači, použijte Azure CLI verze 2.0.56 nebo vyšší. Pokyny k instalaci nejnovější verze Azure CLI najdete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Chcete-li vypnout dead-lettering, spusťte znovu příkaz pro vytvoření odběru událostí, ale neposkytují hodnotu `DeadLetterEndpoint`. Není nutné odstranit odběr události.

> [!NOTE]
> Pokud používáte Azure PowerShell na místním počítači, pomocí prostředí Azure PowerShell verze 1.1.0 nebo vyšší. Stáhněte a nainstalujte nejnovější Azure PowerShell z [soubory ke stažení Azure](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Nastavit zásady opakování

Při vytváření odběr Event gridu, můžete nastavit hodnoty pro jak dlouho by měl zkuste doručování událostí služby Event Grid. Ve výchozím nastavení služby Event Grid se pokusí za 24 hodin (1 440 minut) nebo 30krát. Pro váš odběr služby event grid můžete nastavit některé z těchto hodnot. Hodnota time to live události musí být celé číslo od 1 do 1440. Hodnota pro maximální počet opakování musí být celé číslo od 1 do 30.

Nejde nakonfigurovat [plán opakování](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Pokud chcete nastavit událost time-to-live na jinou hodnotu než 1 440 minut, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Pokud chcete nastavit maximální počet opakovaných pokusů na jinou hodnotu než 30, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Nastavíte-li `event-ttl` a `max-deliver-attempts`, služby Event Grid použije první vyprší k určení, kdy se má zastavit doručování událostí.

### <a name="powershell"></a>PowerShell

Pokud chcete nastavit událost time-to-live na jinou hodnotu než 1 440 minut, použijte:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Pokud chcete nastavit maximální počet opakovaných pokusů na jinou hodnotu než 30, použijte:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Nastavíte-li `EventTtl` a `MaxDeliveryAttempt`, služby Event Grid použije první vyprší k určení, kdy se má zastavit doručování událostí.

## <a name="next-steps"></a>Další postup

* Ukázková aplikace, která používá aplikaci funkcí Azure ke zpracování událostí nedoručených zpráv, najdete v části [ukázky služby Event Grid nedoručených zpráv Azure pro .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
