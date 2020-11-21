---
title: Ruimtelijke analyse bewerkingen
titleSuffix: Azure Cognitive Services
description: De ruimtelijke analyse bewerkingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b6e20bf2decfe051e79e073736f71181260fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014540"
---
# <a name="spatial-analysis-operations"></a>Ruimtelijke analyse bewerkingen

Met ruimtelijke analyse kunt u het realtime streamen van video van camera's analyseren. Voor elke camera die u configureert, wordt een uitvoerstroom van JSON-berichten gegenereerd die naar uw exemplaar van Azure IoT Hub wordt verzonden. 

De container voor ruimtelijke analyse implementeert de volgende bewerkingen:

| Bewerkings-id| Description|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-personcount | Telt het aantal personen in een aangewezen zone in het veld weer gave van de camera. <br> Hiermee wordt een eerste _personCountEvent_ -gebeurtenis verzonden en vervolgens _personCountEvent_ -gebeurtenissen wanneer het aantal wordt gewijzigd.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline | Hiermee wordt getraceerd wanneer een persoon een opgegeven regel in het veld weer gave van de camera kruist. <br>Hiermee wordt een _personLineEvent_ -gebeurtenis verzonden wanneer de persoon de lijn snijdt en informatie geeft over de richting. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon | Hiermee wordt getraceerd wanneer een persoon een opgegeven regel in het veld weer gave van de camera kruist. <br> Hiermee wordt een _personLineEvent_ -gebeurtenis verzonden wanneer de persoon de zone kruist en informatie bevat. |
| cognitiveservices. Vision. spatialanalysis-persondistance | Hiermee wordt getraceerd wanneer mensen een afstands regel schenden. <br> Verzendt een _personDistanceEvent_ regel matig met de locatie van elke afstands schending. |

Alle bovenstaande bewerkingen zijn ook beschikbaar in de `.debug` versie, die de mogelijkheid hebben om de video frames te visualiseren wanneer ze worden verwerkt. U moet `xhost +` op de hostcomputer worden uitgevoerd om de visualisatie van video frames en-gebeurtenissen in te scha kelen.

| Bewerkings-id| Description|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-personcount. debug | Telt het aantal personen in een aangewezen zone in het veld weer gave van de camera. <br> Hiermee wordt een eerste _personCountEvent_ -gebeurtenis verzonden en vervolgens _personCountEvent_ -gebeurtenissen wanneer het aantal wordt gewijzigd.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline. debug | Hiermee wordt getraceerd wanneer een persoon een opgegeven regel in het veld weer gave van de camera kruist. <br>Hiermee wordt een _personLineEvent_ -gebeurtenis verzonden wanneer de persoon de lijn snijdt en informatie geeft over de richting. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. debug | Hiermee wordt getraceerd wanneer een persoon een opgegeven regel in het veld weer gave van de camera kruist. <br> Hiermee wordt een _personLineEvent_ -gebeurtenis verzonden wanneer de persoon de zone kruist en informatie bevat. |
| cognitiveservices. Vision. spatialanalysis-persondistance. debug | Hiermee wordt getraceerd wanneer mensen een afstands regel schenden. <br> Verzendt een _personDistanceEvent_ regel matig met de locatie van elke afstands schending. |

Ruimtelijke analyse kan ook worden uitgevoerd met [Live video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) als video Ai-module. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Bewerkings-id| Description|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-personcount. livevideoanalytics | Telt het aantal personen in een aangewezen zone in het veld weer gave van de camera. <br> Hiermee wordt een eerste _personCountEvent_ -gebeurtenis verzonden en vervolgens _personCountEvent_ -gebeurtenissen wanneer het aantal wordt gewijzigd.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Hiermee wordt getraceerd wanneer een persoon een opgegeven regel in het veld weer gave van de camera kruist. <br>Hiermee wordt een _personLineEvent_ -gebeurtenis verzonden wanneer de persoon de lijn snijdt en informatie geeft over de richting. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Hiermee wordt getraceerd wanneer een persoon een opgegeven regel in het veld weer gave van de camera kruist. <br> Hiermee wordt een _personLineEvent_ -gebeurtenis verzonden wanneer de persoon de zone kruist en informatie bevat. |
| cognitiveservices. Vision. spatialanalysis-persondistance. livevideoanalytics | Hiermee wordt getraceerd wanneer mensen een afstands regel schenden. <br> Verzendt een _personDistanceEvent_ regel matig met de locatie van elke afstands schending. |

