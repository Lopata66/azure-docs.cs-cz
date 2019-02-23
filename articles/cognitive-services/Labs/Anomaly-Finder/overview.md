---
title: Co je Vyhledávač anomálií? – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Pomocí pokročilých algoritmů vyhledávač anomálií můžete identifikovat anomálie v datech časové řady a vrátí informace o Microsoft Cognitive Services.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 41d20cfd9821fbd2a34a66754e5c2da3d6439fd2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726809"
---
# <a name="what-is-anomaly-finder"></a>Co je Vyhledávač anomálií?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Vyhledávač anomálií vám umožňuje monitorovat data v čase a detekci anomálií machine Learning, která se přizpůsobí svá jedinečná data automaticky použitím přímo statistické modelu bez ohledu na odvětví, scénáři nebo dat svazku. Pomocí časové řady jako vstup, vrátí rozhraní API pro vyhledávač anomálií jestli datový bod je anomálie, určuje očekávanou hodnotu a horní a dolní meze pro vizualizaci. Vyhledávač anomálií jako předem připravené služby AI, nevyžaduje žádné strojového učení odborné znalosti nad rámec pochopení způsobu, jak používat rozhraní RESTful API. Díky tomu vývoj jednoduché a flexibilní protože spolupracuje s všech dat časových řad a také se dají do streamovaných dat systémy. Vyhledávač anomálií zahrnuje široký rozsah případy použití –, finanční nástroje pro správu podvodů, krádeží, změna trhy a potenciální firmy incidentů nebo monitorování provozu zařízení IoT a zachová anonymita. Toto řešení můžete také vydělat jako součást služby pro koncové zákazníky pochopit změny v datech, útraty, návratnost investic nebo aktivity uživatelů.
Vyzkoušejte si rozhraní API pro vyhledávač anomálií a získali lepší představu o datech. 

Zobrazit, můžete vytvořit pomocí tohoto rozhraní API:

* Naučte se předpovídat očekávané hodnoty na základě historických dat v časové řadě
* Zjistit, zda je datový bod anomálií z historických vzor
* Generovat vzdálené vizualizace rozsah "normální" hodnoty

![Anomaly_Finder](./media/anomaly_detection1.png) 

Obrázek 1: Zjišťovat anomálie v prodejní výnosy

![Anomaly_Finder](./media/anomaly_detection2.png)

Obrázek 2: Zjištění změn vzoru v žádosti o služby

## <a name="requirements"></a>Požadavky

- Minimum dat pro vstupní časové řady: Minimální 13 dat body pro bez vymazat periodicitu časové řady minimálně 4 cykly dat body pro časové řady s známé periodicity. 
- Integrita dat: časové řady, jsou ve stejném intervalu a žádné chybějící body oddělené datové body. 

## <a name="identify-anomalies"></a>Identifikovat anomálie

Vrátí výsledek, který, jestli všechny body daného data jsou anomálie, nebo ne rozhraní API pro detekci anomálií a poskytuje další informace o následujícím způsobem
* Období - periodicitu, který používá rozhraní API pro detekci anomálií body.
* WarningText - upozornění je to možné.
* Model založený na ExpectedValue - předpovězené hodnoty učení
* IsAnomaly - výsledek toho, zda datové body se anomálie nebo ne
* IsAnomaly_Neg - výsledek toho, zda datové body se anomálie ve směru záporná (DIP)
* IsAnomaly_Pos - výsledek toho, zda datové body se anomálie v kladné směru (špičky)
* UpperMargin – součet ExpectedValue a UpperMargin Určuje, že je stále představit jako obvykle horní mez, která datového bodu
* LowerMargin - (ExpectedValue - LowerMargin) Určuje dolní mez, datový bod je stále si mysleli, že jako obvykle

> [!Note]
> UpperMargin a LowerMargin můžete použít ke generování pásem kolem skutečné časové řady k vizualizaci řadu běžných hodnot. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Úprava dolní a horní hranice v příspěvku na zpracování v odpovědi

Rozhraní API pro detekci anomálií vrátí výchozí výsledek toho, jestli na datový bod anomálií nebo Ne, a z ExpectedValue a UpperMargin/LowerMargin lze vypočítat horní a dolní mez. Tyto výchozí hodnoty by měly fungovat stejně dobře většině případů. Některé scénáře však vyžadují jiný rozsah než výchozí hodnoty. Postup doporučujeme je platné coefficiency UpperMargin nebo LowerMargin upravit dynamický rozsah.

### <a name="examples-with-1152-as-coefficiency"></a>Příklady s 1/1.5/2 jako coefficiency

![Výchozí citlivosti](./media/sensitivity_1.png)

![1.5 citlivosti](./media/sensitivity_1.5.png)

![2 citlivosti](./media/sensitivity_2.png)

Žádost s ukázkovými daty

[!INCLUDE [Request](./includes/request.md)]

Ukázková odpověď JSON

[!INCLUDE [Response](./includes/response.md)]
