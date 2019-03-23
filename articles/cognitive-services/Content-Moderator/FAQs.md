---
title: Časté otázky – Content Moderator
titlesuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy o Content Moderatoru.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351129"
---
# <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

### <a name="what-does-my-content-moderator-subscription-include"></a>Co dělá Moje Content Moderator předplatného?

Předplatné Content Moderator zahrnuje přístup k nástroj pro recenze a rozhraní API. Můžete se rozhodnout, zda chcete použít jeden nebo druhý nebo obou v závislosti na vašich obchodních potřeb.

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Jaká jsou omezení obsahu, který je možné moderovat pomocí tohoto rozhraní API?

Při použití rozhraní API, musí mít minimálně 128 pixelů a maximální velikost 4 MB obrázky. Text může obsahovat nejvýše 1024 znaků. Neexistuje žádné omezení na době trvání videa.

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Co se stane, když obsah předaný rozhraní Text API nebo Image API přesáhne velikostní limity?

Rozhraní text API vrátí kód chyby, která informuje o tom, že text je delší, než je povoleno. Rozhraní image API také vrátí kód chyby, která informuje o tom, že obrázek nesplňuje požadavky na velikost.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Udržet obrázky, text nebo videa, která odesílají moderován?

Váš obsah je zcela na vás a nemusí být zachováno microsoftem bez vašeho souhlasu. Společnost Microsoft používá oborově špičkový bezpečnostní opatření k ochraně obsahu.

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Můžu použít Content Moderator na obrazovku pro neplatný podřízený využívání Image?

Ne. Však můžete použít kvalifikovaný organizace [PhotoDNA Cloudovou službu](https://www.microsoft.com/photodna "cloudové služby Microsoftu PhotoDNA") na obrazovku pro tento typ obsahu.

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Kolik zkontrolovat týmy můžou spojení uživatele? Může uživatel přepínat mezi týmy?

Uživatele můžete připojit pouze jeden tým najednou.

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Jaký druh člena týmu role jsou podporovány nástrojem pro kontrolu? Jaký je mezi nimi rozdíl?

Nástroje pro recenze aktuálně umožňuje přiřazení rolí správce a revidujícího. Správci, které můžete pozvat další uživatele a mají přístup k nastavení konfigurace, zatímco revidující můžete zkontrolovat výsledky při moderování a značky nebo Odtagujte obsah.

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Co jsou klíčová slova? Podporuje nástroj pro recenze vlastní značky?

Klíčové slovo je jedna nebo dvoupísmenné krátký kód, který označuje moderování příznak, jako je například "a" pro dospělé, "r" pro pikantní a tak dále. Správci můžou určit nové značky pro své obchodní potřeby.

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Kolik členů může mít v Můj tým recenzentů?

Může mít maximálně pět členů týmu, včetně správce v týmu.
