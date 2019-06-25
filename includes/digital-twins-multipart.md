---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/11/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: ac6b008597b6d6e557a0cc412c00c2202231bc3d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175647"
---
> [!NOTE]
> Požadavky na vícedílné zprávy standardu obvykle vyžadují tři údaje:
> * A **Content-Type** hlavičky:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Obsah souboru k odeslání
>
> **Content-Type** a **Content-Disposition** budou lišit v závislosti na scénáři použití.

S více částmi. požadavky můžete provést prostřednictvím kódu programu (prostřednictvím C#), pomocí klienta REST nebo nástroj, jako [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Nástroje klienta REST může mít různé úrovně podpory pro složité požadavky s více částmi. Nastavení konfigurace může nástroj mírně lišit. Ověřte, který nástroj nejlépe vyhovuje vašim potřebám.

> [!IMPORTANT]
> S více částmi. požadavky na rozhraní API Správce Dvojčat digitální Azure obvykle mít dvě části:
> * Metadata objektu BLOB (například přidružený typ MIME), která je deklarována pomocí **Content-Type** a/nebo **Content-Disposition**
> * Obsah objektu BLOB, které zahrnují nestrukturovaný obsah souboru k odeslání
>
> Ani jeden ze dvou částí, je třeba **oprava** požadavky. Jsou potřeba pro **příspěvek** nebo operace vytvoření.

[Zdrojový kód tohoto rychlého startu obsazení](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) obsahuje kompletní C# příklady demonstrující, jak je nechat vícedílné zprávy standardu žádosti na rozhraní API Správce Dvojčat digitální Azure.