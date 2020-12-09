---
title: Een model voor een geavanceerd gesimuleerd apparaat maken-Azure | Microsoft Docs
description: In deze hand leiding vindt u informatie over het maken van een geavanceerd apparaatprofiel dat u kunt gebruiken met de apparaat simulatie oplossings versneller.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852554"
---
# <a name="create-an-advanced-device-model"></a>Een geavanceerd apparaatmodel maken

In deze hand leiding vindt u een beschrijving van de JSON-en Java script-bestanden die een aangepast model definiëren. Het artikel bevat een aantal voor beelden van een definitie bestand voor een apparaat model en laat zien hoe u deze uploadt naar uw apparaat simulatie exemplaar. U kunt geavanceerde modellen maken voor het simuleren van meer realistisch gedrag van apparaten voor uw test doeleinden.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt volgen, hebt u een geïmplementeerd exemplaar van Device simulatie in uw Azure-abonnement nodig.

Zie [device simulatie Deployment](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) op github als u nog geen apparaat simulatie hebt geïmplementeerd.

### <a name="open-device-simulation"></a>Apparaatsimulatie openen

Zie [device simulatie Deployment](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) op github als u nog geen apparaat simulatie hebt geïmplementeerd.

## <a name="device-models"></a>Apparaatmodellen

Elk gesimuleerd apparaat behoort tot een specifiek apparaatprofiel dat het simulatie gedrag definieert. Dit gedrag omvat het regel matig verzenden van telemetrie, het soort berichten dat moet worden verzonden en de ondersteunde methoden.

U definieert een model met behulp van een JSON-apparaat definitie bestand en een set java script-bestanden. Deze Java script-bestanden definiëren het simulatie gedrag, zoals de wille keurige telemetrie en de methode logica.

Een typisch model voor apparaten is:

* Eén JSON-bestand voor elk model apparaat (bijvoorbeeld elevator.jsop).
* Eén java script-gedrag script bestand voor elk model apparaat (bijvoorbeeld elevator-state.js)
* Eén java script-methode script bestand voor elke methode (bijvoorbeeld elevator-go-down.js)

> [!NOTE]
> Niet alle apparaten modellen definiëren methoden. Daarom kan een model van het apparaat geen methode scripts bevatten. Alle apparaatprofielen moeten echter een gedrags script hebben.

## <a name="device-definition-file"></a>Definitie bestand van apparaat

Elk definitie bestand voor het apparaat bevat details van een model met gesimuleerde apparaten, met inbegrip van de volgende informatie:

* Naam van het apparaat model: teken reeks.
* Protocol: AMQP | MQTT | HTTP.
* De initiële Apparaatstatus.
* Hoe vaak de apparaatstatus moet worden vernieuwd.
* Welk Java script-bestand moet worden gebruikt om de status van het apparaat te vernieuwen.
* Een lijst met telemetriegegevens die moeten worden verzonden, elk met een specifieke frequentie.
* Het schema van de telemetrie-berichten, gebruikt door de back-endtoepassing voor het parseren van de ontvangen telemetrie.
* Een lijst met ondersteunde methoden en het Java script-bestand dat moet worden gebruikt voor het simuleren van elke methode.

### <a name="file-schema"></a>Bestandsschema

De schema versie is altijd "1.0.0" en is specifiek voor de indeling van dit bestand:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Beschrijving van het model van het apparaat

De volgende eigenschappen beschrijven het model van het apparaat. Elk type heeft een unieke id, een semantische versie, een naam en een beschrijving:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-Protocol

IoT-apparaten kunnen verbinding maken met behulp van verschillende protocollen. Met de simulatie kunt u **AMQP**, **MQTT** of **http** gebruiken:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Status gesimuleerd apparaat

Elk gesimuleerd apparaat heeft een interne status, die moet worden gedefinieerd. De status definieert ook de eigenschappen die kunnen worden gerapporteerd in telemetrie. Een chiller kan bijvoorbeeld een initiële status hebben, zoals:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Een bewegend apparaat met meerdere Sens oren kan meer eigenschappen hebben, bijvoorbeeld:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

De apparaatstatus wordt in het geheugen bewaard door de simulatie service en wordt opgegeven als invoer voor de Java script-functie. De Java script-functie kan besluiten:

* Om de status te negeren en enige wille keurige gegevens te genereren.
* Om de status van het apparaat op een realistische manier bij te werken voor een bepaald scenario.

De functie die de status genereert, ontvangt ook de invoer:

* De apparaat-ID.
* Het apparaatmodel.
* De huidige tijd. Met deze waarde kunnen per apparaat en tijd verschillende gegevens worden gegenereerd.

### <a name="generating-telemetry-messages"></a>Telemetrie-berichten genereren

De simulatie service kan verschillende typen telemetrie voor elk apparaat verzenden. Meestal bevat telemetrie gegevens uit de Apparaatstatus. Een gesimuleerde ruimte kan bijvoorbeeld elke 10 seconden informatie over de Tempe ratuur en vochtigheid verzenden. Let op de tijdelijke aanduidingen in het volgende code fragment, die automatisch worden vervangen door waarden van de apparaatstatus:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

