---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu Helm textu na řeč.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80874332"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Převod textu na řeč (dílčí graf: grafy/textToSpeech)

Chcete-li potlačit graf "zastřešující", přidejte `textToSpeech.` předponu pro libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, `textToSpeech.numberOfConcurrentRequest` například Overrides. `numberOfConcurrentRequest`

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Určuje, zda je povolena služba **Převod textu na mluvené slovo** . | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků na službu převodu **textu na řeč** . Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForTurboMode`| Zda služba potřebuje optimalizovat pro zadávání textu prostřednictvím textových souborů. Pokud `true`bude tento graf přidělovat službě více prostředků procesoru. | `false` |
| `image.registry`| Registr **textu na řeč** Docker image. | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků Docker **pro převod textu na mluvené slovo** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Značka obrázku Docker **pro převod textu na řeč** | `latest` |
| `image.pullSecrets` | Obrazová tajemství pro vyžádání image Docker **textu na řeč** | |
| `image.pullByHash`| Zda je obrázek Docker načítán pomocí algoritmu hash. Pokud `true` `image.hash` je vyžadováno. | `false` |
| `image.hash`| Hodnota hash obrázku Docker **pro text na řeč** Používá se pouze `image.pullByHash: true`v případě.  | |
| `image.args.eula`požadovanou | Označuje, že jste přijali licenci. Jediná platná hodnota je`accept` | |
| `image.args.billing`požadovanou | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal. | |
| `image.args.apikey`požadovanou | Používá se ke sledování fakturačních informací. ||
| `service.type` | Typ služby Kubernetes služby převodu **textu na řeč** . Další podrobnosti najdete v [pokynech k typům služby Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověření podpory poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby převodu **textu na mluvené slovo** . | `80` |
| `service.annotations` | Poznámky pro **Převod textu na mluvené slovo** pro metadata služby Poznámky jsou páry klíč-hodnota. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Určuje, zda je [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) povoleno. Pokud `true`se `text-to-speech-autoscaler` bude nasadit do clusteru Kubernetes. | `true` |
| `service.podDisruption.enabled` | Zda je povoleno [rozpočet přerušení pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud `true`se `text-to-speech-poddisruptionbudget` bude nasadit do clusteru Kubernetes. | `true` |