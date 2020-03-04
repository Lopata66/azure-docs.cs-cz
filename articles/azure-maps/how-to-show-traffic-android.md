---
title: Zobrazit data o přenosech na mapě Androidu | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit data o přenosech na mapě pomocí Android SDK Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 37de55d671bb19cfcd9fd494c2e76f658fc7db21
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249499"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Zobrazit data o přenosech na mapě pomocí Azure Maps Android SDK

Data toku a data incidentů jsou dva typy dat přenosů, které lze zobrazit na mapě. V této příručce se dozvíte, jak zobrazit oba typy dat přenosů. Data incidentů se skládají z bodových a řádkových dat pro věci, jako jsou konstrukce, uzavření provozu a nehody. Data toku zobrazují metriky týkající se toku provozu na cestách.

## <a name="prerequisites"></a>Předpoklady

Než budete moci zobrazit provoz na mapě, je třeba [vytvořit účet Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)a [získat klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account). Pak je nutné nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) a načíst mapu.

## <a name="incidents-traffic-data"></a>Data o provozu incidentů 

Aby bylo možné volat `setTraffic` a `incidents`, bude nutné importovat následující knihovny:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Následující fragment kódu ukazuje, jak zobrazit data o přenosech na mapě. Do metody `incidents` předáte logickou hodnotu a předáte ji do metody `setTraffic`. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Data toku přenosu dat

Nejprve bude nutné importovat následující knihovny, aby bylo možné volat `setTraffic` a `flow`:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Pomocí následujícího fragmentu kódu nastavte data toku provozu. Podobně jako u kódu v předchozí části předáte vrácenou hodnotu metody `flow` metodě `setTraffic`. Existují čtyři hodnoty, které lze předat `flow`a každá hodnota by aktivovala `flow`, aby vrátila příslušnou hodnotu. Návratová hodnota `flow` se pak předává jako argument pro `setTraffic`. Tyto čtyři hodnoty najdete v následující tabulce:

| | |
| :-- | :-- |
| TrafficFlow. NONE | Nezobrazuje data o přenosech na mapě. |
| TrafficFlow. relativní | Zobrazuje přenosová data, která se vztahují k rychlosti volného toku provozu. |
| TrafficFlow. RELATIVE_DELAY | Zobrazí oblasti, které jsou pomalejší než průměrná očekávaná prodleva. |
| TrafficFlow. absolutní | Zobrazuje absolutní rychlost všech vozidel na cestách. |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Zobrazit data o provozu incidentu kliknutím na funkci

Chcete-li získat incidenty pro konkrétní funkci, můžete použít následující kód. Při kliknutí na funkci logika kódu zkontroluje incidenty a vytvoří zprávu o incidentu. V dolní části obrazovky se zobrazí zpráva s podrobnostmi.

1. Nejprve je třeba upravit **> rozložení res > activity_main. XML**, aby vypadala níže. `mapcontrol_centerLat`, `mapcontrol_centerLng`a `mapcontrol_zoom` můžete nahradit požadovanými hodnotami. Odvolání, úroveň přiblížení je hodnota mezi 0 a 22. Na úrovni přiblížení 0 se celý svět vejde na jednu dlaždici.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Do souboru **MainActivity. Java** přidejte následující kód. Balíček je ve výchozím nastavení zahrnutý, proto se ujistěte, že váš balíček zůstane v horní části.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Po začlenění výše uvedeného kódu do aplikace budete moci kliknout na funkci a zobrazit podrobnosti o incidentech provozu. V závislosti na hodnotách Zeměpisná šířka a délka a úroveň přiblížení, které jste použili v souboru **activity_main. XML** , se zobrazí podobné výsledky jako na následujícím obrázku:

   <center>

   ![Incident – přenos na mapě](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Další kroky

V následujících průvodcích se dozvíte, jak na mapu přidat další data:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidat dlaždicovou vrstvu](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Přidat obrazce do mapy pro Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Zobrazit informace o funkci](display-feature-information-android.md)
