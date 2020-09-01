---
title: Spravujte rychlost a souběžnost kódování pomocí Azure Media Services | Microsoft Docs
description: Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlost a souběžnost vašich úloh a úloh kódování pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 4b6f843678d64bddd276f6123a432699efc89ad9
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269280"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Správa rychlosti a souběžného zpracování vašeho kódování

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Tento článek poskytuje stručný přehled o tom, jak můžete spravovat rychlost a souběžnost vašich úloh a úloh kódování.

## <a name="overview"></a>Přehled

V Media Services **typ rezervované jednotky** určuje rychlost, s jakou jsou zpracovávány úlohy zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**. V tématu [škálování jednotek kódování](media-services-scale-media-processing-overview.md) se zobrazuje tabulka, která vám pomůže udělat si rozhodnutí při výběru mezi různými rychlostmi kódování.

Kromě určení typu rezervované jednotky můžete zadat, aby se účet zřídil **rezervovanými jednotkami**. Počet zřízených rezervovaných jednotek určuje počet úloh médií, které je možné v daném účtu zpracovávat současně. Pokud má váš účet například pět rezervovaných jednotek, pak pět mediálních úloh bude spuštěno souběžně, dokud budou zpracovány úkoly. Zbývající úlohy budou čekat ve frontě a budou vyzvednuty pro zpracování po dokončení běžící úlohy. Pokud účet nemá zřízeny žádné rezervované jednotky, budou úkoly postupně vyzvednuty. V tomto případě bude doba čekání mezi dokončením jednoho úkolu a dalším počátkem záviset na dostupnosti prostředků v systému.

Podrobné informace a příklady, které ukazují, jak škálovat jednotky kódování, najdete v [tomto](media-services-scale-media-processing-overview.md) tématu.

## <a name="next-step"></a>Další krok

[Jednotky kódování škály](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

