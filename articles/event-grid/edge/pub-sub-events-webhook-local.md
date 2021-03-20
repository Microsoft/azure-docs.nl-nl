---
title: Publiceren, abonneren op gebeurtenissen lokaal-Azure Event Grid IoT Edge | Microsoft Docs
description: Publiceer en Abonneer u op gebeurtenissen lokaal met behulp van webhook met Event Grid op IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171445"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Zelf studie: publiceren, lokaal abonneren op gebeurtenissen

Dit artikel begeleidt u bij de stappen die nodig zijn voor het publiceren en abonneren op gebeurtenissen met behulp van Event Grid op IoT Edge.

> [!NOTE]
> Zie [Event grid-concepten](concepts.md)voor meer informatie over Azure Event grid onderwerpen en abonnementen.

## <a name="prerequisites"></a>Vereisten 
Als u deze zelf studie wilt volt ooien, hebt u het volgende nodig:

* **Azure-abonnement** : Maak een [gratis account](https://azure.microsoft.com/free) als u er nog geen hebt. 
* **Azure IOT hub en IOT edge apparaat** : Volg de stappen in de Quick start voor [Linux](../../iot-edge/quickstart-linux.md) -of [Windows-apparaten](../../iot-edge/quickstart.md) als u er nog geen hebt.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge-module implementeren

Er zijn verschillende manieren om modules op een IoT Edge apparaat te implementeren en ze werken allemaal voor Azure Event Grid op IoT Edge. In dit artikel worden de stappen beschreven voor het implementeren van Event Grid op IoT Edge van de Azure Portal.

>[!NOTE]
> In deze zelf studie implementeert u de module Event Grid zonder persistentie. Dit betekent dat alle onderwerpen en abonnementen die u in deze zelf studie maakt, worden verwijderd wanneer u de module opnieuw implementeert. Raadpleeg de volgende artikelen voor meer informatie over het instellen van persistentie: [behoud de status in Linux](persist-state-linux.md) of [persistent in Windows](persist-state-windows.md). Voor werk belastingen wordt u aangeraden de Event Grid module met persistentie te installeren.


### <a name="select-your-iot-edge-device"></a>Uw IoT Edge-apparaat selecteren

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Ga naar uw IoT Hub.
1. Selecteer **IOT Edge** in het menu van het gedeelte **Automatic Device Management** . 
1. Klik op de ID van het doel apparaat in de lijst met apparaten
1. Selecteer **modules instellen**. Laat de pagina geopend. U gaat verder met de stappen in de volgende sectie.

### <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. De Azure Portal bevat een wizard die u helpt bij het maken van een implementatie manifest, in plaats van het JSON-document hand matig te bouwen.  Er zijn drie stappen: **modules toevoegen**, **routes opgeven** en de **implementatie controleren**.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer in de sectie **implementatie modules** de optie **toevoegen**
1. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
1. Geef de naam, de afbeelding en de opties voor het maken van de container van de container op:

   * **Naam**: eventgridmodule
   * **Afbeeldings-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opties** voor het maken van containers:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Klik op **Opslaan**.
 1. Ga verder met de volgende sectie om de Azure Event Grid Subscriber module toe te voegen voordat u ze samen implementeert.

    >[!IMPORTANT]
    > In deze zelf studie implementeert u de module Event Grid met client verificatie uitgeschakeld. Voor werk belastingen wordt u aangeraden de client verificatie in te scha kelen. Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over het veilig configureren van Event grid module.
    > 
    > Als u een virtuele machine van Azure als een edge-apparaat gebruikt, voegt u een regel voor binnenkomende poort toe om binnenkomend verkeer op poort 4438 toe te staan. Zie [poorten openen voor een virtuele machine](../../virtual-machines/windows/nsg-quickstart-portal.md)voor instructies over het toevoegen van de regel.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid Subscriber IoT Edge-module implementeren

In deze sectie wordt beschreven hoe u een andere IoT-module implementeert die als een gebeurtenis-handler zou fungeren waarmee gebeurtenissen kunnen worden geleverd.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer opnieuw **toevoegen** in de sectie **implementatie modules** . 
1. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
1. Geef de naam, de afbeelding en de opties voor het maken van de container op van de container:

   * **Naam**: abonnee
   * **Afbeeldings-URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opties** voor het maken van containers: geen
1. Klik op **Opslaan**.
1. Klik op **volgende** om door te gaan naar de sectie routes

 ### <a name="setup-routes"></a>Installatie routes

Behoud de standaard routes en selecteer **volgende** om door te gaan naar de sectie beoordeling

### <a name="submit-the-deployment-request"></a>De implementatie aanvraag verzenden

1. In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige sectie. Controleer of de modules: **eventgridmodule** en **Subscriber** in de JSON worden weer gegeven. 
1. Controleer uw implementatie gegevens en selecteer vervolgens **verzenden**. Nadat u de implementatie hebt verzonden, keert u terug naar de pagina **apparaat** .
1. Controleer in de **sectie modules** of zowel de **eventgrid** -als de **abonnee** modules worden weer gegeven. Controleer of de **opgegeven in-implementatie** en **gerapporteerd door** de kolom apparaat is ingesteld op **Ja**.

    Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.

## <a name="create-a-topic"></a>Een onderwerp maken

Als uitgever van een gebeurtenis moet u een event grid-onderwerp maken. In Azure Event Grid verwijst een onderwerp naar een eind punt waarnaar uitgevers gebeurtenissen naar kunnen verzenden.

1. Maak topic.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md)voor meer informatie over de payload.

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Voer de volgende opdracht uit om een event grid-onderwerp te maken. Controleer of u de HTTP-status code ziet `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Voer de volgende opdracht uit om het onderwerp te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Voorbeelduitvoer:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Een gebeurtenisabonnement maken

Abonnees kunnen zich registreren voor gebeurtenissen die naar een onderwerp worden gepubliceerd. Als u een gebeurtenis wilt ontvangen, moet u een Event Grid-abonnement maken voor een onderwerp van belang.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Maak subscription.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > De eigenschap **endpointType** geeft aan dat de abonnee een **webhook** is.  De **endpointUrl** geeft de URL aan waar de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met het voor beeld van Azure Subscriber dat u eerder hebt geïmplementeerd.
2. Voer de volgende opdracht uit om een abonnement voor het onderwerp te maken. Controleer of u de HTTP-status code ziet `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Voer de volgende opdracht uit om het abonnement te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Een gebeurtenis publiceren