Live video Analytics-bewerkingen zijn ook beschikbaar in de `.debug` versie (bijvoorbeeld cognitiveservices. Vision. spatialanalysis-personcount. livevideoanalytics. debug), die de mogelijkheid heeft om de video frames te visualiseren als verwerkt. U moet `xhost +` op de hostcomputer worden uitgevoerd om de visualisatie van de video frames en gebeurtenissen in te scha kelen

> [!IMPORTANT]
> De computer vision-AI-modellen detecteren en zoeken naar menselijke aanwezigheid in video beelden en uitvoer met behulp van een omsluitend kader rond een menselijk lichaam. De AI-modellen proberen geen gezichten te detecteren of de identiteiten of demografische gegevens van personen te ontdekken.

Dit zijn de para meters die vereist zijn voor elk van deze ruimtelijke analyse bewerkingen.

| Bewerkingsparameters| Description|
|---------|---------|
| Bewerkings-ID | De bewerkings-id uit de bovenstaande tabel.|
| enabled | Booleaans: waar of onwaar|
| VIDEO_URL| De RTSP-URL voor het camera apparaat (bijvoorbeeld: `rtsp://username:password@url` ). Ruimtelijke analyse ondersteunt de H. 264-gecodeerde stroom via RTSP, http of MP4 |
| VIDEO_SOURCE_ID | Een beschrijvende naam voor het camera apparaat of de video stroom. Dit wordt geretourneerd met de JSON-uitvoer van de gebeurtenis.|
| VIDEO_IS_LIVE| True voor camera apparaten; ONWAAR voor opgenomen Video's.|
| VIDEO_DECODE_GPU_INDEX| De GPU voor het decoderen van het video frame. De standaard waarde is 0. Moet hetzelfde zijn als de `gpu_index` in andere configuratie van het knoop punt `VICA_NODE_CONFIG` , zoals, `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | JSON die aangeeft in welke GPU het detector knooppunt moet worden uitgevoerd. Moet de volgende indeling hebben: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | De JSON-configuratie voor de zone en de regel, zoals hieronder wordt beschreven.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Zone configuratie voor cognitiveservices. Vision. spatialanalysis-personcount

 Dit is een voor beeld van een JSON-invoer voor de para meter SPACEANALYTICS_CONFIG waarmee een zone wordt geconfigureerd. U kunt meerdere zones configureren voor deze bewerking.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `zones` | list| Lijst met zones. |
| `name` | tekenreeks| Beschrijvende naam voor deze zone.|
| `polygon` | list| Elk waardepaar vertegenwoordigt de x, y voor hoek punten van een veelhoek. De veelhoek vertegenwoordigt de gebieden waarin gebruikers worden bijgehouden of geteld en veelhoek punten zijn gebaseerd op genormaliseerde coördinaten (0-1), waarbij de linkerbovenhoek (0,0, 0,0) en de rechter benedenhoek (1,0, 1,0) is.   
| `threshold` | float| Gebeurtenissen worden egressed wanneer het vertrouwen van de AI-modellen groter is dan of gelijk is aan deze waarde. |
| `type` | tekenreeks| Voor **cognitiveservices. Vision. spatialanalysis-personcount** dit moet zijn `count` .|
| `trigger` | tekenreeks| Het type trigger voor het verzenden van een gebeurtenis. Ondersteunde waarden zijn `event` voor het verzenden van gebeurtenissen wanneer het aantal wordt gewijzigd of als `interval` er periodiek gebeurtenissen worden verzonden, ongeacht of het aantal is gewijzigd of niet.
| `interval` | tekenreeks| Een tijd in seconden dat het aantal personen wordt geaggregeerd voordat een gebeurtenis wordt gestart. Met de bewerking wordt de scène op constant tempo geanalyseerd en wordt het meest voorkomende aantal voor dat interval geretourneerd. Het aggregatie-interval is van toepassing op zowel als `event` `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Lijn configuratie voor cognitiveservices. Vision. spatialanalysis-personcrossingline

