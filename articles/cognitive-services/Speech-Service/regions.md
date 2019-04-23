---
title: Oblastí – hlasové služby
titlesuffix: Azure Cognitive Services
description: Referenční informace pro oblasti Speech Service.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: eb3697eefb5835305b2ec80ca1e77ccade19e44f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480653"
---
# <a name="speech-service-supported-regions"></a>Speech Service podporované oblasti

Služba Speech umožňuje vaší aplikaci převod zvuku na text, překlad řeči a převod textu na řeč. Služba je k dispozici v několika oblastech pomocí jedinečných koncových bodů pro rozhraní API REST a sadou SDK pro řeč.

Ujistěte se, že používáte koncového bodu, který se shoduje s oblastí pro vaše předplatné.

## <a name="speech-sdk"></a>Speech SDK

V [sadou SDK pro řeč](speech-sdk.md), oblastí jsou určené jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadou SDK pro řeč pro jazyk C#).

### <a name="speech-recognition-and-translation"></a>Rozpoznávání řeči a překladu

Sadou SDK pro řeč je k dispozici v těchto oblastech pro **rozpoznávání řeči** a **překlad**:

  Oblast | Parametr SDK řeči | Přizpůsobení portálu řeči
 ------|-------|--------
 Západní USA | `westus` | https://westus.cris.ai
 Západní USA 2 | `westus2` | https://westus2.cris.ai
 USA – východ | `eastus` | https://eastus.cris.ai
 USA – východ 2 | `eastus2` | https://eastus2.cris.ai
 USA – střed | `centralus` | https://centralus.cris.ai
 Středoseverní USA | `northcentralus` | https://northcentralus.cris.ai
 Středojižní USA | `southcentralus` | https://southcentralus.cris.ai
 Střed Indie | `centralindia` | https://centralindia.cris.ai
 Východní Asie | `eastasia` | https://eastasia.cris.ai
 Jihovýchodní Asie | `southeastasia` | https://southeastasia.cris.ai
 Japonsko – východ | `japaneast` | https://japaneast.cris.ai
 Korea – střed | `koreacentral` | https://koreacentral.cris.ai
 Austrálie – východ | `australiaeast` | https://australiaeast.cris.ai
 Kanada – střed | `canadacentral` | https://canadacentral.cris.ai
 Severní Evropa | `northeurope` | https://northeurope.cris.ai
 Západní Evropa | `westeurope` | https://westeurope.cris.ai
 Velká Británie – jih | `uksouth` | https://uksouth.cris.ai
 Francie – střed | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Rozpoznání záměru

Dostupné oblasti pro **rozpoznání záměru** prostřednictvím sadou SDK pro řeč jsou následující:

 Globální oblast | Oblast | Parametr SDK řeči
 ------|-------|--------
 Asie | Východní Asie | `eastasia`
 Asie | Jihovýchodní Asie | `southeastasia`
 Austrálie | Austrálie – východ | `australiaeast`
 Evropa | Severní Evropa | `northeurope`
 Evropa | Západní Evropa | `westeurope`
 Severní Amerika | USA – východ | `eastus`
 Severní Amerika | Východní USA 2 | `eastus2`
 Severní Amerika | Středojižní USA | `southcentralus`
 Severní Amerika | Západní střed USA | `westcentralus`
 Severní Amerika | Západní USA | `westus`
 Severní Amerika | Západní USA 2 | `westus2`
 Jižní Amerika | Brazílie – jih | `brazilsouth`

Toto je podmnožinou publikování oblastí podporovaných službou [služby Language Understanding (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>Rozhraní REST API

Speech service také poskytuje koncové body REST pro požadavky na převod řeči na text a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Převod řeči na text referenční dokumentaci, naleznete v tématu [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Převod textu na řeč

Převod textu na řeč referenční dokumentaci najdete v tématu [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
