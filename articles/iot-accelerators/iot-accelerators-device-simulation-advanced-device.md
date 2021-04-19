---
title: Een geavanceerd gesimuleerd apparaatmodel maken - Azure| Microsoft Docs
description: In deze handleiding leert u hoe u een geavanceerd apparaatmodel maakt voor gebruik met de oplossingsversneller apparaatsimulatie.
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
ms.openlocfilehash: dc2eac4e784d4224581078348f12b231befe1f35
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714106"
---
# <a name="create-an-advanced-device-model"></a>Een geavanceerd apparaatmodel maken

In deze handleiding worden de JSON- en JavaScript-bestanden beschreven die een aangepast apparaatmodel definiëren. Het artikel bevat enkele voorbeeldbestanden voor apparaatmodeldefinitie en laat zien hoe u deze uploadt naar uw Device Simulation-exemplaar. U kunt geavanceerde apparaatmodellen maken om realistischer gedrag van apparaten te simuleren voor uw tests.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt volgen, hebt u een geïmplementeerd exemplaar van Apparaatsimulatie in uw Azure-abonnement nodig.

Als u nog geen apparaatsimulatie hebt geïmplementeerd, raadpleegt u [Apparaatsimulatie implementeren](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) in GitHub.

### <a name="open-device-simulation"></a>Apparaatsimulatie openen

Als u nog geen apparaatsimulatie hebt geïmplementeerd, raadpleegt u [Apparaatsimulatie implementeren](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) in GitHub.

## <a name="device-models"></a>Apparaatmodellen

Elk gesimuleerd apparaat behoort tot een specifiek apparaatmodel dat het simulatiegedrag definieert. Dit gedrag omvat hoe vaak telemetrie moet worden verzonden, wat voor soort berichten moeten worden verzonden en welke methoden worden ondersteund.

U definieert een apparaatmodel met behulp van een JSON-apparaatdefinitiebestand en een set JavaScript-bestanden. Deze JavaScript-bestanden definiëren het simulatiegedrag, zoals de willekeurige telemetrie en de methodelogica.

Een typisch apparaatmodel heeft:

* Eén JSON-bestand voor elk apparaatmodel (bijvoorbeeld elevator.jsaan).
* Eén JavaScript-gedragsscriptbestand voor elk apparaatmodel (bijvoorbeeld elevator-state.js)
* Eén scriptbestand voor de JavaScript-methode voor elke apparaatmethode (bijvoorbeeld elevator-go-down.js)

> [!NOTE]
> Niet alle apparaatmodellen definiëren methoden. Daarom kan een apparaatmodel al dan niet methodescripts hebben. Alle apparaatmodellen moeten echter een gedragsscript hebben.

## <a name="device-definition-file"></a>Apparaatdefinitiebestand

Elk apparaatdefinitiebestand bevat details van een gesimuleerd apparaatmodel, met inbegrip van de volgende informatie:

* Naam van apparaatmodel: tekenreeks.
* Protocol: AMQP-| MQTT-| HTTP.
* De initiële apparaattoestand.
* Hoe vaak de apparaattoestand moet worden vernieuwd.
* Welk JavaScript-bestand moet worden gebruikt om de apparaattoestand te vernieuwen.
* Een lijst met telemetrieberichten die moeten worden verzonden, elk met een specifieke frequentie.
* Het schema van de telemetrieberichten, gebruikt door de back-endtoepassing om de ontvangen telemetrie te parseren.
* Een lijst met ondersteunde methoden en het JavaScript-bestand dat moet worden gebruikt om elke methode te simuleren.

### <a name="file-schema"></a>Bestandsschema

De schemaversie is altijd '1.0.0' en is specifiek voor de indeling van dit bestand:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Beschrijving van apparaatmodel

De volgende eigenschappen beschrijven het apparaatmodel. Elk type heeft een unieke id, een semantische versie, een naam en een beschrijving:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protocol

IoT-apparaten kunnen verbinding maken met behulp van verschillende protocollen. Met de simulatie kunt u **AMQP,** **MQTT** of **HTTP gebruiken:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Status van gesimuleerd apparaat

Elk gesimuleerd apparaat heeft een interne status die moet worden gedefinieerd. De status definieert ook de eigenschappen die in telemetrie kunnen worden gerapporteerd. Een chiller kan bijvoorbeeld een initiële status hebben, zoals:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Een bewegend apparaat met verschillende sensoren heeft mogelijk meer eigenschappen, bijvoorbeeld:

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

De apparaattoestand wordt in het geheugen opgeslagen door de simulatieservice en als invoer voor de JavaScript-functie geleverd. De JavaScript-functie kan het volgende bepalen:

* Om de status te negeren en willekeurige gegevens te genereren.
* Om de apparaattoestand op een realistische manier bij te werken voor een bepaald scenario.

De functie die de status genereert, ontvangt ook als invoer:

* De apparaat-id.
* Het apparaatmodel.
* De huidige tijd. Met deze waarde kunt u verschillende gegevens per apparaat en per tijd genereren.

### <a name="generating-telemetry-messages"></a>Telemetrieberichten genereren

De simulatieservice kan verschillende telemetrietypen voor elk apparaat verzenden. Normaal gesproken bevat telemetrie gegevens van de apparaattoestand. Een gesimuleerde ruimte kan bijvoorbeeld elke 10 seconden informatie verzenden over de temperatuur en vochtigheid. Let op de tijdelijke aanduidingen in het volgende codefragment, die automatisch worden vervangen door waarden uit de apparaattoestand:

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

