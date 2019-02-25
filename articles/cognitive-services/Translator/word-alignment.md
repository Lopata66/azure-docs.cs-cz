---
title: Zarovnání Word – Translator Text API
titlesuffix: Azure Cognitive Services
description: Získat aplikace word zarovnání informace z rozhraní Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.custom: seodec18
ms.openlocfilehash: bdb52e48b6a962c5b38e837f1e2bc0064539951e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734587"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak získat informace o zarovnání aplikace word

## <a name="receiving-word-alignment-information"></a>Příjem informací zarovnání aplikace word
Zobrazí informace o zarovnání, použijte metodu přeložit a přidejte parametr volitelný includeAlignment.

## <a name="alignment-information-format"></a>Formát informací o zarovnání
Zarovnání se vrátí jako hodnotu řetězce formátu pro každé slovo zdroje. Informace o jednotlivých slov je oddělena mezerou, včetně jazyků bez oddělených mezerami (skripty) jako čínština:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Příklad řetězce zarovnání: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Jinými slovy dvojtečka odděluje počáteční a koncová hodnota, pomlčku odděluje jazyky a slova jsou odděleny mezerou. O jedno slovo může bylo v souladu s žádným, jedním nebo více slov v jiném jazyce, a může být nesouvislé zarovnané slova. Pokud je k dispozici žádné informace o zarovnání, zarovnání element bude prázdný. Metoda v takovém případě vrátí žádná chyba.

## <a name="restrictions"></a>Omezení
Zarovnání je vrácena pouze pro celou dílčí sadu párů jazyka v tomto okamžiku:
* z angličtiny do jakéhokoli jiného jazyka;
* z jakéhokoli jiného jazyka na angličtinu, s výjimkou zjednodušené čínštiny, tradiční čínštině a lotyština angličtina
* z japonština, korejština nebo korejština, japonština neobdrží informace o zarovnání, pokud uvedeny překladu věty. Příklad uvedeny překladu je "Toto je test", "Mám rád je" a dalších věty, vysoká frekvence.

## <a name="example"></a>Příklad:

Ukázkový soubor JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
