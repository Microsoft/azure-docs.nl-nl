---
title: Ruwe Herlokalisatie
description: Meer informatie over het gebruik van grove Herlokalisatie om ankers bij u in de buurt te vinden.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a65b2ca4ba9f1912adeaf60df123bcd3c8833bd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496899"
---
# <a name="coarse-relocalization"></a>Coarse-relokalisatie

Ruwe Herlokalisatie is een functie die een eerste antwoord geeft op de vraag: *waar is mijn apparaat nu/welke inhoud moet ik naachten?* Het antwoord is niet nauw keurig, maar in plaats daarvan bevindt zich in de vorm: *u sluit deze ankers aan. Probeer een van de twee te vinden*.

Ruwe Herlokalisatie werkt door verschillende metingen op de sensor van een apparaat te koppelen aan zowel het maken als de query op ankers. Voor scenario's voor buiten komen de sensor gegevens doorgaans de GPS (Global Positioning System) positie van het apparaat. Wanneer GPS niet beschikbaar of onbetrouwbaar is (zoals in de lucht), bestaan de sensor gegevens uit de Wi-Fi-toegangs punten en Bluetooth beacons in het bereik. Alle verzamelde sensor gegevens dragen bij aan het behoud van een ruimtelijke index. dit wordt gebruikt door de ruimtelijke ankers van Azure om snel de ankers te bepalen die zich binnen ongeveer 100 meters van uw apparaat bevinden.

De snelle zoek functie die door ruwe Herlokalisatie is ingeschakeld, vereenvoudigt de ontwikkeling van toepassingen die worden ondersteund door grootschalige verzamelingen (bijvoorbeeld miljoenen geo-gedistribueerde) ankers. De complexiteit van anker beheer is verborgen, zodat u meer aandacht kunt richten op uw meester toepassings logica. Alle verankeringen worden vóór de schermen zwaar gemaakt door Azure spatiale ankers.

## <a name="collected-sensor-data"></a>Verzamelde sensor gegevens

De sensor gegevens die u naar de anker service kunt verzenden, zijn een van de volgende:

* GPS-positie: breedte graad, lengte graad, hoogte.
* Signaal sterkte van WiFi-toegangs punten in bereik.
* De signaal sterkte van de Bluetooth-beacons in het bereik.

In het algemeen moet uw toepassing apparaatspecifieke machtigingen verkrijgen voor toegang tot GPS-, WiFi-of prodata-gegevens. Daarnaast zijn enkele van de bovenstaande sensor gegevens niet beschikbaar voor het ontwerp op bepaalde platforms. Voor deze situaties is het verzamelen van sensor gegevens optioneel en is deze standaard uitgeschakeld.

## <a name="set-up-the-sensor-data-collection"></a>De sensor gegevens verzameling instellen

Laten we beginnen met het maken van een sensor vingerafdruk provider en de sessie op de hoogte brengen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Vervolgens moet u bepalen welke Sens oren u wilt gebruiken voor grove Herlokalisatie. Deze beslissing is specifiek voor de toepassing die u ontwikkelt, maar de aanbevelingen in de volgende tabel moeten u een goed uitgangs punt bieden:

|                 | Binnendeuren | Gek |
|-----------------|---------|----------|
| **GPS**         | Aan | Uit |
| **WiFi**        | Uit | Aan (optioneel) |
| **Conbakeners** | Aan (optioneel met aanvullende opmerkingen, zie hieronder) | Aan |

### <a name="enabling-gps"></a>GPS inschakelen

Als uw toepassing al toegangs rechten heeft voor de GPS-positie van het apparaat, kunt u Azure spatiale ankers configureren om het te gebruiken:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Wanneer u GPS gebruikt in uw toepassing, moet u er ook voor zorgen dat de beschik bare leesingen door de hardware doorgaans:

* asynchrone en lage frequentie (minder dan 1 Hz).
* onbetrouwbaar/ruis (gemiddeld 7-m standaard afwijking).

In het algemeen worden zowel het besturings systeem als het ruimtelijke anker van Azure een aantal filtering en extrapolatie op het onbewerkte GPS-signaal in een poging om deze problemen te verhelpen. Deze extra verwerking vereist extra tijd voor convergentie, zodat u de beste resultaten kunt proberen:

* zo snel mogelijk een provider voor sensor vingerafdruk maken in uw toepassing
* de sensor vingerafdruk provider in stand houden tussen meerdere sessies
* de sensor vingerafdruk provider delen tussen meerdere sessies

