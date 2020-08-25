---
title: Umělá inteligence (AI)
description: LUIS používá umělou Intelligence (AI) k poskytování jazyka pro porozumění vašim datům na základě schématu, které jste definovali.
ms.topic: conceptual
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: bdcede01ec6bab5f8b43d89ff40d588a011d5dfb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751342"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Umělá inteligentní funkce v Language Understanding (LUIS)

LUIS používá umělou Intelligence (AI) k poskytování vašich dat pro porozumění přirozenému jazyku (NLU) na základě schématu, které jste definovali.

## <a name="natural-language-processing-nlp"></a>Zpracování přirozeného jazyka (NLP)

Přirozené Language Understanding (NLU) je konkrétní dílčí téma zpracování přirozeného jazyka (NLP).

Zpracování přirozeného jazyka je širší koncept, který zpracovává jakoukoli formu zpracování textových dat, zahrnuje například:

* Tokenizace
* Označení částí řeči (POS)
* Segmentation
* Morfologická analýza
* Sémantická podobnost
* Rozprava
* Překlad

## <a name="natural-language-processing-in-luis"></a>Zpracování přirozeného jazyka v LUIS

Pro aplikaci LUIS je k dispozici zpracování přirozeného jazyka následujícími způsoby:
* [Principy přirozeného jazyka](#natural-language-processing-nlp) (Luis)
* Konfigurovatelné NLP aspekty v LUIS:
    * [Tokenizace](luis-language-support.md#tokenization)
    * Nastavení morfologie prostřednictvím diakritických znamének, interpunkčních znamének a [rozhraní](luis-reference-application-settings.md) Word Forms API
* Předběžná nebo následná zpracování dotazů utterance poskytovaných jinými [Cognitive Services](../Welcome.md) , jako jsou:
    * [NAT](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>Principy přirozeného jazyka (NLU)

NLU je schopnost _transformovat_ lingvistické prohlášení na reprezentaci, která umožňuje pochopit uživatele přirozeně. Porozumění přirozenému jazyku zůstává velmi náročný problém a je definován jako problém _s pevným problémem AI_ .

LUIS je zaměřený na záměr a extrakci, což zahrnuje i schopnost identifikovat:
* Co chce uživatel
* O čem mluví.

LUIS má málo nebo žádné znalosti o širších _NLP_ aspektech, jako je například sémantická podobnost, bez explicitní identifikace v příkladech. Například následující tokeny (slova) jsou tři různé věci, dokud nejsou použity v podobných kontextech v uvedených příkladech:

* zakoupit
* nákupu
* jste

## <a name="next-steps"></a>Další kroky

* Další informace o [životním cyklu vývoje](luis-concept-app-iteration.md) aplikace Luis