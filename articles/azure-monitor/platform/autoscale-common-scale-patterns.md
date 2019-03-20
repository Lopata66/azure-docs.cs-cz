---
title: Přehled obecné vzory automatického škálování
description: Seznamte se s některými běžných vzorů pro automatické škálování prostředku v Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 8356a8c8c31a043197485b4913b4a67d7d719778
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997256"
---
# <a name="overview-of-common-autoscale-patterns"></a>Přehled obecné vzory automatického škálování
Tento článek popisuje některé běžné vzory škálování prostředku v Azure.

Automatické škálování služby Azure Monitor se týká pouze [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), a [služby API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Umožňuje začít

Tento článek předpokládá, že máte zkušenosti s automatické škálování. Je možné [začněte tady škálovat prostředek][1]. Následují některé běžné vzory škálování.

## <a name="scale-based-on-cpu"></a>Škálování podle využití procesoru

Můžete mít třeba webovou aplikaci (/ VMSS/role cloudové služby) a

- Chcete škálování na více instancí a škálování v závislosti na využití procesoru.
- Kromě toho budete chtít zajistit, že je minimální počet instancí.
- Chcete také, ujistěte se, že nastavíte maximální limit počtu instancí, které je možné škálovat na.

![Škálování podle využití procesoru][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Jinak škálování o víkendech vs dny v týdnu

Můžete mít třeba webovou aplikaci (/ VMSS/role cloudové služby) a

- Ve výchozím nastavení chcete 3 instance (ve všední dny)
- Provoz nečekáte o víkendech, a proto chcete škálovat dolů 1 instance o víkendech.

![Jinak škálování o víkendech vs dny v týdnu][3]

## <a name="scale-differently-during-holidays"></a>Škálovat jinak během svátků

Můžete mít třeba webovou aplikaci (/ VMSS/role cloudové služby) a

- Chcete škálovat směrem nahoru nebo dolů podle využití procesoru ve výchozím nastavení
- Nicméně během sváteční období (nebo konkrétní dny, které jsou důležité pro vaši společnost) chcete přepsat výchozí hodnoty a mít k dispozici větší kapacitu.

![Jinak na svátků škálování][4]

## <a name="scale-based-on-custom-metric"></a>Škálování na základě vlastní metriky

Máte webového front-endu a vrstvu rozhraní API, která komunikuje s back-endu.

- Chcete-li škálovat vrstvu rozhraní API na základě vlastních událostí v části front end (Příklad: Chcete škálovat váš proces platby u pokladny na základě počtu položek v nákupním košíku)

![Škálování na základě vlastní metriky][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

