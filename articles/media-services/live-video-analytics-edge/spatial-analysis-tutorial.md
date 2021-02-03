---
title: Livevideo analyseren met Computer Vision voor ruimtelijke analyse, Azure
description: In deze zelfstudie leert u hoe u Live Video Analytics samen met de AI-functie Computer Vision ruimtelijke analyse gebruikt van Azure Cognitive Services om een livevideofeed van een (gesimuleerde) IP-camera te analyseren.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: f8a828d27b69fedefe7ccbc2ad7290bf300afc16
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526786"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Livevideo analyseren met Computer Vision voor ruimtelijke analyse (preview)

In deze zelfstudie leert u hoe u Live Video Analytics samen met de [AI-service Computer Vision ruimtelijke analyse gebruikt van Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) om een livevideofeed van een (gesimuleerde) IP-camera te analyseren. U ziet hoe u met deze deductieserver de streaming video kunt analyseren voor een beter begrip van ruimtelijke relaties tussen mensen en beweging in fysieke ruimte.  Een subset van de frames in de videofeed wordt verzonden naar deze deductieserver en de resultaten worden verzonden naar IoT Edge Hub. Wanneer aan bepaalde voorwaarden wordt voldaan, worden videoclips vastgelegd en opgeslagen als Azure Media Services-assets.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Resources instellen.
> * De code controleren.
> * De voorbeeldcode uitvoeren.
> * Gebeurtenissen bewaken.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > U hebt een Azure-abonnement met machtigingen nodig voor het maken van service-principals (de **rol van eigenaar** biedt dit). Als u niet over de juiste machtigingen beschikt, neemt u contact op met uw account beheerder om u de juiste machtigingen te verlenen. 
## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen

