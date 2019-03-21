---
title: Atributy entit Academic Graph – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributech entity, která vám pomůže s využitím rozhraní Academic Graph rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183488"
---
# <a name="entity-attributes"></a>Atributy entit

Academic graph se skládá ze 7 typů entit. Všechny entity budou mít Entity ID a typ Entity.

## <a name="common-entity-attributes"></a>Běžné atributy entit
Název    |Popis                |Type       | Operace
------- | ------------------------- | --------- | ----------------------------
ID      |ID entity                  |Int64      |Rovná se
Ty      |Typ entity                |Výčet   |Rovná se

## <a name="entity-type-enum"></a>Entitu typu enum
Název                                                            |hodnota
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                               |0
[Author](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Konference řady](JournalEntityAttributes.md)                 |3
[Konference Instance](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Pole studie](FieldsOfStudyEntityAttributes.md)                      |6

