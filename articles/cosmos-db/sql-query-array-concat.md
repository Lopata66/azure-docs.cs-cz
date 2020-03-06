---
title: ARRAY_CONCAT v dotazovacím jazyce Azure Cosmos DB
description: Seznamte se s tím, jak funkce pole Concat SQL System v Azure Cosmos DB vrátí pole, které je výsledkem zřetězení dvou nebo více hodnot pole.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295874"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Vrátí pole, která je výsledkem zřetězení dvou nebo více hodnot pole.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je výraz pole, který se má zřetězit k ostatním hodnotám. Funkce `ARRAY_CONCAT` vyžaduje nejméně dva argumenty *arr_expr* .  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz pole.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad jak ke zřetězení dvou polí.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Zde je sada výsledků.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
