---
title: Jak používat Azure Queue Storage z Pythonu – Azure Storage
description: Naučte se používat Azure Služba front z Pythonu k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/25/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 79334db46b6f035aabffcca133ed4f5cb8df3637
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855550"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Jak používat Azure Queue Storage z Pythonu

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Přehled

Tento článek popisuje běžné scénáře použití služby Azure Queue Storage. Mezi zahrnuté scénáře patří vkládání, prohlížení, získávání a odstraňování zpráv fronty. Kód pro vytváření a odstraňování front je také pokrytý.

Příklady v tomto článku jsou napsané v Pythonu a používají [klientskou knihovnu Azure Queue Storage pro Python]. Další informace o frontách najdete v části [Další kroky](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhnout a nainstalovat sadu SDK služby Azure Storage pro Python

[Sada SDK Azure Storage pro Python](https://github.com/azure/azure-storage-python) vyžaduje python verze 2,7, 3,3 nebo novější.
 
### <a name="install-via-pypi"></a>Instalace prostřednictvím PyPI

K instalaci prostřednictvím indexu balíčku Pythonu (PyPI) zadejte:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Pokud provádíte upgrade ze sady Azure Storage SDK pro Python verze 0,36 nebo starší, odinstalujte starší sadu SDK pomocí nástroje `pip uninstall azure-storage` před instalací nejnovějšího balíčku.

Alternativní metody instalace najdete v tématu [sada Azure SDK pro Python].

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup do úložiště Queue

# <a name="python-v12"></a>[Python V12](#tab/python)

Objekt [QueueClient](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) vám umožní pracovat s frontou. V horní části každého souboru Pythonu, ve kterém chcete programově přistupovat ke frontě Azure, přidejte následující kód:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Objekt [QueueService](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2) vám umožní pracovat s frontami. Následující kód vytvoří `QueueService` objekt. Do horní části každého souboru Pythonu, do kterého chcete programově přistupovat, přidejte následující kód Azure Storage:

```python
from azure.storage.queue import (
        QueueService, 
        QueueMessageFormat
)

import os, uuid
```

---

`os`Balíček poskytuje podporu pro načtení proměnné prostředí. `uuid`Balíček poskytuje podporu pro vygenerování jedinečného identifikátoru pro název fronty.

## <a name="create-a-queue"></a>Vytvoření fronty

Připojovací řetězec je načten z `AZURE_STORAGE_CONNECTION_STRING` dříve nastavené proměnné prostředí.

# <a name="python-v12"></a>[Python V12](#tab/python)

Následující kód vytvoří `QueueClient` objekt pomocí připojovacího řetězce úložiště.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Následující kód vytvoří `QueueService` objekt pomocí připojovacího řetězce úložiště.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>Vložení zprávy do fronty

# <a name="python-v12"></a>[Python V12](#tab/python)

Chcete-li vložit zprávu do fronty, použijte metodu [send_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Chcete-li vložit zprávu do fronty, pomocí metody [put_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) vytvořte novou zprávu a přidejte ji do fronty.

```python
message = u"Hello World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

Zprávy fronty Azure se ukládají jako text. Pokud chcete uložit binární data, nastavte funkce kódování a dekódování Base64 před vložením zprávy do fronty.

# <a name="python-v12"></a>[Python V12](#tab/python)

Nakonfigurujte funkce kódování a dekódování Base64 na objekt klienta Queue.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Nakonfigurujte funkce kódování a dekódování Base64 v objektu Queue Service.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>Prohlížet zprávy

# <a name="python-v12"></a>[Python V12](#tab/python)

Můžete prohlížet zprávy bez jejich odebrání z fronty voláním metody [peek_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . Ve výchozím nastavení se `peek_messages` v jedné zprávě prohlédne.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Můžete prohlížet zprávy bez jejich odebrání z fronty voláním metody [peek_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#peek-messages-queue-name--num-messages-none--timeout-none-) . Ve výchozím nastavení se `peek_messages` v jedné zprávě prohlédne.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje úkol, můžete tuto funkci použít k aktualizaci stavu úlohy.

# <a name="python-v12"></a>[Python V12](#tab/python)

Následující kód používá metodu [update_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) k aktualizaci zprávy. Časový limit viditelnosti je nastavený na 0, což znamená, že se zpráva zobrazuje hned a obsah se aktualizuje.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Následující kód používá metodu [update_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) k aktualizaci zprávy. Časový limit viditelnosti je nastavený na 0, což znamená, že se zpráva zobrazuje hned a obsah se aktualizuje.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello World Again")
```

---

## <a name="get-the-queue-length"></a>Získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě.

# <a name="python-v12"></a>[Python V12](#tab/python)

Metoda [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) požádá službu front, aby vrátila vlastnosti fronty, včetně `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Metoda [get_queue_metadata](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-queue-metadata-queue-name--timeout-none-) požádá službu front, aby vrátila metadata o frontě, včetně `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Výsledek je pouze přibližný, protože je možné přidat nebo odebrat zprávy poté, co služba Queue odpoví na vaši žádost.

## <a name="dequeue-messages"></a>Vyřadit zprávy z fronty

Odeberte zprávu z fronty ve dvou krocích. Pokud kód nedokáže zpracovat zprávu, tento proces se dvěma kroky zajistí, že můžete získat stejnou zprávu a zkusit to znovu. Zavolejte `delete_message` po úspěšném zpracování zprávy.

# <a name="python-v12"></a>[Python V12](#tab/python)

Když zavoláte [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-), ve výchozím nastavení se ve frontě zobrazí další zpráva. Zpráva vrácená z `receive_messages` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Když zavoláte [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), ve výchozím nastavení se ve frontě zobrazí další zpráva. Zpráva vrácená z `get_messages` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-message-queue-name--message-id--pop-receipt--timeout-none-).

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

# <a name="python-v12"></a>[Python V12](#tab/python)

Následující příklad kódu používá metodu [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) k získání zpráv v dávkách. Potom zpracuje každou zprávu v rámci každé dávky pomocí vnořené `for` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Následující příklad kódu používá metodu [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) k získání 16 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `for` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Odstranění fronty

# <a name="python-v12"></a>[Python V12](#tab/python)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu [delete_queue](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu [delete_queue](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-queue-queue-name--fail-not-exist-false--timeout-none-) .

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy ukládání do fronty, můžete získat další informace pomocí těchto odkazů.

* [Reference k rozhraní API Pythonu pro fronty Azure](/python/api/azure-storage-queue)
* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Klientská knihovna pro Azure Queue Storage pro Python]: https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue
[Azure SDK pro Python]: https://github.com/azure/azure-sdk-for-python
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
