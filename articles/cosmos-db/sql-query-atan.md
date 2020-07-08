---
title: ATAN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si, jak funkce arkustangens (ATAN) SQL System v Azure Cosmos DB vrátí úhel v radiánech, jehož tangens je zadaný numerický výraz.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302674"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Vrátí úhel v radiánech, jejichž tangens je zadaný numerický výraz. Toto je také označováno jako arkustangens.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí `ATAN` zadanou hodnotu.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Zde je sada výsledků.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
