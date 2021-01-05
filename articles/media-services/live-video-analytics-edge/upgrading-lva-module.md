---
title: Live video Analytics bijwerken op IoT Edge van 1,0 naar 2,0
description: In dit artikel worden de verschillen beschreven en de verschillende zaken die u moet overwegen bij het upgraden van de live video Analytics (LVA) op Azure IoT Edge module.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 9621f0a933c6102309286505f2c551c5256c5506
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901552"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Live video Analytics bijwerken op IoT Edge van 1,0 naar 2,0

In dit artikel worden de verschillen beschreven en de verschillende zaken die u moet overwegen bij het upgraden van de live video Analytics (LVA) op Azure IoT Edge module.

## <a name="change-list"></a>Lijst wijzigen

> [!div class="mx-tdCol4BreakAll"]
> |Titel|Live video Analytics 1,0|Live video Analytics 2,0|Beschrijving|
> |-------------|----------|---------|---------|
> |Container installatie kopie|mcr.microsoft.com/media/live-video-analytics:1.0.0|mcr.microsoft.com/media/live-video-analytics:2.0.0|Micro soft heeft docker-installatie kopieën gepubliceerd voor live video Analytics op Azure IoT Edge|
> |**MediaGraph-knoop punten** |    |   |   |
> |Bronnen|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-bron </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bron van IoT Hub bericht |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-bron </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bron van IoT Hub bericht | MediaGraph-knoop punten die fungeren als bronnen voor het opnemen van media en berichten.|
> |Processors|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings detectie processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processor voor frame frequentie filter </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http-extensie processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc-extensie processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signal Gate-processor |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings detectie processor </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Processor voor frame frequentie filter**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Http-extensie processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc-extensie processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signal Gate-processor | MediaGraph-knoop punten waarmee u de media kunt Format teren voordat ze naar AI-servers voor afleiding worden verzonden.|
> |Wastafel|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Asset Sink </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bestands Sink </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bericht Sink IoT Hub|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Asset Sink </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bestands Sink </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bericht Sink IoT Hub| MediaGraph-knoop punten waarmee u de verwerkte media kunt opslaan.|
> |**Ondersteunde MediaGraphs** |    |   |   |
> |Topologieën|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Continue video-opname </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings analyse en continue video-opname </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Externe analyse en continue video-opname </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Op gebeurtenissen gebaseerde opname voor beweging </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Op gebeurtenissen gebaseerde opname op AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Op gebeurtenissen gebaseerde opname op externe gebeurtenis </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings analyse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings analyse gevolgd door AI-interferentie |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Continue video-opname </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings analyse en continue video-opname </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Externe analyse en continue video-opname </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Op gebeurtenissen gebaseerde opname voor beweging </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Op gebeurtenissen gebaseerde opname op AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Op gebeurtenissen gebaseerde opname op externe gebeurtenis </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings analyse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Bewegings analyse gevolgd door AI-interferentie </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI-compositie** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Audio-en video-opname** </br>  | MediaGraph-topologieën waarmee u de blauw druk van een grafiek kunt definiëren, met para meters als tijdelijke aanduidingen voor waarden.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Upgrade van de live video Analytics op IoT Edge-module van 1,0 naar 2,0
Zorg ervoor dat u de volgende informatie bijwerkt wanneer u een upgrade uitvoert voor de module live video Analytics op IoT Edge.
### <a name="container-image"></a>Container installatie kopie
Zoek in uw implementatie sjabloon naar uw live video-analyse op IoT Edge module onder het `modules` knoop punt en werk het `image` veld bij als:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
Als u de naam van de live video Analytics op IoT Edge module niet hebt gewijzigd, zoekt u `lvaEdge` onder het module knooppunt.

### <a name="topology-file-changes"></a>Wijzigingen in het topologie bestand
Zorg er in uw topologie bestanden **`apiVersion`** voor dat is ingesteld op 2,0

### <a name="mediagraph-node-changes"></a>Wijzigingen in het MediaGraph-knoop punt


* De audio van de camera bron kan nu worden doorgevoerd en video. U kunt **alleen audio** en video of **Audio en video** **alleen** door geven met behulp van **`outputSelectors`** een grafiek knooppunt. Als u bijvoorbeeld alleen video wilt selecteren uit de RTSP-bron en deze wilt laten door lopen, voegt u het volgende toe aan het RTSP-bron knooppunt:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>De **`outputSelectors`** is een optionele eigenschap. Als deze niet wordt gebruikt, geeft de media grafiek de audio (indien ingeschakeld) en video van de RTSP-camera stroomafwaarts door. 

