---
title: .NET-programmeer handleiding-Azure Event Hubs (verouderd) | Microsoft Docs
description: Dit artikel bevat informatie over het schrijven van code voor Azure-Event Hubs met behulp van de Azure .NET SDK.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 46bd0c3c1488d6dd7afbae5e88e0b83f56654bb8
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131233"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET-programmeer handleiding voor Azure Event Hubs (verouderd micro soft. Azure. Event hubs-pakket)
In dit artikel worden enkele algemene scenario's beschreven voor het schrijven van code met behulp van Azure Event Hubs. Er wordt uitgegaan van een basisbegrip van Event Hubs. Zie het [Overzicht van Event Hubs](./event-hubs-about.md) voor een conceptueel overzicht van Event Hubs.

> [!WARNING]
> Deze hand leiding is voor het oude pakket **micro soft. Azure. Event hubs** . U wordt aangeraden uw code te [migreren](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) voor het meest recente [Azure. Messa ging. Event hubs](event-hubs-dotnet-standard-getstarted-send.md) -pakket.  


## <a name="event-publishers"></a>Gebeurtenisuitgevers

U verzendt gebeurtenissen naar een Event Hub via HTTP POST of via een AMQP 1,0-verbinding. De keuze die moet worden gebruikt en wanneer dit afhankelijk is van het specifieke scenario dat wordt beschreven. AMQP 1.0-verbindingen zijn brokered verbindingen in Service Bus. Ze zijn met name geschikt voor scenario‘s met vaak voorkomende hogere berichtvolumes en lagere latentievereisten, omdat ze een permanent berichtenkanaal bieden.

Wanneer de beheerde .NET-API‘s worden gebruikt, zijn de klassen [EventHubClient][] en [EventData][] de primaire constructs voor het publiceren van gegevens naar Event Hubs. [EventHubClient][] biedt het communicatie kanaal van AMQP waarover gebeurtenissen worden verzonden naar de Event hub. De klasse [Event Data][] vertegenwoordigt een gebeurtenis en wordt gebruikt om berichten te publiceren naar een event hub. Deze klasse omvat de hoofd tekst, sommige meta gegevens (eigenschappen) en header-informatie (SystemProperties) over de gebeurtenis. Andere eigenschappen worden toegevoegd aan het object [Event Data][] wanneer het wordt door gegeven via een event hub.

