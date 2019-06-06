---
title: Znak počty – Translator Text API
titlesuffix: Azure Cognitive Services
description: Jak rozhraní Translator Text API vrátí počet znaků.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: eccd0c47c1bfc071f43a6329406cd14f704d019e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514290"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak rozhraní Translator Text API vrátí počet znaků

Translator Text API se počítá každý bod kódu Unicode vstupního textu jako znak. Každý překlad textu do jazyka se počítá jako samostatný překladu i v případě, že byla žádost učiněna v jediné rozhraní API volání přitom přeložte do více jazyků. Délka odpovědi, nezáleží.

Co se počítá je:

* Text předané rozhraní Translator Text API v textu požadavku
   * `Text` Při použití metody přeložit, Transliterate a vyhledávací slovník
   * `Text` a `Translation` při použití metody ukázky slovníků
* Všechny značky: HTML, značky XML a další v rámci textového pole z textu požadavku. Zápis JSON použitý k vytvoření žádosti (například "Text:") se nepočítá.
* Jednotlivé písmeno
* Interpunkce
* Mezera, kartu, značky a jakýkoli druh prázdný znak
* Každý bod kódu definované v kódování Unicode
* Opakované překladu, i když mají přeložit stejný text dříve

U skriptů podle ideogramů například čínštiny a japonská Kanji Translator Text API bude stále počet kódové body sady Unicode, ideogram o jeden znak. Výjimka: Náhrady kódu Unicode počet jako dva znaky.

Počet požadavků, slova, bajtů nebo věty je bezvýznamná v počtu znaků.

Volání metody rozpoznat a BreakSentence započítávají spotřeby znak. Předpokládáme, že volání metody rozpoznat a BreakSentence jsou v rozumné poměru k použití jiné funkce, které jsou započteny. Pokud počet rozpoznat nebo BreakSentence volání, které provedete překračuje počet jiných spočítaný počet metod 100krát, Microsoft si vyhrazuje právo k omezení použití metody rozpoznat a BreakSentence.


Další informace o počtu znaků je v [nejčastější dotazy k Microsoft Translatoru](https://www.microsoft.com/en-us/translator/faq.aspx).