* `MediaGraphHttpExtension` `MediaGraphGrpcExtension` Houd rekening met de volgende wijzigingen in en processors:  
    * **Eigenschappen van installatie kopie**
        * `MediaGraphImageFormatEncoded` wordt niet meer ondersteund. 
        * Gebruik in plaats daarvan **`MediaGraphImageFormatBmp`** of **`MediaGraphImageFormatJpeg`** of **`MediaGraphImageFormatPng`** . Bijvoorbeeld:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Als u onbewerkte installatie kopieën wilt gebruiken, gebruikt u **`MediaGraphImageFormatRaw`** . Als u dit wilt gebruiken, moet u het knoop punt van de installatie kopie bijwerken als:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Mogelijke waarden van pixelFormat zijn: `yuv420p` , `rgb565be` , `rgb565le` , `rgb555be` , `rgb555le` , `rgb24` , `bgr24` , `argb` , `rgba` , `abgr` , `bgra`  

    * **extensionConfiguration voor Grpc-extensie processor**  
        * In `MediaGraphGrpcExtension` processor is een nieuwe eigenschap met de naam **`extensionConfiguration`** beschikbaar. Dit is een optionele teken reeks die kan worden gebruikt als onderdeel van het gRPC-contract. Dit veld kan worden gebruikt om gegevens door te geven aan de server voor afwijzen en u kunt definiëren hoe deze gegevens worden gebruikt door de server voor het afwijzen van interferentie.  
        Eén use-case van deze eigenschap is wanneer u meerdere AI-modellen hebt ingepakt in één server voor ingrijpen. Met deze eigenschap hoeft u geen knoop punt beschikbaar te maken voor elk AI-model. In plaats daarvan kunt u, voor een exemplaar van een grafiek, definiëren hoe de verschillende AI-modellen moeten worden geselecteerd met behulp van de **`extensionConfiguration`** eigenschap en tijdens de uitvoering. LVA wordt deze teken reeks door gegeven aan de server voor het afwijzen van de bewerking, waarmee het gewenste AI-model kan worden aangeroepen.  

    * **AI-compositie**
        * Live video Analytics 2,0 ondersteunt nu het gebruik van meer dan één media Graph extension-processor binnen een topologie. U kunt de media frames van de RTSP-camera naar verschillende AI-modellen sequentieel, parallel of in een combi natie van beide door geven. Bekijk een voor beeld van een topologie waarin twee AI-modellen opeenvolgend worden gebruikt.


* In het knoop punt **Bestands Sink** kunt u nu opgeven hoeveel schijf ruimte de live video-analyse op IOT Edge module kan gebruiken om de verwerkte installatie kopieën op te slaan. Als u dit wilt doen, voegt u het **`maximumSizeMiB`** veld toe aan het knoop punt FileSink. Een voor beeld van een Sink-knoop punt voor bestanden is als volgt:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* In uw **Asset Sink** -knoop punt kunt u opgeven hoeveel schijf ruimte de live video-analyse op IOT Edge module kan gebruiken om de verwerkte installatie kopieën op te slaan. Als u dit wilt doen, voegt u het **`localMediaCacheMaximumSizeMiB`** veld toe aan het knoop punt Asset sink. Een voor beeld van een Asset Sink-knoop punt is als volgt:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  Het pad naar de **Bestands Sink** is gesplitst in het pad naar de hoofdmap en het bestandsnaam patroon, terwijl het pad naar de hoofdmap van de **Asset-Sink** het pad naar de basismap bevat.  

* **`MediaGraphFrameRateFilterProcessor`** is afgeschaft in **Live video Analytics op IoT Edge 2,0** -module.
    * Als u een voor beeld wilt van de inkomende video voor verwerking, voegt **`samplingOptions`** u de eigenschap toe aan de MediaGraph-extensie processors ( `MediaGraphHttpExtension` of `MediaGraphGrpcExtension` ).  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Metrische modules in de Prometheus-indeling met behulp van Telegraf
Met deze versie kan Telegraf worden gebruikt om metrische gegevens naar Azure Monitor te verzenden. Van daaruit kunnen de metrische gegevens worden omgeleid naar Log Analytics of een Event Hub.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Taxonomie van gebeurtenissen":::

U kunt eenvoudig een telegrafie-installatie kopie maken met een aangepaste configuratie met behulp van docker. Meer informatie vindt u op de pagina [bewaking en logboek registratie](monitoring-logging.md#azure-monitor-collection-via-telegraf) .

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met live video Analytics op IoT Edge](get-started-detect-motion-emit-events-quickstart.md)