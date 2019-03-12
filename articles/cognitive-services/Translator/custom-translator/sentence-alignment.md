---
title: Věta párování a zarovnání – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Během provádění trénování jsou věty, které jsou k dispozici v dokumentech paralelní spárované nebo zarovnána. Vlastní Translator učí jedné věty překlady najednou, načtením věty, překlad tuto větu. Potom zarovná slova a slovní spojení v těchto dvou věty k sobě navzájem.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 8d2933f6a3bbab792acb708f2a59cad4eb2cabf7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777154"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Párování věty a zarovnání v paralelní dokumenty

Při výuce, jsou věty, které jsou k dispozici v dokumentech paralelní spárované nebo zarovnána. Vlastní Translator hlásí počet věty, které bylo možné spárovat jako věty zarovnané ve všech datových sad.

## <a name="pairing-and-alignment-process"></a>Proces párování a zarovnání

Vlastní Translator učí překlady jedné věty věty najednou. Jeho čtení a větu ze zdroje a překladu tuto větu z cíle. Potom zarovná slova a slovní spojení v těchto dvou věty k sobě navzájem. Tento proces umožňuje vytvořit mapu slova a slovní spojení do jedné věty na ekvivalentní slova a slovní spojení v překladu tuto větu. Zarovnání se pokusí zajistit, že systém trénovat na věty, které jsou vzájemně překlady.

## <a name="pre-aligned-documents"></a>Předem zarovnané dokumenty

Pokud víte, že máte paralelní dokumentů, můžou přepsat zarovnání věty zadáním předem zarovnané textové soubory. Extrahovat všech vět z oba dokumenty do textového souboru, uspořádané jedné věty na řádku a nahrávání se `.align` rozšíření. `.align` Rozšíření signalizuje vlastní Translator, přeskočte zarovnání věty.

Nejlepších výsledků dosáhnete pokuste se ujistěte se, že máte jeden větu na řádek v souborech. Nemáte znaky nového řádku v rámci věty, protože to způsobí, že špatná zarovnání.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Doporučený minimální počet extrahované a zarovnané věty

Školení na úspěšné, následující tabulka ukazuje minimální počet extrahované věty a zarovnané věty v každé datové sady. Minimální doporučený počet extrahované věty je mnohem vyšší než minimální doporučený počet zarovnané věty a vezměte v úvahu skutečnost, že zarovnání věty nemusí být možné úspěšně zarovnat všechny extrahované věty.

| Datové sady   | Navrhované extrahované věty minimální počet | Navrhované zarovnané věty minimální počet | Počet maximální zarovnané větu |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Školení   | 10 000                                     | 2 000                                    | Žádná horní hranice                 |
| Ladění     | 2 000                                      | 500                                      | 2,500                          |
| Testování    | 2 000                                      | 500                                      | 2,500                          |
| Slovník | 0                                          | 0                                        | Žádná horní hranice                 |

## <a name="next-steps"></a>Další postup

- Další informace o použití [slovníku](what-is-dictionary.md) ve vlastní překladač.
