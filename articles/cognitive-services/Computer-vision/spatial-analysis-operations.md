---
title: Bewerkingen voor ruimtelijke analyse
titleSuffix: Azure Cognitive Services
description: De bewerkingen voor ruimtelijke analyse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 37ac7573a1794c97c81fe5364204f85ff14d9fa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538089"
---
# <a name="spatial-analysis-operations"></a>Bewerkingen voor ruimtelijke analyse

Ruimtelijke analyse maakt het mogelijk om realtime streamingvideo van cameraapparaten te analyseren. Voor elk cameraapparaat dat u configureert, genereren de bewerkingen voor ruimtelijke analyse een uitvoerstroom van JSON-berichten die worden verzonden naar uw exemplaar van Azure IoT Hub. 

De container Ruimtelijke analyse implementeert de volgende bewerkingen:

| Bewerkings-id| Description|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Telt personen in een aangewezen zone in het gezichtsveld van de camera. De zone moet volledig worden gedekt door één camera, zodat PersonCount een nauwkeurig totaal kan opnemen. <br> Stuurt een eerste _personCountEvent-gebeurtenis_ en vervolgens _personCountEvent-gebeurtenissen_ wanneer het aantal verandert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Houdt bij wanneer een persoon een aangewezen lijn in het gezichtsveld van de camera passeert. <br>Stuurt een _personLineEvent-gebeurtenis_ wanneer de persoon de lijn passeert en directionele informatie verstrekt. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Stuurt een _personZoneEnterExitEvent-gebeurtenis_ wanneer een persoon de zone binnenkomt of verlaat en geeft directionele informatie met de genummerde kant van de zone die is kruist. Er wordt een _personZoneDwellTimeEvent_ uitgegeven wanneer de persoon de zone verlaat en directionele informatie biedt, evenals het aantal milliseconden dat de persoon in de zone heeft besteed. |
| cognitiveservices.vision.spatialanalysis-persondistance | Houdt bij wanneer mensen een afstandsregel schenden. <br> Er wordt periodiek _een personDistanceEvent_ met de locatie van elke schending van de afstand. |
| cognitiveservices.vision.spatialanalysis | Algemene bewerking die kan worden gebruikt om alle hierboven genoemde scenario's uit te voeren. Deze optie is handiger wanneer u meerdere scenario's op dezelfde camera wilt uitvoeren of systeemresources (bijvoorbeeld GPU) efficiënter wilt gebruiken. |

Alle bovenstaande bewerkingen zijn ook beschikbaar in de versie, die de mogelijkheid hebben om de videoframes te visualiseren `.debug` terwijl ze worden verwerkt. U moet uitvoeren op de hostcomputer om de visualisatie `xhost +` van videoframes en gebeurtenissen mogelijk te maken.

| Bewerkings-id| Description|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Telt personen in een aangewezen zone in het gezichtsveld van de camera. <br> Stuurt een eerste _personCountEvent-gebeurtenis_ en vervolgens _personCountEvent-gebeurtenissen_ wanneer het aantal verandert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Houdt bij wanneer een persoon een aangewezen lijn in het gezichtsveld van de camera passeert. <br>Stuurt een _personLineEvent-gebeurtenis_ wanneer de persoon de lijn passeert en directionele informatie verstrekt. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Stuurt een _personZoneEnterExitEvent_ gebeurtenis wanneer een persoon binnenkomt of verlaat de zone en directionele informatie met de genummerde kant van de zone die is kruist. Stuurt een _personZoneDwellTimeEvent_ wanneer de persoon de zone verlaat en geeft informatie over de richting en het aantal milliseconden dat de persoon in de zone heeft besteed. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Houdt bij wanneer mensen een afstandsregel schenden. <br> Er wordt periodiek _een personDistanceEvent_ met de locatie van elke schending van de afstand. |
| cognitiveservices.vision.spatialanalysis.debug | Algemene bewerking die kan worden gebruikt om alle hierboven genoemde scenario's uit te voeren. Deze optie is handiger wanneer u meerdere scenario's op dezelfde camera wilt uitvoeren of systeembronnen (bijvoorbeeld GPU) efficiënter wilt gebruiken. |

