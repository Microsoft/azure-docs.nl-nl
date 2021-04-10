---
title: gRPC-extensieprotocol - Azure
description: In dit artikel leert u het gRPC-extensieprotocol gebruiken om berichten te verzenden tussen de Live Video Analytics-module en uw aangepaste AI- of CV-extensie.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 8d153b472e54b221b60a2b584043ffaf68e8ff82
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565806"
---
# <a name="grpc-extension-protocol"></a>gRPC-extensieprotocol

Met Live Video Analytics op IoT Edge kunt u de mogelijkheden voor mediagrafiekverwerking uitbreiden via een [grafiekextensieknooppunt](./media-graph-extension-concept.md). Als u de gRPC-extensieprocessor gebruikt als het extensieknooppunt, verloopt de communicatie tussen de Live Video Analytics-module en uw AI- of CV-module via een gestructureerd gRPC-protocol met hoge prestaties.

In dit artikel leert u het gRPC-extensieprotocol gebruiken om berichten te verzenden tussen de Live Video Analytics-module en uw aangepaste AI- of CV-extensie.

gRPC is een modern, opensource-RPC-framework met hoge prestaties, dat in elke omgeving kan worden uitgevoerd en ondersteuning biedt voor communicatie tussen verschillende platformen en in verschillende talen. De gRPC-transportservice maakt gebruik van HTTP/2 bidirectionele streaming tussen:

* de gRPC-client ( Live Video Analytics in IoT Edge-module) en 
* de gRPC-server (uw aangepaste extensie).

Een gRPC-sessie is een enkele verbinding van de gRPC-client naar de gRPC-server via de TCP/TLS-poort. 

In één sessie: De client verzendt een mediastream-descriptor gevolgd door videoframes naar de server als een [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)-bericht over de gRPC-stroomsessie. De server valideert de stream-descriptor, analyseert het videoframe en retourneert de deductieresultaten als een protobuf-bericht. 

Het wordt aanbevolen om antwoorden te retourneren met behulp van geldige JSON-documenten, volgens het vooraf vastgelegde schema gedefinieerd in het [objectmodel voor het metagegevensschema voor deductie](./inference-metadata-schema.md). Dit zorgt voor betere interoperabiliteit met andere onderdelen en eventueel toekomstige mogelijkheden die aan de Live Video Analytics-module worden toegevoegd.

