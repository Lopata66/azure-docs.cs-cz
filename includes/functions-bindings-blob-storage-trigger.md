---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77202109"
---
Pro triggerový objekt blob můžete použít následující typy parametrů:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO serializovatelný jako JSON
* `ICloudBlob`<sup>první</sup>
* `CloudBlockBlob`<sup>první</sup>
* `CloudPageBlob`<sup>první</sup>
* `CloudAppendBlob`<sup>první</sup>

<sup>1</sup> vyžaduje vázání "InOut" `direction` v *function.js* v `FileAccess.ReadWrite` knihovně tříd jazyka C#.

Pokud se pokusíte vytvořit propojení s jedním z typů sad SDK úložiště a získat chybovou zprávu, ujistěte se, že máte odkaz na [správnou verzi sady SDK služby Storage](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Vazba na `string` , `Byte[]` nebo POCO se doporučuje pouze v případě, že je velikost objektu BLOB malá, protože celý obsah objektu BLOB je načten do paměti. Obecně je vhodnější použít `Stream` `CloudBlockBlob` typ nebo. Další informace najdete v části [využití souběžnosti a paměti](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) dále v tomto článku.