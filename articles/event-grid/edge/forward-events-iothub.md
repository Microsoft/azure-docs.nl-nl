---
title: Event Grid gebeurtenissen door sturen naar IoTHub-Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid gebeurtenissen door sturen naar IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171547"
---
# <a name="tutorial-forward-events-to-iothub"></a>Zelf studie: gebeurtenissen door sturen naar IoTHub

In dit artikel worden alle stappen beschreven die nodig zijn om Event Grid-gebeurtenissen door te sturen naar andere IoT Edge modules, IoTHub met routes. U kunt dit het beste doen om de volgende redenen:

* Blijf bestaande investeringen gebruiken die al aanwezig zijn met de route ring van edgeHub
* Stuur liever alleen alle gebeurtenissen vanaf een apparaat via IoT Hub

Voor het volt ooien van deze zelf studie moet u de volgende concepten begrijpen:

- [Event Grid concepten](concepts.md)
- [IoT Edge hub](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Vereisten 
Als u deze zelf studie wilt volt ooien, hebt u het volgende nodig:

* **Azure-abonnement** : Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IOT hub en IOT edge apparaat** : Volg de stappen in de Quick start voor [Linux](../../iot-edge/quickstart-linux.md) -of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Onderwerp maken

Als uitgever van een gebeurtenis moet u een event grid-onderwerp maken. Het onderwerp verwijst naar een eind punt waarnaar uitgevers gebeurtenissen vervolgens naar kunnen verzenden.

1. Maak topic4.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Voer de volgende opdracht uit om het onderwerp te maken. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Voer de volgende opdracht uit om het onderwerp te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Gebeurtenisabonnement maken

Abonnees kunnen zich registreren voor gebeurtenissen die naar een onderwerp worden gepubliceerd. Als u een gebeurtenis wilt ontvangen, moet u een event grid-abonnement maken op een onderwerp van belang.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription4.jsmet de onderstaande inhoud. Raadpleeg onze [API-documentatie](api.md) voor meer informatie over de payload.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > `endpointType`Hiermee geeft u op dat de abonnee is `edgeHub` . `outputName`Hiermee geeft u de uitvoer op waarop de Event grid-module gebeurtenissen stuurt die overeenkomen met dit abonnement op edgeHub. Bijvoorbeeld: gebeurtenissen die overeenkomen met het bovenstaande abonnement, worden geschreven naar `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. Voer de volgende opdracht uit om het abonnement te maken. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Voer de volgende opdracht uit om het abonnement te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Een Edge hub-route instellen

Werk de route van de Edge hub bij om de gebeurtenissen van het gebeurtenis abonnement door te sturen naar IoTHub als volgt:

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com)
1. Navigeer naar het **IOT hub**.
1. **IOT Edge** selecteren in het menu
1. Selecteer de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer **modules instellen**.
1. Selecteer **volgende** en naar het gedeelte routes.
1. Voeg in de routes een nieuwe route toe

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Bijvoorbeeld:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Met de bovenstaande route worden alle gebeurtenissen die overeenkomen voor dit abonnement, doorgestuurd naar de IoT-hub. U kunt de routerings functies van de [Edge hub](../../iot-edge/module-composition.md) gebruiken om de Event grid-gebeurtenissen te filteren op andere IOT Edge modules.

## <a name="setup-iot-hub-route"></a>IoT Hub route instellen

Raadpleeg de [zelf studie over IOT hub route ring](../../iot-hub/tutorial-routing.md) voor het instellen van een route van de IOT-hub, zodat u gebeurtenissen kunt weer geven die zijn doorgestuurd vanuit de module Event grid. Gebruiken `true` voor de query om de zelf studie eenvoudig te laten blijven.  



## <a name="publish-an-event"></a>Een gebeurtenis publiceren

1. Maak event4.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Voer de volgende opdracht uit om de gebeurtenis te publiceren:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Gebeurtenis levering verifiëren

Raadpleeg de [zelf studie over IOT hub route ring](../../iot-hub/tutorial-routing.md) voor de stappen om de gebeurtenissen weer te geven.

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle bijbehorende abonnementen aan de rand te verwijderen:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Verwijder alle resources die zijn gemaakt bij het instellen van IoTHub-route ring in de Cloud.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een event grid-onderwerp, Edge hub-abonnement en gepubliceerde gebeurtenissen gemaakt. Nu u weet wat de basis stappen zijn om door te sturen naar een Edge hub, raadpleegt u de volgende artikelen:

* Zie [probleemoplossings gids voor informatie](troubleshoot.md)over het oplossen van problemen met het gebruik van Azure Event Grid op IOT Edge.
* [Edge hub](../../iot-edge/module-composition.md) -route filters gebruiken om gebeurtenissen te partitioneren
* Persistentie van Event Grid module instellen in [Linux](persist-state-linux.md) of [Windows](persist-state-windows.md)
* Volg de [documentatie](configure-client-auth.md) voor het configureren van client verificatie
* Door sturen van gebeurtenissen naar Azure Event Grid in de Cloud door deze [zelf studie](forward-events-event-grid-cloud.md) te volgen
* [Onderwerpen en abonnementen bewaken aan de rand](monitor-topics-subscriptions.md)