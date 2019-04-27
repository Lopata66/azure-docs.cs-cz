---
title: Vrátí N-Best překlady – Translator Text API
titlesuffix: Azure Cognitive Services
description: Vrátí N-Best překlady pomocí rozhraní Microsoft Translator Text API.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 62992122dc16003078bb80ecd87c2bd3692586dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610591"
---
# <a name="how-to-return-n-best-translations"></a>Jak vracet N-Best překlady

> [!NOTE]
> Tato metoda je zastaralá. Není k dispozici v V3.0 Translator Text API.

Metody GetTranslations() a GetTranslationsArray() rozhraní Microsoft Translator API zahrnují volitelný logický příznak "IncludeMultipleMTAlternatives".
Metoda vrátí až maxTranslations alternativy kde rozdílového pochází ze seznamu N-Best translator stroje.

Podpis je:

**Syntaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametry**

| Parametr | Popis |
|:---|:---|
| appId | **Vyžaduje** Pokud hlavička autorizace se používá, ponechejte tuto položku appid pole prázdné jinak zadat řetězec obsahující "Nosiče" + "" + přístupový token.|
| text | **Vyžaduje** řetězec představující text k přeložení. Velikost textu nesmí být delší než 10000 znaků.|
| od | **Vyžaduje** řetězec představující kód jazyka textu pro převod. |
| na | **Vyžaduje** řetězec představující kód jazyka můžete přeložit text do. |
| maxTranslations | **Vyžaduje** celé číslo představující maximální počet překlady se vraťte. |
| Možnosti | **Volitelné** A TranslateOptions objekt, který obsahuje níže uvedené hodnoty. Jsou nepovinné a nejběžnější nastavení ve výchozím nastavení.

* Kategorie: Jediný podporovaný a výchozí možnost je "general".
* ContentType: Jediný podporovaný a výchozí hodnota, je možnost "text/plain".
* Stav: Stav uživatele korelovat požadavku a odpovědi. Vrátí se stejným obsahem v odpovědi.
* IncludeMultipleMTAlternatives: Příznak k určení, jestli se má vrátit více než jeden modul MT alternativy. Výchozí hodnotu false a obsahuje pouze 1 alternativu.

## <a name="ratings"></a>Hodnocení
Hodnocení se použijí následujícím způsobem: Nejlepší automatický překlad má hodnocení 5.
Automaticky generované alternativy překladu (N-Best) mají hodnocení 0 a shoda stupeň 100.

## <a name="number-of-alternatives"></a>Počet alternativy
Počet vrácených alternativy záleží maxTranslations, ale může být nižší.

## <a name="language-pairs"></a>Dvojice jazyků
Tato funkce není k dispozici pro překlad mezi zjednodušená a tradiční čínštiny, obou směrech. Je k dispozici pro všechny ostatní dvojice jazyků Microsoft Translatoru podporována.
