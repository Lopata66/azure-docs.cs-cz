---
title: Hranice objektů
description: Vysvětluje, jak lze dotazovat hranice prostorového objektu.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021846"
---
# <a name="object-bounds"></a>Hranice objektů

Hranice objektů reprezentují svazek, který zabírá [entita](entities.md) a její potomky. Ve vzdáleném vykreslování Azure se hranice objektů vždycky předávají jako *ohraničovací rámečky zarovnané na osy* (AABB). Hranice objektů mohou být buď v *místním prostoru* , nebo v *prostoru světa*. V obou případech jsou vždy zarovnané osy, což znamená, že rozsahy a svazky se mohou lišit mezi znázorněním místního a světového prostoru.

## <a name="querying-object-bounds"></a>Dotazování na hranice objektů

Místní AABB [sítě](meshes.md) lze dotazovat přímo z prostředku sítě. Tyto hranice je možné transformovat do místního prostoru nebo světového prostoru entity pomocí transformace entity.

Je možné vypočítat hranice celé hierarchie objektů tímto způsobem, ale to vyžaduje, aby procházela hierarchii, dotazoval hranice pro každou síť a byly kombinovány ručně. Tato operace je únavné a neefektivní.

Lepším způsobem je zavolat `QueryLocalBoundsAsync` nebo `QueryWorldBoundsAsync` na entitu. Výpočet se pak přesměruje na server a vrátí se s minimálním zpožděním.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Další kroky

* [Prostorové dotazy](../overview/features/spatial-queries.md)
