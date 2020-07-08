---
title: Zpracování událostí v reálném čase pomocí Azure Stream Analytics
description: Tento článek popisuje referenční architekturu pro zajištění zpracování událostí a analýzy v reálném čase pomocí Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 23c7112639a64097d95df29bde16636702837f9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83832975"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referenční architektura: zpracování událostí v reálném čase pomocí Microsoft Azure Stream Analytics
Referenční architektura pro zpracování událostí v reálném čase s Azure Stream Analytics je určena k poskytnutí obecného podrobného plánu pro nasazení řešení pro zpracování datových proudů platformy jako služby (PaaS) v reálném čase pomocí Microsoft Azure.

## <a name="summary"></a>Souhrn
Analytická řešení jsou tradičně založená na možnostech, jako je ETL (extrakce, transformace, zatížení) a datové sklady, kde jsou data uložená před analýzou. Změny požadavků, včetně rychlejšího doručování dat, přenáší tento existující model do limitu. Možnost analyzovat data v rámci přesunu datových proudů před úložištěm je jedním z řešení a i když se nejedná o novou funkci, přístup se v celém oboru vertikálně nepřijal. 

Microsoft Azure poskytuje rozsáhlý katalog analytických technologií, které podporují pole různých scénářů řešení a požadavků. Výběr služeb Azure, které se mají nasadit pro ucelené řešení, může být výzvou s ohledem na širokou škálu nabídek. Tento dokument je navržený tak, aby popsal možnosti a spolupráci různých služeb Azure, které podporují řešení streamování událostí. Vysvětluje taky některé z scénářů, ve kterých můžou zákazníci využít tento typ přístupu.

## <a name="contents"></a>Obsah
* Celkové shrnutí
* Úvod do analýz v reálném čase
* Hodnota umístění dat v reálném čase v Azure
* Běžné scénáře analýzy v reálném čase
* Architektura a součásti
  * Zdroje dat
  * Vrstva integrace dat
  * Analytická vrstva v reálném čase
  * Vrstva úložiště dat
  * Prezentační/spotřebovaná vrstva
* Závěr

**Autor:** Charles Feddersen, architekt řešení, středisko pro data Insights, špičkové – Microsoft Corporation

**Publikováno:** Leden 2015

**Revize:** 1,0

**Stažení:** [zpracování událostí v reálném čase pomocí Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Podpora
Pokud potřebujete další pomoc, zkuste [&stránku s otázkou pro Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

