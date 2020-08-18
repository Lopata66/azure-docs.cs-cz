---
title: Párování a zarovnání vět – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Během provádění školení jsou věty přítomné v paralelních dokumentech spárovány nebo zarovnány. Vlastní Překladatel se učí překlady po jednotlivých větách, a to tak, že si přečte větu a překládá tuto větu. Pak zarovnává slova a fráze v těchto dvou větách sobě navzájem.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 97327517b40e715294393a97e8a26c078173e762
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510840"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Párování a zarovnání vět v paralelních dokumentech

V průběhu školení jsou věty přítomné v paralelních dokumentech spárovány nebo zarovnány. Vlastní Překladatel oznamuje počet vět, které byly v každé sadě dat spárovány jako zarovnaných vět.

## <a name="pairing-and-alignment-process"></a>Párování a proces zarovnání

Vlastní Překladatel se učí překlady vět v jednom okamžiku. Čte ze zdroje větu a pak překládá tuto větu z cíle. Pak zarovnává slova a fráze v těchto dvou větách sobě navzájem. Tento proces umožňuje IT vytvořit mapu slov a frází v jedné větě na ekvivalentní slova a fráze v překladu této věty. Zarovnání se snaží zajistit, aby systém vlakových a vět navzájem přecházejí.

## <a name="pre-aligned-documents"></a>Předem zarovnané dokumenty

Pokud víte, že máte paralelní dokumenty, můžete zarovnání věty přepsat zadáním předem zarovnaných textových souborů. Můžete extrahovat všechny věty z obou dokumentů do textového souboru, uspořádat jednu větu na řádek a nahrát s `.align` příponou. `.align`Rozšíření signalizuje vlastní překladateli, že by měl přeskočit zarovnání věty.

Pro dosažení co nejlepších výsledků se ujistěte, že máte v souborech jednu větu na řádek.Ve větě nemusíte mít znaky nového řádku, protože by to způsobilo špatné zarovnání.

## <a name="suggested-minimum-number-of-sentences"></a>Navrhovaný minimální počet vět

Aby bylo školení úspěšné, zobrazí následující tabulka minimální počet vět požadovaných v každém typu dokumentu.Toto omezení je bezpečnostní síť, aby bylo zajištěno, že vaše paralelní věty obsahují dostatek jedinečného slovníku, aby bylo možné přeškolit model překladu. Obecný návod má více v doméně – paralelní věty kvality lidského překladu by měly vydávat vyšší kvality modelů.

| Typ dokumentu   | Navrhovaný minimální počet vět | Maximální počet vět |
|------------|--------------------------------------------|--------------------------------|
| Školení   | 10 000                                     | Bez horní meze                 |
| Ladění     | 500                                      | 2,500       |
| Testování    | 500                                      | 2,500  |
| Slovník | 0                                          | Bez horní meze                 |

> [!NOTE]
> - Školení nebude zahájeno a nebude úspěšné, pokud není splněna minimální počet vět 10 000 pro školení. 
> - Ladění a testování jsou volitelné. Pokud je nezadáte, systém odebere příslušné procento z školení pro použití pro ověřování a testování. 
> - Model můžete vytvořit pouze pomocí dat ze slovníku. Přečtěte si prosím, [co je slovník](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Další kroky

- Naučte se používat [slovník](what-is-dictionary.md) ve vlastním překladateli.