Lees deze artikelen voordat u begint:

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)
* [Mediagrafiekconcepten](media-graph-concept.md)
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Zelfstudie: Een IoT Edge-module ontwikkelen](../../iot-edge/tutorial-develop-for-linux.md)
* [Live Video Analytics implementeren op Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten om de module ruimtelijke analyse te verbinden met de module Live Video Analytics.

* [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer. Zorg ervoor dat u beschikt over de [Azure IoT Tools-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Zorg ervoor dat het netwerk waarmee uw ontwikkelcomputer is verbonden Advanced Message Queueing Protocol toestaat via poort 5671. Dankzij deze instelling kan Azure IoT Tools communiceren met Azure IoT Hub.
* [Azure Stack Edge ](https://azure.microsoft.com/products/azure-stack/edge/)met GPU-versnelling.  
    U wordt aangeraden Azure Stack Edge te gebruiken met GPU-versnelling, maar de container kan worden uitgevoerd op elk ander apparaat met een [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Azure Cognitive Service Computer Vision-container](https://azure.microsoft.com/services/cognitive-services/computer-vision/) voor ruimtelijke analyse.  
    Om deze container te kunnen gebruiken, moet u een Computer Vision-resource hebben om de bijbehorende **API-sleutel** en **eindpunt-URI** op te halen. De API-sleutel is beschikbaar op de Azure Portal-pagina's Computer Vision Overzicht en Sleutels. De sleutel en het eindpunt zijn vereist om de container te kunnen starten.

## <a name="overview"></a>Overzicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Overzicht van ruimtelijke analyse":::
 
Dit diagram laat zien hoe de signalen in deze zelfstudie stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Met een [RTSP-bron](media-graph-concept.md#rtsp-source)knooppunt wordt de videofeed van deze server gehaald, en worden videoframes verzonden naar het knooppunt `MediaGraphCognitiveServicesVisionExtension`processorknooppunt.

De node MediaGraphCognitiveServicesVisionExtension speelt de rol van proxy. Het converteert videoframes naar het opgegeven afbeeldingstype. Vervolgens wordt de afbeelding via **gedeeld geheugen** doorgestuurd naar een andere Edge-module die AI-bewerkingen achter een gRPC-eindpunt uitvoert. In dit voorbeeld is die Edge-module de module ruimtelijke analyse. Het processorknooppunt MediaGraphCognitiveServicesVisionExtension voert twee dingen uit:

* Het verzamelt de resultaten en publiceert gebeurtenissen naar het sinkknooppunt [IoT Hub](media-graph-concept.md#iot-hub-message-sink). Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Het legt ook een videoclip van 30 seconden vast van de RTSP-bron met een [signaalpoortprocessor](media-graph-concept.md#signal-gate-processor) en slaat het op als een Media Services-asset.

## <a name="create-the-computer-vision-resource"></a>De Computer Vision-resource maken

U moet een Azure-resource maken van het type Computer Vision op de [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) of via Azure CLI. U kunt de resource maken wanneer uw aanvraag voor toegang tot de container is goedgekeurd en uw Azure-abonnements-id is geregistreerd. Ga naar https://aka.ms/csgate om uw use-case en uw Azure-abonnements-id in te dienen.  U moet de Azure-resource maken met hetzelfde Azure-abonnement dat is opgegeven in het aanvraagformulier voor toegang.

### <a name="gathering-required-parameters"></a>Vereiste parameters verzamelen

Er zijn drie primaire parameters vereist voor alle Cognitive Services-containers, met inbegrip van de container voor ruimtelijke analyse. De gebruiksrechtovereenkomst (EULA) moet aanwezig en geaccepteerd zijn. Bovendien zijn er een eindpunt-URI en API-sleutel nodig.

### <a name="keys-and-endpoint-uri"></a>Sleutels en eindpunt-URI

Een sleutel wordt gebruikt om de container voor de ruimtelijke analyse te starten en is beschikbaar op de Azure Portal-pagina `Keys and Endpoint` van de bijbehorende Cognitive Service-resource. Navigeer naar die pagina en zoek de sleutels en de eindpunt-URI.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Eindpunt-URI":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack Edge instellen

Volg [deze stappen](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) om de Azure Stack Edge in te stellen en vervolg met onderstaande stappen om de modules Live Video Analytics en ruimtelijke analyse te implementeren.

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

1. Kloon de opslagplaats vanuit deze locatie: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Open in Visual Studio Code de map waarin de opslagplaats is gedownload.
1. Ga in Visual Studio Code naar de map src/cloud-to-device-console-app. Maak daar een bestand en geef het de naam appsettings.json. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. U haalt de IotHubConnectionString op uit de Azure Stack Edge door deze stappen te volgen:

    * Ga naar uw IoT Hub in Azure Portal en klik op `Shared access policies` in het linkerdeelvenster.
    * Klik op `iothubowner` om de gedeelde toegangssleutels op te halen.
    * Kopieer de `Connection String – primary key` en plak deze vervolgens in het invoervak in de VSCode.
    
        De verbindingsreeks ziet er als volgt uit: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Kopieer onderstaande inhoud in het bestand. Zorg ervoor dat u de variabelen vervangt.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Ga naar de map src/edge en maak een bestand met de naam .env.
1. Kopieer de inhoud van het bestand /clouddrive/lva-sample/edge-deployment/.env. De tekst moet lijken op de volgende code.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Implementatiesjabloon instellen  

Zoek het implementatiebestand in /src/edge/deployment.spatialAnalysis.template.json. In het sjabloon bevinden zich de modules IvaEdge, rtspsim en onze module ruimtelijke analyse.

Er zijn een paar dingen waar u op moet letten in het bestand van de implementatiesjabloon:

1. Stel de poortbinding in.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` in IvaEdge en de ruimtelijkeanalysemodule createOptions moeten hetzelfde zijn en ingesteld op host.
1. Zorg ervoor dat u de Volume Bounds hebt ingesteld, zodat de RTSP-simulator kan werken. Zie [Docker Volume Mounts instellen](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts) voor meer informatie.

    1. [Maak verbinding met de SMB-share](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) en kopieer het [voorbeeldbestand van de bulldozervideo](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) naar de lokale share.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Controleer of de module rtspsim de volgende configuratie bevat:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Het implementatiemanifest genereren en implementeren

Het implementatiemanifest geeft aan welke modules op een edge-apparaat worden geïmplementeerd. Ook bevat het de configuratie-instellingen voor deze modules.

Volg deze stappen om het manifest te genereren op basis van het sjabloonbestand en het vervolgens te implementeren op het edge-apparaat.

1. Open Visual Studio Code.
1. Selecteer naast het deelvenster AZURE IOT HUB het pictogram Meer acties om de IoT Hub-verbindingsreeks in te stellen. U kunt de tekenreeks kopiëren uit het bestand `src/cloud-to-device-console-app/appsettings.json`.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Ruimtelijke analyse: verbindingsreeks":::
1. Klik met de rechtermuisknop op `src/edge/deployment.spatialAnalysis.template.json` en selecteer IoT Edge-implementatiemanifest genereren.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Ruimtelijke analyse: implementatie amd64 JSON":::
    
    Met deze actie maakt u een manifestbestand met de naam deployment.amd64.json in de map src/edge/config.
1. Klik met de rechtermuisknop op `src/edge/config/deployment.spatialAnalysis.amd64.json`, selecteer Implementatie voor één apparaat maken en selecteer vervolgens de naam van uw edge-apparaat.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Ruimtelijke analyse: implementatiesjabloon JSON":::   
1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, kiest u uw Azure Stack Edge-naam in de vervolgkeuzelijst.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:
    
    * Live Video Analytics in IoT Edge (modulenaam lvaEdge).
    * RTSP-simulator (Real-Time Streaming Protocol) (modulenaam rtspsim).
    * Ruimtelijke analyse (modulenaam spatialAnalysis).
    
Als de implementatie is geslaagd, verschijnt het volgende bericht in OUTPUT:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Vervolgens kunt u de modules `lvaEdge`, `rtspsim`, `spatialAnalysis` en `rtspsim` vinden onder Apparaten/modules en de status moet 'actief' zijn.

## <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

Volg deze stappen om deze gebeurtenissen te bekijken:

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Uitgebreid bericht tonen":::
1. Open het deelvenster Explorer en zoek Azure IoT Hub in de linkerbenedenhoek.
1. Vouw het knooppunt Apparaten uit.
1. Klik met de rechtermuisknop op uw Azure Stack Edge en selecteer Bewaking van ingebouwd gebeurteniseindpunt starten.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Ruimtelijke analyse: bewaking starten":::
     
## <a name="run-the-program"></a>Het programma uitvoeren

Er is een program.cs dat de directe methodes in src/cloud-to-device-console-app/operations.json aanroept. We moeten de operations.json instellen en een topologie opgeven voor het mediagrafiekgebruik.  

In operations.json:

* Stel de topologie als volgt in:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Maak een grafiekexemplaar als dit, stel hier de parameters in topologie in:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

>[!Note]
Controleer het gebruik van MediaGraphRealTimeComputerVisionExtension om verbinding te maken met de module ruimtelijke analyse. Stel de ${grpcUrl} in op **tcp://spatialAnalysis:<POORTNUMMER>** , bijv. tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Voer een foutopsporingssessie uit en volg de **TERMINAL**-instructies. Hiermee worden de topologie en het grafiekexemplaar ingesteld, het grafiekexemplaar geactiveerd en ten slotte de resources verwijderd.

## <a name="interpret-results"></a>Resultaten interpreteren

Wanneer een mediagrafiek wordt gemaakt, zou u een 'MediaSessionEstablished'-gebeurtenis moeten zijn. Hier een [voorbeeld van een MediaSessionEstablished-gebeurtenis](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

De module ruimtelijke analyse verzendt ook AI Insight-gebeurtenissen naar Live Video Analytics en vervolgens naar IoTHub, en wordt ook weergegeven in **OUTPUT**. De ENTITEIT is detecteren van objecten en GEBEURTENIS is ruimteanalysegebeurtenissen. Deze uitvoer wordt doorgegeven naar Live Video Analytics.

Voorbeelduitvoer voor personZoneEvent (van cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics-bewerking):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Probeer verschillende bewerkingen die de module `spatialAnalysis` biedt uit, zoals **personCount** en **personDistance** door de vlag 'ingeschakeld' in en uit te schakelen in het grafiekknooppunt van uw distributiemanifestbestand.
>[!Tip]
> Gebruik een [voorbeeldvideobestand](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) met meer dan één persoon in het frame.

> [!NOTE]
> U kunt maar één bewerking tegelijkertijd uitvoeren. Zorg er dus voor dat er maar één vlag is ingesteld op **waar** en dat de andere op **onwaar** staan.