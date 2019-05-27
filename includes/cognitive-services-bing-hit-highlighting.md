---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: fccc036a5e0422508f7ebc3370a4b5faa5176dc2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124777"
---
Bingu podporuje zvýraznění shod, které označí termíny dotazu (nebo jiné podmínky této Bingu najde relevantní) v zobrazení řetězce některé části odpovědi. Například na webové stránce společnosti `name`, `displayUrl`, a `snippet` pole označit termíny dotazu.

Ve výchozím nastavení neobsahuje Bingu zvýraznění značky v zobrazení řetězce. Zahrnout značek, zahrnout `textDecorations` parametr v žádosti o dotazu a nastavte ho na **true**. Bing označí termíny dotazu pomocí znaků E000 a E001 Unicode pro označení začátku a konci období. Například pokud termín dotazu je řízení ledě a buď termín existuje v poli, termín je ohraničen přístupů zvýraznění znaky jak je znázorněno v následujícím příkladu:  
  
![Zvýrazňování položek](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Před zobrazením řetězce v uživatelském rozhraní, by znaky Unicode nahradit znaky, které jsou vhodné pro váš formát zobrazení. Například chcete-li zobrazit text ve formátu HTML, můžete chtít zvýraznit termín dotazu tak, že nahradíte E000 s < b\> a E001 s < /b\>. Pokud nechcete použít formátování, odeberte z řetězce značky. 

Bing poskytuje možnost používat znaky znakové sady Unicode nebo značky HTML jako značky. Pokud chcete zadat značky, které používat, patří `textFormat` parametr dotazu. Chcete-li označit obsah se znaky Unicode, nastavte `textFormat` Raw (výchozí) a označí obsah pomocí značky jazyka HTML, nastavte `textFormat` do formátu HTML. 
  
Pokud `textDecorations` je **true**, Bingu může zahrnovat následující značky v zobrazení řetězce odpovědi. Pokud neexistuje žádný ekvivalent HTML, buněk tabulky HTML je prázdný.

|Kódování Unicode|HTML|Popis
|-|-|-
|U + E000|\<b>|Označuje začátek výrazu dotazu (zvýrazňování shod)
|U+E001|\</b>|Označuje konec termín dotazu
|U+E002|\<i>|Označuje začátek kurzívou obsahu 
|U+E003|\</i>|Označuje konec obsahu kurzívou
|U+E004|\<br/>|Označuje konec řádku
|U+E005||Označuje začátek telefonní číslo
|U+E006||Označuje konec telefonní číslo
|U+E007||Označuje začátek adresy
|U+E008||Označuje konec adresu
|U+E009|\&nbsp;|Označí pevná mezera
|U + E00C|\<strong>|Označuje začátek tučného písma obsahu
|U+E00D|\</strong>|Označuje konec tučného písma obsahu
|U + E00E||Označuje konec obsahu, jehož pozadí by měla být nižší než jeho okolního pozadí
|U+E00F||Označuje konec obsahu, jehož pozadí by měla být nižší než jeho okolního pozadí
|U+E010||Označuje konec obsahu, jehož pozadí by měl být o něco tmavší než jeho okolního pozadí
|U + E011||Označuje konec obsahu, jehož pozadí by měl být o něco tmavší než jeho okolního pozadí
|U + E012|\<del>|Označuje konec obsahu, který by měl být přeškrtnutá.
|U+E013|\</del>|Označuje konec obsahu, který by měl být přeškrtnutá.
|U+E016|\<sub>|Označuje začátek dolního indexu obsah
|U+E017|\</sub>|Označuje konec obsahu dolního indexu
|U+E018|\<sup>|Označuje začátek horní index obsahu
|U+E019|\</sup>|Označuje konec obsahu horní index

Následující příklad ukazuje `Computation` odpověď, která obsahuje dolního indexu značky pro termín log(2) dotazu. `expression` Pole obsahuje značky pouze tehdy, pokud `textDecoration` je **true**.

![Výpočet značky](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Pokud požadavek nezažádali dekorace, výraz by log10(2). 
  
