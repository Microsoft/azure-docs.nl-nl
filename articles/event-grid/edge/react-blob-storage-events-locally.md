---
title: Reageren op gebeurtenissen van Blob Storage-module-Azure Event Grid IoT Edge | Microsoft Docs
description: Reageren op gebeurtenissen van Blob Storage-module
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005058"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Zelf studie: reageren op Blob Storage gebeurtenissen in IoT Edge (preview-versie)
Dit artikel laat u zien hoe u de Azure Blob Storage kunt implementeren in IoT-module, die als Event Grid Publisher kan fungeren voor het verzenden van gebeurtenissen bij het maken van een BLOB en het verwijderen van een BLOB naar Event Grid.  

Zie voor een overzicht van de Azure-Blob Storage op IoT Edge [azure Blob Storage op IOT Edge](../../iot-edge/how-to-store-data-blob.md) en de bijbehorende functies.

> [!WARNING]
> Azure Blob Storage op IoT Edge integratie met Event Grid is in Preview

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

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
    > In deze zelf studie leert u hoe u de Event Grid-module implementeert om HTTP/HTTPs-aanvragen toe te staan. client verificatie is uitgeschakeld. Voor werk belastingen wordt u aangeraden alleen HTTPs-aanvragen en abonnees in te scha kelen met client verificatie ingeschakeld. Zie [beveiliging en verificatie](security-authentication.md)voor meer informatie over het veilig configureren van Event grid module.
    

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
1. Ga door naar de volgende sectie om de Azure Blob Storage-module toe te voegen

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob Storage-module implementeren

In deze sectie wordt beschreven hoe u de Azure Blob Storage-module implementeert, die zou fungeren als Event Grid Publisher-publicatie-BLOB gemaakt en verwijderde gebeurtenissen.

### <a name="add-modules"></a>Modules toevoegen