Dit is een voor beeld van een JSON-invoer voor de para meter SPACEANALYTICS_CONFIG die een regel configureert. U kunt meerdere snij lijnen configureren voor deze bewerking.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `lines` | list| Lijst met regels.|
| `name` | tekenreeks| Beschrijvende naam voor deze regel.|
| `line` | list| De definitie van de regel. Dit is een omleidings lijn waarmee u de vermelding ' entry ' versus ' exit ' kunt begrijpen.|
| `start` | waardepaar| x, y-coördinaten voor het begin punt van de lijn. De float-waarden geven de positie van het hoek punt aan ten opzichte van de bovenkant van de linkerbovenhoek. U kunt de absolute x-en y-waarden berekenen door deze waarden te vermenigvuldigen met de grootte van het kader. |
| `end` | waardepaar| x, y-coördinaten voor het eind punt van de lijn. De float-waarden geven de positie van het hoek punt aan ten opzichte van de bovenkant van de linkerbovenhoek. U kunt de absolute x-en y-waarden berekenen door deze waarden te vermenigvuldigen met de grootte van het kader. |
| `threshold` | float| Gebeurtenissen worden egressed wanneer het vertrouwen van de AI-modellen groter is dan of gelijk is aan deze waarde. |
| `type` | tekenreeks| Voor **cognitiveservices. Vision. spatialanalysis-personcrossingline** dit moet zijn `linecrossing` .|
|`trigger`|tekenreeks|Het type trigger voor het verzenden van een gebeurtenis.<br>Ondersteunde waarden: ' event ': wordt gestart wanneer iemand de regel kruist.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zone configuratie voor cognitiveservices. Vision. spatialanalysis-personcrossingpolygon

Dit is een voor beeld van een JSON-invoer voor de para meter SPACEANALYTICS_CONFIG waarmee een zone wordt geconfigureerd. U kunt meerdere zones configureren voor deze bewerking.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `zones` | list| Lijst met zones. |
| `name` | tekenreeks| Beschrijvende naam voor deze zone.|
| `polygon` | list| Elk waardepaar vertegenwoordigt de x, y voor hoek punten van de polygoon. De veelhoek vertegenwoordigt de gebieden waarin mensen worden getraceerd of geteld. De float-waarden geven de positie van het hoek punt aan ten opzichte van de bovenkant van de linkerbovenhoek. U kunt de absolute x-en y-waarden berekenen door deze waarden te vermenigvuldigen met de grootte van het kader. 
| `threshold` | float| Gebeurtenissen worden egressed wanneer het vertrouwen van de AI-modellen groter is dan of gelijk is aan deze waarde. |
| `type` | tekenreeks| Voor **cognitiveservices. Vision. spatialanalysis-personcrossingpolygon** dit moet zijn `enter` of `exit` .|
| `trigger`|tekenreeks|Het type trigger voor het verzenden van een gebeurtenis<br>Ondersteunde waarden: ' event ': wordt geactiveerd wanneer iemand de zone invoert of verlaat.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zone configuratie voor cognitiveservices. Vision. spatialanalysis-persondistance

