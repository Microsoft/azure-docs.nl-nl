---
title: Bewaking van Azure-IoT Hub bewerkingen (afgeschaft) | Microsoft Docs
description: Azure IoT Hub Operations monitoring gebruiken om de status van bewerkingen op uw IoT-hub in realtime te controleren.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408121"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Bewaking van IoT Hub bewerkingen (afgeschaft)

Met IoT Hub bewerkingen bewaken kunt u de status van de bewerkingen op uw IoT-hub in realtime bewaken. IoT Hub houdt gebeurtenissen in verschillende categorieën bewerkingen bij. U kunt ervoor kiezen gebeurtenissen te verzenden van een of meer categorieën naar een eind punt van uw IoT-hub voor verwerking. U kunt de gegevens controleren op fouten of complexere verwerking instellen op basis van gegevens patronen.

>[!NOTE]
>IoT Hub **bewerkings bewaking is afgeschaft en is op 10 maart 2019 verwijderd uit IOT hub**. Zie [IOT hub bewaken](monitor-iot-hub.md)voor het bewaken van de bewerkingen en de status van IOT hub. Zie [uw Azure IOT-oplossingen bewaken met Azure monitor en Azure resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)voor meer informatie over de tijd lijn van de afschaffing.

IoT Hub bewaken zes categorieën gebeurtenissen:

* Bewerkingen voor apparaat-id's
* Apparaattelemetrie
* Cloud-naar-apparaat-berichten
* Verbindingen
* Uploads van bestanden
* Berichtroutering

> [!IMPORTANT]
> IoT Hub Operations-bewaking garandeert geen betrouw bare of bestelde levering van gebeurtenissen. Afhankelijk van IoT Hub onderliggende infra structuur, kunnen sommige gebeurtenissen verloren gaan of in de juiste volg orde worden afgeleverd. Gebruik Operations-bewaking om waarschuwingen te genereren op basis van fout signalen zoals mislukte Verbindings pogingen, of verbroken verbindingen met hoge frequentie voor specifieke apparaten. U moet niet vertrouwen op gebeurtenissen voor bewerkingen bewaking om een consistente opslag voor de apparaatstatus te maken, bijvoorbeeld een status van een opgeslagen opslag of verbinding met een apparaat. 

## <a name="how-to-enable-operations-monitoring"></a>Operations-bewaking inschakelen

1. Maak een IoT-hub. In de hand leiding [aan de slag](quickstart-send-telemetry-dotnet.md) vindt u instructies voor het maken van een IOT-hub.