Als u van plan bent de sensor vingerafdruk provider buiten een anker sessie te gebruiken, moet u deze starten voordat u de sensor schattingen aanvraagt. De volgende code zorgt er bijvoorbeeld voor dat de positie van uw apparaat in real-time wordt bijgewerkt op de kaart:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="objc"></a>[ObjC](#tab/objc)

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

# <a name="swift"></a>[Swift](#tab/swift)

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

# <a name="java"></a>[Java](#tab/java)

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

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

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

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

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

### <a name="enabling-wifi"></a>WiFi inschakelen

Als uw toepassing al toegangs rechten heeft voor de Wi-Fi-status van het apparaat, kunt u Azure spatiale ankers configureren om het te gebruiken:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Wanneer u Wi-Fi gebruikt in uw toepassing, moet u erop letten dat de leesingen die worden meegeleverd door de hardware doorgaans:

* asynchrone en lage frequentie (minder dan 0,1 Hz).
* mogelijk beperkt op het niveau van het besturings systeem.
* onbetrouwbaar/ruis (gemiddeld 3-dBm-standaard deviatie).

Bij een poging om deze problemen te verhelpen, wordt geprobeerd een gefilterde WiFi-signaal sterkte toewijzing te bouwen tijdens een sessie. Voor de beste resultaten moet u het volgende proberen:

* Maak de sessie goed voordat u het eerste anker plaatst.
* Bewaar de sessie zo lang mogelijk (dat wil zeggen, alle ankers en query's in één sessie maken).

### <a name="enabling-bluetooth-beacons"></a>Bluetooth-beacons inschakelen

Als uw toepassing al toegangs rechten heeft voor de Bluetooth-status van het apparaat, kunt u de Azure spatiale ankers configureren om het te gebruiken:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Beacons zijn doorgaans veelzijdige apparaten, waarbij alles, inclusief UUIDs en MAC-adressen, kan worden geconfigureerd. Deze flexibiliteit kan problemen opleveren voor Azure spatiale ankers, omdat er beacons zijn die uniek worden geïdentificeerd door hun UUID. Het is niet mogelijk om ervoor te zorgen dat deze uniekheid waarschijnlijk ruimtelijke worm holes veroorzaakt. Voor de beste resultaten moet u het volgende doen:

* Wijs unieke UUID toe aan uw beacons.
* Implementeer deze-doorgaans in een gewoon patroon, zoals een raster.
* Geef de lijst met unieke Beacon-UUID door aan de sensor fingerprint provider:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

In azure spatiale ankers worden alleen Bluetooth-beacons bijgehouden die zich in de lijst met bekende Beacon-UUID bevinden. Kwaadwillende beacons die zijn geprogrammeerd met Allow-List-UUIDen kunnen nog steeds een negatieve invloed hebben op de kwaliteit van de service. Daarom moet u bakens alleen gebruiken in de ruimten met een hoofd element, waar u de implementatie ervan kunt beheren.

## <a name="querying-with-sensor-data"></a>Query's uitvoeren met sensor gegevens

Zodra u ankers met gekoppelde sensor gegevens hebt gemaakt, kunt u deze ophalen met behulp van de sensor-lees acties die zijn gerapporteerd door uw apparaat. U hoeft de service niet langer te leveren met een lijst met bekende ankers die u verwacht te vinden. in plaats daarvan kunt u de service alleen de locatie van uw apparaat laten weten zoals gerapporteerd door de opstart Sens oren. In azure spatiale ankers worden vervolgens de set ankers dicht op het apparaat weer gegeven en wordt geprobeerd om deze visueel te vergelijken.

Als u wilt dat query's gebruikmaken van de sensor gegevens, begint u met het maken van "bijna op apparaat" criteria:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

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

De `DistanceInMeters` para meter bepaalt hoe ver de anker grafiek wordt verkend om inhoud op te halen. Stel dat u een ruimte hebt gevuld met ankers met een constante densiteit van 2 elke meter. Bovendien bewaart de camera op het apparaat een enkel anker en de service heeft deze gevonden. U bent waarschijnlijk geïnteresseerd in het ophalen van alle ankers die u in de buurt hebt geplaatst, in plaats van het ene anker dat u momenteel bekijkt. Ervan uitgaande dat de ankers die u hebt geplaatst in een grafiek zijn verbonden, kan de service alle in de buurt geplaatste ankers voor u ophalen door de randen in de grafiek te volgen. De mate waarin de grafiek wordt gepasseerd, wordt beheerd door `DistanceInMeters` . u krijgt alle ankers die zijn verbonden met het punt dat u hebt gevonden, die dichter bij zijn dan `DistanceInMeters` .

Houd er bij dat grote waarden voor de `MaxResultCount` prestaties negatief kunnen beïnvloeden. Stel deze in op een bestellende waarde voor uw toepassing.

Ten slotte moet u de sessie laten weten dat u de op de sensor gebaseerde zoek opdracht wilt gebruiken:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[WinRT van C++](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Verwachte resultaten

GPS-apparaten van consumenten kwaliteit zijn doorgaans onnauwkeurig. Een studie van [Zandenbergen en Barbeau (2011)][6] rapporteert de mediaan nauw keurigheid van mobiele telefoons met ondersteuning van GPS (A-GPS) om ongeveer 7 meters te zijn. een grote waarde wordt genegeerd. Als u deze meet fouten wilt verwerken, behandelt de service de ankers als waarschijnlijkheids distributies in GPS-ruimte. Als zodanig is een anker nu de ruimte die het meest waarschijnlijk is (dat wil zeggen, met meer dan 95% betrouw baarheid). Dit is waar, onbekende GPS-positie.

Dezelfde reden hiervoor wordt toegepast bij het uitvoeren van query's met GPS. Het apparaat wordt weer gegeven als een andere ruimtelijk betrouw bare regio rondom de True, onbekende GPS-positie. Het detecteren van naburige ankers vertaalt gewoon het vinden van de ankers met betrouw bare regio's die *voldoende dicht* bij de betrouw bare regio van het apparaat staan, zoals in de onderstaande afbeelding wordt weer gegeven:

![Selectie van anker kandidaten met GPS](media/coarse-reloc-gps-separation-distance.png)

De nauw keurigheid van het GPS-signaal, zowel bij het maken van ankers als tijdens query's, heeft een grote invloed op de set geretourneerde ankers. Query's op basis van WiFi/beacons worden daarentegen beschouwd als alle ankers die ten minste één toegangs punt of Beacon bevatten die gemeen schappelijk zijn met de query. In dat opzicht wordt het resultaat van een query op basis van WiFi/beacons meestal bepaald door het fysieke bereik van de toegangs punten/beacons en omgevings obstakels.

In de onderstaande tabel wordt de verwachte Zoek ruimte voor elk type sensor geschat:

| Sensoren      | RADIUS van zoek ruimte (ong.) | Details |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Bepaald door de GPS-onzekerheid onder andere factoren. De gerapporteerde getallen worden geschat voor de mediaan GPS nauw keurigheid van mobiele telefoons met een-GPS, dat wil zeggen 7 meters. |
| WiFi        | 50 m-100 m | Bepaald door het bereik van de draadloze toegangs punten. Is afhankelijk van de frequentie, de verzender sterkte, de fysieke obstakels, de interferentie, enzovoort. |
| Conbakeners |  70 m | Bepaald door het bereik van het Beacon. Is afhankelijk van de frequentie, overdrachts sterkte, fysieke obstakels, interferentie, enzovoort. |

## <a name="per-platform-support"></a>Ondersteuning per platform

De volgende tabel bevat een overzicht van de sensor gegevens die op elk van de ondersteunde platforms zijn verzameld, samen met alle platformspecifieke voor behoud:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | N.v.t. | Ondersteund via [LocationManager][3] API'S (GPS en netwerk) | Ondersteund via [CLLocationManager][4] -api's |
| **WiFi**        | Wordt ondersteund met een snelheid van ongeveer één scan om de 3 seconden | Ondersteund. Vanaf API Level 28 worden WiFi-scans om de 2 minuten beperkt tot 4 aanroepen. In Android 10 kan de beperking worden uitgeschakeld vanuit het menu instellingen voor ontwikkel aars. Raadpleeg de [Android-documentatie][5]voor meer informatie. | N.v.t.-geen open bare API |
| **Conbakeners** | Beperkt tot [Eddystone][1] en [iBeacon][2] | Beperkt tot [Eddystone][1] en [iBeacon][2] | Beperkt tot [Eddystone][1] en [iBeacon][2] |

## <a name="next-steps"></a>Volgende stappen

Ruwe Herlokalisatie in een app gebruiken.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

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
