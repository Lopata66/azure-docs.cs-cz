---
title: Nastavení stylu mapy pomocí Azure Maps Android SDK | Mapy Microsoft Azure
description: V tomto článku se dozvíte o funkcích, které se týkají Android SDK Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 81f89a4b5aa758d506ecdb9cf7b4f510e333ef25
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131255"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Nastavení stylu mapy pomocí Azure Maps Android SDK

Tento článek ukazuje dva způsoby, jak nastavit styly mapy pomocí Azure Maps Android SDK. Azure Maps má šest různých stylů mapy, ze kterých si můžete vybrat. Další informace o podporovaných stylech map najdete [v tématu Podporované styly mapy v Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Předpoklady

Chcete-li dokončit proces v tomto článku, je nutné nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) , aby se načetla mapa.


## <a name="set-map-style-in-the-layout"></a>Nastavení stylu mapy v rozložení

Můžete nastavit styl mapy v souboru rozložení pro třídu Activity. Upravte **> rozložení res > activity_main.xml**, takže vypadá takto:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`Atribut výše nastaví styl mapy na **grayscale_dark**. 

<center>

![styl – grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Nastavit styl mapy ve třídě Activity

Styl mapy lze nastavit ve třídě Activity. Zkopírujte následující fragment kódu do metody **Create ()** vaší `MainActivity.java` třídy. Tento kód nastaví styl mapy na **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![styl – satelitní-silniční-štítky](./media/set-android-map-styles/satellite-road-labels.png)</center>