---
title: Nedoručené písmeno a zásady opakování – Azure Event Grid
description: Popisuje, jak přizpůsobit možnosti doručení událostí pro Event Grid. Nastavte cíl nedoručených zpráv a určete, jak dlouho se má opakovat doručení.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "72794039"
---
# <a name="dead-letter-and-retry-policies"></a>Nedoručené dopisy a zásady opakování

Při vytváření odběru událostí můžete přizpůsobit nastavení pro doručování událostí. V tomto článku se dozvíte, jak nastavit umístění nedoručených zpráv a přizpůsobit nastavení opakování. Informace o těchto funkcích naleznete v tématu [Event Grid doručování zpráv a zkuste to znovu](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Nastavit umístění nedoručených zpráv

Chcete-li nastavit umístění nedoručených zpráv, budete potřebovat účet úložiště pro ukládání událostí, které nelze doručit do koncového bodu. V příkladech se zobrazí ID prostředku existujícího účtu úložiště. Vytvoří odběr událostí, který používá kontejner v tomto účtu úložiště pro koncový bod s nedoručenými písmeny.

> [!NOTE]
> - Před spuštěním příkazů v tomto článku vytvořte účet úložiště a kontejner objektů BLOB v úložišti.
> - Služba Event Grid vytvoří objekty BLOB v tomto kontejneru. Názvy objektů BLOB budou mít název předplatného Event Grid se všemi písmeny velkými písmeny. Pokud má například název předplatného hodnotu My-BLOB-Subscription, názvy objektů BLOB s nedoručenými písmeny budou mít na úrovni MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.js). Toto chování je chráněno proti rozdílům v případě manipulace mezi službami Azure.


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

Pokud chcete vypnout nedoručené zprávy, znovu spusťte příkaz pro vytvoření odběru události, ale nezadávejte hodnotu pro `deadletter-endpoint` . Odběr událostí není nutné odstraňovat.

> [!NOTE]
> Pokud na místním počítači používáte rozhraní příkazového řádku Azure CLI, použijte Azure CLI verze 2.0.56 nebo novější. Pokyny k instalaci nejnovější verze rozhraní příkazového řádku Azure najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

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

Pokud chcete vypnout nedoručené zprávy, znovu spusťte příkaz pro vytvoření odběru události, ale nezadávejte hodnotu pro `DeadLetterEndpoint` . Odběr událostí není nutné odstraňovat.

> [!NOTE]
> Pokud používáte Azure PowerShell na místním počítači, použijte Azure PowerShell verze 1.1.0 nebo vyšší. Stáhněte si a nainstalujte nejnovější Azure PowerShell ze služby [Azure downloads](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Nastavit zásady opakování

Při vytváření předplatného Event Grid můžete nastavit hodnoty pro dobu, po kterou by se Event Grid měla pokusit událost doručit. Ve výchozím nastavení Event Grid pokusy po dobu 24 hodin (1440 minut) nebo 30 krát. Můžete nastavit jednu z těchto hodnot pro odběr služby Event Grid. Hodnota TTL (Time to Live) musí být celé číslo od 1 do 1440. Hodnota maximálního počtu opakování musí být celé číslo od 1 do 30.

Nemůžete nakonfigurovat [plán opakování](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Pokud chcete nastavit hodnotu TTL (Time-to-Live) na jinou hodnotu než 1440 minut, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Chcete-li nastavit maximální počet opakování na jinou hodnotu než 30, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Pokud nastavíte `event-ttl` a `max-deliver-attempts` , Event Grid používá první, aby vyprší jeho doručení, aby bylo možné určit, kdy se má zastavit doručování událostí.

### <a name="powershell"></a>PowerShell

Pokud chcete nastavit hodnotu TTL (Time-to-Live) na jinou hodnotu než 1440 minut, použijte:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Chcete-li nastavit maximální počet opakování na jinou hodnotu než 30, použijte:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Pokud nastavíte `EventTtl` a `MaxDeliveryAttempt` , Event Grid používá první, aby vyprší jeho doručení, aby bylo možné určit, kdy se má zastavit doručování událostí.

## <a name="next-steps"></a>Další kroky

* Ukázkovou aplikaci, která používá aplikaci funkcí Azure ke zpracování událostí nedoručených zpráv, najdete v tématu [Azure Event Grid ukázek nedoručených zpráv pro .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Pro informace o doručení a opakování události [Event Grid doručování zpráv a akci opakujte](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