## <a name="get-started"></a>Aan de slag
De .NET-klassen die Event Hubs ondersteunen, zijn opgenomen in het [micro soft. Azure. Event hubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-pakket. U kunt installeren met behulp van de Visual Studio Solution Explorer of de [Package Manager-console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio. Hiervoor voert u de volgende opdracht uit in het venster voor de [Pakketbeheerconsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt de Azure Portal, Azure PowerShell of Azure CLI gebruiken om Event Hubs te maken. Zie [een event hubs naam ruimte maken en een event hub met behulp van de Azure Portal](event-hubs-create.md)voor meer informatie.

## <a name="create-an-event-hubs-client"></a>Een Event Hubs-client maken

De primaire klasse voor interactie met Event Hubs is [micro soft. Azure. Event hubs. EventHubClient][EventHubClient]. U kunt deze klasse instantiëren met behulp van de [API createfromconnectionstring](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) -methode, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Gebeurtenissen verzenden naar een Event Hub

U verzendt gebeurtenissen naar een Event Hub door een [EventHubClient][] -exemplaar te maken en dit asynchroon te verzenden via de methode [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) . Deze methode maakt gebruik van één [Event Data][] -exemplaar parameter en verzendt deze asynchroon naar een event hub.

## <a name="event-serialization"></a>Gebeurtenisserialisatie

De klasse [Event Data][] heeft [twee overbelaste constructors](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) die verschillende para meters, bytes of een byte matrix hebben die de nettolading van de gebeurtenis gegevens vertegenwoordigen. Als u JSON gebruikt met [EventData][], kunt u **Encoding.UTF8.GetBytes()** gebruiken om de bytematrix op te halen voor een met JSON gecodeerde tekenreeks. Bijvoorbeeld:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partitiesleutel

> [!NOTE]
> Als u niet bekend bent met partities, raadpleegt u [dit artikel](event-hubs-features.md#partitions). 

Bij het verzenden van gebeurtenis gegevens kunt u een waarde opgeven die wordt gehasht voor het produceren van een partitie toewijzing. U geeft de partitie op met behulp van de eigenschap [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) . De beslissing om partities te gebruiken, impliceert echter een keuze tussen Beschik baarheid en consistentie. 

### <a name="availability-considerations"></a>Beschikbaarheidsoverwegingen

Het gebruik van een partitie sleutel is optioneel en Overweeg zorgvuldig of u er een moet gebruiken. Als u geen partitie sleutel opgeeft bij het publiceren van een gebeurtenis, Event Hubs balanceert u de belasting tussen de partities. In veel gevallen is het gebruik van een partitie sleutel een goede keuze als de volg orde van de gebeurtenissen belang rijk is. Wanneer u een partitie sleutel gebruikt, zijn voor deze partities Beschik baarheid op één knoop punt vereist en kunnen er storingen optreden in de loop van de tijd. bijvoorbeeld wanneer reken knooppunten opnieuw worden opgestart en patch. Als u een partitie-ID instelt en deze partitie om een of andere reden niet meer beschikbaar is, mislukt de poging om toegang te krijgen tot de gegevens in die partitie. Als hoge Beschik baarheid het belangrijkst is, geeft u geen partitie sleutel op. In dat geval worden gebeurtenissen via een intern taakverdelings algoritme verzonden naar partities. In dit scenario maakt u een expliciete keuze tussen Beschik baarheid (geen partitie-ID) en consistentie (het vastmaken van gebeurtenissen aan een partitie-ID).

Een andere overweging is het verwerken van vertragingen bij het verwerken van gebeurtenissen. In sommige gevallen kan het beter zijn om gegevens te verwijderen en opnieuw te proberen dan om de verwerking te blijven uitvoeren. Dit kan leiden tot verdere downstream verwerkings vertragingen. Met een aandelen tikker is het echter beter te wachten op de volledige actuele gegevens, maar in een live chat-of VOIP-scenario kunt u in plaats daarvan snel de gegevens gebruiken, zelfs als deze niet volledig zijn.

Op basis van deze beschikbaarheids overwegingen kunt u in deze scenario's een van de volgende strategieën voor het afhandelen van fouten kiezen:

- Stoppen (lezen van Event Hubs stoppen totdat er dingen zijn hersteld)
- Drop (berichten zijn niet belang rijk, verwijder ze)
- Probeer het opnieuw (probeer de berichten opnieuw uit te voeren zoals u ziet)

Zie [Beschik baarheid en consistentie in Event hubs](event-hubs-availability-and-consistency.md)voor meer informatie en een bespreking over de wissel werking tussen Beschik baarheid en consistentie. 

## <a name="batch-event-send-operations"></a>Batchbewerkingen voor het verzenden van gebeurtenissen

Door gebeurtenissen in batches te verzenden, kunt u de door Voer verbeteren. U kunt de [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) -API gebruiken om een batch te maken waaraan gegevens objecten later kunnen worden toegevoegd voor een [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -aanroep.

Eén batch mag niet groter zijn dan de limiet van 1 MB van een gebeurtenis. Daarnaast maakt elk bericht in de batch gebruik van dezelfde uitgever-id. Het is de verantwoordelijkheid van de afzender om ervoor te zorgen dat de batch de maximale gebeurtenisgrootte niet overschrijdt. Als dit wel gebeurt, wordt aan clientzijde een **Verzendfout** gegenereerd. U kunt de Help-methode [EventHubClient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) gebruiken om ervoor te zorgen dat de batch niet meer dan 1 MB overschrijdt. U krijgt een leeg [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) van de [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) -API en vervolgens gebruikt u [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) om gebeurtenissen toe te voegen om de batch te maken. 

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchroon verzenden en op schaal verzenden

U verzendt gebeurtenissen asynchroon naar een Event Hub. Het per ongeluk verzenden van de frequentie verhoogt de snelheid waarmee een client gebeurtenissen kan verzenden. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) retourneert een [Task](/dotnet/api/system.threading.tasks.task?view=netcore-3.1) -object. U kunt de [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) -klasse op de client gebruiken om de opties voor de client opnieuw proberen te beheren.

## <a name="event-consumers"></a>Gebeurtenisconsumers
Met de klasse [EventProcessorHost][] worden gegevens uit Event Hubs verwerkt. Gebruik deze implementatie bij het bouwen van gebeurtenislezers op het .NET-platform. [EventProcessorHost][] biedt een thread-veilige, beveiligde runtimeomgeving met meerdere processen voor implementaties van gebeurtenisprocessors die ook beheer biedt van controlepunten en partitielease.

Als u de klasse [EventProcessorHost][] wilt gebruiken, kunt u [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementeren. Deze interface bevat vier methoden:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Als u gebeurtenis verwerking wilt starten, maakt u een exemplaar van [EventProcessorHost][], waarbij u de juiste para meters voor uw event hub opgeeft. Bijvoorbeeld:

> [!NOTE]
> EventProcessorHost en de bijbehorende klassen zijn opgenomen in het pakket **micro soft. Azure. Event hubs. processor** . Voeg het pakket toe aan uw Visual Studio-project door de instructies in [dit artikel](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) te volgen of door de volgende opdracht in het console venster van [Package Manager](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) te geven: `Install-Package Microsoft.Azure.EventHubs.Processor` .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Roep vervolgens [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) aan om uw [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementatie te registreren bij de runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Op dit moment probeert de host een lease te verkrijgen op elke partitie in de Event Hub met behulp van een ' Greedy-algoritme. Deze leases duren voor een bepaalde periode en moeten vervolgens worden verlengd. Zodra nieuwe knooppunten, in dit geval werkrolexemplaren, online komen, worden er reserveringen voor leases geplaatst. Later verschuift de werklast tussen de knooppunten wanneer elk knooppunt probeert om meer leases te verkrijgen.

![Gebeurtenisprocessorhost](./media/event-hubs-programming-guide/IC759863.png)

In de loop van de tijd komt er een evenwicht tot stand. Dankzij deze dynamische mogelijkheid kan er op consumers automatisch schalen op basis van CPU worden toegepast, zowel voor omhoog als voor omlaag schalen. Omdat Event Hubs geen direct concept van het aantal berichten bevat, is gemiddeld CPU-gebruik vaak het beste mechanisme voor het meten van back-end-of consumenten schalen. Als uitgevers meer gebeurtenissen publiceren dan consumers kunnen verwerken, kan de toename van het CPU-gebruik voor consumers worden gebruikt om het aantal werkrolexemplaren automatisch te schalen.

Met de klasse [EventProcessorHost][] wordt ook een op Azure Storage gebaseerd mechanisme van controlepunten geïmplementeerd. Hiermee wordt de offset opgeslagen op basis van partitie, zodat elke consumer kan bepalen wat het laatste controlepunt van de vorige consumer was. Terwijl partities via leases schakelen tussen knooppunten, maakt dit synchronisatiemechanisme de verschuiving van werklasten mogelijk.

## <a name="publisher-revocation"></a>Uitgever intrekken

Naast de geavanceerde runtime functies van Event processor host, kan de Event Hubs-service de intrekking van een [Uitgever](/rest/api/eventhub/revoke-publisher) inschakelen om te voor komen dat specifieke uitgevers een gebeurtenis verzenden naar een event hub. Deze functies zijn nuttig als er een token van een uitgever is aangetast of als een software-update ervoor zorgt dat deze niet op de juiste wijze werkt. In deze gevallen kan de uitgever-id, die onderdeel is van het bijbehorende SAS-token, worden geblokkeerd voor het uitgeven van gebeurtenissen.

> [!NOTE]
> Op dit moment wordt deze functie alleen door REST API ondersteund ([intrekken](/rest/api/eventhub/revoke-publisher)van de uitgever).


## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor meer informatie over Event Hubs-scenario‘s:

* [Event Hubs-API-overzicht](./event-hubs-samples.md)
* [Wat is Event Hubs](./event-hubs-about.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [API-verwijzing voor de gebeurtenisprocessorhost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[Event Data]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
