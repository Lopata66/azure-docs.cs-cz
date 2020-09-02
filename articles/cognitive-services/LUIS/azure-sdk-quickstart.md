---
title: 'Rychlý Start: Language Understanding (LUIS) SDK klientské knihovny'
description: V tomto rychlém startu pomocí jazyků C#, Python nebo JavaScript vytvoříte a Dotazujte aplikaci LUIS pomocí klientských knihoven LUIS SDK.
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, umělal Intelligence, AI, zpracování přirozeného jazyka, NLP, porozumění přirozenému jazyku, NLU, konverzace AI, konverzace AI, AI chatovací robot, chatovací robot maker, LUIS, NLP AI, Luis AI, Azure Luis, principy přirozeného jazyka
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323231"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>Rychlý Start: Language Understanding (LUIS) SDK klientské knihovny pro vytvoření a dotazování aplikace LUIS

V tomto rychlém startu pomocí jazyků C#, Python nebo JavaScript vytvoříte a Dotazujte aplikaci LUIS pomocí klientských knihoven LUIS SDK.

Language Understanding (LUIS) vám umožňuje použít vlastní informace o strojovém učení na konverzaci uživatele, text přirozeného jazyka a předpovědět celkový význam a vyžádat si relevantní a podrobné informace.

* Klientská knihovna pro **vytváření obsahu** umožňuje vytváření, úpravy, výuku a publikování aplikace Luis. * Klientská knihovna **sady SDK předpovědi prostředí** umožňuje dotazování publikované aplikace.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto aplikaci můžete z [portálu Luis](https://www.luis.ai) odstranit a odstranit prostředky Azure z [Azure Portal](https://portal.azure.com/).

## <a name="troubleshooting"></a>Řešení potíží

* Ověřování ke klientské knihovně – chyby ověřování obvykle signalizují, že byly použity nesprávné klíčové & koncové body. V tomto rychlém startu se používá vytváření klíčového a koncového bodu pro předpověď za běhu, jako je pohodlí, ale funguje jenom v případě, že jste ještě nepoužívali měsíční kvótu. Pokud nemůžete použít klíč a koncový bod pro vytváření obsahu, musíte při přístupu ke klientské knihovně sady SDK předpovědi prostředí použít předpověď klíč modulu runtime a koncový bod.
* Vytváření entit – Pokud se při vytváření vnořené entity strojového učení, která se používá v tomto kurzu, zobrazí chyba, ujistěte se, že jste kód zkopírovali a nezměnili kód, aby vytvořil jinou entitu.
* Vytvoření příkladu projevy – Pokud se zobrazí chyba při vytváření ukázkového utterance, který se používá v tomto kurzu, ujistěte se, že jste kód zkopírovali a nezměnili kód tak, aby vytvořil jiný označený příklad.
* Školení – Pokud obdržíte chybu školení, obvykle to indikuje prázdnou aplikaci (žádné záměry s příkladem projevy) nebo aplikaci s úmysly nebo nepoškozenými entitami.
* Různé chyby – protože kód volá do klientských knihoven s objekty text a JSON, ujistěte se, že jste kód nezměnili.

Další chyby – Pokud se zobrazí chyba, která není pokrytá v předchozím seznamu, dejte nám vědět o zpětné vazbě na této stránce níže. Zahrňte programovací jazyk a verzi nainstalovaných klientských knihoven. 

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro Language Understanding (LUIS)?](what-is-luis.md)
* [Co je nového?](whats-new.md)
* [Záměry](luis-concept-intent.md), [entity](luis-concept-entity-types.md)a [Příklady projevy](luis-concept-utterance.md)a [předem připravených entit](luis-reference-prebuilt-entities.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
