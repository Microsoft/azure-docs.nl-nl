---
title: Publiceren, abonneren op gebeurtenissen in Cloud-Azure Event Grid IoT Edge | Microsoft Docs
description: Publiceren, abonneren op gebeurtenissen in de Cloud met behulp van een webhook met Event Grid op IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ec7ecb77d37ed1cdf1d13aa7191f5d50e0008c20
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790796"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Zelf studie: publiceren, abonneren op gebeurtenissen in de Cloud

In dit artikel worden alle stappen beschreven die nodig zijn voor het publiceren en abonneren op gebeurtenissen met behulp van Event Grid op IoT Edge. Deze zelf studie maakt gebruik van en Azure function als gebeurtenis-handler. Zie voor aanvullende doel typen [gebeurtenis-handlers](event-handlers.md).

Zie [Event grid concepten](concepts.md) om te begrijpen wat een event grid-onderwerp en-abonnement zijn voordat u verdergaat.

## <a name="prerequisites"></a>Vereisten 
Als u deze zelf studie wilt volt ooien, hebt u het volgende nodig:

* **Azure-abonnement** : Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IOT hub en IOT edge apparaat** : Volg de stappen in de Quick start voor [Linux](../../iot-edge/quickstart-linux.md) -of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Een Azure-functie maken in de Azure Portal

Volg de stappen die worden beschreven in de [zelf studie](../../azure-functions/functions-get-started.md) voor het maken van een Azure-functie. 

Vervang het code fragment door de volgende code:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Selecteer in de nieuwe functie rechtsboven **functie-URL ophalen** , Selecteer standaard (**functie toets**) en selecteer vervolgens **kopiëren**. Verderop in de zelf studie gebruikt u de waarde van de functie-URL.

> [!NOTE]
> Raadpleeg de [Azure functions](../../azure-functions/functions-overview.md) -documentatie voor meer voor beelden en zelf studies over het opnieuw handelen van gebeurtenissen met behulp van EventGrid-gebeurtenis triggers.

## <a name="create-a-topic"></a>Een onderwerp maken

Als uitgever van een gebeurtenis moet u een event grid-onderwerp maken. Onderwerp verwijst naar een eind punt waarnaar uitgevers gebeurtenissen kunnen verzenden.

1. Maak topic2.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Voer de volgende opdracht uit om het onderwerp te maken. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Voer de volgende opdracht uit om het onderwerp te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken

Abonnees kunnen zich registreren voor gebeurtenissen die naar een onderwerp worden gepubliceerd. Als u een gebeurtenis wilt ontvangen, moeten de abonnees een event grid-abonnement maken op een onderwerp van belang.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription2.jsmet de volgende inhoud. Raadpleeg onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > De **endpointType** geeft aan dat de abonnee een webhook is.  De **endpointUrl** geeft de URL aan waar de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met de Azure function-voor beeld die u eerder hebt ingesteld.
2. Voer de volgende opdracht uit om het abonnement te maken. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Voer de volgende opdracht uit om het abonnement te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Een gebeurtenis publiceren

1. Maak event2.jsmet de volgende inhoud. Raadpleeg onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Voer de volgende opdracht uit om de gebeurtenis te publiceren

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Gebeurtenis levering verifiëren

U kunt de gebeurtenis weer geven die in de Azure Portal is geleverd met de optie **monitor** van uw functie.

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle bijbehorende abonnementen te verwijderen

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Verwijder de Azure-functie die in de Azure Portal is gemaakt.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een event grid-onderwerp,-abonnement en-gepubliceerde gebeurtenissen gemaakt. Nu u de basis stappen kent, raadpleegt u de volgende artikelen:

* Zie [probleemoplossings gids voor informatie](troubleshoot.md)over het oplossen van problemen met het gebruik van Azure Event Grid op IOT Edge.
* Een abonnement met [filters](advanced-filtering.md)maken/bijwerken.
* Persistentie van Event Grid module instellen in [Linux](persist-state-linux.md) of [Windows](persist-state-windows.md)
* Volg de [documentatie](configure-client-auth.md) voor het configureren van client verificatie
* Door sturen van gebeurtenissen naar Azure Event Grid in de Cloud door deze [zelf studie](forward-events-event-grid-cloud.md) te volgen
* [Onderwerpen en abonnementen bewaken aan de rand](monitor-topics-subscriptions.md)