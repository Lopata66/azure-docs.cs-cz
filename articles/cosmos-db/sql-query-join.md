---
title: Dotazy SQL JOIN pro Azure Cosmos DB
description: Přečtěte si o připojení syntaxe SQL pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: d78904fde53da0e800a69d2148a9c4e3acf57307
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494415"
---
# <a name="joins-in-azure-cosmos-db"></a>Spojení v Azure Cosmos DB

V relační databázi jsou spojení mezi tabulkami logickým Corollary návrhu normalizovaných schémat. Naproti tomu rozhraní SQL API používá denormalizovaný datový model položek bez schématu, což je logický ekvivalent samostatného *spojení*.

Výsledkem interního spojení je kompletní meziproduktová sada, která se účastní spojení. Výsledkem N-Way připojení je sada N-elementů N-elementů, kde každá hodnota v řazené kolekci členů je přidružena k nastavenému aliasu účasti v JOIN a je možné k nim přistoupit odkazem na tento alias v jiných klauzulích.

## <a name="syntax"></a>Syntaxe

Jazyk podporuje `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`syntaxe. Tento dotaz vrací sadu řazených kolekcí členů s hodnotami `N`. Každá řazená kolekce členů má hodnoty vytvořené iterací všech aliasů kontejnerů přes jejich příslušné sady. 

Pojďme se podívat na následující klauzuli FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Umožňuje každému zdroji definovat `input_alias1, input_alias2, …, input_aliasN`. Tato klauzule FROM vrací sadu N-tice (řazené kolekce členů s N hodnotami). Každá řazená kolekce členů má hodnoty vytvořené iterací všech aliasů kontejnerů přes jejich příslušné sady.  
  
**Příklad 1** – 2 zdroje  
  
- Nechejte `<from_source1>` být v oboru kontejneru a reprezentovat sadu {A, B, C}.  
  
- Nechte `<from_source2>` být odkazovány na input_alias1 s rozsahem dokumentu a reprezentovat sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2>` bude mít za následek následující řazené kolekce členů:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Příklad 2** – 3 zdroje  
  
- Nechejte `<from_source1>` být v oboru kontejneru a reprezentovat sadu {A, B, C}.  
  
- Povolit `<from_source2>` odkazování na `input_alias1` v oboru dokumentu a reprezentovat sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Povolit `<from_source3>` odkazování na `input_alias2` v oboru dokumentu a reprezentovat sady:  
  
    {100, 200} pro `input_alias2 = 1,`  
  
    {300} pro `input_alias2 = 3,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazené kolekce členů:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Nedostatek řazených kolekcí členů pro jiné hodnoty `input_alias1`, `input_alias2`, pro které `<from_source3>` nevrátily žádné hodnoty.  
  
**Příklad 3** – 3 zdroje  
  
- Nechejte < from_source1 > být v oboru kontejneru a reprezentovat sadu {A, B, C}.  
  
- Nechejte `<from_source1>` být v oboru kontejneru a reprezentovat sadu {A, B, C}.  
  
- Nechte < from_source2 > být odkazováno na input_alias1 v oboru dokumentu a reprezentovat sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Povolit `<from_source3>`ům rozsah `input_alias1` a reprezentovat sady:  
  
    {100, 200} pro `input_alias2 = A,`  
  
    {300} pro `input_alias2 = C,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazené kolekce členů:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Výsledkem je mezi produktem `<from_source2>` a `<from_source3>`, protože obě jsou vymezeny na stejný `<from_source1>`.  Výsledkem je, že řazené kolekce členů 4 (2x2) mají hodnotu A, 0 n-tice mají hodnoty B (1x0) a 2 (2x1) řazené kolekce členů s hodnotou C.  
  
## <a name="examples"></a>Příklady

Následující příklady znázorňují, jak klauzule JOIN funguje. Před spuštěním těchto příkladů nahrajte ukázková [data rodiny](sql-query-getting-started.md#upload-sample-data). V následujícím příkladu je výsledek prázdný, protože mezi produktem každé položky ze zdroje a prázdnou sadou je prázdný:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Výsledek je následující:

```json
    [{
    }]
```

V následujícím příkladu je spojení meziproduktem mezi dvěma objekty JSON, kořenovým adresářem položky `id` a podkořenem `children`. Skutečnost, že `children` pole není ve spojení platná, protože se zabývá jediným kořenem, který je polem `children`. Výsledek obsahuje jenom dva výsledky, protože u různých produktů každé položky s polem je přesně jenom jedna položka.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Výsledky jsou:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Následující příklad ukazuje několik konvenčních spojení:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Výsledky jsou:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Zdrojem od klauzule JOIN je iterátor. Proto tok v předchozím příkladu:  

1. Rozbalíte všechny `c` podřízených elementů v poli.
2. Použijte u každého podřízeného prvku mezi produktem kořen položky `f` a `c`, že první krok je plochý.
3. Nakonec naprojektujte kořenový objekt `f` samotné vlastnosti `id`.

První položka, `AndersenFamily`, obsahuje pouze jeden prvek `children`, takže sada výsledků obsahuje pouze jeden objekt. Druhá položka, `WakefieldFamily`, obsahuje dva `children`, takže mezi produkty vznikne dva objekty, jeden pro každý prvek `children`. Kořenová pole v obou těchto položkách jsou stejná, stejně jako byste očekávali v různých produktech.

Skutečným nástrojem klauzule JOIN je vytvořit řazené kolekce členů z různých produktů v obrazci, který je jinak obtížné projektovat. Níže uvedený příklad filtruje kombinaci řazené kolekce členů, která umožňuje uživateli zvolit stav splněný celkovými řazenými kolekcemi členů.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Výsledky jsou:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Následující přípona v předchozím příkladu provádí dvojité spojení. Mezi produkty můžete zobrazit následující pseudo kód:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` má jednu podřízenou položku, která má jednu PET, takže mezi produkty patří jeden řádek (1\*1\*1) z této rodiny. `WakefieldFamily` má dvě podřízené položky, jenom jeden z nich má domácí položku, ale má dvě domácí. Mezi produktem této řady patří 1\*1\*2 = 2 řádky.

V následujícím příkladu je k dispozici další filtr na `pet`, který vylučuje všechny řazené kolekce členů, kde název PET není `Shadow`. Můžete sestavovat řazené kolekce členů z polí, filtrovat na libovolný prvek řazené kolekce členů a projektovat libovolnou kombinaci prvků.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Výsledky jsou:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poddotazy](sql-query-subquery.md)