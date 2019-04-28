---
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737179"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|routePrefix|rozhraní api|Předpona trasy, které platí pro všechny trasy. Chcete-li odebrat výchozí předponu použijte prázdný řetězec. |
|maxOutstandingRequests|200*|Maximální počet nevyřízených požadavků, které jsou uloženy v daném okamžiku. Toto omezení zahrnuje požadavků, které jsou zařazeny do fronty, ale nebyla spuštěna, a také v průběhu provádění. Všechny příchozí žádosti o nad tento limit se odmítne kvůli odpověď 429 "Příliš zaneprázdněn a nemůže". Který umožňuje volajícím využívat strategie opakování podle času a také vám umožňuje řídit maximální požadavek latenci. Tím je řízeno jen služby Řízení front, který se nachází v rámci cesty spuštění skriptu hostitele. Další front, jako jsou fronty požadavků ASP.NET zůstanou v platnosti a toto nastavení nemá vliv. * Výchozí verzi 1.x je bez vazby. Výchozí hodnota pro verzi 2.x v plánu consumption je 200. Výchozí hodnota pro verzi 2.x v vyhrazený plán je bez vazby.|
|maxConcurrentRequests|100 *|Maximální počet funkce protokolu http, které budou spuštěny paralelně. Umožňuje řízení souběžnosti, který může pomoct spravovat využití prostředků. Například můžete mít funkce protokolem http, který používá velké množství systémových prostředků (procesoru/paměti/sockets) tak, že je způsobuje problémy, když je souběžnost příliš vysoká. Nebo můžete mít funkci, která umožňuje odchozí požadavky na služby třetí strany a tato volání musí být rychlost je omezená. V těchto případech může pomoci použití omezení tady. * Výchozí verzi 1.x je bez vazby. Výchozí hodnota pro verzi 2.x v plánu consumption je 100. Výchozí hodnota pro verzi 2.x v vyhrazený plán je bez vazby.|
|dynamicThrottlesEnabled|Hodnota TRUE *|V případě, že povoleno, toto nastavení příčiny v kanálu zpracování požadavků na výkon systému pravidelně kontrolovat čítače, jako je připojení, vlákna a procesy/paměti/procesoru/další a pokud některý z těchto čítačů jsou nad integrovanou vysokou prahovou hodnotou (80 %), si vyžádá bude odmítne kvůli odpověď 429 "Příliš zaneprázdněn a nemůže", dokud counter(s) vrátit na běžnou úroveň. * Výchozí verzi 1.x je false. Výchozí verze 2.x v plánu consumption má hodnotu true. Výchozí hodnota pro verzi 2.x v vyhrazený plán má hodnotu false.|

<!-- ms.date: 04/26/2019 -->