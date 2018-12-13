---
title: Splunk ke službě Azure Log Analytics | Dokumentace Microsoftu
description: Pomoc uživatelům, kteří znají Splunk studium dotazovací jazyk Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 61f0cff661c79f994a5b3c20646996f617a31b7e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882482"
---
# <a name="splunk-to-log-analytics"></a>Splunk ke službě Log Analytics

Tento článek je určený jako pomoc uživatelům, kteří znají Splunk studium dotazovací jazyk Log Analytics. Přímé porovnání mezi dvěma pochopit klíčové rozdíly a podobnosti probíhají, kde můžete využít své stávající znalosti.

## <a name="structure-and-concepts"></a>Struktura a koncepty

Následující tabulka porovnává koncepty a datových struktur mezi Splunk a Log Analytics.

 | Koncept  | Splunk | Log Analytics |  Poznámka
 | --- | --- | --- | ---
 | Jednotka nasazení  | Cluster |  Cluster |  Log Analytics umožňuje libovolné různé dotazy clusteru. Splunk je nepodporuje. |
 | Mezipaměti dat |  kontejnery  |  Zásady ukládání do mezipaměti a jejich uchovávání |  Určuje období a ukládání do mezipaměti úrovně pro data. Toto nastavení přímo ovlivňuje výkon dotazů a náklady na nasazení. |
 | Logický oddíl dat  |  index  |  databáze  |  Umožňuje logické rozdělení data. Obou implementacích povolit sjednocení a propojení mezi tyto oddíly. |
 | Strukturované událost metadat | neuvedeno | tabulka |  Splunk nemá koncept vystaveni vyhledávací jazyk metadat události. Log Analytics nemá koncept tabulku, která má sloupce. Každá instance události je namapována na řádek. |
 | Datový záznam | událost | řádek |  Terminologie pouze změny. |
 | Atribut záznam dat | Pole |  Sloupec |  Ve službě Log Analytics je předdefinovaná jako součást struktura tabulky. Každá událost ve Splunku, má svou vlastní sadu polí. |
 | Typy | datový typ |  datový typ |  Log Analytics datové typy jsou více explicitní, jako jsou nastaveny na sloupce. Oba se budou moct pracovat dynamicky datových typů a zhruba ekvivalentní sadu datových typů včetně podpory JSON. |
 | Dotazy a hledání  | hledat | query |  Koncepty jsou v podstatě stejné mezi Log Analytics a Splunk. |
 | Čas ingestování události | Systémový čas | ingestion_time() |  Každé události ve Splunku, získá systému časové razítko času, které události se indexovat. V Log Analytics můžete definovat zásadu ingestion_time, který zpřístupňuje systémový sloupec, který může být odkazováno pomocí funkce ingestion_time(). |

## <a name="functions"></a>Functions

Následující tabulka obsahuje funkce v Log Analytics, která jsou rovnocenná Splunk funkce.

|Splunk | Log Analytics |Poznámka
|---|---|---
|strcat – | strcat()| (1) |
|split  | split() | (1) |
|If     | IFF()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|horní<br>Nižší |ToUpper()<br>ToLower() byly|(1) |
| nahradit | replace() | (1)<br> Všimněte si také, že při `replace()` přijímá tři parametry v obou produktů, parametry jsou různé. |
| substr – | substring() | (1)<br>Všimněte si také, že používá Splunk založen na jedničce indexy. Log Analytics zaznamená indexy od nuly. |
| ToLower |  ToLower() byly | (1) |
| ToUpper | ToUpper() | (1) |
| shoda | odpovídá regulárnímu |  (2)  |
| regulární výraz | odpovídá regulárnímu | Ve Splunku `regex` je operátor. Ve službě Log Analytics je relační operátor. |
| searchmatch | == | Ve Splunku `searchmatch` umožňuje vyhledat přesný řetězec.
| náhodná | rand()<br>rand(n) | Splunk vaší funkce vrátí číslo od nuly do 2<sup>31</sup>-1. Log Analytics vrátí číslo v rozsahu od 0,0 do 1,0, nebo pokud parametr zadán, mezi 0 a n-1.
| teď | now() | (1)
| relative_time | ToTimeSpan() | (1)<br>Ve službě Log Analytics je od Splunk ekvivalent relative_time (datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Například <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> stane <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) ve Splunku, funkce se vyvolala s `eval` operátor. Ve službě Log Analytics se používá jako součást `extend` nebo `project`.<br>(2) ve Splunku, funkce se vyvolala s `eval` operátor. Ve službě Log Analytics můžete použít s `where` operátor.