2. Open de Blade van uw IoT-hub. Klik vervolgens op **bewerkingen bewaking**.

    ![Configuratie van bewerkings bewaking openen in de portal](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Selecteer de bewakings categorieën die u wilt bewaken en klik vervolgens op **Opslaan**. De gebeurtenissen zijn beschikbaar voor het lezen van het eind punt dat compatibel is met Event hub, vermeld in **bewakings instellingen**. Het IoT Hub-eind punt wordt aangeroepen `messages/operationsmonitoringevents` .

    ![Bewaking van bewerkingen op uw IoT-hub configureren](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Als u **uitgebreide** bewaking selecteert voor de categorie **verbindingen** , worden er IOT hub extra diagnostische berichten gegenereerd. Voor alle andere categorieën wijzigt de instelling **uitgebreid** de hoeveelheid gegevens IOT hub in elk fout bericht bevat.

## <a name="event-categories-and-how-to-use-them"></a>Gebeurtenis categorieën en hoe u deze kunt gebruiken

Elke categorie voor bewerkingen bewaking houdt een ander type interactie met IoT Hub bij en elke bewakings categorie heeft een schema dat definieert hoe gebeurtenissen in die categorie worden gestructureerd.

### <a name="device-identity-operations"></a>Bewerkingen voor apparaat-id's

De categorie apparaat-id bewerkingen houdt fouten bij die optreden wanneer u probeert een vermelding te maken, bij te werken of te verwijderen in het id-REGI ster van uw IoT-hub. Het bijhouden van deze categorie is handig voor het inrichten van scenario's.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Apparaattelemetrie

De telemetrie-categorie van het apparaat houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de telemetrie-pijp lijn. Deze categorie bevat fouten die optreden bij het verzenden van telemetrie-gebeurtenissen (zoals het beperken) en het ontvangen van telemetrie-gebeurtenissen (zoals niet-geautoriseerde lezer). Deze categorie kan geen fouten ondervangen die worden veroorzaakt door code die op het apparaat wordt uitgevoerd.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Cloud-naar-apparaat-opdrachten

De categorie Cloud-naar-apparaat-opdrachten houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de Cloud-naar-apparaat-berichten pijplijn. Deze categorie bevat fouten die zich voordoen bij het verzenden van Cloud-naar-apparaat-berichten (zoals een ongeautoriseerde afzender), het ontvangen van Cloud-naar-apparaat-berichten (zoals het aantal leveringen is overschreden), en het ontvangen van feedback over Cloud-naar-apparaat-berichten (zoals feedback verlopen). Deze categorie onderschept geen fouten van een apparaat waardoor een Cloud-naar-apparaat-bericht niet goed wordt verwerkt als het Cloud-naar-apparaat-bericht is bezorgd.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Verbindingen

In de categorie verbindingen worden fouten bijgehouden die optreden wanneer apparaten verbinding maken met of de verbinding met een IoT-hub. Het bijhouden van deze categorie is handig voor het identificeren van niet-geautoriseerde Verbindings pogingen en voor het bijhouden wanneer een verbinding wordt verbroken voor apparaten op gebieden met een slechte connectiviteit.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Uploads van bestanden

De categorie bestand uploaden houdt fouten bij die zich op de IoT-hub voordoen en zijn gerelateerd aan de functionaliteit voor het uploaden van bestanden. Deze categorie omvat:

* Fouten die zich voordoen met de SAS-URI, zoals wanneer deze verlopen voordat een apparaat de hub van een voltooide upload ontvangt.

* Mislukte uploads die zijn gerapporteerd door het apparaat.

* Fouten die optreden wanneer een bestand niet wordt gevonden in de opslag tijdens het maken van IoT Hub meldings bericht.

Deze categorie kan geen fouten ondervangen die rechtstreeks optreden terwijl het apparaat een bestand naar de opslag uploadt.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Berichtroutering

In de categorie bericht routering worden fouten bijgehouden die optreden tijdens de bericht route-evaluatie en de eindpunt status, zoals wordt waargenomen door IoT Hub. Deze categorie bevat gebeurtenissen, zoals wanneer een regel resulteert in "niet-gedefinieerd", wanneer IoT Hub een eind punt markeert als Dead en eventuele andere fouten die zijn ontvangen van een eind punt. Deze categorie bevat geen specifieke fouten met betrekking tot de berichten zelf (zoals problemen met het beperken van apparaten), die worden gerapporteerd onder de categorie telemetrie van het apparaat.

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Verbinding maken met het bewakings eindpunt

Het bewakings eindpunt van uw IoT-hub is een event hub-compatibel eind punt. U kunt elk mechanisme gebruiken dat werkt met Event Hubs om bewakings berichten te lezen van dit eind punt. In het volgende voor beeld wordt een eenvoudige lezer gemaakt die niet geschikt is voor een implementatie met hoge door voer. Zie voor meer informatie over het verwerken van Event Hubs-berichten de zelfstudie [Aan de slag met Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Als u verbinding wilt maken met het bewakings eindpunt, hebt u een connection string en de naam van het eind punt nodig. De volgende stappen laten zien hoe u de vereiste waarden kunt vinden in de portal:

1. Navigeer in de portal naar de Blade IoT Hub resource.

2. Kies **bewerkingen bewaken** en noteer de **Event hub-compatibele naam** en **Event hub-compatibele eindpunt** waarden:

    ![Event hub-compatibele eindpunt waarden](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Kies **beleid voor gedeelde toegang** en kies vervolgens **service**. Noteer de waarde van de **primaire sleutel** :

    ![Primaire sleutel voor het beleid voor gedeelde toegang van services](./media/iot-hub-operations-monitoring/service-key.png)

Het volgende C#-code voorbeeld is afkomstig uit een Visual Studio **Windows Classic Desktop** C# console-app. Het **WindowsAzure. ServiceBus** NuGet-pakket is geïnstalleerd op het project.

* Vervang de tijdelijke aanduiding connection string door een connection string die gebruikmaakt van het **Event hub-compatibele eind punt** en de **primaire sleutel** waarden van de service die u eerder hebt genoteerd, zoals wordt weer gegeven in het volgende voor beeld:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Vervang de tijdelijke aanduiding voor bewakings eindpunt naam door de **Event hub-compatibele naam** waarde die u eerder hebt genoteerd.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van Azure Monitor om IoT Hub te bewaken:

* [IoT Hub bewaken](monitor-iot-hub.md)

* [Migreren van IoT Hub Operations-bewaking naar Azure Monitor](iot-hub-migrate-to-diagnostics-settings.md)