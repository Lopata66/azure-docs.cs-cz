---
title: Azure Application Gateway brány firewall webových aplikací CRS a skupin pravidel CRS pravidla
description: Tato stránka obsahuje informace o skupin pravidel CRS firewallu webových aplikací a pravidla.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/11/2019
ms.author: victorh
ms.openlocfilehash: 0ad5cc76c0f4631fd60eea7d0a57e4740b6a9db3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832910"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Skupin pravidel CRS firewallu webových aplikací a pravidla

Firewall webových aplikací brány aplikací (WAF) chrání webové aplikace z nejběžnějších ohrožení zabezpečení a zneužitím. To se provádí prostřednictvím pravidla, které jsou definovány podle základní sady pravidel OWASP 3.0 nebo 2.2.9. Tato pravidla je možné zakázat na základě pravidla pomocí pravidla. Tento článek obsahuje aktuální pravidla a nabízí sady pravidel.

Následující pravidlo skupiny a pravidla jsou k dispozici při použití aplikační brány s firewallem webových aplikací.

# <a name="owasp-30tabowasp3"></a>[OWASP 3.0](#tab/owasp3)

## <a name="owasp30"></a> Sady pravidel

### <a name="General"></a> <p x-ms-format-detection="none">Obecné</p>

|RuleId|Popis|
|---|---|
|200004|Je to možné vícedílné zprávy standardu bezkonkurenční hranice.|

### <a name="crs911"></a> <p x-ms-format-detection="none">ŽÁDOST O 911 ZPŮSOB VYNUCENÍ</p>

|RuleId|Popis|
|---|---|
|911100|Metoda není povolena zásadami|


### <a name="crs913"></a> <p x-ms-format-detection="none">ŽÁDOST O 913 SKENER DETEKCE</p>

