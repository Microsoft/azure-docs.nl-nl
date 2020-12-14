---
title: Een gRPC-uitstel server ontwikkelen en implementeren-Azure
description: Dit artikel bevat richt lijnen voor het ontwikkelen en implementeren van een gRPC-inrichtings server.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 3f732a7432dacebeeefddd1822fec7d95dfbaa97
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425896"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Instructies voor het ontwikkelen en implementeren van een gRPC-Afleidings server

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u een AI-model (en) van uw keuze kunt teruglopen binnen een gRPC-server voor deservering, zodat deze kan worden geïntegreerd met live video Analytics (LVA) via de grafiek extensie. 

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen

* [Media Graph-extensies](media-graph-extension-concept.md)
* [gRPC-extensieprotocol](grpc-extension-protocol.md)
* [Meta gegevens schema afleiding](inference-metadata-schema.md)
* [Inleiding tot gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [gids voor proto3 language](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Vereisten

* Een x86-64-of een ARM64-apparaat met een van de [ondersteunde Linux-besturings systemen](https://docs.microsoft.com/azure/iot-edge/support#operating-systems) of een Windows-machine.
* [Installeer docker](https://docs.docker.com/desktop/#download-and-install) op uw computer.
* Installeer [IOT Edge runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?tabs=linux).

## <a name="grpc-implementation-steps"></a>gRPC-implementaties tappen

Als u een gRPC wilt maken en deze wilt implementeren als een uitbrei ding met live video analyse, worden de volgende stappen gebruikt:

### <a name="setup"></a>Installatie:

Voer de benodigde stappen uit om [een live video Analytics-module te implementeren en te werken op een IOT edge apparaat](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Implementatie stappen op hoog niveau:

1. Kies een van de vele talen die worden ondersteund door gRPC: C#, C++, dart, go, Java, node, objectief-C, PHP, Python, Ruby.
1. Implementeer een gRPC-server die communiceert met live video Analytics met behulp van [de proto3-bestanden](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="gRPC-server die communiceert met live video Analytics met behulp van de proto3-bestanden":::

    Binnen deze service:
    1. Sessie beschrijving bericht uitwisseling tussen de server en de client afhandelen.
    1. [Voorbeeld berichten van media](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) verwerken en retour resultaten weer geven.

        1. Roep uw mechanisme voor het afleiden van een getraind model aan om te zorgen voor de inkomend berichten.
        1. Als u de resultaten van de engine wilt decoderen, kunt u ze terugpakken als een media-voor beeld en deze terugsturen naar live video-analyses met behulp van het bestand dezicht [. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) .

            U kunt ook een media transformatie functie aanroepen naar het media-voor beeld.
1. Implementeer de implementatie van de gRPC-server. Er zijn twee manieren om dit te doen:

    1. Implementeren als een IoT-module die zich met een live video Analytics-module bevindt
    1. Implementeer als een IoT-module op een netwerk toegankelijk knoop punt (on-premises of in de Cloud) waarmee gegevens kunnen worden uitgewisseld met de module live video Analytics.
1. Configureer een live video Analytics-grafiek topologie met de module live video analyse en wijs deze toe aan de gRPC-server.

### <a name="recommendation"></a>Advies

Wanneer collocating op hetzelfde knoop punt, kan gedeeld geheugen worden gebruikt voor de beste prestaties. Hiervoor moet u de functionaliteit voor gedeeld Linux-geheugen gebruiken die wordt weer gegeven door de programmeer taal/omgeving.

1. Open de koppeling voor gedeeld Linux-geheugen.
1. Wanneer u een frame ontvangt, krijgt u toegang tot de adres verschuiving binnen het gedeelde geheugen.
1. Bevestig de voltooiing van de kader verwerking zodat het geheugen kan worden vrijgemaakt door live video Analytics.

## <a name="create-a-grpc-inference-server"></a>Een gRPC-Afleidings server maken

Nu bouwt u een IoT Edge module (externe AI) die video frames van live video-analyses accepteert met behulp van [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -berichten via gedeeld geheugen, classificeert u de frames als ' donker ' of ' licht ' en retourneert u de resultaten van het gebruik van het [meta gegevens schema](inference-metadata-schema.md)voor het afleiden naar de IOT hub bericht sink in live video-analyses.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="een IoT Edge module bouwen (externe AI)":::

Deze gRPC-Afleidings server is een .NET core-console toepassing die de [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) berichten verwerkt die tussen live video Analytics en uw aangepaste AI worden verzonden. Hieronder volgt de stroom van berichten tussen live video Analytics en de gRPC-Afleidings server:

1. Live video Analytics verzendt een Media stream-descriptor (Zie [extensie. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)) waarmee de gegevens van de media stroom worden gedefinieerd die worden verzonden, gevolgd door video frames naar de server als een [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -bericht over de gRPC-stroom sessie. 
1. De server valideert en erkent de stroom descriptor en stelt de gewenste methode voor gegevens overdracht in.
1. Live video Analytics begint vervolgens met het verzenden van de MediaSample-bestanden die de video frames bevatten.
1. De server analyseert de video frames tijdens het ontvangen en begint met de verwerking van een afbeeldings processor die door u is gedefinieerd.
1. De server retourneert vervolgens de resultaten van de inschakeling als [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -berichten zodra deze beschikbaar zijn. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Een gRPC-Afleidings server maken":::

De video frames kunnen worden overgebracht via [gedeeld geheugen](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) of ze kunnen worden inge sloten in het protobuf-bericht. De modus voor gegevens overdracht kan worden geconfigureerd in de LVA-grafiek topologie om te bepalen hoe frames worden overgedragen. Dit wordt bereikt door het **dataTransfer** -element van de eigenschap MediaGraphGrpcExtension te configureren, zoals hieronder wordt weer gegeven:

Geïntegreerde

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Gedeeld geheugen:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Bij het communiceren via gedeeld geheugen moet de waarde van IpcMode worden ingesteld op **deelbaar** en wordt in de gRPC-server module de waarde van IpcMode ingesteld op **container: {CONTAINER_NAME}**. Deze instellingen moeten worden gemaakt in het manifest bestand van de implementatie dat wordt gebruikt voor het implementeren van de modules voor de Azure-IoT Hub. Hieronder ziet u een voor beeld van de container opties die u kunt gebruiken bij het instellen van de IoT Edge-modules.

Module live video Analytics:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC-extensie module:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Zorg ervoor dat u toegang hebt tot het gedeelde geheugen gebied van ' container: lvaEdge ' in de grpcExtension.

## <a name="sample-grpc-server"></a>Voor beeld van gRPC-server

Voor meer informatie over hoe gRPC-server is ontwikkeld, gaan we het code voorbeeld door nemen.

1. Kloon de opslag plaats van de GitHub-koppeling [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Start VSCode en navigeer naar de map/src/edge/modules/grpcExtension.
1. Laten we een korte beschrijving van de bestanden doen:

    1. **Program.cs**: dit is het toegangs punt van de toepassing. Het is verantwoordelijk voor het initialiseren en beheren van de gRPC-server, die als host fungeert. In ons voor beeld is de poort voor het Luis teren naar binnenkomende gRPC-berichten van een gRPC-client (zoals live video Analytics) op is opgegeven door het grpcBindings-configuratie-element in de AppConfig.jsop.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: deze klasse is verantwoordelijk voor het verwerken van de [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -berichten. Hiermee wordt het frame in het bericht gelezen, wordt de ImageProcessor aangeroepen en worden de resultaten van de deinterferentie geschreven.
Nu we de poort verbindingen van de gRPC-server hebben geconfigureerd en geïnitialiseerd, gaan we kijken hoe we de binnenkomende gRPC-berichten kunnen verwerken.

        * Zodra een gRPC-sessie tot stand is gebracht, wordt het eerste bericht dat de gRPC-server ontvangen van de client (live video Analytics) een MediaStreamDescriptor is die is gedefinieerd in het bestand [extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) . 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * Bij de server implementatie `ProcessMediaStreamDescriptor` valideert de methode de eigenschap MediaDescriptor van het MediaStreamDescriptor voor een video bestand en wordt vervolgens de modus voor gegevens overdracht ingesteld (die gebruikmaakt van gedeeld geheugen of het gebruik van de Inge sloten frame overdrachts modus), afhankelijk van wat u opgeeft in de topologie en het gebruikte implementatie sjabloon bestand. 
        * Wanneer het bericht wordt ontvangen en de modus voor gegevens overdracht is ingesteld, retourneert de gRPC-server het MediaStreamDescriptor-bericht terug naar de client als bevestiging en wordt er een verbinding tussen de server en de client tot stand gebracht.    
        * Nadat live video Analytics de bevestiging heeft ontvangen, begint de media stroom over te brengen naar de gRPC-server. In onze server implementatie verwerkt de methode `ProcessMediaStream` de inkomende MediaStreamMessage. De MediaStreamMessage wordt ook gedefinieerd in de [extensie. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Afhankelijk van de waarde van batchSize in de Appconfig.jsop, zal onze server de berichten blijven ontvangen en de video frames in een lijst opslaan. Zodra de limiet voor batchSize is bereikt, wordt de functie aangeroepen of het bestand waarmee de installatie kopie wordt verwerkt. In ons geval roept de methode een bestand met de naam BatchImageProcessor.cs aan
    1. **Processors\BatchImageProcessor.cs**: deze klasse is verantwoordelijk voor het verwerken van de afbeelding (en). In dit voor beeld hebben we een afbeeldings classificatie model gebruikt. Voor elke installatie kopie die wordt verwerkt, wordt het volgende algoritme gebruikt:

        1. Converteer de afbeelding in een byte matrix voor verwerking. Zie methode: `GetBytes(Bitmap image)`
        
            De voorbeeld processor die wordt gebruikt, ondersteunt alleen JPG-afbeeldings kaders en geen als pixel indeling. Als uw aangepaste processor een andere code ring en/of indeling ondersteunt, werkt u de `IsMediaFormatSupported` methode van de processor klasse bij.
        1. Gebruik de [ColorMatrix-klasse](https://docs.microsoft.com/dotnet/api/system.drawing.imaging.colormatrix?redirectedfrom=MSDN&view=dotnet-plat-ext-3.1&preserve-view=true)om de afbeelding naar grijs schalen te converteren. Zie methode: `ToGrayScale(Image source)` .
        1. Zodra de grijs schaal afbeelding is opgehaald, berekenen we het gemiddelde van de bytes van de grijs schaal.
        1. Als de gemiddelde waarde < 127, wordt de afbeelding als ' donker ' geclassificeerd. anders worden deze als ' licht ' ingedeeld als ' Light ' met een betrouw bare waarde van 1,0. Zie methode: `ProcessImage(List<Image> images)` .

    U kunt uw eigen processor logica toevoegen door deze klasse te wijzigen of door een nieuwe klasse toe te voegen en de-methode te implementeren:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Wanneer u de nieuwe klasse hebt toegevoegd, moet u de MediaGraphExtensionService.cs bijwerken zodat deze uw klasse start en de ProcessImage-methode erop aanroept om uw verwerkings logica uit te voeren. 

## <a name="connect-with-live-video-analytics-module"></a>Verbinding maken met de module live video Analytics

Nu u de extensie module gRPC hebt gemaakt, wordt de media grafiek topologie nu gemaakt en geïmplementeerd.

1. Volg met behulp van Visual Studio Code [deze instructies](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) om u aan te melden bij Docker.
1. Ga in Visual Studio Code naar src/edge. U ziet het bestand .env en enkele implementatiesjabloonbestanden.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat. Deze bevat enkele tijdelijke waarden. Het .env-bestand bevat de waarden voor die variabelen.
    
    Selecteer vervolgens IoT Edge-oplossingen bouwen en pushen. Gebruik src/Edge/deployment.grpc.template.jsvoor deze stap.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Verbinding maken met de module live video Analytics":::
    
    Met deze actie wordt de grpc-server module gebouwd en wordt de installatie kopie naar uw Azure Container Registry gepusht.
    Controleer of u de omgevingsvariabelen CONTAINER_REGISTRY_USERNAME_myacr en CONTAINER_REGISTRY_PASSWORD_myacr zijn gedefinieerd in het .env-bestand.
1. Ga naar de map src/cloud-to-device-console-app. Hier ziet u het bestand appsettings.json en enkele andere bestanden:

    * c2d-console-app.csproj: het projectbestand voor Visual Studio Code.
    * operations.json: een lijst met de bewerkingen die u het programma wilt laten uitvoeren.
    * Program.cs: de voorbeeldcode van het programma. Deze code:

        * De app-instellingen laden.
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om livevideostreams te analyseren door de bijbehorende [directe methoden](direct-methods.md) aan te roepen.
        * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het TERMINAL-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het UITVOER-venster.
        * Roept directe methoden aan voor het opschonen van resources.
1. Bewerk het bestand operations.json:

    * Wijzig de link naar de graaftopologie:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * Bewerk onder GraphInstanceSet de naam van de grafiektopologie zodat deze overeenkomt met de waarde in de voorgaande koppeling:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * Bewerk de naam onder GraphTopologyDelete:<br/>`"name": "InferencingWithGrpcExtension"`

            In de topologie (bijvoorbeeld `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json` ) moet een extensie adres worden gedefinieerd:
    * Extensie adres parameter

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Configuratie

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren

Het distributiemanifest definieert welke modules op een Edge-apparaat worden geïmplementeerd en de configuratie-instellingen voor deze modules. Volg deze stappen om een dergelijk manifest van het sjabloonbestand te genereren en vervolgens te implementeren op het Edge-apparaat.
Met deze stap maakt u het IoT Edge implementatie manifest op brondoc/Edge/config/deployment.grpc.amd64.jsop. Klik met de rechter muisknop op het bestand en selecteer **implementatie maken voor één apparaat**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Het IoT Edge-implementatiemanifest genereren en implementeren":::

Vervolgens wordt u in Visual Studio Code gevraagd om een IoT Hub-apparaat te selecteren. Selecteer uw IoT Edge-apparaat. Dit moet lva-sample-device zijn.
Nu is de implementatie van Edge-modules op uw IoT Edge-apparaat gestart. Vernieuw Azure IoT Hub, over ongeveer 30 seconden, in de sectie linksonder in Visual Studio Code. U ziet dat een nieuwe module is geïmplementeerd met de naam lvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="Er is een nieuwe module met de naam lvaExtension geïmplementeerd":::

## <a name="next-steps"></a>Volgende stappen

Volg de **voor bereiding voor het bewaken van gebeurtenissen die worden** vermeld in de Snelstartgids [Live video analyseren met uw model](use-your-model-quickstart.md) Quick Start om het voor beeld uit te voeren en de resultaten te interpreteren. Bekijk ook onze voor beelden van gRPC-topologieën: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension en [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).

