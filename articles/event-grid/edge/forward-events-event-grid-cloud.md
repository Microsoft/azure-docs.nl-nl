---
title: Edge-gebeurtenissen door sturen naar Event Grid Cloud-Azure Event Grid IoT Edge | Microsoft Docs
description: Edge-gebeurtenissen naar Event Grid Cloud door sturen
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171598"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Zelf studie: gebeurtenissen door sturen naar Event Grid Cloud

In dit artikel worden alle stappen beschreven die nodig zijn voor het door sturen van Edge-gebeurtenissen naar Event Grid in de Azure-Cloud. U kunt dit het beste doen om de volgende redenen:

* Reageren op Edge-gebeurtenissen in de Cloud.
* Stuur gebeurtenissen door naar Event Grid in de Cloud en gebruik Azure Event Hubs of Azure Storage wacht rijen om gebeurtenissen te bufferen voordat ze in de cloud worden verwerkt.

 Voor het volt ooien van deze zelf studie hebt u een goed idee van Event Grid concepten over [Edge](concepts.md) en [Azure](../concepts.md). Zie voor aanvullende doel typen [gebeurtenis-handlers](event-handlers.md). 

## <a name="prerequisites"></a>Vereisten 
Als u deze zelf studie wilt volt ooien, hebt u het volgende nodig:

* **Azure-abonnement** : Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IOT hub en IOT edge apparaat** : Volg de stappen in de Quick start voor [Linux](../../iot-edge/quickstart-linux.md) -of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Een onderwerp en een abonnement op gebeurtenis raster maken in de Cloud

Maak een event grid-onderwerp en-abonnement in de Cloud door [deze zelf studie](../custom-event-quickstart-portal.md)te volgen. Noteer omlaag `topicURL` , `sasKey` en `topicName` van het zojuist gemaakte onderwerp dat u later in de zelf studie gaat gebruiken.

Als u bijvoorbeeld een onderwerp hebt gemaakt `testegcloudtopic` met de naam in VS West, zien de waarden er ongeveer als volgt uit:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Onderwerpnaam**: `testegcloudtopic`
* **SasKey**: beschikbaar onder **accessKey** van uw onderwerp. Gebruik **key1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Event grid-onderwerp maken aan de rand

1. Maak topic3.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Voer de volgende opdracht uit om het onderwerp te maken. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Voer de volgende opdracht uit om het onderwerp te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Event Grid abonnement maken aan de rand

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription3.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > De **endpointUrl** geeft aan dat de URL van het event grid onderwerp in de Cloud. De **sasKey** verwijst naar Event grid de sleutel van het Cloud onderwerp. De waarde in **topic** wordt gebruikt om alle uitgaande gebeurtenissen te stempelen Event grid. Dit kan handig zijn wanneer u naar een onderwerp van een Event Grid domein boekt. Zie [gebeurtenis domeinen](../event-domains.md) voor meer informatie over het onderwerp van Event grid domein

    Bijvoorbeeld:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Voer de volgende opdracht uit om het abonnement te maken. De HTTP-status code van 200 OK moet worden geretourneerd.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Voer de volgende opdracht uit om het abonnement te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Een gebeurtenis aan de rand publiceren

1. Maak event3.jsmet de volgende inhoud. Zie [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. Voer de volgende opdracht uit:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>De rand gebeurtenis in de Cloud controleren

Raadpleeg de [zelf studie](../custom-event-quickstart-portal.md)voor meer informatie over het weer geven van gebeurtenissen die worden geleverd door het Cloud onderwerp.

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle bijbehorende abonnementen te verwijderen

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Verwijder ook het onderwerp en de abonnementen die in de Cloud (Azure Event Grid) zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een gebeurtenis op de rand gepubliceerd en doorgestuurd naar Event Grid in de Azure-Cloud. Nu u weet welke basis stappen u kunt door sturen naar Event Grid in de Cloud:

* Zie [probleemoplossings gids voor informatie](troubleshoot.md)over het oplossen van problemen met het gebruik van Azure Event Grid op IOT Edge.
* Door sturen van gebeurtenissen naar IoTHub door deze [zelf studie](forward-events-iothub.md) te volgen
* Door sturen van gebeurtenissen naar webhook in de Cloud door deze [zelf studie](pub-sub-events-webhook-cloud.md) te volgen
* [Onderwerpen en abonnementen bewaken aan de rand](monitor-topics-subscriptions.md)