|RuleId|Popis|
|---|---|
|913100|Najít User-Agent přidružené kontrolu zabezpečení|
|913110|Najít záhlaví požadavku přidružený k kontrolu zabezpečení|
|913120|Nalezena žádost o název souboru/argument přidružený k kontrolu zabezpečení|
|913101|Najít související s klientem HTTP/generické uživatelského agenta|
|913102|Najít přidružené webové prohledávacího modulu/bot uživatelského agenta|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Popis|
|---|---|
|920100|Neplatný požadavek HTTP řádku|
|920130|Nepovedlo se analyzovat datovou část požadavku.|
|920140|Striktní ověření se nezdařilo text požadavku s více částmi.|
|920160|Hlavičku HTTP Content-Length není číselná.|
|920170|ZÍSKÁNÍ nebo HEAD žádost s obsah textu.|
|920180|Chybí hlavička Content-Length požadavek POST.|
|920190|Rozsah = poslední bajt je neplatný.|
|920210|Více/konfliktní Data záhlaví připojení nenašel.|
|920220|Pokus o útok URL kódování urážlivého příspěvku|
|920240|Pokus o útok URL kódování urážlivého příspěvku|
|920250|UTF8 Pokus o útok zneužití kódování|
|920260|Pokus o útok Unicode plné/poloviční šířku urážlivý příspěvek|
|920270|Neplatný znak v požadavku (znak null)|
|920280|Požadavku chybí hlavička hostitele|
|920290|Hlavička hostitele prázdný|
|920310|Požadavek má prázdnou hlavičky Accept|
|920311|Požadavek má prázdnou hlavičky Accept|
|920330|Záhlaví prázdného uživatelského agenta|
|920340|Požádat o obsahující obsahu, ale hlavička Content-Type chybí|
|920350|Hlavička hostitele je číselný IP adresa|
|920380|V požadavku má moc argumentů|
|920360|Příliš dlouhý název argumentu|
|920370|Hodnota argumentu je příliš dlouhý|
|920390|Překročila se velikost celkový počet argumentů|
|920400|Nahraný soubor příliš velký.|
|920410|Celkový počet odeslaných souborů příliš velký.|
|920420|Typ obsahu požadavku není povolena zásadami|
|920430|Verze protokolu HTTP není povolena zásadami|
|920440|Adresa URL přípona souboru je omezena zásadami|
|920450|Hlavička protokolu HTTP je omezený zásad (%@{MATCHED_VAR})|
|920200|Rozsah = příliš mnoho polí (6 nebo více)|
|920201|Rozsah = příliš mnoho polí pro požadavek pdf (35 nebo více)|
|920230|Zjištěn více kódování URL|
|920300|Požadavku chybí hlavičky Accept|
|920271|Neplatný znak v požadavku (bez tisknutelných znaků)|
|920320|Chybí hlavička uživatelského agenta|
|920272|Neplatný znak v požadavku (mimo tisknutelných znaků pod ascii 127)|
|920202|Rozsah = příliš mnoho polí pro požadavek pdf (6 nebo více)|
|920273|Neplatný znak v požadavku (mimo velmi přísná set)|
|920274|Neplatný znak v záhlaví požadavku (mimo velmi přísná set)|
|920460|Neobvyklé řídicí znaky|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Popis|
|---|---|
|921100|Útok podvržení požadavku HTTP.|
|921110|Požadavek HTTP pašování útoku|
|921120|Odpověď HTTP, rozdělování útoku|
|921130|Odpověď HTTP, rozdělování útoku|
|921140|Útok prostřednictvím injektáže záhlaví HTTP přes záhlaví|
|921150|Útok prostřednictvím injektáže záhlaví HTTP přes datová část (zjistil znaků CR/LF)|
|921160|Útok prostřednictvím injektáže záhlaví HTTP přes datová část (CR/LF a zjištěna název hlavičky)|
|921151|Útok prostřednictvím injektáže záhlaví HTTP přes datová část (zjistil znaků CR/LF)|
|921170|Parametr HTTP znečištění|
|921180|Znečištění HTTP parametr (% @{TX.1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Popis|
|---|---|
|930100|Cesty přechodu útoku (/.. /)|
|930110|Cesty přechodu útoku (/.. /)|
|930120|Pokus o přístup k souboru operačního systému|
|930130|Pokus o přístup k souboru s omezeným přístupem|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Popis|
|---|---|
|931100|Možných útoků zahrnutí vzdáleného souboru (RFI) = parametr adresy URL pomocí IP adresy|
|931110|Možných útoků zahrnutí vzdáleného souboru (RFI) = běžný RFI zranitelné parametr název používaný w nebo adresa URL datové části|
|931120|Je to možné vzdáleného souboru (RFI) útok = adresy URL datové části používá w a koncové otázku Označit znak (?)|
|931130|Je to možné vzdáleného souboru (RFI) útok = vypnout domény odkazu|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Popis|
|---|---|
|932120|Vzdálené spouštění příkazů Windows Powershellu příkaz nenašel =|
|932130|Spuštění vzdálených příkazů = výraz prostředí Unix nalezen|
|932140|Spuštění vzdálených příkazů = Windows pro, nebo pokud se najde příkaz|
|932160|Vzdálené spouštění příkazů prostředí Unix kód =|
|932170|Spuštění vzdálených příkazů = Shellshock (CVE 2014 6271)|
|932171|Spuštění vzdálených příkazů = Shellshock (CVE 2014 6271)|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Popis|
|---|---|
|933100|PHP injektáže = nalezena otevírací nebo uzavírací značka|
|933110|PHP injektáže = nalezen nahrávání souborů skriptů PHP|
|933120|PHP injektáže = direktivě konfiguraci nalezen|
|933130|PHP injektáže = proměnné nalezen|
|933150|PHP injektáže = název funkce s vysokým rizikem PHP nalezen.|
|933160|PHP injektáže = volání funkce PHP s vysokým rizikem, nalezena|
|933180|PHP injektáže = proměnné funkce volání nalezen|
|933151|PHP injektáže = najít název funkce PHP střední riziko|
|933131|PHP injektáže = proměnné nalezen|
|933161|PHP injektáže = PHP s nízkou hodnotu volání funkce nalezen|
|933111|PHP injektáže = nalezen nahrávání souborů skriptů PHP|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Popis|
|---|---|
|941100|XSS útoku zjištěna prostřednictvím libinjection|
|941110|Filtr XSS – kategorie 1 = Vector značky skriptu|
|941130|Filtr XSS – kategorie 3 = atribut vektoru|
|941140|Filtr XSS – kategorie 4 = Vector URI jazyka Javascript|
|941150|Filtr XSS – kategorie 5 = atributů zakázaného HTML|
|941180|Uzel ověření Blacklist klíčová slova|
|941190|Skriptování mezi servery pomocí šablony stylů|
|941200|Skriptování mezi servery pomocí VML snímků|
|941210|Obfuskovaný XSS pomocí jazyka Javascript|
|941220|Skriptování mezi servery pomocí obfuskovaný skript jazyka Visual Basic|
|941230|XSS pomocí značky 'embed.|
|941240|Skriptování mezi servery pomocí atributu "import" nebo "implementace.|
|941260|XSS tagu 'meta'|
|941270|Skriptování mezi servery pomocí "link" href|
|941280|XSS tagu "základní"|
|941290|XSS tagu "aplet.|
|941300|XSS tagu "objekt"|
|941310|US ASCII poškozený kódování XSS filtru – zjištěna útoku.|
|941330|Filtruje - IE XSS zjistil útoku.|
|941340|Filtruje - IE XSS zjistil útoku.|
|941350|Kódování UTF-7 kódování aplikace Internet Explorer XSS - zjistil útoku.|
|941320|Je to možné XSS útoku zjistil - obslužnou rutinu značky HTML|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Popis|
|---|---|
|942100|SQL zjistila útoku prostřednictvím injektáže prostřednictvím libinjection|
|942110|Útok prostřednictvím injektáže SQL: Běžné vkládání testování zjištěné|
|942130|Útok prostřednictvím injektáže SQL: Zjištěna Tautology SQL.|
|942140|Útok prostřednictvím injektáže SQL = běžné názvy DB zjistil|
|942160|Zjistí skrytá sqli testy pomocí sleep() nebo benchmark().|
|942170|Detekuje pokusy o srovnávací test a přejít do režimu spánku útok prostřednictvím injektáže SQL, včetně podmíněného dotazů|
|942190|Zjistí MSSQL provádění kódu a pokusy o shromažďování informací|
|942200|Zjistí MySQL komentář – / místo obfuskovaný injektáže a prvními ukončení|
|942230|Zjistí podmíněného pokusu o Injektáž SQL|
|942260|Zjistí, že základní ověřovací přihlášení SQL pokusí 2/3|
|942270|Hledáte základní sql injection. Běžné útoku řetězec pro mysql oracle a dalších.|
|942290|Vyhledá pokusu o Injektáž SQL základní MongoDB|
|942300|Zjistí MySQL komentáře, podmínky a injektáže ch (a) jazyka r|
|942320|Zjistí, MySQL a PostgreSQL uložené procedury/funkce injektáže|
|942330|Zjistí classic probings prostřednictvím injektáže SQL 1/2|
|942340|Zjistí, že základní ověřovací přihlášení SQL pokusí 3/3|
|942350|Zjistí vkládání MySQL UDF a manipulaci s další data nebo strukturu pokusí|
|942360|Zjistí zřetězených základní útok prostřednictvím injektáže SQL a pokusů o zadání SQLLFI|
|942370|Zjistí classic probings prostřednictvím injektáže SQL 2/2|
|942150|Útok prostřednictvím injektáže SQL|
|942410|Útok prostřednictvím injektáže SQL|
|942430|S omezením pomocí specifikátoru detekce anomálií znak SQL (args): (12) překročil počet speciálních znaků|
|942440|Zjištěna pořadí Komentář SQL.|
|942450|SQL šestnáctkové kódování identifikovat|
|942251|Zjistí injektáže HAVING|
|942460|Výstraha detekce anomálií meta znak – opakované mimoslovní znaky|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Popis|
|---|---|
|943100|Útok záznam možné relace = nastavení hodnoty souboru Cookie ve formátu HTML|
|943110|Útok záznam možné relace = název parametru ID relace s odkazující server vypnout domény|
|943120|Útok záznam možné relace = název parametru ID relace se žádné odkazující server|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a> Sady pravidel

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Popis|
|---|---|
|960911|Neplatný požadavek HTTP řádku|
|981227|Chyba Apache = neplatný identifikátor URI požadavku.|
|960912|Nepovedlo se analyzovat datovou část požadavku.|
|960914|Striktní ověření se nezdařilo text požadavku s více částmi.|
|960915|S více částmi. analyzátor zjistil možné bezkonkurenční hranice.|
|960016|Hlavičku HTTP Content-Length není číselná.|
|960011|ZÍSKÁNÍ nebo HEAD žádost s obsah textu.|
|960012|Chybí hlavička Content-Length požadavek POST.|
|960902|Neplatné použití Identity kódování.|
|960022|Můžete očekávejte záhlaví není povolený pro protokol HTTP 1.0.|
|960020|Záhlaví – Direktiva pragma vyžaduje hlavičku Cache-Control pro požadavky HTTP/1.1.|
|958291|Rozsah = pole existuje a začíná na 0.|
|958230|Rozsah = poslední bajt je neplatný.|
|958295|Více/konfliktní Data záhlaví připojení nenašel.|
|950107|Pokus o útok URL kódování urážlivého příspěvku|
|950109|Zjištěn více kódování URL|
|950108|Pokus o útok URL kódování urážlivého příspěvku|
|950801|UTF8 Pokus o útok zneužití kódování|
|950116|Pokus o útok Unicode plné/poloviční šířku urážlivý příspěvek|
|960901|Neplatný znak v požadavku|
|960018|Neplatný znak v požadavku|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Popis|
|---|---|
|960008|Požadavku chybí hlavička hostitele|
|960007|Hlavička hostitele prázdný|
|960015|Požadavku chybí hlavičky Accept|
|960021|Požadavek má prázdnou hlavičky Accept|
|960009|Požadavku chybí záhlaví Agent uživatele|
|960006|Záhlaví prázdného uživatelského agenta|
|960904|Požádat o obsahující obsahu, ale hlavička Content-Type chybí|
|960017|Hlavička hostitele je číselný IP adresa|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Popis|
|---|---|
|960209|Příliš dlouhý název argumentu|
|960208|Hodnota argumentu je příliš dlouhý|
|960335|V požadavku má moc argumentů|
|960341|Překročila se velikost celkový počet argumentů|
|960342|Nahraný soubor příliš velký.|
|960343|Celkový počet odeslaných souborů příliš velký.|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Popis|
|---|---|
|960032|Metoda není povolena zásadami|
|960010|Typ obsahu požadavku není povolena zásadami|
|960034|Verze protokolu HTTP není povolena zásadami|
|960035|Adresa URL přípona souboru je omezena zásadami|
|960038|Hlavička protokolu HTTP je omezen pomocí zásad|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Popis|
|---|---|
|990002|Žádost o označuje, že kontrola zabezpečení zkontrolovat webu|
|990901|Žádost o označuje, že kontrola zabezpečení zkontrolovat webu|
|990902|Žádost o označuje, že kontrola zabezpečení zkontrolovat webu|
|990012|Neautorizovaný webu prohledávacího modulu|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Popis|
|---|---|
|960024|Výstraha detekce anomálií meta znak – opakované mimoslovní znaky|
|950008|Vkládání značky aplikace nezdokumentovaný ColdFusion|
|950010|Útok prostřednictvím injektáže LDAP|
|950011|Vkládání SSI útoku|
|950018|Adresa URL univerzální XSS PDF zjištěna.|
|950019|Útok prostřednictvím injektáže e-mailu|
|950012|Útok podvržení požadavku HTTP.|
|950910|Odpověď HTTP, rozdělování útoku|
|950911|Odpověď HTTP, rozdělování útoku|
|950117|Vzdálený soubor zahrnutí útoku|
|950118|Vzdálený soubor zahrnutí útoku|
|950119|Vzdálený soubor zahrnutí útoku|
|950120|Je to možné vzdáleného souboru (RFI) útok = vypnout domény odkazu|
|981133|Pravidlo 981133|
|981134|Pravidlo 981134|
|950009|Útok záznam relace|
|950003|Záznam relace|
|950000|Záznam relace|
|950005|Vzdálený soubor k pokusu o přístup|
|950002|Přístup k systému příkaz|
|950006|Injektáž příkazů systému|
|959151|Útok prostřednictvím injektáže PHP|
|958976|Útok prostřednictvím injektáže PHP|
|958977|Útok prostřednictvím injektáže PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Popis|
|---|---|
|981231|Zjištěna pořadí Komentář SQL.|
|981260|SQL šestnáctkové kódování identifikovat|
|981320|Útok prostřednictvím injektáže SQL = běžné názvy DB zjistil|
|981300|Pravidlo 981300|
|981301|Pravidlo 981301|
|981302|Pravidlo 981302|
|981303|Pravidlo 981303|
|981304|Pravidlo 981304|
|981305|Pravidlo 981305|
|981306|Pravidlo 981306|
|981307|Pravidlo 981307|
|981308|Pravidlo 981308|
|981309|Pravidlo 981309|
|981310|Pravidlo 981310|
|981311|Pravidlo 981311|
|981312|Pravidlo 981312|
|981313|Pravidlo 981313|
|981314|Pravidlo 981314|
|981315|Pravidlo 981315|
|981316|Pravidlo 981316|
|981317|Výstrahy detekce anomálií SQL příkazu SELECT|
|950007|Útok prostřednictvím injektáže SQL skrytá|
|950001|Útok prostřednictvím injektáže SQL|
|950908|Útok prostřednictvím injektáže SQL.|
|959073|Útok prostřednictvím injektáže SQL|
|981272|Zjistí skrytá sqli testy pomocí sleep() nebo benchmark().|
|981250|Detekuje pokusy o srovnávací test a přejít do režimu spánku útok prostřednictvím injektáže SQL, včetně podmíněného dotazů|
|981241|Zjistí podmíněného pokusu o Injektáž SQL|
|981276|Hledáte základní sql injection. Běžné útoku řetězec pro mysql oracle a dalších.|
|981270|Vyhledá pokusu o Injektáž SQL základní MongoDB|
|981253|Zjistí, MySQL a PostgreSQL uložené procedury/funkce injektáže|
|981251|Zjistí vkládání MySQL UDF a manipulaci s další data nebo strukturu pokusí|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Popis|
|---|---|
|973336|Filtr XSS – kategorie 1 = Vector značky skriptu|
|973338|Filtr XSS – kategorie 3 = Vector URI jazyka Javascript|
|981136|Pravidlo 981136|
|981018|Pravidlo 981018|
|958016|Útoky skriptování napříč weby (XSS)|
|958414|Útoky skriptování napříč weby (XSS)|
|958032|Útoky skriptování napříč weby (XSS)|
|958026|Útoky skriptování napříč weby (XSS)|
|958027|Útoky skriptování napříč weby (XSS)|
|958054|Útoky skriptování napříč weby (XSS)|
|958418|Útoky skriptování napříč weby (XSS)|
|958034|Útoky skriptování napříč weby (XSS)|
|958019|Útoky skriptování napříč weby (XSS)|
|958013|Útoky skriptování napříč weby (XSS)|
|958408|Útoky skriptování napříč weby (XSS)|
|958012|Útoky skriptování napříč weby (XSS)|
|958423|Útoky skriptování napříč weby (XSS)|
|958002|Útoky skriptování napříč weby (XSS)|
|958017|Útoky skriptování napříč weby (XSS)|
|958007|Útoky skriptování napříč weby (XSS)|
|958047|Útoky skriptování napříč weby (XSS)|
|958410|Útoky skriptování napříč weby (XSS)|
|958415|Útoky skriptování napříč weby (XSS)|
|958022|Útoky skriptování napříč weby (XSS)|
|958405|Útoky skriptování napříč weby (XSS)|
|958419|Útoky skriptování napříč weby (XSS)|
|958028|Útoky skriptování napříč weby (XSS)|
|958057|Útoky skriptování napříč weby (XSS)|
|958031|Útoky skriptování napříč weby (XSS)|
|958006|Útoky skriptování napříč weby (XSS)|
|958033|Útoky skriptování napříč weby (XSS)|
|958038|Útoky skriptování napříč weby (XSS)|
|958409|Útoky skriptování napříč weby (XSS)|
|958001|Útoky skriptování napříč weby (XSS)|
|958005|Útoky skriptování napříč weby (XSS)|
|958404|Útoky skriptování napříč weby (XSS)|
|958023|Útoky skriptování napříč weby (XSS)|
|958010|Útoky skriptování napříč weby (XSS)|
|958411|Útoky skriptování napříč weby (XSS)|
|958422|Útoky skriptování napříč weby (XSS)|
|958036|Útoky skriptování napříč weby (XSS)|
|958000|Útoky skriptování napříč weby (XSS)|
|958018|Útoky skriptování napříč weby (XSS)|
|958406|Útoky skriptování napříč weby (XSS)|
|958040|Útoky skriptování napříč weby (XSS)|
|958052|Útoky skriptování napříč weby (XSS)|
|958037|Útoky skriptování napříč weby (XSS)|
|958049|Útoky skriptování napříč weby (XSS)|
|958030|Útoky skriptování napříč weby (XSS)|
|958041|Útoky skriptování napříč weby (XSS)|
|958416|Útoky skriptování napříč weby (XSS)|
|958024|Útoky skriptování napříč weby (XSS)|
|958059|Útoky skriptování napříč weby (XSS)|
|958417|Útoky skriptování napříč weby (XSS)|
|958020|Útoky skriptování napříč weby (XSS)|
|958045|Útoky skriptování napříč weby (XSS)|
|958004|Útoky skriptování napříč weby (XSS)|
|958421|Útoky skriptování napříč weby (XSS)|
|958009|Útoky skriptování napříč weby (XSS)|
|958025|Útoky skriptování napříč weby (XSS)|
|958413|Útoky skriptování napříč weby (XSS)|
|958051|Útoky skriptování napříč weby (XSS)|
|958420|Útoky skriptování napříč weby (XSS)|
|958407|Útoky skriptování napříč weby (XSS)|
|958056|Útoky skriptování napříč weby (XSS)|
|958011|Útoky skriptování napříč weby (XSS)|
|958412|Útoky skriptování napříč weby (XSS)|
|958008|Útoky skriptování napříč weby (XSS)|
|958046|Útoky skriptování napříč weby (XSS)|
|958039|Útoky skriptování napříč weby (XSS)|
|958003|Útoky skriptování napříč weby (XSS)|
|973300|Je to možné XSS útoku zjistil - obslužnou rutinu značky HTML|
|973301|Útoky XSS zjistil|
|973302|Útoky XSS zjistil|
|973303|Útoky XSS zjistil|
|973304|Útoky XSS zjistil|
|973305|Útoky XSS zjistil|
|973306|Útoky XSS zjistil|
|973307|Útoky XSS zjistil|
|973308|Útoky XSS zjistil|
|973309|Útoky XSS zjistil|
|973311|Útoky XSS zjistil|
|973313|Útoky XSS zjistil|
|973314|Útoky XSS zjistil|
|973331|Filtruje - IE XSS zjistil útoku.|
|973315|Filtruje - IE XSS zjistil útoku.|
|973330|Filtruje - IE XSS zjistil útoku.|
|973327|Filtruje - IE XSS zjistil útoku.|
|973326|Filtruje - IE XSS zjistil útoku.|
|973346|Filtruje - IE XSS zjistil útoku.|
|973345|Filtruje - IE XSS zjistil útoku.|
|973324|Filtruje - IE XSS zjistil útoku.|
|973323|Filtruje - IE XSS zjistil útoku.|
|973348|Filtruje - IE XSS zjistil útoku.|
|973321|Filtruje - IE XSS zjistil útoku.|
|973320|Filtruje - IE XSS zjistil útoku.|
|973318|Filtruje - IE XSS zjistil útoku.|
|973317|Filtruje - IE XSS zjistil útoku.|
|973329|Filtruje - IE XSS zjistil útoku.|
|973328|Filtruje - IE XSS zjistil útoku.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Popis|
|---|---|
|950103|Cesty přechodu útoku|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Popis|
|---|---|
|950110|Přístup přes zadní vrátka|
|950921|Přístup přes zadní vrátka|
|950922|Přístup přes zadní vrátka|

---

## <a name="next-steps"></a>Další postup

Zjistěte, jak zakázat pravidla firewallu webových aplikací: [Přizpůsobit pravidla firewallu webových aplikací](application-gateway-customize-waf-rules-portal.md)