Ruimtelijke analyse kan ook worden uitgevoerd met [Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) hun Video AI-module. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Bewerkings-id| Description|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Telt personen in een aangewezen zone in het gezichtsveld van de camera. <br> Stuurt een eerste _personCountEvent-gebeurtenis_ en vervolgens _personCountEvent-gebeurtenissen_ wanneer het aantal verandert.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Houdt bij wanneer een persoon een aangewezen lijn in het gezichtsveld van de camera passeert. <br>Stuurt een _personLineEvent-gebeurtenis_ wanneer de persoon de lijn passeert en directionele informatie verstrekt. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Stuurt een _personZoneEnterExitEvent-gebeurtenis_ wanneer een persoon de zone binnenkomt of verlaat en geeft directionele informatie met de genummerde kant van de zone die is kruist. Er wordt een _personZoneDwellTimeEvent_ uitgegeven wanneer de persoon de zone verlaat en directionele informatie biedt, evenals het aantal milliseconden dat de persoon in de zone heeft besteed.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Houdt bij wanneer mensen een afstandsregel schenden. <br> Er wordt periodiek _een personDistanceEvent_ met de locatie van elke schending van de afstand. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | Algemene bewerking die kan worden gebruikt voor het uitvoeren van alle hierboven genoemde scenario's. Deze optie is handiger wanneer u meerdere scenario's op dezelfde camera wilt uitvoeren of systeembronnen (bijvoorbeeld GPU) efficiënter wilt gebruiken. |

Live Video Analytics-bewerkingen zijn ook beschikbaar in de versie `.debug` (bijvoorbeeld cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug) die de mogelijkheid biedt om de videoframes te visualiseren als ze worden verwerkt. U moet uitvoeren op de hostcomputer om de visualisatie van de videoframes en `xhost +` gebeurtenissen in te kunnen stellen

> [!IMPORTANT]
> De COMPUTER Vision AI-modellen detecteren en vinden menselijke aanwezigheid in videobeelden en -uitvoer met behulp van een begrensingsvak rond een menselijk lichaam. De AI-modellen proberen niet de identiteiten of demografische gegevens van personen te ontdekken.

Dit zijn de parameters die vereist zijn voor elk van deze ruimtelijke analysebewerkingen.

