---
title: Mezery mezi funkcemi Azure Media Services V2 a V3
description: Tento článek popisuje funkce mezi Azure Media Services V2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 2fa827bc2841a0bae4c9646c8a70e42dc2b500e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898405"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Mezery mezi funkcemi Azure Media Services V2 a V3

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-2.svg)

Tato část pokynů k migraci poskytuje podrobné informace o rozdílech mezi rozhraními API v2 a v3.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Mezery mezi funkcemi v rozhraních API v2 a V3

Rozhraní V3 API má na rozdíl od rozhraní v2 API následující funkce. Některé z pokročilých funkcí Media Encoder Standard v rozhraních API v2 v současnosti nejsou v systému V3 k dispozici:

- Vložení tichého zvukového záznamu v případě, že vstup nemá zvuk, protože už se Azure Media Player nepožaduje.

- Vložení stop videa, když vstup nemá žádné video.

- Živé události s překódováním v současné době nepodporují vložení do středu a vložení značek AD prostřednictvím volání rozhraní API.

- Kodér Azure Media Premium už nebude ve verzi v2 podporován. Pokud ho používáte pro 8bitové kódování HEVC, použijte novou podporu HEVC ve standardním kodéru. 
    - Přidali jsme podporu pro mapování zvukových kanálů na standardní kodér.  Podívejte [se na zvuk v dokumentaci k rozhraní Swagger pro kódování Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Pokud jste používali pokročilé funkce nebo formáty výstupu licencovaného produktu třetí strany, jako je například MXF nebo ProRes, použijte Partnerská řešení Azure ze služby streamování, které bude transakčním časem po vyřazení v2. Alternativně můžete použít předpracované sdělení nebo [Bitmovin](http://bitmovin.com).

- Vlastnost "Skupina dostupnosti" na koncovém bodu streamování ve verzi v2 již není podporována. Podívejte se na vzorový projekt a pokyny pro VOD doručování s [vysokou dostupností](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding) v rozhraní V3 API.

- V Media Services V3 nelze zadat FairPlay IV. I když nemá vliv na zákazníky, kteří používají Media Services pro balíčky i doručování licencí, může to být problém při použití systému DRM jiného výrobce k doručování licencí FairPlay (hybridní režim).

- Šifrování úložiště na straně klienta pro ochranu prostředků v klidovém úložišti se odebralo v rozhraní V3 API a nahrazuje šifrování služby úložiště pro neaktivní uložená data. Rozhraní API V3 dál fungují s existujícími šifrovanými prostředky úložiště, ale neumožňují vytváření nových.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
