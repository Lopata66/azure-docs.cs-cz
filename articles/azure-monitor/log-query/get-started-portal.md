---
title: Začínáme s Azure Monitor Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro psaní dotazů pomocí Log Analytics na portálu Azure portal.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: 17b5c0b459e70909d9f305beb8bf87b83f1cf65c
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296519"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Začínáme se službou Log Analytics ve službě Azure Monitor

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

V tomto kurzu se dozvíte, jak používat službu Log Analytics na portálu Azure portal pro zápis dotazů na protokoly Azure monitoru. To se dozvíte, jak do:

- Můžete napsat jednoduchý dotaz Log Analytics
- Pochopení schématu dat
- Filtrace, řazení a seskupení výsledků
- Použít časový rozsah
- Vytváření grafů
- Uložit a načíst dotazy
- Export a sdílet dotazy

Kurz o psaní dotazů na protokoly, najdete v tématu [Začínáme s dotazy protokolu ve službě Azure Monitor](get-started-queries.md).<br>
Podrobné informace o dotazech protokolu naleznete v tématu [přehled protokolu dotazů ve službě Azure Monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Musí splňovat Log Analytics
Log Analytics je webový nástroj pro zápis a spouštění dotazů na protokoly Azure monitoru. Otevřete ho tak, že vyberete **protokoly** v nabídce Azure Monitor. Spustí se nový prázdný dotaz.

![Domovská stránka](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Požadavky na bránu firewall
Chcete-li používat službu Log Analytics, váš prohlížeč vyžaduje přístup k následující adresy. Pokud váš prohlížeč je přístup k portálu Azure přes bránu firewall, je třeba povolit přístup pro tyto adresy.

| Uri | IP adresa | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamické | 80,443 |
| api.loganalytics.io | Dynamické | 80,443 |
| docs.loganalytics.io | Dynamické | 80,443 |

## <a name="basic-queries"></a>Základní dotazy
Hledané termíny, rozpoznávejte trendy, analyzovat vzory a poskytují mnoho přehledy na základě vašich dat je možné dotazy. Začínáme s základní dotazy:

```Kusto
Event | search "error"
```

Tento dotaz vyhledá _události_ tabulky pro záznamy, které obsahují pojem _chyba_ v jakékoli vlastnosti.

Dotazy můžete spustit buď pomocí názvu tabulky nebo [hledání](/kusto/query/searchoperator) příkazu. Výše uvedený příklad začíná název tabulky _události_, který načte všechny záznamy z tabulky událostí. Znak svislé čáry (|) odděluje příkazy, takže výstup první z nich slouží jako vstup následující příkaz. Můžete přidat libovolný počet příkazů do jednoho dotazu.

Jiný způsob psaní tohoto stejného dotazu by byl:

```Kusto
search in (Event) "error"
```

V tomto příkladu **hledání** působí na _události_ tabulku a všechny záznamy v této tabulce jsou prohledány na termín _chyba_.

## <a name="running-a-query"></a>Spuštění dotazu
Spusťte dotaz kliknutím **spustit** tlačítko nebo stisknutím klávesy **Shift + Enter**. Vezměte v úvahu následující informace, které určíte, kód, který se spustí a data, která je vrácena:

- Konce řádků: Jediné přerušení usnadňuje čtení dotazu. Více konce řádků ho rozdělit na samostatné dotazy.
- Cursor: Umístěte kurzor někam uvnitř dotazu k jeho provedení. Aktuální dotaz se považuje za kód, dokud nenajde prázdný řádek.
- Časový rozsah - časový rozsah _posledních 24 hodin_ ve výchozím nastavení. Pokud chcete použít jiný rozsah, použijte Výběr času nebo přidat explicitní čas filtr rozsahu do dotazu.


## <a name="understand-the-schema"></a>Vysvětlené schématu
Schéma je kolekce tabulek vizuálně seskupené pod logických kategorií. Některé z kategorií jsou z monitorování řešení. _LogManagement_ kategorie obsahuje běžné data, jako jsou Windows a protokolu Syslog události, údaje o výkonu a agent vysílat prezenční signál.

![Schéma](media/get-started-portal/schema.png)

V každé tabulce dat uspořádány do sloupce s různými datovými typy je určeno ikonami vedle jejich názvu sloupce. Například _události_ tabulka ukazuje snímek obrazovky obsahuje sloupce, například _počítače_ což je text, _EventCategory_ což je číslo, a  _TimeGenerated_ což je datum a čas.

## <a name="filter-the-results"></a>Filtrování výsledků
Začněte tím, že tu být všechno _události_ tabulky.

```Kusto
Event
```

Log Analytics automaticky obory výsledky podle:

- Časový rozsah:  Ve výchozím nastavení dotazy jsou omezené na poslední 24 hodin.
- Počet výsledků: Výsledky jsou omezeny na maximálně 10 000 záznamů.

Tento dotaz je velmi obecná a vrátí příliš mnoho výsledků. aby byla užitečná. Můžete filtrovat výsledky přes prvky tabulky nebo tak, že explicitně přidáte filtr do dotazu. Filtrování výsledků přes prvky tabulky platí pro stávající sadu výsledků, zatímco filtru samotný dotaz vrátí novou filtrovaných výsledků nastavit a může proto poskytuje přesnější výsledky.

### <a name="add-a-filter-to-the-query"></a>Přidání filtru do dotazu
Existuje šipku nalevo od každého záznamu. Klepnutím na šipku otevřete podrobnosti konkrétního záznamu.

Najeďte myší nad název sloupce pro "+" a "-" k zobrazení ikony. Pokud chcete přidat filtr, který vrátí pouze záznamy se stejnou hodnotou, klikněte na znaménko "+". Klikněte na tlačítko "-" vyloučit záznamy s touto hodnotou a potom klikněte na **spustit** spusťte dotaz znovu.

![Přidání filtru do dotazu](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrovat přes prvky tabulky
Nyní zaměřme se na události závažnost _chyba_. Tento parametr je zadán v sloupec s názvem _EventLevelName_. Budete muset posunout doprava najdete v tomto sloupci.

Kliknutím na ikonu filtru vedle záhlaví sloupce a v automaticky otevíraném okně vyberte hodnoty, které _začíná_ text _chyba_:

![Filtr](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Seřadit a seskupit výsledky
Výsledky jsou nyní zúží a obsahovat pouze chybové události z SQL serveru vytvořené za posledních 24 hodin. Však nejsou seřazené výsledky žádným způsobem. Chcete-li seřadit výsledky podle konkrétního sloupce, jako například _časové razítko_ například klikněte na záhlaví sloupce. Jedno kliknutí Seřadí seznam vzestupně během druhé kliknutí se seřadit sestupně.

![Sloupec pro řazení](media/get-started-portal/sort-column.png)

Dalším způsobem, jak výsledky uspořádat je podle skupin. K seskupení výsledků podle konkrétního sloupce, jednoduše přetáhněte záhlaví sloupce vyšší než ostatní sloupce. K vytvoření podskupiny, přetáhněte další sloupce na horním panelu také.

![Skupiny](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Vyberte sloupce, které chcete zobrazit
Tabulka výsledků často obsahuje mnoho sloupců. Můžete zjistit, že některé vrácené sloupce se nezobrazují ve výchozím nastavení, nebo můžete chtít odebrat některé sloupce, které jsou zobrazeny. Pokud chcete vybrat sloupce, které chcete zobrazit, klikněte na tlačítko sloupce:

![Výběr sloupců](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Vyberte časový rozsah
Ve výchozím nastavení, Log Analytics se vztahuje _posledních 24 hodin_ časový rozsah. Pokud chcete použít jiný rozsah, vyberte jinou hodnotu prostřednictvím nástroje pro výběr času a klikněte na **spustit**. Kromě přednastavené hodnoty, můžete použít _vlastního časového rozsahu_ možnost vybrat si absolutní rozsahu pro váš dotaz.

![Výběr času](media/get-started-portal/time-picker.png)

Když vyberete vlastní časový rozsah, vybraných hodnot se ve standardu UTC, což může být jiný než místní časové pásmo.

Pokud dotaz obsahuje explicitně filtr pro _TimeGenerated_, se zobrazí název výběr času _nastavit v dotazu_. Aby se zabránilo konfliktu se zakážou ruční výběr.


## <a name="charts"></a>Grafy
Kromě vracení výsledků v tabulce, můžete výsledky dotazu uvedené v visual formátů. Jako příklad použijte následující dotaz:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Ve výchozím nastavení výsledky se zobrazí v tabulce. Klikněte na tlačítko _grafu_ pro zobrazení výsledků v grafické zobrazení:

![Pruhový graf](media/get-started-portal/bar-chart.png)

Výsledky jsou zobrazeny skládaném pruhovém grafu. Klikněte na tlačítko _skládaný sloupcový_ a vyberte _výsečový_ do jiného zobrazení výsledků:

![Výsečový graf](media/get-started-portal/pie-chart.png)

Různé vlastnosti objektu zobrazení, jako je například x a osy y, nebo seskupování a rozdělování předvolby, můžete ručně změnit na ovládacím panelu.

Požadované zobrazení můžete také nastavit v dotazu, použití operátoru vykreslování.

### <a name="smart-diagnostics"></a>Inteligentní Diagnostika
Na časový graf Pokud náhlá Špička nebo kroku ve vašich datech, může se zobrazit bod zvýrazněný na řádku. Znamená to, že _inteligentní Diagnostika_ zjistila kombinací vlastností, které odfiltrovat náhlé změny. Klikněte na bod, abyste získali více podrobností na filtr a zobrazíte filtrované verze. To může pomoci zjistit, co způsobilo změny:

![Inteligentní Diagnostika](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Připnout na řídicí panel
Pro Připnutí diagramu nebo tabulku k jedné sdílené řídicí panely Azure, klikněte na ikonu připínáčku.

![Připnout na řídicí panel](media/get-started-portal/pin-dashboard.png)

Některé zjednodušení jsou použity pro graf připnout na řídicí panel:

- Sloupce tabulky a řádky: Pokud chcete připnout tabulku na řídicí panel, musí mít čtyři nebo méně sloupců. Zobrazí se jenom prvních řádků sedm.
- Čas omezení: Dotazy jsou automaticky omezeny na posledních 14 dní.
- Omezení počtu Bin: Pokud zobrazíte graf, který obsahuje mnoho intervalů diskrétní méně mají údaj vyplněný přihrádek budou automaticky seskupeny do jednoho _ostatní_ bin.

## <a name="save-queries"></a>Ukládání dotazů
Po vytvoření užitečného dotazu, můžete chtít uložit nebo sdílet s ostatními. **Uložit** je ikona na horním panelu.

Na stránce celý dotaz nebo pomocí jediného dotazu můžete uložit jako funkce. Funkce jsou dotazy, které lze také odkazovat pomocí jiných dotazů. Aby bylo možné uložit dotaz jako funkce, je nutné zadat alias funkce, což je název používaný k volání tohoto dotazu, když odkazují jiné dotazy.

![Uložit – funkce](media/get-started-portal/save-function.png)

Dotazy log Analytics jsou vždy uloženy do vybraného pracovního prostoru a sdílet s ostatními uživateli daného pracovního prostoru.

## <a name="load-queries"></a>Načíst dotazy
Ikona Průzkumníka dotazů je v horní pravé oblasti. Vypíšou se všechny uložené dotazy podle kategorie. Také umožňuje označit jako oblíbené položky v budoucnu je rychle vyhledat konkrétní dotazy. Klikněte dvakrát na uložený dotaz se přidá do aktuálního okna.

![Průzkumník dotazů](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Export a sdílenou složku jako odkaz
Log Analytics podporuje několik metod pro export:

- Excel: Uložte výsledky do souboru .csv.
- Power BI: Exportujte výsledky do Power BI. Zobrazit [data protokolu Azure Monitor importovat do Power BI](../../azure-monitor/platform/powerbi.md) podrobnosti.
- Sdílejte odkaz: Samotný dotaz mohou být sdíleny jako odkaz, který lze potom odeslat a spustit další uživatelé, kteří mají přístup do stejného pracovního prostoru.

## <a name="next-steps"></a>Další postup

- Další informace o [psaní dotazů na protokoly Azure monitoru](get-started-queries.md).
