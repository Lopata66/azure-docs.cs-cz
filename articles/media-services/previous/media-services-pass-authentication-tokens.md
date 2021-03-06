---
title: Předat ověřovací tokeny do Azure Media Services | Microsoft Docs
description: Přečtěte si, jak odesílat ověřovací tokeny z klienta do služby pro doručování klíčů Azure Media Services
services: media-services
keywords: Ochrana obsahu, DRM, ověřování tokenů
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 498e6d9e38468b126dbe10e0d0f59f54f1816ff0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89261032"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Přečtěte si, jak klienti přecházejí tokeny službě doručování klíčů Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Zákazníci se často dotazují, jak může hráč předat tokeny službě doručování klíčů Azure Media Services k ověření, aby tento klíč mohl získat hráč. Media Services podporuje formáty jednoduchých webových tokenů (SWT) a JSON Web Token (JWT). Ověřování tokenu se aplikuje na libovolný typ klíče bez ohledu na to, jestli v systému používáte společné šifrování nebo šifrování obálek s standard AES (Advanced Encryption Standard) (AES).

 V závislosti na přehrávači a platformě, na kterou cílíte, můžete token předat pomocí přehrávače následujícími způsoby:

- Prostřednictvím hlavičky autorizace protokolu HTTP.
    > [!NOTE]
    > Na základě specifikací OAuth 2,0 se očekává prefix "nosič". Ukázkový přehrávač s konfigurací tokenu je hostovaný na [stránce Azure Media Player demo](https://ampdemo.azureedge.net/). Zdroj videa nastavíte tak, že vyberete **AES (token JWT)** nebo **AES (SWT token)**. Token se předává přes autorizační hlavičku.

- Prostřednictvím přidání parametru dotazu URL s parametrem token = tokenvalue.  
    > [!NOTE]
    > Předpona "nosiče" není očekávána. Vzhledem k tomu, že se token odesílá prostřednictvím adresy URL, je nutné obraně řetězec tokenu. Zde je ukázkový kód C#, který ukazuje, jak to provést:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Prostřednictvím pole CustomData.
Tato možnost se používá jenom pro získání licence PlayReady, a to prostřednictvím pole CustomData výzvy pro získání licence PlayReady. V takovém případě musí být token uvnitř dokumentu XML, jak je popsáno zde:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Do elementu tokenu vložte svůj ověřovací token.

- Pomocí alternativního seznamu testů HTTP Live Streaming (HLS). Pokud potřebujete nakonfigurovat ověřování pomocí tokenu pro AES + HLS přehrávání v iOS/Safari, neexistuje způsob, jakým můžete přímo odeslat token. Další informace o tom, jak alternativní seznam testů povolit pro tento scénář, najdete v tomto [blogovém příspěvku](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