Dit is een voor beeld van een JSON-invoer voor de para meter SPACEANALYTICS_CONFIG waarmee een zone wordt geconfigureerd voor **cognitiveservices. Vision. spatialanalysis-persondistance**. U kunt meerdere zones configureren voor deze bewerking.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `zones` | list| Lijst met zones. |
| `name` | tekenreeks| Beschrijvende naam voor deze zone.|
| `polygon` | list| Elk waardepaar vertegenwoordigt de x, y voor hoek punten van de polygoon. De veelhoek vertegenwoordigt de gebieden waarin mensen worden geteld en de afstand tussen mensen wordt gemeten. De float-waarden geven de positie van het hoek punt aan ten opzichte van de bovenkant van de linkerbovenhoek. U kunt de absolute x-en y-waarden berekenen door deze waarden te vermenigvuldigen met de grootte van het kader. 
| `threshold` | float| Gebeurtenissen worden egressed wanneer het vertrouwen van de AI-modellen groter is dan of gelijk is aan deze waarde. |
| `type` | tekenreeks| Voor **cognitiveservices. Vision. spatialanalysis-persondistance** dit moet zijn `people_distance` .|
| `trigger` | tekenreeks| Het type trigger voor het verzenden van een gebeurtenis. Ondersteunde waarden zijn `event` voor het verzenden van gebeurtenissen wanneer het aantal wordt gewijzigd of als `interval` er periodiek gebeurtenissen worden verzonden, ongeacht of het aantal is gewijzigd of niet.
| `interval` | tekenreeks | Een tijd in seconden dat de schendingen worden geaggregeerd voordat een gebeurtenis wordt gestart. Het aggregatie-interval is van toepassing op zowel als `event` `interval` .|
| `output_frequency` | int | De snelheid waarmee gebeurtenissen worden egressed. Als `output_frequency` = X, elke X gebeurtenis is egressed, ex. `output_frequency` = 2 betekent dat elke andere gebeurtenis uitvoer is. De output_frequency is van toepassing op zowel als `event` `interval` .|
| `minimum_distance_threshold` | float| Een afstand in meter waarmee een "TooClose"-gebeurtenis wordt geactiveerd wanneer mensen kleiner zijn dan de afstand tussen elkaar.|
| `maximum_distance_threshold` | float| Een afstand in meter waarmee de gebeurtenis ' TooFar ' wordt geactiveerd wanneer mensen groter zijn dan de afstand tussen elkaar.|

Dit is een voor beeld van een JSON-invoer voor de para meter DETECTOR_NODE_CONFIG waarmee een **cognitiveservices. Vision. spatialanalysis-persondistance-** zone wordt geconfigureerd.

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `gpu_index` | tekenreeks| De GPU-index waarop deze bewerking wordt uitgevoerd.|
| `do_calibration` | tekenreeks | Hiermee wordt aangegeven dat de kalibratie is ingeschakeld. `do_calibration` moet waar zijn om **cognitiveservices. Vision. spatialanalysis-persondistance** goed te laten functioneren.|

Raadpleeg de richt lijnen voor [camera plaatsing](spatial-analysis-camera-placement.md)  voor meer informatie over zone-en lijn configuraties.

## <a name="spatial-analysis-operation-output"></a>Uitvoer van ruimtelijke analyse bewerkingen

De gebeurtenissen van elke bewerking zijn egressed naar Azure IoT Hub in JSON-indeling.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-indeling voor cognitiveservices. Vision. spatialanalysis-personcount AI Insights

Voor beeld-JSON voor een gebeurtenis uitvoer door deze bewerking.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Naam van gebeurtenis veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoon die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `trackinId` | tekenreeks| De unieke id van de gedetecteerde persoon|
| `status` | tekenreeks| ' Enter ' of ' exit '|
| `side` | int| Het nummer van de zijde van de veelhoek die de persoon heeft gekruist|
| `zone` | tekenreeks | Het veld naam van de veelhoek dat de gekruiste zone vertegenwoordigt|
| `trigger` | tekenreeks| Het trigger type is ' event ' of ' interval ', afhankelijk van de waarde `trigger` in SPACEANALYTICS_CONFIG|

| Naam van detectie veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-ID|
| `type` | tekenreeks| Detectie type|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| Linksboven en rechtsonder, wanneer het gebieds type rechthoek is |
| `confidence` | float| Algoritme vertrouwen|