![gRPC-extensiecontract](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>gRPC-protocol implementeren

### <a name="creating-a-grpc-connection"></a>Een gRPC-verbinding maken

De aangepaste uitbreiding moet de volgende gRPC-service implementeren:

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

Als deze wordt aangeroepen, wordt er een bidirectionele stroom geopend om berichten te verzenden tussen de gRPC-extensie en Live Video Analytics-graaf. Het eerste bericht dat door elke partij in deze stroom wordt verzonden, bevat een MediaStreamDescriptor, waarmee wordt gedefinieerd welke gegevens in de volgende MediaSamples worden verzonden.

De graafextensie kan bijvoorbeeld het bericht (hier uitgedrukt in JSON) verzenden om aan te geven dat deze 416x416 met rgb24 gecodeerde in de gRPC ingesloten frames naar de aangepaste extensie zal verzenden.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

De aangepaste extensie zou als antwoord het volgende bericht verzenden om aan te geven dat deze alleen deducties retourneert.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Nu beide zijden media-descriptors hebben uitgewisseld, begint Live Video Analytics frames naar de extensie te verzenden.

> [!NOTE]
> De gRPC-implementatie aan de serverzijde kan worden uitgevoerd in de programmeertaal van uw keuze.
### <a name="sequence-numbers"></a>Reeksnummers

Zowel het gRPC-extensieknooppunt als de aangepaste extensie houden een afzonderlijke reeks volgnummers bij die worden toegewezen aan hun berichten. Deze volgnummers moeten monotoon toenemen, te beginnen bij 1. `ack_sequence_number` kan worden genegeerd als er geen bericht wordt bevestigd. Dit kan zich voordoen wanneer het eerste bericht wordt verzonden.

Een aanvraag moet worden bevestigd zodra het bijbehorende bericht volledig is verwerkt. In het geval van een gedeelde geheugenoverdracht geeft deze bevestiging aan dat de afzender het gedeelde geheugen kan vrijmaken en dat de ontvanger geen toegang meer nodig heeft. De afzender moet gedeeld geheugen vasthouden totdat het bericht is bevestigd.

### <a name="reading-embedded-content"></a>Ingesloten inhoud lezen

Ingesloten inhoud kan rechtstreeks uit het `MediaSample`-bericht worden gelezen via het veld `content_byte`s. De gegevens worden gecodeerd op basis van de `MediaDescriptor`.

### <a name="reading-content-from-shared-memory"></a>Inhoud lezen uit gedeeld geheugen

Bij het overbrengen van inhoud via gedeeld geheugen neemt de afzender `SharedMemoryBufferTransferProperties` op in de `MediaStreamDescriptor` wanneer deze voor het eerst een sessie tot stand brengt. Dit kan er als volgt uitzien (uitgedrukt in JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

De ontvanger opent vervolgens het bestand `/dev/shm/inference_client_share_memory_2146989006636459346`. De afzender stuurt een `MediaSample`-bericht, dat een `ContentReference` bevat die verwijst naar een specifieke locatie in dit bestand.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

De ontvanger leest vervolgens de gegevens van deze locatie in het bestand.

De IPC-modus van de container moet correct zijn geconfigureerd om de Live Video Analytics-container te laten communiceren via gedeeld geheugen. Dit kan op allerlei manieren worden gedaan, maar hier zijn enkele aanbevolen configuraties.

* Bij het communiceren met een gRPC-deductie-engine op het hostapparaat moet de IPC-modus zijn ingesteld op host.
* Bij het communiceren met een gRPC-server die wordt uitgevoerd in een andere IoT Edge-module moet de IPC-modus zijn ingesteld op deelbaar voor de Live Video Analytics-module en `container:liveVideoAnalytics` voor de aangepaste extensie, waarbij `liveVideoAnalytics` de naam van de Live Video Analytics-module is.

Dit kan er als volgt uitzien op de apparaatdubbel met de eerste optie van hierboven.

```
"liveVideoAnalytics": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "host"
      }
  }
}
```

Zie https://docs.docker.com/engine/reference/run/#ipc-settings---ipc voor meer informatie over IPC-modi.

## <a name="mediagraph-grpc-extension-contract-definitions"></a>Contractdefinities voor de MediaGraph gRPC-extensie

In deze sectie wordt het gRPC-contract gedefinieerd waarmee de gegevensstroom wordt gedefinieerd.

### <a name="protocol-messages"></a>Protocolberichten

![Protocolberichten](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>Clientverificatie

Implementaties van aangepaste extensies kunnen de authenticiteit van binnenkomende gRPC-verbindingen valideren om er zeker van te zijn dat ze afkomstig zijn van het gRPC-extensieknooppunt. Het knooppunt bevat een vermelding in de aanvraagheaders waarmee kan worden gevalideerd.

Gebruikersnaam-wachtwoordreferenties kunnen worden gebruikt om dit te bereiken. Bij het maken van een gRPC-extensieknooppunt worden de referenties opgegeven zoals hieronder:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Wanneer de gRPC-aanvraag wordt verzonden, wordt de volgende header opgenomen in de metagegevens van de aanvraag om de basisverificatie van HTTP te imiteren.

`x-ms-authentication: Basic (Base64 Encoded username:password)`


## <a name="configuring-inference-server-for-each-mediagraph-over-grpc-extension"></a>De deductieserver configureren voor elke MediaGraph via gRPC-extensie
Bij het configureren van de deductieserver, hoeft u geen knooppunt beschikbaar te maken voor elk AI-model dat is verpakt binnen de deductieserver. In plaats hiervan kunt u voor een grafiekexemplaar de eigenschap `extensionConfiguration` van het knooppunt `MediaGraphGrpcExtension` gebruiken, en definiëren hoe AI-modellen moeten worden geselecteerd. Tijdens de uitvoering geeft LVA deze tekenreeks door aan de deductieserver, die de tekenreeks kan gebruiken om het gewenste AI-model aan te roepen. Deze eigenschap `extensionConfiguration` is een optionele eigenschap en is serverspecifiek. De eigenschap kan als volgt worden gebruikt:
```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "5"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>gRPC via TLS gebruiken

Een gRPC-verbinding die wordt gebruikt voor deductie kan worden beveiligd via TLS. Dit is handig in situaties waarin de beveiliging van het netwerk tussen Live Video Analytics en de deductie-engine niet kan worden gegarandeerd. TLS versleutelt alle inhoud die is ingesloten in de gRPC-berichten, waardoor er extra CPU-overhead ontstaat bij het verzenden van frames met hoge snelheid.

De verificatieopties IgnoreHostname en IgnoreSignature worden niet ondersteund door gRPC, dus het servercertificaat dat de deductie-engine presenteert moet een CN bevatten die exact overeenkomt met het IP-adres/hostnaam in de eindpunt-URL van het gRPC-extensieknooppunt.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het deductiemetagegevensschema](inference-metadata-schema.md)