---
title: Filtrování vulgárních výrazů – Translator Text API
titlesuffix: Azure Cognitive Services
description: Použijte vulgárních výrazů v rozhraní Translator Text API filtrování.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 3ffe14450f0191b88a8d8a71b5f28ae4c59b68c1
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387588"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Přidat vulgárních výrazů filtrování s rozhraním Translator Text API

Službu Translator obvykle uchovává vulgárních výrazů, který je k dispozici ve zdroji v překladu. Do jaké míry vulgárních výrazů a stejného kontextu, který umožňuje neslušná slova se liší mezi jazykovými verzemi. Stupeň vulgárních výrazů v cílovém jazyce v důsledku toho mohou rozšířit nebo nižší.

Pokud chcete-li předejít zobrazení vulgárních výrazů v překladu, i když vulgárních výrazů je k dispozici ve zdrojovém textu, použijte vulgárních výrazů v metodě Translate() k dispozici možnost filtrování. Tato možnost umožňuje zvolte, jestli chcete zobrazit vulgárních výrazů odstraněn, označené odpovídající značky, nebo provést nebyla provedena žádná akce.

Metoda Translate() přijímá parametr "options", který obsahuje nový prvek "ProfanityAction". Přípustné hodnoty ProfanityAction jsou "NoAction", "Označený" a "Odstraněno".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Přípustné hodnoty ProfanityAction a příklady
|Hodnota ProfanityAction | Akce | Příklad: Source – japonština | Příklad: Target – angličtina|
| :---|:---|:---|:---|
| NoAction | Default (Výchozí). Stejně jako nastavit možnost. Vulgárních výrazů předá ze zdroje do cíle. | 彼は変態です。 | Je Hlupák. |
| Označené | Urážlivá slova jsou ohraničeny značky XML \<vulgárních výrazů >... \</profanity >. | 彼は変態です。 | Je \<vulgárních výrazů > jerk\</profanity >. |
| Odstranění | Urážlivá slova se odeberou z výstupu bez nahrazení. | 彼は。 | Je. |

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Použít filtrování pomocí vaše volání rozhraní Translator API vulgárních výrazů](reference/v3-0-translate.md)
