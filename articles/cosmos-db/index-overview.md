---
title: Indexování ve službě Azure Cosmos DB
description: Seznamte se s tím, jak indexování funguje v Azure Cosmos DB, různé druhy indexů, jako je rozsah, prostorové a složené indexy.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: bea6d01a8363dd68cff33435335391e0fb0f76c7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118589"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexování ve službě Azure Cosmos DB – Přehled

Azure Cosmos DB je databáze nezávislá schématu, která umožňuje iterovat na aplikaci bez nutnosti zabývat se správou schématu nebo indexu. Ve výchozím nastavení Azure Cosmos DB automaticky indexuje všechny vlastnosti pro všechny položky ve vašem [kontejneru](databases-containers-items.md#azure-cosmos-containers) bez nutnosti definovat nějaké schéma nebo nakonfigurovat sekundární indexy.

Cílem tohoto článku je vysvětlit, jak služba Azure Cosmos DB indexuje data a jak pomocí indexů zlepšuje výkon dotazů. Před zkoumáním způsobu přizpůsobení [zásad indexování](index-policy.md)doporučujeme projít si tuto část.

## <a name="from-items-to-trees"></a>Z položek do stromů

Pokaždé, když je položka uložená v kontejneru, její obsah se prochází jako dokument JSON a pak se převede do reprezentace stromu. To znamená, že všechny vlastnosti této položky se reprezentují jako uzel ve stromové struktuře. Pseudo kořenový uzel je vytvořen jako nadřazený pro všechny vlastnosti první úrovně položky. Uzly list obsahují skutečné skalární hodnoty přenesené položkou.

Zvažte například tuto položku:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Bude reprezentovaná následujícím stromem:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="Předchozí položka reprezentovaná jako strom" border="false":::

Všimněte si, jak jsou pole kódována ve stromové struktuře: Každá položka v poli získá zprostředkující uzel označený indexem této položky v poli (0, 1 atd.).

## <a name="from-trees-to-property-paths"></a>Ze stromů na cesty vlastností

Důvod, proč Azure Cosmos DB transformuje položky do stromů, je, že umožňuje, aby v rámci těchto stromů byly odkazy na vlastnosti odkazovány pomocí jejich cest. Chcete-li získat cestu k vlastnosti, můžeme procházet stromovou strukturu z kořenového uzlu do této vlastnosti a zřetězit popisky jednotlivých procházených uzlů.

Tady jsou cesty pro jednotlivé vlastnosti z ukázkové položky popsané výše:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Při zápisu položky Azure Cosmos DB efektivně indexuje cestu každé vlastnosti a její odpovídající hodnotu.

## <a name="index-kinds"></a>Typy indexů

Azure Cosmos DB aktuálně podporuje tři druhy indexů.

### <a name="range-index"></a>Index rozsahu

Index **rozsahu** je založen na seřazené struktuře podobné stromové struktury. Typ indexu rozsahu se používá pro:

- Dotazy na rovnost:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Shoda rovnosti na elementu pole
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Dotazy na rozsah:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funguje pro `>` , `<` , `>=` , `<=` , `!=` )

- Kontrola přítomnosti vlastnosti:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Řetězcové funkce systému:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY`odešle

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`odešle

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indexy rozsahu lze použít na skalárních hodnotách (String nebo Number).

### <a name="spatial-index"></a>Prostorový index

**Prostorové** indexy umožňují efektivní dotazy na geoprostorové objekty, jako jsou body, čáry, mnohoúhelníky a víceřádkový mnohoúhelník. Tyto dotazy používají klíčová slova ST_DISTANCE, ST_WITHIN ST_INTERSECTS. Níže jsou uvedeny některé příklady použití prostorového indexu:

- Dotazy na geoprostorové vzdálenosti:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprostorové v rámci dotazů:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Geoprostorové protínající se dotazy:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Prostorové indexy lze použít na správně formátovaných objektech typu [injson](geospatial.md) . V současné době se podporují body, LineStrings, mnohoúhelníky a další mnohoúhelníky.

### <a name="composite-indexes"></a>Složené indexy

**Složené** indexy zvyšují efektivitu při provádění operací s více poli. Typ složeného indexu se používá pro:

- `ORDER BY`dotazy na více vlastností:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Dotazy s filtrem a `ORDER BY` . Tyto dotazy mohou využít složený index, pokud je do klauzule přidána vlastnost Filter `ORDER BY` .

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Dotazy s filtrem na dvou nebo více vlastnostech, kde nejméně jedna vlastnost je filtr rovnosti

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Pokud jeden predikát filtru používá jeden z druhů indexů, nástroj pro dotaz vyhodnotí, že nejprve vyhodnocuje, zda je před kontrolou zbývajících. Například pokud máte dotaz SQL, například`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Výše uvedený dotaz nejprve vyfiltruje položky, kde firstName = "Andrew" pomocí indexu. Pak předá všechny záznamy firstName = "Andrew" prostřednictvím následného kanálu k vyhodnocení predikátu OBSAHUJÍCÍho filtr.

* Můžete zrychlit dotazy a vyhnout se úplným kontrolám kontejnerů při použití funkcí, které nepoužívají index (např. obsahuje) přidáním dalších predikátů filtru, které používají index. Pořadí klauzulí filtru není důležité. Dotazovací modul zjistí, které predikáty jsou podrobněji selektivní a spustí dotaz odpovídajícím způsobem.


## <a name="querying-with-indexes"></a>Dotazování s indexy

Cesty extrahované při indexování dat usnadňují vyhledání indexu při zpracování dotazu. Porovnáním `WHERE` klauzule dotazu se seznamem indexovaných cest je možné identifikovat položky, které odpovídají predikátu dotazu velmi rychle.

Zvažte například následující dotaz: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . Predikát dotazu (filtrování položek, kde jakékoli umístění má "Francie" jako země nebo oblast) by odpovídala cestě zvýrazněné červeně:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="Odpovídá konkrétní cestě v rámci stromu." border="false":::

> [!NOTE]
> `ORDER BY`Klauzule, která má ORDER by jedna vlastnost, *vždy* potřebuje index rozsahu a nezdaří se, pokud cesta, na kterou odkazuje, nemá jednu. Podobně dotaz, `ORDER BY` který ORDER by má více vlastností, *vždy* potřebuje složený index.

## <a name="next-steps"></a>Další kroky

Další informace o indexování najdete v následujících článcích:

- [Zásady indexování](index-policy.md)
- [Jak spravovat zásady indexování](how-to-manage-indexing-policy.md)