De tijdelijke aanduidingen gebruiken een speciale syntaxis **$ {name}** waarbij **name** een sleutel is uit het object status van het apparaat dat door de Java script- **hoofd** functie wordt geretourneerd. Teken reeksen moeten worden genoteerd, terwijl de getallen niet mogen zijn.

#### <a name="message-schema"></a>Bericht schema

Elk bericht type moet een goed gedefinieerd schema hebben. Het bericht schema wordt ook gepubliceerd op IoT Hub, zodat back-end-toepassingen de gegevens opnieuw kunnen gebruiken om de binnenkomende telemetrie te interpreteren.

Het schema ondersteunt de JSON-indeling, waarmee eenvoudig parseren, trans formatie en analyses kunnen worden geparseerd in verschillende systemen en services.

De velden die in het schema worden weer gegeven, kunnen van de volgende typen zijn:

* Object-geserialiseerd met JSON
* Binair geserialiseerd met base64
* Tekst
* Booleaans
* Geheel getal
* Dubbel
* DateTime

### <a name="supported-methods"></a>Ondersteunde methoden

Gesimuleerde apparaten kunnen ook reageren op methode aanroepen, in welk geval ze een bepaalde logica uitvoeren en een antwoord geven. Net als bij de simulatie wordt de methode logica opgeslagen in een Java script-bestand en kan de status van het apparaat worden gebruikt. Bijvoorbeeld:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Een definitie bestand voor een apparaat maken

In deze hand leiding ziet u hoe u een model maakt voor een drone. De drone keert wille keurig rond een initiële set coördinaten om locatie en hoogte te wijzigen.

Kopieer de volgende JSON naar een tekst editor en sla deze **op alsdrone.jsop**.

### <a name="device-definition-json-example"></a>JSON-voor beeld van Device definition

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Script bestanden voor gedrag

De code in het script bestand voor gedrag verplaatst de drone. Het script wijzigt de uitbrei ding en locatie van de drone door de status van het apparaat in het geheugen te bewerken.

De Java script-bestanden moeten een **hoofd** functie hebben die twee para meters accepteert:

* Een **context** object dat drie eigenschappen bevat:
    * **currentTime** als een teken reeks met de notatie **jjjj-mm-dd'T'HH: mm: sszzz**.
    * **deviceId**. Bijvoorbeeld **gesimuleerd. lift. 123**.
    * **deviceModel**. Bijvoorbeeld **Lift**.
* Een **status** object dat de waarde die door de functie in de vorige aanroep wordt geretourneerd. Deze Apparaatstatus wordt onderhouden door de simulatie service en wordt gebruikt voor het genereren van telemetrie-berichten.

De functie **Main** retourneert de nieuwe Apparaatstatus. Bijvoorbeeld:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Een script bestand voor gedrag maken

Kopieer de volgende Java script in een tekst editor en sla het op als **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Voor beeld van Java script-simulatie van Device model

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Een methode script bestand maken

Methode scripts zijn vergelijkbaar met gedrags scripts. Hiermee wordt het gedrag van het apparaat gedefinieerd wanneer een specifieke Cloud naar een methode wordt aangeroepen.

Het drone-aanroepen script stelt de coördinaten van de Drone in op een vast punt om de drone terugkerende start te simuleren.

Kopieer de volgende Java script in een tekst editor en sla het op als **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Voor beeld van Java script-simulatie van Device model

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Script bestanden voor fout opsporing

Hoewel u een fout opsporingsprogramma niet kunt koppelen aan een actief gedrags bestand, is het mogelijk om gegevens naar het service logboek te schrijven met behulp van de **logboek** functie. Voor syntaxis fouten mislukt de interpreter en schrijft informatie over de uitzonde ring naar het logboek.

Voor beeld van logboek registratie:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Een geavanceerd model voor apparaten implementeren

Als u uw geavanceerde model voor apparaten wilt implementeren, uploadt u de bestanden uw simulatie-exemplaar van apparaat:

Selecteer **Apparaatmodellen** in de menubalk. De pagina **modellen van apparaten** bevat een lijst met de modellen die beschikbaar zijn in deze instantie van Device simulatie:

![Apparaatmodellen](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klik op **+ Apparaatmodellen toevoegen** in de rechterbovenhoek van de pagina:

![Apparaatmodel toevoegen](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klik op **Geavanceerd** om het tabblad Geavanceerd model voor apparaten te openen:

![Tabblad Geavanceerd](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klik op **Bladeren** en selecteer de JSON-en Java script-bestanden die u hebt gemaakt. Zorg ervoor dat u alle drie de bestanden selecteert. Als er één bestand ontbreekt, mislukt de validatie:

![Door bestanden bladeren](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Als uw bestanden worden gevalideerd, klikt u op **Opslaan** en is het model van uw apparaat klaar voor gebruik in een simulatie. Corrigeer anders eventuele fouten en upload de bestanden opnieuw:

![Opslaan](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding vindt u informatie over de model bestanden van het apparaat die worden gebruikt in simulatie van apparaten en het maken van een geavanceerd model voor apparaten. Vervolgens wilt u mogelijk verkennen hoe u Time Series Insights kunt [gebruiken om de telemetrie te visualiseren die wordt verzonden vanuit de Device simulatie Solution Accelerator](./iot-accelerators-device-simulation-time-series-insights.md).