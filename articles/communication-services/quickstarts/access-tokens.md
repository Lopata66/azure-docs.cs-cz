---
title: Rychlý Start – vytvoření a Správa přístupových tokenů
titleSuffix: An Azure Communication Services quickstart
description: Naučte se spravovat identity a přístupové tokeny pomocí klientské knihovny pro správu služby Azure Communication Services.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 246b8041e7e12ea1a1391cb4690d02892bc31b63
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325285"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Rychlý Start: vytváření a Správa přístupových tokenů

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Začněte s komunikačními službami Azure pomocí klientské knihovny pro správu komunikačních služeb. Umožňuje vytvářet identity a spravovat přístupové tokeny. Identita představuje entitu aplikace ve službě Azure Communication Service (například uživatel nebo zařízení). Přístupové tokeny umožňují konverzaci a volání klientských knihoven přímo s komunikačními službami Azure. Pro službu na straně serveru doporučujeme generovat přístupové tokeny. Přístupové tokeny se pak používají k inicializaci klientských knihoven komunikačních služeb na klientských zařízeních.

Všechny ceny zobrazené v rámci tohoto kurzu jsou pouze pro demonstrační účely.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

Výstup aplikace popisuje každou akci, která je dokončena:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at Fri Nov 27 2020 16:47:05 GMT-0800 (Pacific Standard Time):
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Správa identit
> * Vystavení přístupových tokenů
> * Použití klientské knihovny pro správu komunikačních služeb


> [!div class="nextstepaction"]
> [Přidání hlasových hovorů do aplikace](./voice-video-calling/getting-started-with-calling.md)

Můžete také chtít:

 - [Další informace o ověřování](../concepts/authentication.md)
 - [Přidání chatu do aplikace](./chat/get-started.md)
 - [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)
 