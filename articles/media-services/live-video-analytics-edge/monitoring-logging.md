---
title: Bewaking en logboek registratie-Azure
description: Dit artikel bevat een overzicht van live video analyses op IoT Edge bewaking en logboek registratie.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 8ae455a4157cd649f610620e486323ac2c0a5744
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401046"
---
# <a name="monitoring-and-logging"></a>Bewaking en registratie

In dit artikel vindt u informatie over hoe u gebeurtenissen kunt ontvangen van de module live video Analytics op IoT Edge voor externe controle. 

U leert ook hoe u de logboeken kunt beheren die door de module worden gegenereerd.

## <a name="taxonomy-of-events"></a>Taxonomie van gebeurtenissen

Live video Analytics op IoT Edge verzendt gebeurtenissen of telemetriegegevens op basis van de volgende taxonomie.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Taxonomie van gebeurtenissen":::

* Operationeel: gebeurtenissen die worden gegenereerd als onderdeel van acties die worden uitgevoerd door een gebruiker of tijdens de uitvoering van een [Media grafiek](media-graph-concept.md).
   
   * Volume: er wordt naar verwachting laag (een paar keer per minuut of zelfs lagere frequentie).
   * Voorbeelden:

      Opname is gestart (onder), de opname is gestopt
      
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
* Diagnostische gegevens: gebeurtenissen die helpen bij het vaststellen van problemen en/of problemen met prestaties.

   * Volume: kan hoog zijn (enkele keren per minuut).
   * Voorbeelden:
   
      RTSP- [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -informatie (hieronder) of hiaten in de feed voor binnenkomende video.

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
* Analytics: gebeurtenissen die worden gegenereerd als onderdeel van de video analyse.

   * Volume: kan hoog zijn (meermaals een minuut of vaker).
   * Voorbeelden:
      
      Gedetecteerde bewegingen (hieronder), het afleiden van resultaten.

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

De gebeurtenissen die door de module worden gegenereerd, worden naar de [IOT Edge-hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)verzonden en van daaruit kan worden doorgestuurd naar andere bestemmingen. 

### <a name="timestamps-in-analytic-events"></a>Tijds tempels in analytische gebeurtenissen

Zoals hierboven aangegeven, hebben gebeurtenissen die als onderdeel van de video analyse worden gegenereerd, een tijds tempel gekoppeld. Als u [de live video hebt opgenomen](video-recording-concept.md) als onderdeel van de grafiek topologie, helpt deze tijds tempel u bij het zoeken naar waar in de opgenomen video dat bepaalde gebeurtenis heeft plaatsgevonden. Hieronder vindt u de richt lijnen voor het toewijzen van de tijds tempel in een analytische gebeurtenis aan de tijd lijn van de video die is opgenomen in een [Azure media service-Asset](terminology.md#asset).

Haal eerst de `eventTime` waarde op. Gebruik deze waarde in een [tijds bereik filter](playback-recordings-how-to.md#time-range-filters) om een geschikt deel van de opname op te halen. U kunt bijvoorbeeld video ophalen die 30 seconden eerder begint `eventTime` en eindigt 30 seconden daarna. In het bovenstaande voor beeld, waarbij `eventTime` 2020-05-12T23:33:09.381 z, een aanvraag voor een HLS-manifest voor het venster +/-30s ziet er ongeveer als volgt uit:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

De URL hierboven retourneert een ' so-' [Master-afspeel lijst](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)met url's voor afspeel lijsten met media. De afspeel lijst van media bevat vermeldingen zoals de volgende:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
In het bovenstaande rapporteert de vermelding dat er een video fragment beschikbaar is dat begint met een time stamp-waarde van `143039375031270` . De `timestamp` waarde in de analyse gebeurtenis maakt gebruik van dezelfde tijd schaal als de media-afspeel lijst en kan worden gebruikt om het relevante video fragment te identificeren en te zoeken naar het juiste frame.

Voor meer informatie kunt u een van de vele [artikelen](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) in het kader nauw keurig zoeken in HLS lezen.

## <a name="controlling-events"></a>Gebeurtenissen beheren

U kunt de volgende module dubbele eigenschappen, zoals beschreven in [module dubbele JSON-schema](module-twin-configuration-schema.md), gebruiken om de operationele en diagnostische gebeurtenissen te beheren die door de live video Analytics op IOT Edge module worden gepubliceerd.

`diagnosticsEventsOutputName` – Neem een waarde voor deze eigenschap op en geef deze op om diagnostische gebeurtenissen van de module op te halen. Laat deze weg of laat het leeg om te voor komen dat de module diagnostische gebeurtenissen publiceert.
   
`operationalEventsOutputName` – Neem een waarde voor deze eigenschap op en geef deze op om operationele gebeurtenissen van de module op te halen. Laat deze weg of laat het leeg om te voor komen dat de module operationele gebeurtenissen publiceert.
   
De analyse gebeurtenissen worden gegenereerd door knoop punten zoals de bewegings detectie processor of de HTTP-extensie processor, en de IoT hub-sink wordt gebruikt om ze naar de IoT Edge hub te verzenden. 

U kunt de [route ring van alle bovenstaande gebeurtenissen](../../iot-edge/module-composition.md#declare-routes) beheren via een gewenste eigenschap van de $edgeHub module (in het implementatie manifest):

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

In het bovenstaande is lvaEdge de naam voor de live video Analytics op IoT Edge module en volgt de routerings regel het schema dat is gedefinieerd in de [Declareer routes](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Om ervoor te zorgen dat Analytics-gebeurtenissen de IoT Edge hub bereiken, moet er een-Sink-knoop punt van de IoT-hub worden aangetroffen in het knoop punt van een wille keurig bewegings detectie processor en/of een HTTP extension-processor knooppunt.

## <a name="event-schema"></a>Gebeurtenisschema

Gebeurtenissen zijn afkomstig van het apparaat aan de rand en kunnen worden gebruikt op de rand of in de Cloud. Gebeurtenissen die worden gegenereerd door live video Analytics op IoT Edge voldoen aan het [streaming-berichten patroon](../../iot-hub/iot-hub-devguide-messages-construct.md) dat is vastgesteld door Azure IOT hub, met systeem eigenschappen, toepassings eigenschappen en een hoofd tekst.

### <a name="summary"></a>Samenvatting

Bij elke gebeurtenis, wanneer deze via de IoT Hub wordt waargenomen, wordt een aantal algemene eigenschappen weer gegeven, zoals hieronder wordt beschreven.

|Eigenschap   |Eigenschapstype| Gegevenstype   |Beschrijving|
|---|---|---|---|
|bericht-id |systeem |guid|  Unieke gebeurtenis-ID.|
|onderwerp| applicationProperty |tekenreeks|    Azure Resource Manager pad voor het Media Services-account.|
|Onderwerp|   applicationProperty |tekenreeks|    Subpad van de entiteit die de gebeurtenis verstuurt.|
|eventTime| applicationProperty|    tekenreeks| Tijdstip waarop de gebeurtenis is gegenereerd.|
|eventType| applicationProperty |tekenreeks|    Gebeurtenis type-id (zie hieronder).|
|body|body  |object|    Bepaalde gebeurtenis gegevens.|
|dataVersion    |applicationProperty|   tekenreeks  |{Major}. Secundair|

### <a name="properties"></a>Eigenschappen

#### <a name="message-id"></a>bericht-id

Gebeurtenis Globally Unique Identifier (GUID)

#### <a name="topic"></a>onderwerp

Vertegenwoordigt het Azure media service-account dat is gekoppeld aan de grafiek.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>Onderwerp

Entiteit die de gebeurtenis verzendt:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

De eigenschap subject kan algemene gebeurtenissen toewijzen aan de bijbehorende genererende module. Als er bijvoorbeeld een ongeldige RTSP-gebruikers naam of wacht woord is opgegeven, zou de gegenereerde gebeurtenis zich `Microsoft.Media.Graph.Diagnostics.ProtocolError` op het knoop punt bevindt `/graphInstances/myGraph/sources/myRtspSource` .

#### <a name="event-types"></a>Gebeurtenistypen

Gebeurtenis typen worden toegewezen aan een naam ruimte op basis van het volgende schema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Gebeurtenisklassen

|Klassenaam|Beschrijving|
|---|---|
|Analyse  |Gebeurtenissen die worden gegenereerd als onderdeel van inhouds analyse.|
|Diagnostiek    |Gebeurtenissen die hulp bieden bij diagnose van problemen en prestaties.|
|Operationeel    |Gebeurtenissen die worden gegenereerd als onderdeel van de resource bewerking.|

De gebeurtenis typen zijn specifiek voor elke gebeurtenis klasse.

Voorbeelden:

* Micro soft. media. Graph. Analytics. deinterferentie
* Micro soft. media. Graph. Diagnostics. AuthorizationError
* Micro soft. media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Tijd van gebeurtenis

De tijd van de gebeurtenis wordt beschreven in de ISO8601-teken reeks en het tijdstip waarop de gebeurtenis heeft plaatsgevonden.

### <a name="azure-monitor-collection-using-telegraf"></a>Azure Monitor verzameling met telegrafie

Deze metrische gegevens worden gerapporteerd als live video Analytics op IoT Edge module:  

|Naam meetwaarde|Type|Label|Beschrijving|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Meter|iothub, edge_device, module_name, graph_topology|Totaal aantal actieve grafieken per topologie.|
|lva_received_bytes_total|Prestatiemeteritem|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Het totale aantal bytes dat door een knoop punt is ontvangen. Alleen ondersteund voor RTSP-bronnen|
|lva_data_dropped_total|Prestatiemeteritem|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Teller van verwijderde gegevens (gebeurtenissen, media, enzovoort)|

> [!NOTE]
> Een [Prometheus-eind punt](https://prometheus.io/docs/practices/naming/) wordt weer gegeven op poort **9600** van de container. Als u uw live video-analyse op IoT Edge module ' lvaEdge ' benoemen, hebben ze toegang tot metrische gegevens door een GET-aanvraag te verzenden naar http://lvaEdge:9600/metrics .   

Volg deze stappen om het verzamelen van metrische gegevens in te scha kelen op de module live video Analytics op IoT Edge:

1. Maak een map op uw ontwikkel computer en navigeer naar die map

1. Maak in die map `telegraf.toml` een bestand met de volgende inhoud
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Zorg ervoor dat u de variabelen vervangt (gemarkeerd door de `{ }` ) in het inhouds bestand

1. Maak een `.dockerfile` met de volgende inhoud in deze map
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Nu gebruikt u docker CLI-opdracht om **het docker-bestand te maken** en de installatie kopie naar uw Azure container Registry te publiceren.
    1. Meer informatie over het [pushen en ophalen van docker-installatie kopieën-Azure container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).  Meer informatie over Azure Container Registry (ACR) vindt u [hier](https://docs.microsoft.com/azure/container-registry/).


1. Wanneer de push naar ACR is voltooid, voegt u in het manifest bestand van de implementatie het volgende knoop punt toe:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{ACR_LINK_TO_YOUR_TELEGRAF_IMAGE}"
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
    > Zorg ervoor dat u de variabelen vervangt (gemarkeerd door de `{ }` ) in het inhouds bestand


1. **Verificatie**
    1. Azure Monitor kunnen worden [geverifieerd door de Service-Principal](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        1. De Azure Monitor-telegrafe-invoeg toepassing bevat [verschillende verificatie methoden](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). De volgende omgevings variabelen moeten worden ingesteld op het gebruik van Service-Principal-verificatie.  
            • AZURE_TENANT_ID: Hiermee geeft u de Tenant op waarnaar moet worden geverifieerd.  
            • AZURE_CLIENT_ID: Hiermee geeft u de ID van de app-client op die moet worden gebruikt.  
            • AZURE_CLIENT_SECRET: Hiermee geeft u het app-geheim op dat moet worden gebruikt.  
    >[!TIP]
    > De service-principal kan de rol '**bewaking metrische gegevens Uitgever**' krijgen.

1. Zodra de modules zijn geïmplementeerd, worden metrische gegevens weer gegeven in Azure Monitor onder één naam ruimte met metrische namen die overeenkomen met de waarden die zijn gegenereerd door Prometheus. 
    1. In dit geval navigeert u in uw Azure Portal naar de IoT Hub en klikt u op de koppeling **metrische gegevens** in het navigatie deel venster aan de linkerkant. Hier ziet u de metrische gegevens.
## <a name="logging"></a>Logboekregistratie

Net als bij andere IoT Edge modules kunt u ook [de container logboeken](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) op het apparaat Edge bekijken. De informatie die naar de logboeken wordt geschreven, kan worden beheerd met de [volgende module dubbele](module-twin-configuration-schema.md) eigenschappen:

* logLevel

   * Toegestane waarden zijn uitgebreid, informatie, waarschuwing, fout, geen.
   * De standaard waarde is informatie: de logboeken bevatten fout, waarschuwing en informatie. meldingen.
   * Als u de waarde instelt op waarschuwing, bevatten de logboeken fout-en waarschuwings berichten
   * Als u de waarde instelt op fout, bevatten de Logboeken alleen fout berichten.
   * Als u de waarde instelt op geen, worden er geen logboeken gegenereerd (dit wordt niet aanbevolen).
   * U moet alleen uitgebreider gebruiken als u logboeken wilt delen met Azure-ondersteuning voor het vaststellen van een probleem.
* logCategories

   * Een door komma's gescheiden lijst met een of meer van de volgende items: toepassing, gebeurtenissen, MediaPipeline.
   * Standaard: toepassing, gebeurtenissen.
   * Toepassing: dit is informatie op hoog niveau van de module, zoals opstart berichten van een module, omgevings fouten en directe-methode aanroepen.
   * Gebeurtenissen: Dit zijn alle gebeurtenissen die eerder in dit artikel zijn beschreven.
   * MediaPipeline: Dit zijn enkele logboeken op laag niveau die inzicht kunnen bieden bij het oplossen van problemen, zoals problemen met het maken van een verbinding met een RTSP-compatibele camera.
   
### <a name="generating-debug-logs"></a>Logboeken voor fout opsporing genereren

In bepaalde gevallen moet u mogelijk gedetailleerdere logboeken genereren dan hierboven beschreven, om ondersteuning van Azure te helpen bij het oplossen van een probleem. Er zijn twee stappen om dit te bereiken.

Eerst [koppelt u de module opslag aan de opslag van het apparaat](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via createOptions. Als u een sjabloon voor de [implementatie manifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) van snel starten onderzoekt, ziet u het volgende:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Hierboven kunt u met de module Edge logboeken schrijven naar het opslagpad (apparaat) '/var/Local/mediaservices/'. Als u de volgende gewenste eigenschap aan de module toevoegt:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Vervolgens schrijft de module Logboeken voor fout opsporing in een binaire indeling naar het opslagpad (apparaat)/var/Local/mediaservices/debuglogs/, dat u met de ondersteuning van Azure kunt delen.

## <a name="faq"></a>Veelgestelde vragen

[Veelgestelde vragen](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Volgende stappen

[Continue video-opname](continuous-video-recording-tutorial.md)
