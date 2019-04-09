---
title: Možnosti podpory a Nápověda
titlesuffix: Azure Cognitive Services
description: Jak získat pomoc a podpora dotazů a problémů při vytváření aplikací, které se integrují se službou Speech
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: wolfma
ms.openlocfilehash: c403bca3abf493448265e25316babe20ea9c8544
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272016"
---
# <a name="support-and-help-options"></a>Možnosti podpory a Nápověda

Jste zrovna začínáte a prozkoumejte funkci hlasových služeb? Jsou implementace novou funkci pro vaši aplikaci? Tady jsou návrhy kde můžete získat pomoc jako vývojář.

> [!div class="checklist"]
> * Budete informováni o nové vývoj *Azure Cognitive Services*, nebo najít nejnovější informace související s *Speech service*.
> * Poznámky k verzi obsahují informace pro všechny verze.
> * Hledáním můžete zjistit, zda váš problém byl popsán komunitou nebo pokud stávající dokumentaci pro funkce, kterou chcete implementovat již existuje.
> * Pokud nemůže najít uspokojivou odpovědí, zeptejte se na *Stack Overflow*.
> * Pokud narazíte na problém s jednou z ukázek na Githubu, zvýšit *Githubu* problém.
> * Vyhledejte řešení v *fórum UserVoice*.

## <a name="stay-informed"></a>Aktuální informace

Novinky o službě Cognitive Services se shromažďují v [blog služeb Cognitive Services](https://azure.microsoft.com/blog/topics/cognitive-services/). Nejnovější informace o hlasové služby sledování [hlasové služby blogu](https://azure.microsoft.com/blog/tag/speech-service/).

## <a name="release-notes"></a>Poznámky k verzi

[Poznámky k verzi]] (https://aka.ms/csspeech/whatsnew) jsou aktualizovány, jakmile je k dispozici nová verze. V poznámkách k obsahují informace o nových funkcí, vylepšení a oprav chyb.

## <a name="search"></a>Search

Můžete se setkat odpověď, musíte v dokumentaci, ukázky, nebo odpovědi na [Stack Overflow](https://www.stackoverflow.com) dotazy nebo ve vzorcích.

### <a name="scoped-search"></a>Hledání s vymezeným oborem

Pro rychlejší výsledky, omezit rozsah hledání na Stack Overflow, dokumentace a ukázky kódu s použitím následujícího dotazu na vaše [oblíbený vyhledávací web](https://bing.com):

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples)
```

Kde *{Your hledané termíny}* je klíčová slova pro vyhledávání.

## <a name="create-an-azure-support-request"></a>Vytvoření žádosti o podporu Azure

Zákazníci Azure můžete vytvářet a spravovat žádosti o podporu na webu Azure Portal.

* [portál Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [Portál Azure pro vládu Spojených států amerických](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Odeslat dotaz na Stack Overflow

Přetečení zásobníku je upřednostňovaný kanál pro dotazy související s vývojem. Je to, kde členové komunity a členové týmu Microsoft se přímo podílí na využít pomoci při řešení problémů.

Pokud nemůžete najít odpověď na váš problém prostřednictvím vyhledávání, odešlete nový dotaz na Stack Overflow. Když jste formulovali dotaz, použijte jednu z následujících značek:

|Komponenta nebo oblast  |Značky  |
|---------|---------|
|Rozpoznávání řeči |[[microsoft-cognitive+speech-to-text]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|Syntézu řeči |[[microsoft-cognitive+text-to-speech]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|Překlad řeči |[[microsoft-cognitive+translation]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|Speech Intent |[[microsoft-cognitive+luis]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|Obecné sadou SDK pro řeč |[[microsoft-cognitive+microsoft-speech-api]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|

> [!TIP]
> Následující příspěvky z přetečení zásobníku obsahuje tipy, jak dotazy formulář a přidejte zdrojový kód. Dodržení těchto pokynů může zvýšit pravděpodobnost, že členové komunity, vyhodnocení a rychle reagovat na vaši otázku:  
> * [Jak můžu pokládat správné otázky?](https://stackoverflow.com/help/how-to-ask)
> * [Vytvoření minimální, dokončeno a ověřitelné příklad](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Vytvořit problém na Githubu

Ukázky jsou často odeslány jako open source. Pro dotazy a problémy, vytvořte *problém* v příslušné úložiště GitHub. Příliš můžete odeslat žádost o přijetí změn. Následující seznam obsahuje odkazy na ukázkové úložiště:

* [Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [Devices SDK](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

Můžete vytvořit hlášení o chybě, žádost o funkci, nebo požádejte obecný dotaz a osvědčenými postupy. Sestavy chyb postupujte prosím podle poskytnuté šabloně:

**Popis chyby**

Jasné a stručné popis co se chyba nachází.

**Chcete-li reprodukovat**

Kroky pro reprodukci chování:
1. ...
2. ...

**Očekávané chování**

Jasný a srozumitelný popis co jste očekávali.

**Verze služeb Cognitive Services řeči SDK**

Kterou verzi sady SDK je používáte.

**Programovací jazyk, platformu a operačního systému**

 - Operační systém: [třeba Windows, Linux, Android, iOS,...] - buďte konkrétní
 - Hardware - x64, x86, ARM, ...
 - Prohlížeč [např Chrome, Safari] (pokud existuje)-buďte konkrétní

**Další kontext**

 - Chybové zprávy, informace protokolu trasování, zásobníku...
 - Pokud byste nahlásit chybu pro konkrétní službu interakce, ohlaste SessionId a čas (včetně časového pásma) ohlášených incidentů. SessionId je uveden ve všech-zpětných/událostí volání, který jste dostali.
 - Jakékoli další informace


## <a name="uservoice-forum"></a>Fórum UserVoice

Sdílení nápadů pro vytváření služeb Cognitive Services a doprovodných rozhraní API fungují lépe, aplikací, které vytvoříte. Použijte náš rostoucí znalostní báze a vyhledat odpovědi na běžné otázky:

[UserVoice](https://cognitive.uservoice.com/)
