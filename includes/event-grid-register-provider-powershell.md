---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "67175623"
---
## <a name="enable-event-grid-resource-provider"></a>Povolit poskytovatele prostředků služby Event Grid

Pokud jste ve vašem předplatném Azure ještě službu Event Grid nepoužívali, budete možná muset zaregistrovat poskytovatele prostředků služby Event Grid. Spusťte následující příkaz:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Dokončení registrace může chvíli trvat. Pokud chcete zkontrolovat stav, spusťte:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Jakmile `RegistrationStatus` je `Registered`, můžete pokračovat.
