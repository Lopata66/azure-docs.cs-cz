---
title: Vytvořte mapu s Azure Maps | Dokumentace Microsoftu
description: Jak vytvořit mapu jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0e292bfd38402d5a36be217746fd352ce4627177
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633312"
---
# <a name="create-a-map"></a>Vytvoření mapy

Tento článek ukazuje, jak vytvořit mapu.  

## <a name="understand-the-code"></a>Vysvětlení kódu

Existují dva způsoby, jak můžete vytvořit mapu. Můžete nastavit fotoaparát na mapě zadáním středový bod a úroveň zvětšení nebo nastavit fotoaparát mezí mapy zadáním jihozápadně ohraničující bodu a severovýchodní ohraničující bodu.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Nastavení fotoaparátu/kamery

<iframe height='310' scrolling='no' title='Vytvořte mapu prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Vytvořte mapu prostřednictvím CameraOptions</a> pomocí Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu [objekt map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) byla vytvořena přes `new atlas.Map()`. Vlastnosti mapy, jako je úroveň Centrování a přiblížení jsou součástí [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). CameraOptions lze definovat v mapě konstruktoru, nebo prostřednictvím [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkce třídy map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Nastavení fotoaparátu hranice

<iframe height='310' scrolling='no' title='Vytvořte mapu prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Vytvořte mapu prostřednictvím CameraBoundsOptions</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu [objekt map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) je vytvořená prostřednictvím `new atlas.Map()`. Vlastnosti mapy, jako je například ohraničující rámeček jsou součástí [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions lze definovat prostřednictvím [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map.

## <a name="try-out-the-code"></a>Vyzkoušejte si kód

Podívejte se na výše uvedeném ukázkovém kódu. Můžete upravit jeho kód JavaScript na kartě JS na levé straně a podívat se na změny zobrazení mapy na kartě výsledek na pravé straně. Můžete také kliknutím na tlačítko "Upravit na CodePen" a úpravy kódu v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Příklady kódu pro přidání funkce do vaší aplikace:

> [!div class="nextstepaction"]
> [Vyberte styl mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Přidat ovládací prvky mapy](map-add-controls.md)