1. Maak event.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md)voor meer informatie over de payload.

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Voer de volgende opdracht uit om een gebeurtenis te publiceren.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Gebeurtenis levering verifiëren

1. SSH of RDP in uw IoT Edge-VM.
1. Controleer de logboeken van de abonnee server:

    Voer de volgende opdracht uit in Windows:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Voer de volgende opdracht uit op Linux:

    ```sh
    sudo docker logs subscriber
    ```

    Voorbeelduitvoer:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Resources opruimen

* Voer de volgende opdracht uit om het onderwerp en alle bijbehorende abonnementen te verwijderen.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Verwijder de abonnee module van uw IoT Edge-apparaat.


## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een event grid-onderwerp,-abonnement en-gepubliceerde gebeurtenissen gemaakt. Nu u de basis stappen kent, raadpleegt u de volgende artikelen: 

- Zie [probleemoplossings gids voor informatie](troubleshoot.md)over het oplossen van problemen met het gebruik van Azure Event Grid op IOT Edge.
- Een abonnement met [filters](advanced-filtering.md)maken/bijwerken.
- Persistentie van Event Grid-module op [Linux](persist-state-linux.md) of [Windows](persist-state-windows.md) inschakelen
- Volg de [documentatie](configure-client-auth.md) voor het configureren van client verificatie
- Door sturen van gebeurtenissen naar Azure Functions in de Cloud door deze [zelf studie](pub-sub-events-webhook-cloud.md) te volgen
- [Reageren op Blob Storage gebeurtenissen op IoT Edge](react-blob-storage-events-locally.md)
- [Onderwerpen en abonnementen bewaken aan de rand](monitor-topics-subscriptions.md)