| Bewerkingsparameters| Description|
|---------|---------|
| Bewerkings-id | De bewerkings-id uit de bovenstaande tabel.|
| enabled | Booleaanse naam: waar of onwaar|
| VIDEO_URL| De RTSP-URL voor het cameraapparaat (voorbeeld: `rtsp://username:password@url` ). Ruimtelijke analyse ondersteunt met H.264 gecodeerde stream via RTSP, http of mp4. Video_URL kunnen worden opgegeven als een versleutelde base64-tekenreekswaarde met behulp van AES-versleuteling. Als de video-URL is versleuteld, moet deze worden opgegeven als `KEY_ENV` `IV_ENV` omgevingsvariabelen. Voorbeeldprogramma voor het genereren van sleutels en versleuteling vindt u [hier](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | Een gebruiksvriendelijke naam voor het cameraapparaat of de videostream. Dit wordt geretourneerd met de JSON-uitvoer van de gebeurtenis.|
| VIDEO_IS_LIVE| Waar voor camera-apparaten; false voor opgenomen video's.|
| VIDEO_DECODE_GPU_INDEX| Welke GPU het videoframe moet decoderen. Standaard is dit 0. Moet hetzelfde zijn als `gpu_index` de in de andere knooppunt-configuratie, zoals , `VICA_NODE_CONFIG` `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Voer de framebreedte van de video/stream in (bijvoorbeeld 1920). Dit is een optioneel veld en indien opgegeven, wordt het frame naar deze dimensie geschaald met behoud van de aspectverhouding.|
| DETECTOR_NODE_CONFIG | JSON die aangeeft op welke GPU het detector-knooppunt moet worden uitgevoerd. Deze moet de volgende indeling hebben: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | JSON-configuratie voor zone en regel, zoals hieronder wordt beschreven.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` om het detecteren van personen die gezichtsmaskers in de videostream dragen, in te schakelen, `False` om deze uit te schakelen. Dit is standaard uitgeschakeld. Voor gezichtsmaskerdetectie moet de parameter voor de invoervideobreedte 1920 `"INPUT_VIDEO_WIDTH": 1920` zijn. Het kenmerk gezichtsmasker wordt niet geretourneerd als gedetecteerde personen de camera niet of te ver van de camera hebben. Raadpleeg de handleiding [voor cameraplaatsing](spatial-analysis-camera-placement.md) voor meer informatie |

### <a name="detector-node-parameter-settings"></a>Parameterinstellingen detector-knooppunt
Dit is een voorbeeld van de DETECTOR_NODE_CONFIG parameters voor alle bewerkingen voor ruimtelijke analyse.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `gpu_index` | tekenreeks| De GPU-index waarop deze bewerking wordt uitgevoerd.|
| `do_calibration` | tekenreeks | Geeft aan dat kalibratie is ingeschakeld. `do_calibration` moet waar zijn als **cognitiveservices.vision.spatialanalysis-persondistance** goed werkt. do_calibration is standaard ingesteld op Waar. |
| `enable_recalibration` | booleaans | Geeft aan of automatische hercalibratie is ingeschakeld. De standaardinstelling is `true`.|
| `calibration_quality_check_frequency_seconds` | int | Minimaal aantal seconden tussen elke kwaliteitscontrole om te bepalen of er opnieuw moet worden gecalibreerd. De standaardwaarde is `86400` (24 uur). Alleen gebruikt wanneer `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | Minimaal aantal seconden tussen het verzamelen van nieuwe gegevensvoorbeelden voor hercalibratie en kwaliteitscontrole. De standaardwaarde is `300` (5 minuten). Alleen gebruikt wanneer `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | int | Minimum aantal nieuwe gegevensvoorbeelden dat per ronde van de voorbeeldverzameling moet worden verzameld. De standaardinstelling is `10`. Alleen gebruikt wanneer `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | int | Maximum aantal gegevensvoorbeelden dat moet worden opgeslagen wanneer het cameramodel wordt ge kalibreerd. De standaardinstelling is `1000`. Alleen gebruikt wanneer `enable_recalibration=True` .|
| `enable_breakpad`| booleaans | Geeft aan of u breakpad wilt inschakelen, dat wordt gebruikt voor het genereren van crashdumps voor foutopsporing. Dit is `false` standaard. Als u deze in stelt `true` op , moet u ook toevoegen aan het deel van de container `"CapAdd": ["SYS_PTRACE"]` `HostConfig` `createOptions` . De crashdump wordt standaard geüpload naar de AppCenter-app [RealTimePersonTracking.](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) Als u wilt dat de crashdumps worden geüpload naar uw eigen AppCenter-app, kunt u de omgevingsvariabele overschrijven met het app-geheim van uw `RTPT_APPCENTER_APP_SECRET` app.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Configuratie en uitvoer van ruimtelijke analysebewerkingen
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Zoneconfiguratie voor cognitiveservices.vision.spatialanalysis-personcount

 Dit is een voorbeeld van een JSON-invoer voor de SPACEANALYTICS_CONFIG parameter die een zone configureert. U kunt meerdere zones configureren voor deze bewerking.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `zones` | list| Lijst met zones. |
| `name` | tekenreeks| Gebruiksvriendelijke naam voor deze zone.|
| `polygon` | list| Elk waardepaar vertegenwoordigt de x,y voor de vertices van een veelhoek. De veelhoek vertegenwoordigt de gebieden waarin mensen worden bijgespoord of geteld en veelhoekpunten zijn gebaseerd op genormaliseerde coördinaten (0-1), waarbij de linkerbovenhoek (0,0, 0,0) is en de rechteronderhoek (1,0, 1,0).   
| `threshold` | float| Gebeurtenissen worden verlaten wanneer de persoon groter is dan dit aantal pixels binnen de zone. |
| `type` | tekenreeks| Voor **cognitiveservices.vision.spatialanalysis-personcount** moet dit `count` zijn.|
| `trigger` | tekenreeks| Het type trigger voor het verzenden van een gebeurtenis. Ondersteunde waarden zijn voor het verzenden van gebeurtenissen wanneer het aantal wordt gewijzigd of voor het periodiek verzenden van gebeurtenissen, ongeacht of het aantal `event` `interval` is gewijzigd of niet.
| `output_frequency` | int | De snelheid waarmee gebeurtenissen worden uitgegaan. Wanneer `output_frequency` = X, wordt elke X-gebeurtenis weggegaan, bijvoorbeeld. `output_frequency` = 2 betekent dat elke andere gebeurtenis uitvoer is. De `output_frequency` is van toepassing op zowel als `event` `interval` . |
| `focus` | tekenreeks| De puntlocatie binnen het begrensingsvak van de persoon die wordt gebruikt om gebeurtenissen te berekenen. De waarde van de focus kan zijn (de footprint van de persoon), (het onderste midden van het begrenzesvak van de persoon) (het midden van het `footprint` `bottom_center` `center` begrensvak van de persoon).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Lijnconfiguratie voor cognitiveservices.vision.spatialanalysis-personcrossingline

Dit is een voorbeeld van een JSON-invoer voor de SPACEANALYTICS_CONFIG parameter die een regel configureert. U kunt meerdere kruisingslijnen configureren voor deze bewerking.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `lines` | list| Lijst met regels.|
| `name` | tekenreeks| Gebruiksvriendelijke naam voor deze regel.|
| `line` | list| De definitie van de regel. Dit is een richtingslijn waarmee u inzicht hebt in 'vermelding' versus 'afsluiten'.|
| `start` | waardepaar| x, y-coördinaten voor het beginpunt van de regel. De float-waarden vertegenwoordigen de positie van het hoekpunt ten opzichte van de linkerbovenhoek. Als u de absolute x-, y-waarden wilt berekenen, vermenigvuldigt u deze waarden met de framegrootte. |
| `end` | waardepaar| x, y-coördinaten voor het eindpunt van de regel. De float-waarden vertegenwoordigen de positie van het hoekpunt ten opzichte van de linkerbovenhoek. Als u de absolute x-, y-waarden wilt berekenen, vermenigvuldigt u deze waarden met de framegrootte. |
| `threshold` | float| Gebeurtenissen worden uitgegaan wanneer de persoon groter is dan dit aantal pixels in de zone. De standaardwaarde is 16. Dit is de aanbevolen waarde voor maximale nauwkeurigheid. |
| `type` | tekenreeks| Voor **cognitiveservices.vision.spatialanalysis-personcrossingline** moet dit `linecrossing` zijn.|
|`trigger`|tekenreeks|Het type trigger voor het verzenden van een gebeurtenis.<br>Ondersteunde waarden: 'gebeurtenis': wordt brand wanneer iemand de regel passeert.|
| `focus` | tekenreeks| De puntlocatie binnen het begrensingsvak van de persoon die wordt gebruikt om gebeurtenissen te berekenen. De waarde van de focus kan zijn (de footprint van de persoon), (het onderste midden van het begrenzesvak van de persoon) (het midden van het `footprint` `bottom_center` `center` begrensingsvak van de persoon). De standaardwaarde is footprint.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zoneconfiguratie voor cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Dit is een voorbeeld van een JSON-invoer voor de SPACEANALYTICS_CONFIG parameter die een zone configureert. U kunt meerdere zones configureren voor deze bewerking.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `zones` | list| Lijst met zones. |
| `name` | tekenreeks| Gebruiksvriendelijke naam voor deze zone.|
| `polygon` | list| Elk waardepaar vertegenwoordigt de x,y voor de punten van veelhoek. De veelhoek vertegenwoordigt de gebieden waarin mensen worden bijgespoord of geteld. De float-waarden vertegenwoordigen de positie van het hoekpunt ten opzichte van de linkerbovenhoek. Als u de absolute x-, y-waarden wilt berekenen, vermenigvuldigt u deze waarden met de framegrootte. 
| `threshold` | float| Gebeurtenissen worden verlaten wanneer de persoon groter is dan dit aantal pixels binnen de zone. De standaardwaarde is 48 wanneer type zonecrossing is en 16 wanneer de tijd DwellTime is. Dit zijn de aanbevolen waarden voor maximale nauwkeurigheid.  |
| `type` | tekenreeks| Voor **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** moet dit `zonecrossing` of `zonedwelltime` zijn.|
| `trigger`|tekenreeks|Het type trigger voor het verzenden van een gebeurtenis<br>Ondersteunde waarden: "event": fire wanneer iemand de zone binnenkomt of verlaat.|
| `focus` | tekenreeks| De puntlocatie binnen het begrensingsvak van de persoon die wordt gebruikt om gebeurtenissen te berekenen. De waarde van de focus kan zijn (de footprint van de persoon), (het onderste midden van het begrenzesvak van de persoon) (het midden van het `footprint` `bottom_center` `center` begrensvak van de persoon). De standaardwaarde is footprint.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zoneconfiguratie voor cognitiveservices.vision.spatialanalysis-persondistance

Dit is een voorbeeld van een JSON-invoer voor de parameter SPACEANALYTICS_CONFIG die een zone configureert voor **cognitiveservices.vision.spatialanalysis-persondistance.** U kunt meerdere zones configureren voor deze bewerking.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Naam | Type| Description|
|---------|---------|---------|
| `zones` | list| Lijst met zones. |
| `name` | tekenreeks| Gebruiksvriendelijke naam voor deze zone.|
| `polygon` | list| Elk waardepaar vertegenwoordigt de x,y voor de punten van een veelhoek. De veelhoek vertegenwoordigt de gebieden waarin mensen worden geteld en de afstand tussen mensen wordt gemeten. De float-waarden vertegenwoordigen de positie van het hoekpunt ten opzichte van de linkerbovenhoek. Als u de absolute x-, y-waarden wilt berekenen, vermenigvuldigt u deze waarden met de framegrootte. 
| `threshold` | float| Gebeurtenissen worden uitgegaan wanneer de persoon groter is dan dit aantal pixels in de zone. |
| `type` | tekenreeks| Voor **cognitiveservices.vision.spatialanalysis-persondistance** moet dit `people_distance` zijn.|
| `trigger` | tekenreeks| Het type trigger voor het verzenden van een gebeurtenis. Ondersteunde waarden zijn voor het verzenden van gebeurtenissen wanneer het aantal verandert of voor het periodiek verzenden van gebeurtenissen, ongeacht of het aantal `event` `interval` is gewijzigd of niet.
| `output_frequency` | int | De snelheid waarmee gebeurtenissen worden uitgegaan. Wanneer `output_frequency` = X, wordt elke X-gebeurtenis uitgegaan, bijvoorbeeld `output_frequency` = 2 betekent dat elke andere gebeurtenis uitvoer is. De `output_frequency` is van toepassing op zowel als `event` `interval` .|
| `minimum_distance_threshold` | float| Een afstand in voet die een 'TooClose'-gebeurtenis activeert wanneer mensen kleiner zijn dan die afstand uit elkaar.|
| `maximum_distance_threshold` | float| Een afstand in voet die een 'TooFar'-gebeurtenis activeert wanneer mensen groter zijn dan die afstand uit elkaar.|
| `aggregation_method` | tekenreeks| De methode voor cumulatief persondistanceresultaat. De aggregation_method is van toepassing op `mode` zowel als `average` .|
| `focus` | tekenreeks| De puntlocatie binnen het begrensingsvak van de persoon die wordt gebruikt om gebeurtenissen te berekenen. De waarde van de focus kan zijn (de footprint van de persoon), (het onderste midden van het begrenzesvak van de persoon) (het midden van het `footprint` `bottom_center` `center` begrensingsvak van de persoon).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Configuratie voor cognitiveservices.vision.spatialanalysis
Dit is een voorbeeld van een JSON-invoer voor de parameter SPACEANALYTICS_CONFIG die een lijn en zone configureert voor **cognitiveservices.vision.spatialanalysis.** U kunt meerdere regels/zones configureren voor deze bewerking en elke regel/zone kan verschillende gebeurtenissen hebben.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Cameraconfiguratie

Zie de [richtlijnen voor cameraplaatsing](spatial-analysis-camera-placement.md) voor meer informatie over het configureren van zones en lijnen.

## <a name="spatial-analysis-operation-output"></a>Uitvoer van ruimtelijke analysebewerking

De gebeurtenissen van elke bewerking worden naar een JSON-Azure IoT Hub uitgevoerd.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-indeling voor cognitiveservices.vision.spatialanalysis-personcount AI-inzichten

Voorbeeld-JSON voor een gebeurtenisuitvoer door deze bewerking.

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
            "status": "Calibrated",
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
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
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
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Naam gebeurtenisveld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoonsdetectie die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `trackinId` | tekenreeks| Unieke id van de gedetecteerde persoon|
| `zone` | tekenreeks | Het veld 'naam' van de veelhoek die de zone vertegenwoordigt die is kruist|
| `trigger` | tekenreeks| Het triggertype is 'gebeurtenis' of 'interval' afhankelijk van de waarde `trigger` van in SPACEANALYTICS_CONFIG|

| Veldnaam detectie | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-id|
| `type` | tekenreeks| Detectietype|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| De punten linksboven en rechtsonder wanneer het regiotype RECHTHOEK is |
| `confidence` | float| Betrouwbaarheid van algoritmen|
| `face_mask` | float | De betrouwbaarheidswaarde van het kenmerk met bereik (0-1) geeft aan dat de gedetecteerde persoon een gezichtsmasker draagt |
| `face_nomask` | float | De betrouwbaarheidswaarde van het kenmerk met bereik (0-1) geeft aan dat de gedetecteerde persoon geen gezichtsmasker draagt  |

| Naam broninfoveld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | date| UTC-datum waarop de JSON-nettolading is weggelaten|
| `width` | int | Breedte van videoframe|
| `height` | int | Hoogte van videoframe|
| `frameId` | int | Frame-id|
| `cameraCallibrationInfo` | verzameling | Verzameling waarden|
| `status` | tekenreeks | De status van de kalibratie in de indeling `state[;progress description]` . De status kan `Calibrating` zijn, `Recalibrating` (als hercalibratie is ingeschakeld) of `Calibrated` . Het gedeelte beschrijving van voortgang is alleen geldig wanneer het zich in de status en , die wordt gebruikt om de voortgang van het `Calibrating` `Recalibrating` huidige kalibratieproces weer te geven.|
| `cameraHeight` | float | De hoogte van de camera boven de grond in voetjes. Dit wordt afgeleid van automatische kalibratie. |
| `focalLength` | float | De brandpuntlengte van de camera in pixels. Dit wordt afgeleid van automatische kalibratie. |
| `tiltUpAngle` | float | De kantelhoek van de camera van verticaal. Dit wordt afgeleid van automatische kalibratie.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-indeling voor cognitiveservices.vision.spatialanalysis-personcrossingline AI-inzichten

Voorbeeld-JSON voor detectie-uitvoer door deze bewerking.

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
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Naam gebeurtenisveld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoonsdetectie die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `trackinId` | tekenreeks| Unieke id van de gedetecteerde persoon|
| `status` | tekenreeks| Richting van lijnkruisingen, 'CrossLeft' of 'CrossRight'. Richting is gebaseerd op de voorstelling die aan het begin staat en naar het einde van de regel gaat. CrossRight kruist van links naar rechts. CrossLeft gaat van rechts naar links.|
| `zone` | tekenreeks | Het veld 'naam' van de regel die is doorkruist|

| Veldnaam detectie | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-id|
| `type` | tekenreeks| Detectietype|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| De punten linksboven en rechtsonder wanneer het regiotype RECHTHOEK is |
| `confidence` | float| Betrouwbaarheid van algoritmen|
| `face_mask` | float | De betrouwbaarheidswaarde van het kenmerk met bereik (0-1) geeft aan dat de gedetecteerde persoon een gezichtsmasker draagt |
| `face_nomask` | float | De betrouwbaarheidswaarde van het kenmerk met bereik (0-1) geeft aan dat de gedetecteerde persoon geen gezichtsmasker draagt  |

| Veldnaam SourceInfo | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | date| UTC-datum waarop de JSON-nettolading is ingediend|
| `width` | int | Breedte van videoframe|
| `height` | int | Hoogte van videoframe|
| `frameId` | int | Frame-id|


> [!IMPORTANT]
> Het AI-model detecteert een persoon, ongeacht of de persoon naar of weg van de camera kijkt. In het AI-model wordt gezichtsherkenning niet uitgevoerd en er wordt geen biometrische informatie verstrekt. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-indeling voor cognitiveservices.vision.spatialanalysis-personcrossingpolygon AI-inzichten

Voorbeeld-JSON voor detectie-uitvoer door deze bewerking met `zonecrossing` type SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
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
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Voorbeeld-JSON voor detectie-uitvoer door deze bewerking met `zonedwelltime` type SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
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

| Naam gebeurtenisveld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype. De waarde kan _personZoneDwellTimeEvent_ of _personZoneEnterExitEvent zijn_|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoonsdetectie die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `trackinId` | tekenreeks| Unieke id van de gedetecteerde persoon|
| `status` | tekenreeks| Richting van veelhoekovergangen, 'Enter' of 'Exit'|
| `side` | int| Het nummer van de zijde van de veelhoek die de persoon heeft doorkruist. Elke zijde is een genummerde rand tussen de twee hoekhoeken van de veelhoek die uw zone vertegenwoordigt. De rand tussen de eerste twee hoekhoeken van de veelhoek vertegenwoordigt de eerste zijde. 'Side' is leeg wanneer de gebeurtenis niet is gekoppeld aan een specifieke zijde vanwege occlusie. Er is bijvoorbeeld een exit opgetreden wanneer een persoon verdwijnt, maar niet is gezien terwijl deze een kant van de zone kruist of er een enter is opgetreden toen er een persoon in de zone stond, maar niet werd gezien die een kant kruist.|
| `durationMs` | float | Het aantal milliseconden dat de tijd vertegenwoordigt die de persoon in de zone heeft doorgebracht. Dit veld wordt opgegeven wanneer het gebeurtenistype _personZoneDwellTimeEvent is_|
| `zone` | tekenreeks | Het veld 'naam' van de veelhoek die de zone vertegenwoordigt die is kruist|

| Veldnaam detectie | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-id|
| `type` | tekenreeks| Detectietype|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| De punten linksboven en rechtsonder wanneer het regiotype RECHTHOEK is |
| `confidence` | float| Betrouwbaarheid van algoritmen|
| `face_mask` | float | De betrouwbaarheidswaarde van het kenmerk met bereik (0-1) geeft aan dat de gedetecteerde persoon een gezichtsmasker draagt |
| `face_nomask` | float | De betrouwbaarheidswaarde van het kenmerk met bereik (0-1) geeft aan dat de gedetecteerde persoon geen gezichtsmasker draagt  |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-indeling voor cognitiveservices.vision.spatialanalysis-persondistance AI-inzichten

Voorbeeld-JSON voor detectie-uitvoer door deze bewerking.

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
            "status": "Calibrated",
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

| Naam gebeurtenisveld | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Gebeurtenis-id|
| `type` | tekenreeks| Gebeurtenistype|
| `detectionsId` | matrix| Matrix van grootte 1 van de unieke id van de persoonsdetectie die deze gebeurtenis heeft geactiveerd|
| `properties` | verzameling| Verzameling waarden|
| `personCount` | int| Aantal personen dat is gedetecteerd toen de gebeurtenis werd uitgezonden|
| `averageDistance` | float| De gemiddelde afstand tussen alle gedetecteerde personen in voet|
| `minimumDistanceThreshold` | float| De afstand in voet die een 'TooClose'-gebeurtenis activeert wanneer mensen kleiner zijn dan die afstand uit elkaar.|
| `maximumDistanceThreshold` | float| De afstand in voet die een 'TooFar'-gebeurtenis activeert wanneer mensen groter zijn dan afstand uit elkaar.|
| `eventName` | tekenreeks| Gebeurtenisnaam is `TooClose` met de `minimumDistanceThreshold` wordt geschonden, wanneer wordt geschonden of wanneer `TooFar` automatische `maximumDistanceThreshold` `unknown` kalibratie niet is voltooid|
| `distanceViolationPersonCount` | int| Aantal personen dat is gedetecteerd in strijd `minimumDistanceThreshold` met of `maximumDistanceThreshold`|
| `zone` | tekenreeks | Het veld 'naam' van de veelhoek die de zone vertegenwoordigt die is bewaakt voor het distancing tussen personen|
| `trigger` | tekenreeks| Het triggertype is 'gebeurtenis' of 'interval' afhankelijk van de waarde `trigger` van in SPACEANALYTICS_CONFIG|

| Veldnaam detectie | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Detectie-id|
| `type` | tekenreeks| Detectietype|
| `region` | verzameling| Verzameling waarden|
| `type` | tekenreeks| Type regio|
| `points` | verzameling| De linkerboven- en rechteronderpunten wanneer het regiotype RECHTHOEK is |
| `confidence` | float| Betrouwbaarheid van algoritmen|
| `centerGroundPoint` | 2 float-waarden| `x`, `y` waarden met de coördinaten van de uitgestelde locatie van de persoon op de grond in voet. `x` en `y` zijn coördinaten op het vlak van de vloer, ervan uitgaande dat de vloer gelijk is. De locatie van de camera is de oorsprong. |

Bij het berekenen van is de afstand van de camera naar de persoon langs een lijn die haaks staat `centerGroundPoint` op het `x` camera-afbeeldingsvlak. `y` is de afstand van de camera naar de persoon langs een lijn die parallel loopt aan het camera-afbeeldingsvlak. 

![Voorbeeld van een middelpunt](./media/spatial-analysis/x-y-chart.png) 

In dit voorbeeld `centerGroundPoint` is `{x: 4, y: 5}` . Dit betekent dat er een persoon is die 1,5 meter van de camera is verwijderd en 1,5 meter rechts, en de kamer van boven naar beneden kijkt.


| Veldnaam SourceInfo | Type| Description|
|---------|---------|---------|
| `id` | tekenreeks| Camera-id|
| `timestamp` | date| UTC-datum waarop de JSON-nettolading is ingediend|
| `width` | int | Breedte van videoframe|
| `height` | int | Hoogte van videoframe|
| `frameId` | int | Frame-id|
| `cameraCallibrationInfo` | verzameling | Verzameling waarden|
| `status` | tekenreeks | De status van de kalibratie in de indeling `state[;progress description]` . De status kan `Calibrating` zijn , `Recalibrating` (als opnieuwliblibreren is ingeschakeld) of `Calibrated` . Het voortgangsbeschrijvingsgedeelte is alleen geldig wanneer het zich in de status en heeft, die wordt gebruikt om de voortgang van het `Calibrating` `Recalibrating` huidige kalibratieproces weer te geven.|
| `cameraHeight` | float | De hoogte van de camera boven de grond in voetjes. Dit wordt afgeleid van automatische kalibratie. |
| `focalLength` | float | De centrale lengte van de camera in pixels. Dit wordt afgeleid van automatische kalibratie. |
| `tiltUpAngle` | float | De kantelhoek van de camera van verticaal. Dit wordt afgeleid van automatische kalibratie.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>JSON-indeling voor cognitiveservices.vision.spatialanalysis AI-inzichten

De uitvoer van deze bewerking is afhankelijk van geconfigureerd. Als er bijvoorbeeld een gebeurtenis is geconfigureerd voor deze bewerking, is de uitvoer `events` `zonecrossing` hetzelfde als `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>De uitvoer gebruiken die is gegenereerd door de container

Mogelijk wilt u detectie van ruimtelijke analyse of gebeurtenissen integreren in uw toepassing. Hier zijn enkele manieren om rekening mee te houden: 

* Gebruik de Azure Event Hub SDK voor de door u gekozen programmeertaal om verbinding te maken met Azure IoT Hub eindpunt en de gebeurtenissen te ontvangen. Zie [Apparaat-naar-cloud-berichten lezen vanaf](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) het ingebouwde eindpunt voor meer informatie. 
* Stel **berichtroutering in** op uw Azure IoT Hub om de gebeurtenissen naar andere eindpunten te verzenden of de gebeurtenissen op te slaan in uw gegevensopslag. Zie [IoT Hub Berichtroutering](../../iot-hub/iot-hub-devguide-messages-d2c.md) voor meer informatie. 
* Stel een Azure Stream Analytics taak in om de gebeurtenissen in realtime te verwerken wanneer ze binnenkomen en visualisaties te maken. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Ruimtelijke analysebewerkingen op schaal implementeren (meerdere camera's)

Om de beste prestaties en het gebruik van de GPU's te krijgen, kunt u ruimtelijke analysebewerkingen op meerdere camera's implementeren met behulp van grafieken. Hieronder vindt u een voorbeeld voor het uitvoeren van `cognitiveservices.vision.spatialanalysis-personcrossingline` de bewerking op vijftien camera's.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
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
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Naam | Type| Description|
|---------|---------|---------|
| `batch_size` | int | Als alle camera's dezelfde resolutie hebben, stelt u in op het aantal camera's dat in die bewerking wordt gebruikt. Anders stelt u in op 1 of laat u deze ingesteld op `batch_size` standaard (1), wat aangeeft dat `batch_size` er geen batch wordt ondersteund. |

## <a name="next-steps"></a>Volgende stappen

* [Een webtoepassing voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Logboekregistratie en problemen oplossen](spatial-analysis-logging.md)
* [Handleiding voor cameraplaatsing](spatial-analysis-camera-placement.md)
* [Handleiding voor zone- en regelplaatsing](spatial-analysis-zone-line-placement.md)
