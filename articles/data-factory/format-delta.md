---
title: Rozdílový formát v Azure Data Factory
description: Transformace a přesun dat ze rozdílových Lake pomocí formátu Delta
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: 74c2e738153b1afa5c90f4769b6d9b0e982af363
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225153"
---
# <a name="delta-format-in-azure-data-factory"></a>Rozdílový formát v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak kopírovat data do a z rozdílového jezera uloženého v [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) nebo v [Azure Blob Storage](connector-azure-blob-storage.md) pomocí rozdílového formátu. Tento konektor je k dispozici jako [vložená datová sada](data-flow-source.md#inline-datasets) v mapování dat toků jako zdroj i jímka.

> [!NOTE]
> Rozdílových formátů konektoru pro mapování datových toků je aktuálně k dispozici ve verzi Public Preview.

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Tento konektor je k dispozici jako [vložená datová sada](data-flow-source.md#inline-datasets) v mapování dat toků jako zdroj i jímka.

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované rozdílovým zdrojem. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Vyžadováno | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být`delta` | ano | `delta` | formát |
| Systém souborů | Kontejner/souborový systém rozdílových Lake | ano | Řetězec | Systému souborů |
| Cesta ke složce | Přímo na rozdíl od rozdílových Lake | ano | Řetězec | folderPath |
| Typ komprese | Typ komprese tabulky Delta | ne | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | Argument |
| Úroveň komprese | Vyberte, jestli se komprese dokončí co nejrychleji, nebo jestli se má výsledný soubor optimálně komprimovat. | požadováno `compressedType` , pokud je zadáno. | compressionLevel |
| Doba jízdy | Vyberte, jestli se má dotazovat na starší snímek tabulky Delta. | ne | Dotaz podle časového razítka: časové razítko <br> Dotaz podle verze: celé číslo | timestampAsOf <br> versionAsOf |

#### <a name="import-schema"></a>Importovat schéma

Rozdíl je k dispozici pouze jako vložená datová sada a ve výchozím nastavení nemá přidružené schéma. Chcete-li získat metadata sloupce, klikněte na tlačítko **importovat schéma** na kartě **projekce** . To vám umožní odkazovat na názvy sloupců a datové typy určené parametrem corpus. Pro import schématu musí být [relace ladění toku dat](concepts-data-flow-debug-mode.md) aktivní a musíte mít existující definiční soubor entity CDM, na který odkazuje.
 

### <a name="delta-source-script-example"></a>Příklad zdrojového skriptu Delta

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které jsou podporovány rozdílovou jímkou. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Vyžadováno | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být`delta` | ano | `delta` | formát |
| Systém souborů | Kontejner/souborový systém rozdílových Lake | ano | Řetězec | Systému souborů |
| Cesta ke složce | Přímo na rozdíl od rozdílových Lake | ano | Řetězec | folderPath |
| Typ komprese | Typ komprese tabulky Delta | ne | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | Argument |
| Úroveň komprese | Vyberte, jestli se komprese dokončí co nejrychleji, nebo jestli se má výsledný soubor optimálně komprimovat. | požadováno `compressedType` , pokud je zadáno. | compressionLevel |
| Vacuum | Zadejte prahovou hodnotu uchování v hodinách pro starší verze tabulky. Výchozí hodnota je 0 nebo menší než 30 dní. | ano | Integer | sávací |
| Update – metoda | Určete, které operace aktualizace jsou povoleny na rozdílovém Lake. Pro metody, které nejsou vloženy, je nutné před označením řádků označit předchozí transformaci řádků. | ano | `true` nebo `false` | lze odstranit <br> vložitelný <br> aktualizovatelné <br> upsertable |

### <a name="delta-sink-script-example"></a>Příklad skriptu jímky v rozdílu

Přidružený skript toku dat je:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Známá omezení

Při zápisu do rozdílové jímky existuje známé omezení, kde počet zapsaných řádků nebude vrácen ve výstupu monitorování.

## <a name="next-steps"></a>Další kroky

* Vytvoří [transformaci zdroje](data-flow-source.md) v toku dat mapování.
* Vytvoří [transformaci jímky](data-flow-sink.md) při mapování toku dat.
* Vytvořte [transformaci ALTER Row](data-flow-alter-row.md) k označení řádků jako INSERT, Update, Upsert nebo DELETE.