| SourceInfo veld naam | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | datum| UTC-datum waarop de JSON-nettolading is verzonden|
| `width` | int | Breedte van video frame|
| `height` | int | Hoogte van video frame|
| `frameId` | int | Frame-id|
| `cameraCallibrationInfo` | verzameling | Verzameling waarden|
| `status` | tekenreeks | Hiermee wordt aangegeven of de camera kalibratie op grond van het wegdek ' volledig ' is|
| `cameraHeight` | float | De hoogte van de camera boven het wegdek in Foot. Dit wordt afgeleid van automatische kalibratie. |
| `focalLength` | float | De brand lengte van de camera in pixels. Dit wordt afgeleid van automatische kalibratie. |
| `tiltUpAngle` | float | De hoek van de camera kantelt van verticaal. Dit wordt afgeleid van automatische kalibratie.|

| SourceInfo veld naam | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | datum| UTC-datum waarop de JSON-nettolading is verzonden|
| `width` | int | Breedte van video frame|
| `height` | int | Hoogte van video frame|
| `frameId` | int | Frame-id|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-indeling voor cognitiveservices. Vision. spatialanalysis-personcrossingline AI Insights

Voorbeeld JSON voor detecties die door deze bewerking worden uitgevoerd.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Naam van gebeurtenis veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoon die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `trackinId` | tekenreeks| De unieke id van de gedetecteerde persoon|
| `status` | tekenreeks| Richting van de lijn kruisingen, ' CrossLeft ' of ' CrossRight '|
| `zone` | tekenreeks | Het veld naam van de regel die is gekruist|

| Naam van detectie veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-ID|
| `type` | tekenreeks| Detectie type|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| Linksboven en rechtsonder, wanneer het gebieds type rechthoek is |
| `confidence` | float| Algoritme vertrouwen|

| SourceInfo veld naam | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | datum| UTC-datum waarop de JSON-nettolading is verzonden|
| `width` | int | Breedte van video frame|
| `height` | int | Hoogte van video frame|
| `frameId` | int | Frame-id|


> [!IMPORTANT]
> Het AI-model detecteert een persoon ongeacht of de persoon naar of buiten de camera is gericht. Het AI-model voert geen gezichts detectie of herkenning uit en verstrekt geen biometrische informatie. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-indeling voor cognitiveservices. Vision. spatialanalysis-personcrossingpolygon AI Insights

Voorbeeld JSON voor detecties die door deze bewerking worden uitgevoerd.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Naam van gebeurtenis veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoon die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `trackinId` | tekenreeks| De unieke id van de gedetecteerde persoon|
| `status` | tekenreeks| Richting van veelhoek kruisingen, ' Enter ' of ' exit '|
| `zone` | tekenreeks | Het veld naam van de veelhoek dat de gekruiste zone vertegenwoordigt|

| Naam van detectie veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-ID|
| `type` | tekenreeks| Detectie type|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| Linksboven en rechtsonder, wanneer het gebieds type rechthoek is |
| `confidence` | float| Algoritme vertrouwen|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-indeling voor cognitiveservices. Vision. spatialanalysis-persondistance AI Insights

Voorbeeld JSON voor detecties die door deze bewerking worden uitgevoerd.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Naam van gebeurtenis veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoon die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `personCount` | int| Aantal personen dat is gedetecteerd bij het verzenden van de gebeurtenis|
| `averageDistance` | float| De gemiddelde afstand tussen alle gedetecteerde mensen in Foot|
| `minimumDistanceThreshold` | float| De afstand in meter waarmee een "TooClose"-gebeurtenis wordt geactiveerd wanneer mensen kleiner zijn dan de afstand tussen elkaar.|
| `maximumDistanceThreshold` | float| De afstand in meter waarmee een "TooFar"-gebeurtenis wordt geactiveerd wanneer mensen groter zijn dan de afstand tussen elkaar.|
| `eventName` | tekenreeks| De gebeurtenis naam wordt `TooClose` `minimumDistanceThreshold` geschonden, wanneer deze wordt `TooFar` geschonden, `maximumDistanceThreshold` of wanneer de `unknown` automatische kalibratie niet is voltooid|
| `distanceViolationPersonCount` | int| Aantal gedetecteerde personen in schending van `minimumDistanceThreshold` of `maximumDistanceThreshold`|
| `zone` | tekenreeks | Het veld naam van de polygoon die de zone vertegenwoordigt die is gecontroleerd op distancing tussen personen|
| `trigger` | tekenreeks| Het trigger type is ' event ' of ' interval ', afhankelijk van de waarde `trigger` in SPACEANALYTICS_CONFIG|