1. Selecteer in de sectie **implementatie modules** de optie **toevoegen**
2. Selecteer **IOT Edge module** uit de typen modules in de vervolg keuzelijst.
3. Geef de naam, de afbeelding en de opties voor het maken van de container op van de container:

   * **Naam**: azureblobstorageoniotedge
   * **Afbeeldings-URI**: mcr.Microsoft.com/Azure-Blob-Storage:Latest
   * **Opties** voor het maken van containers:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - De module Blob Storage kan gebeurtenissen met zowel HTTPS als HTTP publiceren. 
   > - Als u de verificatie op basis van de client voor EventGrid hebt ingeschakeld, moet u ervoor zorgen dat u de waarde van EVENTGRID_ENDPOINT bijwerkt om HTTPS toe te staan, zoals: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` .
   > - U kunt ook een andere omgevings variabele toevoegen `AllowUnknownCertificateAuthority=true` aan de bovenstaande JSON. Bij het praten met EventGrid via HTTPS kan met **AllowUnknownCertificateAuthority** de opslag module zelfondertekende EventGrid-server certificaten vertrouwen.

4. Werk de JSON bij die u hebt gekopieerd met de volgende gegevens:

   - Vervang door `<your storage account name>` een naam die u kunt onthouden. Account namen moeten 3 tot 24 tekens lang zijn, met kleine letters en cijfers. Geen spaties.

   - Vervang door `<your storage account key>` een base64-sleutel van 64-bytes. U kunt een sleutel met hulpprogram ma's zoals [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64)genereren. U gebruikt deze referenties om toegang te krijgen tot de Blob-opslag van andere modules.

   - Vervang door `<event grid module name>` de naam van uw event grid-module.
   - Vervangen door `<storage mount>` het besturings systeem van de container.
     - Voor Linux-containers, **mijn volume:/blobroot**
     - Voor Windows-containers is **mijn volume: C:/BlobRoot**

5. Klik op **Opslaan**.
6. Klik op **volgende** om door te gaan naar de sectie routes

    > [!NOTE]
    > Als u een Azure VM als edge-apparaat gebruikt, voegt u een regel voor binnenkomende poort toe om binnenkomend verkeer toe te staan op de host poorten die in deze zelf studie worden gebruikt: 4438, 5888, 8080 en 11002. Zie [poorten openen voor een virtuele machine](../../virtual-machines/windows/nsg-quickstart-portal.md)voor instructies over het toevoegen van de regel.

### <a name="setup-routes"></a>Installatie routes

Behoud de standaard routes en selecteer **volgende** om door te gaan naar de sectie beoordeling

### <a name="review-deployment"></a>Implementatie controleren

1. In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige sectie. Controleer of de volgende vier modules worden weer geven: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **Subscriber** en **azureblobstorageoniotedge** die allemaal worden geïmplementeerd.
2. Controleer uw implementatie gegevens en selecteer vervolgens **verzenden**.

## <a name="verify-your-deployment"></a>Uw implementatie verifiëren

1. Nadat u de implementatie hebt verzonden, keert u terug naar de pagina IoT Edge van uw IoT-hub.
2. Selecteer het **IOT edge apparaat** waarop de implementatie is gericht om de details ervan te openen.
3. Controleer in de details van het apparaat of de eventgridmodule-, Subscriber-en azureblobstorageoniotedge-modules worden weer gegeven **in de implementatie** en worden **gerapporteerd door het apparaat**.

   Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>BlobCreated-en BlobDeleted-gebeurtenissen publiceren

1. Deze module maakt automatisch onderwerp **MicrosoftStorage**. Controleer of deze bestaat
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Voorbeelduitvoer:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Als de client verificatie is ingeschakeld via SAS-sleutel, moet de eerder opgegeven SAS-sleutel worden toegevoegd als een header voor de HTTPS-stroom. De krul aanvraag is daarom: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Voor de HTTPS-stroom geldt dat als de client verificatie via het certificaat is ingeschakeld, de krul aanvraag: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Abonnees kunnen zich registreren voor gebeurtenissen die naar een onderwerp worden gepubliceerd. Als u een gebeurtenis wilt ontvangen, moet u een Event Grid-abonnement maken voor **MicrosoftStorage** -onderwerp.
    1. Maak blobsubscription.jsmet de volgende inhoud. Zie onze [API-documentatie](api.md) voor meer informatie over de payload.

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
       > De eigenschap **endpointType** geeft aan dat de abonnee een **webhook** is.  De **endpointUrl** geeft de URL aan waar de abonnee naar gebeurtenissen luistert. Deze URL komt overeen met de Azure function-voor beeld die u eerder hebt geïmplementeerd.

    2. Voer de volgende opdracht uit om een abonnement voor het onderwerp te maken. Controleer of u de HTTP-status code ziet `200 OK` .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Als de client verificatie is ingeschakeld via SAS-sleutel, moet de eerder opgegeven SAS-sleutel worden toegevoegd als een header voor de HTTPS-stroom. De krul aanvraag is daarom: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Voor de HTTPS-stroom geldt dat als de client verificatie via het certificaat is ingeschakeld, de krul aanvraag:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Voer de volgende opdracht uit om het abonnement te controleren dat is gemaakt. De HTTP-status code van 200 OK moet worden geretourneerd.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Voorbeelduitvoer:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Als de client verificatie is ingeschakeld via SAS-sleutel, moet de eerder opgegeven SAS-sleutel worden toegevoegd als een header voor de HTTPS-stroom. De krul aanvraag is daarom: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Voor de HTTPS-stroom geldt dat als de client verificatie via het certificaat is ingeschakeld, de krul aanvraag: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) downloaden en [verbinding maken met uw lokale opslag](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Gebeurtenis levering verifiëren

### <a name="verify-blobcreated-event-delivery"></a>Levering van BlobCreated-gebeurtenis verifiëren

1. Upload bestanden als blok-blobs naar de lokale opslag van Azure Storage Explorer. de module publiceert automatisch Create-gebeurtenissen. 
2. Bekijk de logboeken van de abonnee voor het maken van een gebeurtenis. Volg de stappen om [de gebeurtenis levering te controleren](pub-sub-events-webhook-local.md#verify-event-delivery)

    Voorbeeld uitvoer:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Levering van BlobDeleted-gebeurtenis verifiëren

1. Blobs verwijderen uit de lokale opslag met behulp van Azure Storage Explorer en de module publiceert automatisch delete-gebeurtenissen. 
2. Bekijk de gebeurtenis logboeken voor het verwijderen van abonnees. Volg de stappen om [de gebeurtenis levering te controleren](pub-sub-events-webhook-local.md#verify-event-delivery)

    Voorbeeld uitvoer:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Gefeliciteerd U hebt de zelf studie voltooid. De volgende secties bevatten informatie over de gebeurtenis eigenschappen.

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Hier ziet u de lijst met ondersteunde gebeurtenis eigenschappen en hun typen en beschrijvingen. 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| Type | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| gegevens | object | Gebeurtenis gegevens van Blob-opslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| api | tekenreeks | De bewerking die de gebeurtenis heeft geactiveerd. Dit kan een van de volgende waarden zijn: <ul><li>De waarden die zijn toegestaan voor BlobCreated zijn: `PutBlob` en `PutBlockList`</li><li>BlobDeleted: toegestane waarden zijn `DeleteBlob` , `DeleteAfterUpload` en `AutoDelete` . <p>De `DeleteAfterUpload` gebeurtenis wordt gegenereerd wanneer de blob automatisch wordt verwijderd, omdat de gewenste eigenschap deleteAfterUpload is ingesteld op True. </p><p>`AutoDelete` Er wordt een gebeurtenis gegenereerd wanneer de blob automatisch wordt verwijderd, omdat de gewenste eigenschaps waarde voor deleteAfterMinutes is verlopen.</p></li></ul>|
| clientRequestId | tekenreeks | een aanvraag-ID van de client voor de bewerking van de opslag-API. Deze ID kan worden gebruikt om te correleren Azure Storage Diagnostische logboeken met behulp van het veld ' client-request-id ' in de logboeken, en kan worden verschaft in client aanvragen via de header ' x-MS-Client-Request-id '. Zie [logboek indeling](/rest/api/storageservices/storage-analytics-log-format)voor meer informatie. |
| requestId | tekenreeks | Door de service gegenereerde aanvraag-ID voor de bewerking van de opslag-API. Kan worden gebruikt om te correleren Azure Storage Diagnostische logboeken met behulp van het veld aanvraag-id-header in de logboeken en wordt geretourneerd van het initiëren van de API-aanroep in de header x-MS-Request-id. Zie de [logboek indeling](/rest/api/storageservices/storage-analytics-log-format). |
| eTag | tekenreeks | De waarde die u kunt gebruiken om bewerkingen voorwaardelijk uit te voeren. |
| Invoer | tekenreeks | Het opgegeven inhouds type voor de blob. |
| contentLength | geheel getal | De grootte van de BLOB in bytes. |
| blobType | tekenreeks | Het type blob. Geldige waarden zijn ' BlockBlob ' of ' PageBlob '. |
| url | tekenreeks | Het pad naar de blob. <br>Als de client gebruikmaakt van een BLOB-REST API, heeft de URL deze structuur: *\<storage-account-name\> . \<container-name\> / \<file-name\> blob.core.Windows.net/*. <br>Als de client een Data Lake Storage REST API gebruikt, heeft de URL deze structuur: *\<storage-account-name\> . DFS.core.Windows.net/ \<file-system-name\> / \<file-name\>*. |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen in de Blob Storage-documentatie:

- [Blob Storage gebeurtenissen filteren](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Aanbevolen procedures voor het gebruiken van Blob Storage gebeurtenissen](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

In deze zelf studie hebt u gebeurtenissen gepubliceerd door blobs in een Azure-Blob Storage te maken of verwijderen. Raadpleeg de andere zelf studies voor meer informatie over het door sturen van gebeurtenissen naar de Cloud (Azure Event hub of Azure IoT Hub): 

- [Gebeurtenissen doorsturen naar Azure Event Grid](forward-events-event-grid-cloud.md)
- [Gebeurtenissen doorsturen naar Azure IoT Hub](forward-events-iothub.md)
