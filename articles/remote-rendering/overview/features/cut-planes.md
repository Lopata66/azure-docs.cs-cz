---
title: Řezy roviny
description: Vysvětluje, jaké jsou vyjmuté roviny a jak je používat.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 5d641b573a1cad5cac6db6199f5bad5c06151c62
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759077"
---
# <a name="cut-planes"></a>Řezy roviny

*Vyjmutá rovina* je vizuální funkce, která ořízne pixely na jedné straně virtuální plochy a odhalí uvnitř [sítí](../../concepts/meshes.md).
Obrázek níže ukazuje efekt. Vlevo znázorňuje původní síť, napravo od nich může vypadat v rámci sítě:

![Vyjmutá rovina](./media/cutplane-1.png)

## <a name="limitations"></a>Omezení

* V době, kdy vzdálené vykreslování Azure podporuje **maximálně osm aktivních vyjmutých ploch**. Můžete vytvořit další komponenty vyjmuté roviny, ale pokud se pokusíte povolit více současně, bude aktivace ignorována. Chcete-li přepnout, která součást má ovlivnit scénu, zakažte nejprve jiné roviny.
* Každá vyjmutá rovina má vliv na všechny vzdáleně vykreslené objekty. V současné době neexistuje způsob, jak vyloučit konkrétní objekty nebo části sítě.
* Vyjmuté roviny jsou čistě vizuální funkcí, které nemají vliv na výsledek [prostorových dotazů](spatial-queries.md). Pokud chcete přetypování do sítě s vyjmutými otevřeními, můžete nastavit počáteční bod prostorového převodu na vyjmutou plochu. Tímto způsobem může být Ray možné vysáhnout jenom viditelné části.

## <a name="performance-considerations"></a>Otázky výkonu

Každá aktivní vyjmutá rovina při vykreslování způsobí malé náklady. Pokud nepotřebujete, zakažte nebo odstraňte vyjmuté plochy.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Vyjmutou rovinu přidáte do scény vytvořením *CutPlaneComponent*. Umístění a orientace roviny je určena [entitou](../../concepts/entities.md)vlastníka součásti.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>Vlastnosti CutPlaneComponent

Následující vlastnosti jsou zpřístupněny na komponentě vyjmuté roviny:

* **Povoleno:** Vyjmuté plochy můžete dočasně vypnout tím, že tuto součást zakážete. Zakázané vyjmuté plochy neúčtují režii vykreslování a také se nepočítají s omezením globální vyjmuté plochy.

* **Normální:** Určuje směr (+ X,-X, + Y,-Y, + Z,-Z), který je použit jako normální rovina. Tento směr je relativní vzhledem k orientaci entity vlastníka. Přesune a otočí entitu vlastníka pro přesné umístění.

* **FadeColor** a **FadeLength:**

  Pokud hodnota alfa *FadeColor* je nenulová, pixely blízko k vyjmuté rovině se rozstanou k části RGB v FadeColor. Síla alfa kanálu určuje, zda se bude zcela zvolna zobrazovat barvě slábnutí nebo pouze částečně. *FadeLength* definuje, přes jakou vzdálenost bude tato slábnutí probíhat.

## <a name="next-steps"></a>Další kroky

* [Vykreslování s jednou stranou](single-sided-rendering.md)
* [Prostorové dotazy](spatial-queries.md)
