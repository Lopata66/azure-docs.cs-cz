---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160962"
---
Úloha vytvoří výstupní soubor JSON, který obsahuje metadata o zjištěných a sleduje tváře. Metadata obsahují souřadnice označující umístění tváří, jakož i face ID číslo označující sledování tohoto jednotlivce. Face ID čísla jsou náchylné k obnovit okolností, když dojde ke ztrátě nebo překrývajících se v rámci, přední rozpoznávání tváře výsledkem několika jednotlivcům získávání přiřadit víc ID.

Ve výstupu JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>JSON kořenové prvky

| Element | Popis |
| --- | --- |
| version |To se vztahuje na verzi rozhraní API pro Video. |
| Časová osa |"Značky" za sekundu videa. |
| offset |Toto je posunutí čas pro časové razítko. Ve verzi 1.0 rozhraní API pro Video bude vždy 0. V budoucnu scénáře, které podporujeme, tato hodnota se může změnit. |
| Šířka, vysoký |Šířka a vysoký snímek výstup videa, v pixelech.|
| snímkovou |Počet snímků ve videu za sekundu. |
| [fragmenty](#fragments-json-elements) |Metadata je rozdělený do bloků dat až do různých segmentů zvaných fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události. |

### <a name="fragments-json-elements"></a>Elementy fragmentů JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první událost v "impulzech." |
| Doba trvání |Délka fragment v "impulzech." |
| index | (Platí jenom pro Azure Media Redactor) definuje index snímku aktuálního události. |
| interval |Interval každý záznam událostí v rámci fragment v "impulzech." |
| stránka events |Každá událost obsahuje tváří zjištěna a sledovány v rámci této dobu trvání. Je to pole událostí. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály. Prázdné závorky [] znamená, že nebyly zjištěny žádné tváře. |
| id |ID tváře, která je sledována. Toto číslo může tak nechtěně změnit, pokud stane nezjištěné tváře. Dané osoby by mělo mít stejné ID v rámci celkového videa, ale to nemůže být zaručena z důvodu omezení v algoritmu detekce (uzavření atd.). |
| x, y |Vlevo nahoře X a Y souřadnice rozpoznávání tváře ohraničujícího rámečku v normalizovaných škálování od 0,0 do 1,0. <br/>-X a Y souřadnice jsou relativní šířku vždycky, takže pokud máte na výšku videa (nebo vzhůru nohama, v případě iOS), budete muset transponuje souřadnice odpovídajícím způsobem. |
| Šířka, výška |Šířku a výšku plochy ohraničujícího rámečku v normalizovaných škálování od 0,0 do 1,0. |
| facesDetected |Tím se nachází na konci výsledky JSON a shrnuje počet tváří, které algoritmus zjištěného videa. Protože ID můžou neúmyslně resetovat, pokud tváře stane nezjištěné (například typ písma vypnul obrazovky, vyhledá okamžitě), toto číslo nemusí vždy rovná true počet tváří na videu. |