## <a name="operators"></a>Operátory

V následujících částech jsou uvedeny příklady použití různých operátorů mezi Splunk a Log Analytics.

> [!NOTE]
> Pro účely následující příklad pole Splunk _pravidlo_ mapuje na tabulku v Azure Log Analytics a na Splunk výchozí časové razítko map pro analýzu protokolů _ingestion_time()_ sloupce.

### <a name="search"></a>Search
Ve Splunku, můžete vynechat `search` – klíčové slovo a zadejte řetězec bez uvozovek. Ve službě Azure Log Analytics je nutné spustit každé hledání s použitím `find`, bez uvozovek řetězec je název sloupce a hodnota vyhledávání musí být řetězec v uvozovkách. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Start dotazů Azure Log Analytics v tabulkovém výsledku, kde nastavit filtr. Filtrování ve Splunku, je výchozí operaci v aktuální index. Můžete také použít `where` operátor v Splunk, ale nedoporučuje.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **kde** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Získávání n události/řádků pro kontrolu 
Azure Log Analytics podporuje také `take` jako alias pro `limit`. Ve Splunku, pokud jsou řazeny výsledky `head` vrátí prvních n výsledků. Ve službě Azure Log Analytics omezení není seřazené, ale vrátí prvních n řádků, které se nacházejí.

| |  | |
|:---|:---|:---|
| Splunk | **hlavní** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **Limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Získat prvních n události/řádků seřazené podle pole nebo sloupec
Dolní výsledků ve Splunku použijete `tail`. Ve službě Azure Log Analytics můžete určit pořadí směr s `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **hlavní** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **nahoru** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozšíření výsledku sadě pomocí nového pole nebo sloupce
Splunk má také `eval` funkce, která nemá být srovnatelné s `eval` operátor. Oba `eval` operátor ve Splunku a `extend` operátor ve službě Azure Log Analytics podporují jenom skalární funkce a aritmetické operátory.

| |  | |
|:---|:---|:---|
| Splunk | **(V angličtině)** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **Rozšíření** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Přejmenovat 
Azure Log Analytics používá stejný operátor přejmenovat a vytvořit nové pole. Splunk má dva samostatné operátory, `eval` a `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Přejmenovat** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **Rozšíření** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formát výsledky/projekce
Splunk pravděpodobně nemají podobně jako operátor `project-away`. Uživatelské rozhraní můžete použít k filtrování tokeny pole.

| |  | |
|:---|:---|:---|
| Splunk | **Tabulka** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **Projekt**<br>**tokeny pro projekt** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregace
Zobrazit [agregace v dotazy Log Analytics](aggregations.md) pro jinou agregační funkce.

| |  | |
|:---|:---|:---|
| Splunk | **Statistiky** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **shrnutí** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Spojit
Spojení ve Splunku má významné omezení. Poddotaz má limit 10000 výsledky (nastavte v konfiguračním souboru nasazení) a existuje digitálních omezený počet spojení.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | Připojte se k Client.Id maximum = 0 [vyhledat nejbližší Event.Rule="150310.0 = - 24h" Data.Hresult= 2147221040]</code> |
| Log Analytics | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Seřadit
Ve Splunku seřadit v vzestupném pořadí, je nutné použít `reverse` operátor. Azure Log Analytics podporuje také definování kam chcete vložit hodnoty Null, na začátku nebo na konci.

| |  | |
|:---|:---|:---|
| Splunk | **Řazení** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **Řadit podle** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Rozbalte více hodnot
Toto je podobné operátor v Splunk a Log Analytics.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Omezující vlastnosti výsledky, zajímavé pole
Na portálu Log Analytics je přístupný jenom v prvním sloupci. Všechny sloupce jsou k dispozici prostřednictvím rozhraní API.

| |  | |
|:---|:---|:---|
| Splunk | **Pole** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **omezující vlastnosti** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Duplicitní zrušení
Můžete použít `summarize arg_min()` místo toho pořadí, které získá vybrali záznam.

| |  | |
|:---|:---|:---|
| Splunk | **Při odstraňování duplicitních dat** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **shrnutí arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Další postup

- Projděte si lekci [zápis dotazů v Log Analytics](get-started-queries.md).