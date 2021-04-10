---
title: Bewaking en logboek registratie-Azure
description: Dit artikel bevat een overzicht van bewaking en logboek registratie in live video Analytics op IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 08b2f5cce80581d71ce73e97ab30900aa8957c77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564479"
---
# <a name="monitoring-and-logging"></a>Bewaking en registratie

In dit artikel leert u hoe u gebeurtenissen voor externe controle kunt ontvangen vanuit de module live video-analyses op IoT Edge. 

U leert ook hoe u de logboeken beheert die door de module worden gegenereerd.

## <a name="taxonomy-of-events"></a>Taxonomie van gebeurtenissen

Live video Analytics op IoT Edge verzendt gebeurtenissen of telemetriegegevens op basis van de volgende taxonomie:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagram waarin de taxonomie van gebeurtenissen wordt weer gegeven.":::

* Operationeel: gebeurtenissen die worden gegenereerd door de acties van een gebruiker of tijdens de uitvoering van een [Media grafiek](media-graph-concept.md)
   
   * Volume: er wordt naar verwachting laag (enkele keren per minuut of zelfs minder)
   * Voorbeelden:

      - Opname gestart (wordt weer gegeven in het volgende voor beeld)
      - Opname gestopt
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostische gegevens: gebeurtenissen die helpen bij het vaststellen van problemen met prestaties

   * Volume: kan hoog zijn (enkele keren per minuut)
   * Voorbeelden:
   
      - RTSP- [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -informatie (weer gegeven in het volgende voor beeld) 
      - Hiaten in de feed voor inkomende video

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: gebeurtenissen die zijn gegenereerd als onderdeel van de video analyse

   * Volume: kan hoog zijn (enkele keren per minuut of langer)
   * Voorbeelden:
      
      - Bewegings gedetecteerd (Zie het volgende voor beeld) 
      - Resultaat van de deinterferentie

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

De gebeurtenissen die door de module worden gegenereerd, worden verzonden naar de [IOT Edge hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). Ze kunnen van daaruit naar andere bestemmingen worden doorgestuurd. 

### <a name="timestamps-in-analytic-events"></a>Tijds tempels in analytische gebeurtenissen

Zoals eerder aangegeven, hebben gebeurtenissen die als onderdeel van de video analyse worden gegenereerd tijds tempels gekoppeld. Als u [de live video hebt genoteerd](video-recording-concept.md) als onderdeel van de grafiek topologie, kunt u met deze tijds tempels zoeken naar waar in de opgenomen video de betreffende gebeurtenis is opgetreden. Hieronder vindt u richt lijnen voor het toewijzen van de tijds tempel in een analytische gebeurtenis aan de tijd lijn van de video die in een [Azure Media Services Asset](terminology.md#asset)is opgenomen.

Haal eerst de `eventTime` waarde op. Gebruik deze waarde in een [tijds bereik filter](playback-recordings-how-to.md#time-range-filters) om een geschikt deel van de opname op te halen. U kunt bijvoorbeeld video ophalen die 30 seconden eerder begint `eventTime` en eindigt 30 seconden na deze. In het vorige voor beeld, waarbij `eventTime` 2020-05-12T23:33:09.381 z, een aanvraag voor een HLS-manifest gedurende de 30 seconden vóór en na de `eventTime` volgende aanvraag:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

De voor gaande URL retourneert een [hoofd lijst](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) met url's voor afspeel lijsten met media. De afspeel lijst van media bevat vermeldingen zoals deze:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
In de voor gaande vermelding wordt gerapporteerd dat er een video fragment beschikbaar is dat begint met een `timestamp` waarde van `143039375031270` . De `timestamp` waarde in de analytische gebeurtenis maakt gebruik van dezelfde tijd schaal als de media-afspeel lijst. Het kan worden gebruikt om het relevante video fragment te identificeren en te zoeken naar het juiste frame.

Zie voor meer informatie deze [artikelen over frame-nauw keurig zoeken](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) in HLS.

## <a name="controlling-events"></a>Gebeurtenissen beheren

U kunt de volgende module dubbele eigenschappen gebruiken om de operationele en diagnostische gebeurtenissen te beheren die door de live video Analytics op IoT Edge module worden gepubliceerd. Deze eigenschappen zijn gedocumenteerd in het [module-dubbele JSON-schema](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName`: Als u diagnostische gebeurtenissen van de module wilt ophalen, moet u deze eigenschap toevoegen en een wille keurige waarde opgeven. Laat deze weg of laat het leeg om te voor komen dat de module diagnostische gebeurtenissen publiceert.
   
- `operationalEventsOutputName`: Als u operationele gebeurtenissen uit de module wilt ophalen, moet u deze eigenschap toevoegen en een wille keurige waarde opgeven. Laat deze weg of laat het leeg om te voor komen dat de module operationele gebeurtenissen publiceert.
   
Analyse gebeurtenissen worden gegenereerd door knoop punten zoals de bewegings detectie processor of de HTTP-extensie processor. De IoT hub-sink wordt gebruikt om deze naar de IoT Edge hub te verzenden. 

U kunt de [route ring van alle voor gaande gebeurtenissen](../../iot-edge/module-composition.md#declare-routes) bepalen met behulp `desired` van de eigenschap van de `$edgeHub` module dubbele in het implementatie manifest:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

In de voor gaande JSON `lvaEdge` is de naam van de live video Analytics op IOT Edge module. De routerings regel volgt het schema dat is gedefinieerd bij het [declareren van routes](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Om ervoor te zorgen dat Analytics-gebeurtenissen de IoT Edge hub bereiken, moet u een Sink-knoop punt van een IoT-hub hebben met een wille keurig bewegings detectie processor knooppunt en/of een HTTP extension-processor knooppunt.

## <a name="event-schema"></a>Gebeurtenisschema

Gebeurtenissen zijn afkomstig van het apparaat aan de rand en kunnen worden verbruikt aan de rand of in de Cloud. Gebeurtenissen die worden gegenereerd door live video Analytics op IoT Edge voldoen aan het [streaming-berichten patroon](../../iot-hub/iot-hub-devguide-messages-construct.md) dat is vastgesteld door Azure IOT hub. Het patroon bestaat uit systeem eigenschappen, toepassings eigenschappen en een hoofd tekst.

### <a name="summary"></a>Samenvatting

Elke gebeurtenis, wanneer deze wordt waargenomen via IoT Hub, heeft een aantal algemene eigenschappen:

|Eigenschap   |Type eigenschap| Gegevenstype   |Beschrijving|
|---|---|---|---|
|`message-id`   |systeem |guid|  Unieke gebeurtenis-ID.|
|`topic`|   applicationProperty |tekenreeks|    Azure Resource Manager pad voor het Azure Media Services-account.|
|`subject`| applicationProperty |tekenreeks|    Subpad van de entiteit die de gebeurtenis uitzender.|
|`eventTime`|   applicationProperty|    tekenreeks| Tijdstip waarop de gebeurtenis is gegenereerd.|
|`eventType`|   applicationProperty |tekenreeks|    Id van gebeurtenis type. (Zie de volgende sectie.)|
|`body`|body    |object|    Bepaalde gebeurtenis gegevens.|
|`dataVersion`  |applicationProperty|   tekenreeks  |{Major}. Secundair|

### <a name="properties"></a>Eigenschappen

#### <a name="message-id"></a>bericht-id

Een Globally Unique Identifier (GUID) voor de gebeurtenis.

#### <a name="topic"></a>onderwerp

Hiermee geeft u het Azure Media Services-account dat is gekoppeld aan de grafiek.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>Onderwerp

De entiteit die de gebeurtenis verzendt:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Met de `subject` eigenschap kunt u algemene gebeurtenissen toewijzen aan de module genereren. Bijvoorbeeld: voor een ongeldige RTSP-gebruikers naam of-wacht woord is de gegenereerde gebeurtenis `Microsoft.Media.Graph.Diagnostics.ProtocolError` op het `/graphInstances/myGraph/sources/myRtspSource` knoop punt.

#### <a name="event-types"></a>Gebeurtenistypen

Gebeurtenis typen worden toegewezen aan een naam ruimte op basis van dit schema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Gebeurtenisklassen

|Klassenaam|Description|
|---|---|
|Analyse  |Gebeurtenissen die worden gegenereerd als onderdeel van inhouds analyse.|
|Diagnostiek    |Gebeurtenissen die helpen bij het diagnosticeren van problemen en prestaties.|
|Operationeel    |Gebeurtenissen die worden gegenereerd als onderdeel van de resource bewerking.|

De gebeurtenis typen zijn specifiek voor elke gebeurtenis klasse.

Voorbeelden:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Tijdstip van gebeurtenis

De tijd van de gebeurtenis wordt opgemaakt in een ISO 8601-teken reeks. Deze geeft de tijd aan waarop de gebeurtenis heeft plaatsgevonden.

### <a name="azure-monitor-collection-via-telegraf"></a>Azure Monitor verzameling via telegrafie

Deze metrische gegevens worden gerapporteerd uit de live video Analytics op IoT Edge module:  

|Naam van metrische gegevens|Type|Label|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Meter|iothub, edge_device, module_name, graph_topology|Totaal aantal actieve grafieken per topologie.|
|lva_received_bytes_total|Prestatiemeteritem|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Totaal aantal bytes dat door een knoop punt is ontvangen. Alleen ondersteund voor RTSP-bronnen.|
|lva_data_dropped_total|Prestatiemeteritem|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Teller van verwijderde gegevens (gebeurtenissen, media, enzovoort).|

> [!NOTE]
> Een [Prometheus-eind punt](https://prometheus.io/docs/practices/naming/) wordt weer gegeven op poort 9600 van de container. Als u uw live video-analyse op IoT Edge module "lvaEdge" genoemd, kunnen ze toegang krijgen tot metrische gegevens door een GET-aanvraag naar te verzenden http://lvaEdge:9600/metrics .   

Volg deze stappen om het verzamelen van metrische gegevens in te scha kelen op de module live video Analytics op IoT Edge:

1. Maak een map op de ontwikkel computer en ga naar die map.

1. Maak in de map een `telegraf.toml` bestand dat de volgende configuraties bevat:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Zorg ervoor dat u de variabelen vervangt in het. toml-bestand. De variabelen worden aangegeven door accolades ( `{}` ).

1. Maak in dezelfde map een Dockerfile die de volgende opdrachten bevat:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Gebruik docker CLI-opdrachten om het docker-bestand te maken en de installatie kopie naar uw Azure container Registry te publiceren.
    
   Zie [push-en pull-installatie kopieën voor docker](../../container-registry/container-registry-get-started-docker-cli.md)voor meer informatie over het gebruik van de docker CLI om naar een container register te pushen. Raadpleeg de [documentatie](../../container-registry/index.yml)voor meer informatie over Azure container Registry.


1. Nadat de push naar Azure Container Registry is voltooid, voegt u het volgende knoop punt toe aan het manifest bestand van de implementatie:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Zorg ervoor dat u de variabelen in het manifest bestand vervangt. De variabelen worden aangegeven door accolades ( `{}` ).


   Azure Monitor kunnen worden [geverifieerd via Service-Principal](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   De Azure Monitor telegrafe-invoeg toepassing biedt [verschillende verificatie methoden](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Als u Service-Principal-verificatie wilt gebruiken, stelt u deze omgevings variabelen in:  
     `AZURE_TENANT_ID`: Hiermee geeft u de Tenant op waarmee moet worden geverifieerd.  
     `AZURE_CLIENT_ID`: Hiermee geeft u de ID van de app-client op die moet worden gebruikt.  
     `AZURE_CLIENT_SECRET`: Hiermee geeft u het app-geheim op dat moet worden gebruikt.  
     
     >[!TIP]
     > U kunt de functie voor de uitgever van de **bewakings gegevens** voor de Service-Principal opgeven. Volg de stappen in **[Create Service Principal](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** om de service-principal te maken en de rol toe te wijzen.

1. Nadat de modules zijn geïmplementeerd, worden metrische gegevens weer gegeven in Azure Monitor onder één naam ruimte. Metrische namen komen overeen met die van Prometheus. 

   In dit geval gaat u naar de IoT-hub in het Azure Portal en selecteert u **metrische gegevens** in het linkerdeel venster. Hier ziet u de metrische gegevens.

### <a name="log-analytics-metrics-collection"></a>Verzameling Log Analytics metrische gegevens
Met behulp van [Prometheus-eind punt](https://prometheus.io/docs/practices/naming/) samen met [log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)kunt u [metrische gegevens genereren en bewaken](../../azure-monitor/essentials/metrics-supported.md) , zoals het gebruik van CPUPercent, MemoryUsedPercent, enzovoort.   

> [!NOTE]
> In de onderstaande configuratie worden geen logboeken verzameld, **alleen metrische gegevens**. Het is haalbaar om de module Collector uit te breiden om ook logboeken te verzamelen en te uploaden.

[![Diagram waarin de verzameling metrische gegevens wordt weer gegeven met behulp van log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Meer informatie over het [verzamelen van metrische gegevens](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Gebruik docker CLI-opdrachten om het [docker-bestand](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) te maken en de installatie kopie naar uw Azure container Registry te publiceren.
    
   Zie [push-en pull-installatie kopieën voor docker](../../container-registry/container-registry-get-started-docker-cli.md)voor meer informatie over het gebruik van de docker CLI om naar een container register te pushen. Raadpleeg de [documentatie](../../container-registry/index.yml)voor meer informatie over Azure container Registry.

1. Nadat de push naar Azure Container Registry is voltooid, wordt het volgende in het implementatie manifest ingevoegd:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > De modules `edgeHub` `edgeAgent` en `lvaEdge` zijn de namen van de modules die in het manifest bestand van de implementatie zijn gedefinieerd. Zorg ervoor dat de namen van de modules overeenkomen.   

    U kunt uw `LogAnalyticsWorkspaceId` en `LogAnalyticsSharedKey` waarden ophalen door de volgende stappen uit te voeren:
    1. Ga naar de Azure Portal
    1. Zoeken naar uw Log Analytics-werk ruimten
    1. Als u uw Log Analytics-werk ruimte hebt gevonden, gaat u naar de `Agents management` optie in het linkernavigatievenster.
    1. U vindt de werk ruimte-ID en de geheime sleutels die u kunt gebruiken.

1. Maak vervolgens een werkmap door te klikken op het `Workbooks` tabblad in het navigatie deel venster aan de linkerkant.
1. U kunt met behulp van de Kusto-query taal query's schrijven zoals hieronder en het CPU-percentage ophalen dat wordt gebruikt door de IoT Edge modules.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagram waarin de metrische gegevens worden weer gegeven met behulp van de Kusto-query.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Logboekregistratie

Net als bij andere IoT Edge modules kunt u ook [de container logboeken](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) op het apparaat Edge bekijken. U kunt de informatie die naar de logboeken wordt geschreven, configureren met behulp van de [volgende module dubbele](module-twin-configuration-schema.md) eigenschappen:

* `logLevel`

   * Toegestane waarden zijn `Verbose` , `Information` ,, en `Warning` `Error` `None` .
   * De standaardwaarde is `Information`. De logboeken bevatten fout-, waarschuwings-en informatie berichten.
   * Als u de waarde instelt op `Warning` , bevatten de logboeken fout-en waarschuwings berichten.
   * Als u de waarde instelt op `Error` , bevatten de Logboeken alleen fout berichten.
   * Als u de waarde instelt op `None` , worden er geen logboeken gegenereerd. (Deze configuratie wordt niet aanbevolen.)
   * Gebruik deze `Verbose` alleen als u Logboeken moet delen met Azure-ondersteuning om een probleem op te sporen.

* `logCategories`

   * Een door komma's gescheiden lijst met een of meer van deze waarden: `Application` , `Events` , `MediaPipeline` .
   * De standaardwaarde is `Application, Events`.
   * `Application`: Informatie op hoog niveau van de module, zoals opstart berichten van een module, omgevings fouten en directe-methode aanroepen.
   * `Events`: Alle gebeurtenissen die eerder in dit artikel zijn beschreven.
   * `MediaPipeline`: Logboeken op laag niveau die inzicht kunnen bieden bij het oplossen van problemen, zoals problemen met het maken van een verbinding met een RTSP-compatibele camera.
   
### <a name="generating-debug-logs"></a>Logboeken voor fout opsporing genereren

In bepaalde gevallen kan het nodig zijn om ondersteuning voor Azure te helpen bij het oplossen van een probleem, dat u mogelijk meer gedetailleerde logboeken moet genereren dan eerder beschreven. Deze logboeken genereren:

1. [Koppel de module opslag aan de opslag van het apparaat](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via `createOptions` . Als u een sjabloon voor het [implementatie manifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) bekijkt vanuit de Quick starts, ziet u deze code:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Met deze code kan de module Edge logboeken naar het opslagpad van het apparaat schrijven `/var/local/mediaservices/` . 

 1. Voeg de volgende `desired` eigenschap toe aan de module:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

De module schrijft nu de logboeken voor fout opsporing in een binaire indeling naar het opslagpad van het apparaat `/var/local/mediaservices/debuglogs/` . U kunt deze logboeken delen met Azure-ondersteuning.

## <a name="faq"></a>Veelgestelde vragen

Als u vragen hebt, raadpleegt u de [Veelgestelde vragen over bewaking en metrische gegevens](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Volgende stappen

[Continue video-opname](continuous-video-recording-tutorial.md)