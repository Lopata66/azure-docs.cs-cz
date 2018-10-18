---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888692"
---
Vrácená data s očekávanou hodnotou a výchozím horním a spodním okrajem. V praxi můžete definovat parametr [sensitivity] (citlivost) a potom použít (ExpectedValue + citlivost * UpperMargin) jako horní mez a (ExpectedValue – citlivost * LowerMargin) jako spodní mez a vyladit bod anomálie sami. Hodnota [sensitivity] by měla být větší než 1. Níže najdete některé diagramy pro ladění.

> [!NOTE]
> Ukázková aplikace diagramy negeneruje. Vytvářejí se pomocí samostatného nástroje ve spolupráci s ukázkovou aplikací.

![Ladění: citlivost = 1,0](../media/sensitivity_1.png)
![Ladění: citlivost = 1,5](../media/sensitivity_1.5.png)
![Ladění: citlivost = 2](../media/sensitivity_2.png)
![Ladění: citlivost = 3,5](../media/sensitivity_3.5.png)