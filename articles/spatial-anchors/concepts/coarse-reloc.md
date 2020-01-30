---
title: Hrubá reprostředí
description: Přečtěte si o použití hrubých místních rozhledání, abyste našli kotvy blízko sebe.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844390"
---
# <a name="coarse-relocalization"></a>Přibližná relokalizace

Hrubá opětovná rozmístění je funkce, která poskytuje úvodní odpověď na otázku: *kde je moje zařízení,/který obsah mám pozorovat?* Odpověď není přesná, ale je ve formátu: *blížíte se k těmto kotvám; zkuste jednu z nich najít*.

Hrubá revolba funguje tak, že přidruží různé čtecí senzory zařízení k vytváření i dotazování kotev. V případě scénářů v rámci venkovních scénářů jsou data snímače typicky umístěním GPS (Global Positioning System) zařízení. Pokud GPS není k dispozici nebo je nespolehlivá (například z dvířek), data snímače se skládají z přístupových bodů Wi-Fi a signálů Bluetooth v dosahu. Všechna shromážděná data senzorů přispívají k údržbě prostorového indexu, který používají prostorové kotvy Azure k rychlému určení kotev, které jsou v přibližně 100 metrech vašeho zařízení.

Rychlý pohled na kotvy, které jsou povolené hrubou šířkou, zjednodušuje vývoj aplikací, které jsou zajištěné pomocí špičkových kolekcí (například miliony geograficky distribuovaných) kotev. Složitá Správa ukotvení je skrytá, takže vám umožní soustředit se na vaši skvělou logiku aplikace. Všechna kotva se po celém čase vyzvednutím prostorových ukotvení Azure provádí na pozadí.

## <a name="collected-sensor-data"></a>Shromážděná data senzorů

Data snímače, která můžete odeslat do služby kotvy, jsou jedním z následujících:

* Poloha GPS: Zeměpisná šířka, zeměpisná výška.
* Síla signálu přístupových bodů Wi-Fi v dosahu.
* Síla signálu signálů Bluetooth v dosahu.

Obecně platí, že vaše aplikace bude potřebovat získat oprávnění pro přístup k datům GPS, WiFi nebo v prohlížeči. Kromě toho některá z výše uvedených dat snímačů nejsou k dispozici na určitých platformách. Pro účely těchto situací je kolekce dat senzoru volitelná a ve výchozím nastavení je vypnutá.

## <a name="set-up-the-sensor-data-collection"></a>Nastavení kolekce dat senzoru

Začněte tím, že vytvoříte poskytovatele otisku prstu snímače a nadáme jeho relaci:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

V dalším kroku se budete muset rozhodnout, jaké senzory chcete použít pro hrubou reprostředí. Toto rozhodnutí je specifické pro aplikaci, kterou vyvíjíte, ale doporučení v následující tabulce by vám měla poskytnout dobrý výchozí bod:


|             | Nedvířka | Otevřené |
|-------------|---------|----------|
| GPS         | Vypnuto | Zapnuto |
| Wi-Fi        | Zapnuto | Zapnuto (volitelné) |
| Majáky v/v | Zapnuto (volitelné s upozorněními, viz níže) | Vypnuto |


### <a name="enabling-gps"></a>Povolení GPS

Za předpokladu, že aplikace už má oprávnění pro přístup k pozici GPS zařízení, můžete nakonfigurovat prostorové kotvy Azure pro použití:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Při použití GPS v aplikaci mějte na paměti, že čtení, které poskytuje hardware, je obvykle:

* asynchronní a nízká frekvence (méně než 1 Hz).
* nespolehlivé/hlučné (v průměru 7 – m standardní odchylka).

Obecně platí, že operační systém pro zařízení i prostorové ukotvení Azure provede některé filtrování a extrapolaci nezpracovaného signálu GPS při pokusu o zmírnění těchto problémů. Toto dodatečné zpracování vyžaduje další dobu konvergence, takže pro dosažení nejlepších výsledků byste měli zkusit:

* Vytvoření jednoho poskytovatele otisků prstů na senzoru co nejdříve ve vaší aplikaci
* ponechat poskytovatele otisku prstu snímače aktivní mezi více relacemi
* sdílet poskytovatele otisků prstů senzorů mezi více relacemi