| Naam van detectie veld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-ID|
| `type` | tekenreeks| Detectie type|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| Linksboven en rechtsonder, wanneer het gebieds type rechthoek is |
| `confidence` | float| Algoritme vertrouwen|
| `centerGroundPoint` | 2 float-waarden| `x`, `y` waarden met de coördinaten van de uitgestelde locatie van de persoon op het grond vlak. `x` is afstand van de camera lood recht op het camera-afbeeldings vlak dat op het wegdek is geprojecteerd. `y` is de afstand van de camera parallel met het afbeeldings vlak dat op het wegdek is geprojecteerd.|

| SourceInfo veld naam | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | datum| UTC-datum waarop de JSON-nettolading is verzonden|
| `width` | int | Breedte van video frame|
| `height` | int | Hoogte van video frame|
| `frameId` | int | Frame-id|
| `cameraCallibrationInfo` | verzameling | Verzameling waarden|
| `status` | tekenreeks | Hiermee wordt aangegeven of de camera kalibratie op grond van het wegdek ' volledig ' is|
| `cameraHeight` | float | De hoogte van de camera boven het wegdek in Foot. Dit wordt afgeleid van automatische kalibratie. |
| `focalLength` | float | De brand lengte van de camera in pixels. Dit wordt afgeleid van automatische kalibratie. |
| `tiltUpAngle` | float | De hoek van de camera kantelt van verticaal. Dit wordt afgeleid van automatische kalibratie.|


## <a name="use-the-output-generated-by-the-container"></a>De uitvoer gebruiken die door de container is gegenereerd

Mogelijk wilt u de detectie van ruimtelijke analyses of gebeurtenissen in uw toepassing integreren. Hier volgen enkele benaderingen waarmee u rekening moet houden: 

* Gebruik de Azure Event hub SDK voor de gekozen programmeer taal om verbinding te maken met het Azure IoT Hub-eind punt en de gebeurtenissen te ontvangen. Zie [apparaat-naar-Cloud-berichten lezen van het ingebouwde eind punt](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) voor meer informatie. 
* Stel **bericht routering** in op uw Azure-IOT hub om de gebeurtenissen te verzenden naar andere eind punten of sla de gebeurtenissen op in uw gegevens opslag. Zie [IOT hub Message Routing](../../iot-hub/iot-hub-devguide-messages-d2c.md) voor meer informatie. 
* Stel een Azure Stream Analytics-taak in om de gebeurtenissen in realtime te verwerken wanneer ze binnenkomen en visualisaties maken. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Ruimtelijke analyse bewerkingen op schaal implementeren (meerdere camera's)

U kunt de prestaties en het gebruik van de Gpu's optimaal benutten door gebruik te maken van grafiek exemplaren voor het implementeren van ruimtelijke analyse bewerkingen op meerdere camera's. Hieronder ziet u een voor beeld van het uitvoeren `cognitiveservices.vision.spatialanalysis-personcount` van de bewerking op vijf camera's.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Naam | Type| Description|
|---------|---------|---------|
| `batch_size` | int | Hiermee wordt het aantal camera's aangegeven dat in de bewerking wordt gebruikt. |

## <a name="next-steps"></a>Volgende stappen

* [Een web-app voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Logboekregistratie en problemen oplossen](spatial-analysis-logging.md)
* [Gids voor camera plaatsing](spatial-analysis-camera-placement.md)
* [Hand leiding voor zone-en lijn plaatsing](spatial-analysis-zone-line-placement.md)