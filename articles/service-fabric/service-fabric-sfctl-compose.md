---
title: Azure Service Fabric CLI - sfctl compose | Dokumentace Microsoftu
description: Popisuje, Service Fabric CLI sfctl compose příkazy.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 4b5cbb4a24b61de7e64a52ef950deedab3eec263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837262"
---
# <a name="sfctl-compose"></a>sfctl compose
Vytvářet, odstraňovat a spravovat aplikace Docker Compose.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| create | Service Fabric vytvoří nasazení compose. |
| list | Získá seznam compose nasazení vytvořeno v clusteru Service Fabric. |
| odebrat | Odstraní existující Service Fabric tvoří nasazení z clusteru. |
| status | Získá informace o službě Service Fabric nasazení compose. |
| upgrade | Spustí se upgrade nasazení compose v clusteru Service Fabric. |
| upgrade-rollback | Spustí vrácení nasazení compose upgradovat v clusteru Service Fabric. |
| upgrade-status | Získá podrobnosti pro upgrade na nejnovější provést na této platformě Service Fabric nasazení compose. |

## <a name="sfctl-compose-create"></a>sfctl compose vytvořit
Service Fabric vytvoří nasazení compose.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název nasazení | Název nasazení. |
| --[povinné] cesta souboru | Cesta k cílovému souboru Docker Compose. |
| --šifrované pass | Místo vás vyzve k zadání heslo registru kontejneru, použijte již šifrované heslo. |
| --has-pass | Vyzve k zadání hesla do registru kontejneru. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --user | Uživatelské jméno pro připojení k registru kontejneru. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-compose-list"></a>sfctl compose list
Získá seznam compose nasazení vytvořeno v clusteru Service Fabric.

Získá stav o nasazení compose, které byly vytvořeny nebo se právě vytváří v clusteru Service Fabric. Odpověď obsahuje název, stav a další podrobnosti o nasazení compose. Pokud seznam nasazení se nevejdou na stránce, vrátí se jednotlivé stránky výsledků a token pro pokračování, které můžete použít k získání na další stránku.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --max-results | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-compose-remove"></a>sfctl compose remove
Odstraní existující Service Fabric tvoří nasazení z clusteru.

Nasazení compose odstraní existující Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název nasazení | Identita nasazení. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-compose-status"></a>sfctl compose status
Získá informace o službě Service Fabric nasazení compose.

Vrátí stav nasazení compose, který byl vytvořen nebo se právě vytváří v clusteru Service Fabric a jejichž název odpovídá zadanému jako parametr. Odpověď obsahuje název, stav a další podrobnosti o tomto nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název nasazení | Identita nasazení. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgradu
Spustí se upgrade nasazení compose v clusteru Service Fabric.

Ověří zadaný parametry upgradu a začne se upgradovat nasazení, pokud jsou platné parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název nasazení | Název nasazení. |
| --[povinné] cesta souboru | Cesta k cíli Docker compose souboru. |
| --default-svc-type-health-map | JSON kódovaný slovník, který popisu zásad stavu, používá k vyhodnocení stavu služeb. |
| --šifrované pass | Místo vás vyzve k zadání heslo registru kontejneru, použijte již šifrované heslo. |
| --selhání akce | Možné hodnoty zahrnují\: "Neplatný", "Vrácení zpět", "Ruční". |
| --force restartování | Procesy se během upgradu vynuceně restartují, i v případě, že nedošlo ke změně verze kódu. <br><br> Upgrade pouze změny konfigurace nebo data. |
| --has-pass | Vyzve k zadání hesla do registru kontejneru. |
| --health-check-retry | Dlouhá doba mezi pokusy o provádění kontroly stavu, pokud aplikace nebo clusteru není v pořádku. |
| --stabilní verze stavu zaškrtnutí | Množství času, aplikace nebo clusteru musí zůstane v dobrém stavu před provedením upgradu k další upgradovací doméně. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --health-check-wait | Doba čekání po dokončení upgradu domény před zahájením stavu kontroluje procesu. |
| --replica-set-check | Maximální množství času blokování zpracování logických sítí a zabránit ztrátě dostupnosti, když dochází k neočekávaným problémům. <br><br> Když tento časový limit vyprší platnost, zpracování upgradovací doména bude pokračovat bez ohledu na problémy s dostupností ztráty. Časový limit se resetuje na začátku každé upgradovací doméně. Platné hodnoty jsou 0 až 42949672925 (včetně). |
| --svc-type-health-map | JSON kódovaného seznam objektů, které popisují zásady stavu, používá k vyhodnocení stavu různými typy služeb. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --unhealthy-app | Maximální povolené procento žádostí, není v pořádku, před ohlášení chyby. <br><br> Například pokud chcete povolit 10 % aplikací v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento aplikací, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje alespoň jedna aplikace není v pořádku, stav se vyhodnotí jako upozornění. To se vypočítá jako podíl počtu poškozené aplikace přes celkový počet instancí aplikace v clusteru. |
| --upgrade-domain-timeout | Množství času každé domény upgradu musí dokončit před provedením FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --upgrade – typ | Výchozí\: se zajištěním provozu. |
| --upgrade-mode | Možné hodnoty zahrnují\: "Neplatný", "UnmonitoredAuto", "UnmonitoredManual", "Sledované".  Výchozí\: UnmonitoredAuto. |
| --upgrade-timeout | Množství času celkové upgrade musí dokončit před provedením FailureAction. <br><br> Nejprve je interpretován jako řetězec představující dobu trvání ISO 8601. Pokud se to nepodaří, je interpretován jako číslo představující počet milisekund. |
| --user | Uživatelské jméno pro připojení k registru kontejneru. |
| --warning-as-error | Určuje, zda jsou upozornění zpracována stejným závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose vrácení upgradu zpět
Spustí vrácení nasazení compose upgradovat v clusteru Service Fabric.

Vrácení zpět service fabric tvoří upgrade nasazení.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název nasazení | Identita nasazení. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose status upgradu
Získá podrobnosti pro upgrade na nejnovější provést na této platformě Service Fabric nasazení compose.

Vrátí informace o stavu upgradu nasazení compose spolu s podrobnostmi k ladění problémů se stavem žádosti o podporu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název nasazení | Identita nasazení. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Nastavit](service-fabric-cli.md) Service Fabric CLI.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).