---
title: Filtrování dat – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Při odesílání dokumenty, které se použije pro trénování vlastních systému projít dokumenty řadu zpracování a filtrování kroky pro přípravu pro vzdělávání.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a224a30114d03468c5764528e6c7472572a93f1c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443441"
---
# <a name="data-filtering"></a>Filtrování dat

Při odesílání dokumenty, které se použije pro trénování vlastních systému projít dokumenty řadu zpracování a filtrování kroky pro přípravu pro vzdělávání. Tyto kroky jsou popsány zde. Znalost filtrování vám můžou pomoct porozumět počet věty zobrazí ve vlastní translator, jakož i kroky, které může trvat sami Příprava školení s vlastní překlady dokumenty.

## <a name="sentence-alignment"></a>Přidružení vět
Pokud váš dokument není ve formátu XLIFF, TMX nebo zarovnání, vlastní Translator zarovná věty zdroj a cíl dokumentů k sobě navzájem, jednotlivé věty. Translator neprovádí dokumentu zarovnání – splňuje vaše pojmenování dokumenty se najít odpovídající dokument jiný jazyk. V rámci dokumentu vlastní Translator se pokusí najít odpovídající větu v jiném jazyce. Používá dokumentu značky jako vložený HTML tagy abychom vám pomohli s zarovnání.  

Je-li zobrazit velký rozdíl mezi počet věty ve zdroji a cílit na straně dokumenty, váš dokument pravděpodobně paralelní na prvním místě nebo z jiných důvodů nejde zarovnat. Dokumentu společně s velký rozdíl (> 10 %) vět na každé straně žádnou záruku druhý vzhled, abyste měli jistotu, že jsou to skutečně paralelní. Vlastní Translator zobrazují upozornění vedle dokumentu, pokud počet věty se nápadně liší.  


## <a name="deduplication"></a>Odstranění duplicit
Vlastní Translator odebere věty, které se nacházejí v testu a ladění dokumenty z trénovací data. Odebrání se dynamicky stane uvnitř školení spustit, není v kroku zpracování dat Vlastní Translator hlásí počet věty se vám v přehledu projektu před takové odebrání.  

## <a name="length-filter"></a>Délka filtru
* Odeberte věty pomocí pouze jednoho slova na obou stranách.
* Odeberte věty obsahující více než 100 slova na obou stranách.  Čínština, japonština, korejština jsou vyloučené.
* Odeberte vět s méně než 3 znaky. Čínština, japonština, korejština jsou vyloučené.
* Čínština, japonština, korejština odeberte vět s více než 2000 znaků.
* Odeberte vět s méně než 1 % alfanumerické znaky.
* Odeberte položky slovníku obsahující více než 50 slova.

## <a name="white-space"></a>Prázdné znaky
* Nahraďte libovolnou posloupností znaků mezer, včetně karet a posloupnosti znaků CR/LF pomocí jediné místo znak.
* Odebrat začínat ani končit mezerou ve větě

## <a name="sentence-end-punctuation"></a>Věty koncové interpunkce
Nahraďte více věty koncové interpunkce znaky s jednou instancí.  

## <a name="japanese-character-normalization"></a>Normalizace japonské znaky
Převeďte na poloviční šířku znaků celou šířku písmena a číslice.

## <a name="unescaped-xml-tags"></a>Neuvozené značky XML
Filtrování transformace neuvozené klíčová slova do uvozený uvozovacím znakem značky:
* `&lt;` změní `&amp;lt;`
* `&gt;` změní `&amp;gt;`
* `&amp;` změní `&amp;amp;`

## <a name="invalid-characters"></a>Neplatné znaky.
Vlastní Translator odebere věty, které obsahují znak Unicode U + FFFD. Znak U + FFFD označuje neúspěšné převod kódování.

## <a name="next-steps"></a>Další postup

- [Trénování modelu](how-to-train-model.md) ve vlastní překladač.