Pokud máte v úmyslu použít poskytovatele otisku prstu před kotvou relace, ujistěte se, že jste ho spustili před vyhodnocením odhadů senzorů. Například následující kód postará o aktualizaci pozice zařízení na mapě v reálném čase:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Povolení Wi-Fi

Za předpokladu, že aplikace už má oprávnění pro přístup ke stavu Wi-Fi zařízení, můžete nakonfigurovat prostorové kotvy Azure pro použití:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Při použití Wi-Fi ve vaší aplikaci mějte na paměti, že čtení, které poskytuje hardware, je obvykle:

* asynchronní a nízká frekvence (méně než 0,1 Hz).
* potenciálně omezené na úrovni operačního systému.
* nespolehlivé/hlučné (v průměru 3 – směrodatná odchylka přepálené úrovně).

Prostorové kotvy Azure se při pokusu o zmírnění těchto problémů pokusí vytvořit filtrovanou mapu síly signálu WiFi během relace. Nejlepších výsledků byste měli vyzkoušet:

* před umístěním první kotvy vytvořte správnou relaci.
* Udržujte relaci aktivní tak dlouho, jak je to možné (to znamená vytvoření všech kotev a dotazování v jedné relaci).

### <a name="enabling-bluetooth-beacons"></a>Povolování signálů Bluetooth

Za předpokladu, že aplikace už má oprávnění pro přístup ke stavu Bluetooth zařízení, můžete nakonfigurovat prostorové kotvy Azure pro použití:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Majáky jsou obvykle všestranná zařízení, kde je možné nakonfigurovat všechno, co zahrnuje identifikátory UUID a adresy MAC. Tato flexibilita může být problematická pro prostorové kotvy Azure, protože považuje signály, aby je jednoznačně identifikovaly svými identifikátory UUID. Selhání, aby se zajistilo, že tato jedinečnost bude pravděpodobně způsobovat prostorové červy děr. Pro dosažení nejlepších výsledků byste měli:

* Přiřaďte k majákům jedinečné identifikátory UUID.
* Nasaďte je obvykle v běžném vzoru, jako je například mřížka.
* předejte seznam jedinečných identifikátorů UUID signalizace na poskytovatele otisku prstu snímače:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Prostorové kotvy Azure budou sledovat jenom signály Bluetooth, které jsou uvedené v seznamu známých identifikátorů UUID pro signály. Škodlivé signály, které jsou naprogramovány na povolené identifikátory UUID, můžou mít negativní vliv na kvalitu služby, i když. Z tohoto důvodu byste měli používat signály pouze v podaných prostorech, kde můžete řídit jejich nasazení.

## <a name="querying-with-sensor-data"></a>Dotazování na data senzorů

Jakmile vytvoříte kotvy s přidruženými daty senzorů, můžete je začít načítat pomocí čtecího zařízení nahlášeného vaším zařízením. Už nepotřebujete, aby služba poskytovala seznam známých kotev, které očekáváte, místo toho stačí, když službu znáte umístění vašeho zařízení, jak je uvedeno v jeho senzorech zprovoznění. Prostorové kotvy Azure pak přejdou sadu kotev blízko na vaše zařízení a pokusí se je vizuálně vyhledat.

Pokud chcete, aby dotazy používaly data ze senzorů, Začněte vytvořením kritéria "poblíž zařízení":

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Parametr `DistanceInMeters` určuje, jak daleko prozkoumáme graf ukotvení k načtení obsahu. Předpokládejme například, že jste nastavili nějaké místo s kotvami s konstantní hustotou 2 každý měřič. Kromě toho fotoaparát na vašem zařízení pozoruje jedno ukotvení a služba ho úspěšně nastavila. S největší pravděpodobně máte zájem načíst všechny kotvy, které jste umístili do blízkosti, nikoli na jedno ukotveno, které právě sledujete. Za předpokladu, že kotvy, které jste umístili, jsou propojeny v grafu, služba může získat všechny okolní kotvy za vás podle hran v grafu. Množství průchodu v grafu je řízeno `DistanceInMeters`em; budou se vám předávat všechny kotvy připojené k vašemu umístění, které je bližší než `DistanceInMeters`.

