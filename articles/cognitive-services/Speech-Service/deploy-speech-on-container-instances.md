---
title: Spuštění služby Azure Container Instances-Speech
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner služby Speech Service do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 610d5ce095097a31ee92c67f0112d1657424858e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878744"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Nasaďte kontejner služby Speech Service do Azure Container Instances

Přečtěte si, jak nasadit kontejner [služby Cognitive Services Speech Service](speech-container-howto.md) do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku služby Azure Speech Service. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Aby bylo možné požádat o přístup ke kontejneru, je nutné nejprve dokončit a odeslat [formulář žádosti o Cognitive Services kontejnerech řeči](https://aka.ms/speechcontainerspreview/) . 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
