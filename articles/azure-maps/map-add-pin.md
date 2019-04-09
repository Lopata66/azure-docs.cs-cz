---
title: Přidat vrstvu symbolů ke službě Azure Maps | Dokumentace Microsoftu
description: Postup přidání symboly mapy jazyka Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3225ae919e221935b6d8a52e20d943d2178f6a47
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056843"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Přidat vrstvu symbolů do mapy

Tento článek popisuje, jak může vykreslit data bodu ze zdroje dat jako Symbol vrstvu na mapě. Symbol vrstvy jsou vykreslovány pomocí WebGL a podporovat mnohem větší sady bodů než značky HTML, ale nepodporují pro používání stylů pro tradiční prvky šablon stylů CSS a HTML.  

> [!TIP]
> Vykreslí souřadnice všechny geometrie ve zdroji dat se vrstvy symbol ve výchozím nastavení. Omezit vrstvu tak, aby pouze vykreslí bod geometrie funkce set `filter` vlastnost vrstva `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Pokud budete chtít zahrnout i MultiPoint funkce.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

<iframe height='500' scrolling='no' title='Připnout umístění přepínače' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>přepínač připnout umístění</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok výše uvedený kód vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu, je vytvořen objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. [Funkce], který obsahuje [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometrii je uzavřena v [tvar](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) třídy, aby bylo snazší, pokud chcete aktualizovat, pak vytvořen a přidán do zdroje dat.

Vytvoří třetí bloku kódu [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) a aktualizace souřadnice bodu po myši klikněte na tlačítko horizontálních oddílů pomocí třídy tvar [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metoda.

A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě.  Zdroj dat, naslouchací proces událostí kliknutím a vrstvě symbol vytvořen a přidán do mapování v rámci `ready` [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce, aby bod se zobrazí po mapy načteny a připravené k přístupná.

> [!TIP]
> Ve výchozím nastavení pro výkon a optimalizace symbol vrstvy vykreslování symboly skrytím symboly, které se překrývají. Jak přiblížit skryté symboly pak bude viditelný. Chcete-li tuto funkci zakázat a vykreslit všechny symboly za všech okolností, nastavte `allowOverlap` vlastnost `iconOptions` možnosti k `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidat vlastní ikonu na vrstvu symbol

Symbol vrstvy jsou vykreslovány pomocí WebGL. Jako takové všechny prostředky, jako jsou obrázky ikon musí být načtena do kontextu WebGL. Tento příklad ukazuje, jak přidat vlastní ikonu mapování prostředků a použít jej k vykreslení bodu data pomocí vlastní symbol na mapě. `textField` Vlastnost vrstvy symbol vyžaduje výraz, který se zadat. V tomto případě chceme, aby k vykreslení vlastnost teploty, ale protože je číslo, je potřeba převést na řetězec. Kromě toho chceme, aby k němu připojit "° F". Výraz lze použít k tomu; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Obrázek ikony vlastní Symbol' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>vlastní Symbol obrázek ikony</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Přizpůsobení symbol vrstvy 

Symbol vrstva obsahuje mnoho možností stylu. Tady je nástroj pro testování si tyto různé možnosti používání stylů pro.

<br/>

<iframe height='700' scrolling='no' title='Možnosti symbolu vrstvy' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Symbol možností vrstvy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro další ukázky kódu pro přidání do vaše mapy:

> [!div class="nextstepaction"]
> [Přidání místního okna](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Přidání obrazce](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Add HTML Makers](./map-add-bubble-layer.md)