De tijdelijke aanduidingen gebruiken een speciale syntaxis **${NAME}** waarbij **NAME** een sleutel is van het apparaattoestandobject dat wordt geretourneerd door de **JavaScript-hoofdfunctie.** Tekenreeksen moeten worden aangehaald, terwijl getallen dat niet mogen.

#### <a name="message-schema"></a>Berichtschema

Elk berichttype moet een goed gedefinieerd schema hebben. Het berichtschema wordt ook gepubliceerd naar IoT Hub, zodat back-endtoepassingen de informatie opnieuw kunnen gebruiken om de binnenkomende telemetrie te interpreteren.

Het schema ondersteunt de JSON-indeling, waarmee u eenvoudig kunt parseren, transformeren en analyseren in verschillende systemen en services.

De velden die in het schema worden vermeld, kunnen van de volgende typen zijn:

* Object : geseraliseerd met behulp van JSON
* Binair : geseraliseerd met base64
* Tekst
* Booleaans
* Geheel getal
* Dubbel
* DateTime

### <a name="supported-methods"></a>Ondersteunde methoden

Gesimuleerde apparaten kunnen ook reageren op methode-aanroepen. In dat geval voeren ze logica uit en geven ze een antwoord. Net als bij de simulatie wordt de methodelogica opgeslagen in een JavaScript-bestand en kan deze communiceren met de apparaattoestand. Bijvoorbeeld:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Een apparaatdefinitiebestand maken

In deze handleiding ziet u hoe u een apparaatmodel voor een drone maakt. De drone zal willekeurig rond een eerste set coördinaten gaan die de locatie en hoogte wijzigen.

Kopieer de volgende JSON in een teksteditor en sla deze op als **drone.jsop**.

### <a name="device-definition-json-example"></a>JSON-voorbeeld van apparaatdefinitie

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

## <a name="behavior-script-files"></a>Scriptbestanden voor gedrag

De code in het gedragsscriptbestand verplaatst de drone. Het script wijzigt de verhoging en locatie van de drone door de geheugentoestand van het apparaat te bewerken.

De JavaScript-bestanden moeten een **hoofdfunctie** hebben die twee parameters accepteert:

* Een **contextobject** dat drie eigenschappen bevat:
    * **currentTime** als een tekenreeks met de notatie **yyyy-MM-dd'T'HH:mm:sszzz.**
    * **deviceId**. Bijvoorbeeld **Simulated.Elevator.123.**
    * **deviceModel**. Bijvoorbeeld **Lift**.
* Een **statusobject** dat de waarde is die wordt geretourneerd door de functie in de vorige aanroep. Deze apparaattoestand wordt onderhouden door de simulatieservice en wordt gebruikt om telemetrieberichten te genereren.

De **hoofdfunctie** retourneert de status van het nieuwe apparaat. Bijvoorbeeld:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Een scriptbestand voor gedrag maken

Kopieer het volgende JavaScript in een teksteditor en sla het op **alsdrone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Voorbeeld van JavaScript-simulatie van apparaatmodel

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

## <a name="create-a-method-script-file"></a>Een methodescriptbestand maken

Methodescripts zijn vergelijkbaar met gedragsscripts. Ze definiëren het gedrag van het apparaat wanneer een specifieke cloud-naar-apparaat-methode wordt aangeroepen.

Het terugroepscript voor drones stelt de coördinaten van de drone in op een vast punt om te simuleren dat de drone terugkomt.

Kopieer het volgende JavaScript in een teksteditor en sla het op **alsdroneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Voorbeeld van JavaScript-simulatie van apparaatmodel

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

## <a name="debugging-script-files"></a>Scriptbestanden voor het opsporen van bestanden

Hoewel u geen debugger kunt koppelen aan een actief gedragsbestand, is het mogelijk om informatie naar het servicelogboek te schrijven met behulp van de **logboekfunctie.** Voor syntaxisfouten mislukt de interpreter en schrijft informatie over de uitzondering naar het logboek.

Voorbeeld van logboekregistratie:

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

## <a name="deploy-an-advanced-device-model"></a>Een geavanceerd apparaatmodel implementeren

Als u uw geavanceerde apparaatmodel wilt implementeren, uploadt u de bestanden van uw apparaatsimulatie-exemplaar:

Selecteer **Apparaatmodellen** in de menubalk. Op **de pagina Apparaatmodellen** worden de apparaatmodellen weergegeven die beschikbaar zijn in dit exemplaar van Apparaatsimulatie:

![Apparaatmodellen](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klik op **+ Apparaatmodellen toevoegen** in de rechterbovenhoek van de pagina:

![Apparaatmodel toevoegen](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klik **op Geavanceerd** om het tabblad Geavanceerd apparaatmodel te openen:

![Tabblad Geavanceerd](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klik **op Bladeren** en selecteer de JSON- en JavaScript-bestanden die u hebt gemaakt. Zorg ervoor dat u alle drie de bestanden selecteert. Als er één bestand ontbreekt, mislukt de validatie:

![Door bestanden bladeren](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Als uw bestanden zijn gevalideerd, klikt u **op Opslaan** en is uw apparaatmodel gereed voor gebruik in een simulatie. Los anders eventuele fouten op en plaats de bestanden opnieuw:

![Opslaan](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u geleerd over de apparaatmodelbestanden die worden gebruikt in Apparaatsimulatie en hoe u een geavanceerd apparaatmodel maakt. Vervolgens kunt u verkennen hoe u met Time Series Insights telemetrie kunt visualiseren die is verzonden vanuit de oplossingsversneller [voor apparaatsimulatie.](./iot-accelerators-device-simulation-time-series-insights.md)