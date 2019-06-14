---
title: Analýza vzorů využití Azure CDN | Dokumentace Microsoftu
description: Tento článek popisuje různé typy sestav analýzy, které jsou k dispozici pro produkty Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: ef713c954d6eab05259547a277db12a1e9036bcf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60636192"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analýza vzorů využití Azure CDN

Po povolení CDN pro aplikaci monitorovat využití CDN, zkontrolovat stav vaší doručování a řešení potíží s potenciální problémy. Azure CDN nabízí tyto možnosti následujícími způsoby: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Základní analýza prostřednictvím diagnostické protokoly Azure

Základní analýza je k dispozici pro koncové body CDN pro všechny cenové úrovně. Základní analýza, export do služby Azure storage, služby event hubs umožňují protokoly diagnostiky Azure nebo protokoly Azure monitoru. Protokoly služby Azure Monitor nabízí řešení pomocí grafů, které se dají konfigurovat uživatele a přizpůsobitelné. Další informace o diagnostické protokoly Azure najdete v tématu [diagnostické protokoly Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon základní sestavy

Jako uživatel s Azure CDN **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu** profilu, Verizon základní sestavy můžete zobrazit na doplňkovém portálu společnosti Verizon. Verizon základní sestavy je zpřístupněno **spravovat** možnost z webu Azure portal a nabízí celou řadu grafů a zobrazení. Další informace najdete v tématu [základní sestavy od Verizonu](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon vlastní sestavy

Jako uživatel s Azure CDN **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu** profilu, Verizon vlastních sestav můžete zobrazit na doplňkovém portálu společnosti Verizon. Vlastní Verizon sestavy jsou přístupná přes **spravovat** možnost z webu Azure portal. Verizon vlastních sestav stránky zobrazuje počet přístupů nebo dat přenesených pro každou hraniční CName, který patří do profilu Azure CDN. Data lze seskupovat podle kódu nebo mezipaměti stav odpovědi HTTP přes libovolné časové období. Další informace najdete v tématu [vlastní sestavy od Verizonu](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Sestavy Azure CDN Premium od Verizonu

S **Azure CDN Premium od Verizonu**, se dá dostat taky následující sestavy:
   * [Rozšířené sestavy HTTP](cdn-advanced-http-reports.md)
   * [Statistiky v reálném čase](cdn-real-time-stats.md)
   * [Výkon hraničních uzlů](cdn-edge-performance.md)