Pamatujte, že velké hodnoty pro `MaxResultCount` mohou negativně ovlivnit výkon. Nastavte ji na hodnotu rozumné pro vaši aplikaci.

Nakonec budete muset sdělit relaci, aby používala vyhledávání založené na senzorech:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Očekávané výsledky

Zařízení GPS na úrovni příjemce jsou obvykle poměrně nepřesná. Studie od [Zandenbergen a Barbeau (2011)][6] oznamuje střední přesnost mobilních telefonů pomocí programu GPS (A GPS), který má být kolem 7 metrů, takže velká hodnota se bude ignorovat. Aby se tyto chyby měření zohlednily, služba považuje kotvy jako rozdělení pravděpodobnosti v prostoru GPS. V takovém případě je kotva teď oblast místa, která s největší pravděpodobností (tj. s více než 95% spolehlivost) obsahuje svou skutečnou, neznámou polohu GPS.

Stejný důvod je použit při dotazování pomocí GPS. Zařízení je reprezentované jako jiná prostorová důvěra kolem své pravdivé, neznámé pozice GPS. Zjišťování okolních kotev se překládá do pouhého nalezení kotev s oblastmi spolehlivosti *blízkou dostatečně blízko* v oblasti spolehlivosti zařízení, jak je znázorněno na následujícím obrázku:

![Výběr kandidátů kotvy pomocí GPS](media/coarse-reloc-gps-separation-distance.png)

Přesnost signálu GPS, jak při vytváření kotvy, tak i během dotazů má velký vliv na sadu vrácených kotev. Dotazy založené na Wi-Fi a signalizaci naopak budou brát v úvahu všechny kotvy, které mají alespoň jeden přístupový bod nebo signál běžný s dotazem. V takovém smyslu je výsledkem dotazu založeného na Wi-Fi a signalizaci většinou určení fyzickým rozsahem přístupových bodů/majáků a překážek v prostředí.

Následující tabulka odhadne očekávaný hledaný prostor pro každý typ senzoru:

| Elektrické      | Hledat v poloměru místa (přibližně) | Podrobnosti |
|-------------|:-------:|---------|
| GPS         | 20 m – 30 metrů | Určeno nejistotum GPS mezi ostatními faktory. Hlášené počty jsou odhadované pro střední přesnost GPS mobilních telefonů pomocí GPS, což je 7 metrů. |
| Wi-Fi        | 50 m – 100 m | Určuje rozsah bezdrátových přístupových bodů. Závisí na četnosti, síle vysílače, fyzických překážkách, rušení atd. |
| Majáky v/v |  70 m | Určuje rozsahem majáku. Závisí na četnosti, síle přenosu, fyzických překážkách, rušení atd. |

## <a name="per-platform-support"></a>Podpora pro platformy

Následující tabulka shrnuje data senzorů shromážděná na jednotlivých podporovaných platformách spolu s případnými výstrahami specifickými pro konkrétní platformu:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | Nevztahuje se | Podporováno prostřednictvím rozhraní [LocationManager][3] API (GPS i síť) | Podporováno prostřednictvím rozhraní [CLLocationManager][4] API |
| Wi-Fi        | Podporuje se rychlostí přibližně jedné kontroly každé 3 sekundy. | Podporuje se. Počínaje rozhraním API Level 28 jsou kontroly Wi-Fi omezené na 4 volání každé 2 minuty. Z Androidu 10 se omezování dá zakázat v nabídce nastavení pro vývojáře. Další informace najdete v [dokumentaci k Androidu][5]. | Není k dispozici žádné veřejné rozhraní API |
| Majáky v/v | Omezeno na [Eddystone][1] a [blokovat iBeacon u][2] | Omezeno na [Eddystone][1] a [blokovat iBeacon u][2] | Omezeno na [Eddystone][1] a [blokovat iBeacon u][2] |

## <a name="next-steps"></a>Další kroky

V aplikaci můžete použít hrubou reprostředí.

> [!div class="nextstepaction"]
> [Jednot](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
