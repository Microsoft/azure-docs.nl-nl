---
title: Beschik baarheid en consistentie-Azure Event Hubs | Microsoft Docs
description: De maximale hoeveelheid Beschik baarheid en consistentie bieden met Azure Event Hubs met behulp van partities.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b97d76f29ee8b7e44373c865baa09ba5ea4dd23
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631916"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Beschikbaarheid en consistentie in Event Hubs

## <a name="overview"></a>Overzicht
Azure Event Hubs maakt gebruik van een [partitie model](event-hubs-scalability.md#partitions) om Beschik baarheid en parallel Lise ring binnen één event hub te verbeteren. Als een Event Hub bijvoorbeeld vier partities heeft en een van deze partities wordt verplaatst van de ene server naar de andere in een taak verdeling, kunt u nog steeds vanuit drie andere partities verzenden en ontvangen. Daarnaast kunt u met meer partities meer gelijktijdige lezers uw gegevens laten verwerken, waardoor uw geaggregeerde door Voer wordt verbeterd. Meer informatie over de gevolgen van het partitioneren en best Ellen in een gedistribueerd systeem is een essentieel aspect van het oplossings ontwerp.

Zie de [Cap theorema](https://en.wikipedia.org/wiki/CAP_theorem), ook wel bekend als theorema van Brewer, om de afweging tussen volg orde en beschik baarheid te verklaren. In deze theorema wordt de keuze beschreven tussen consistentie, Beschik baarheid en partitie tolerantie. Dit geeft aan dat voor de systemen die door netwerk zijn gepartitioneerd, altijd de consistentie en beschik baarheid worden verzorgd.

Brewer van theorema definieert consistentie en beschik baarheid als volgt:
* Partitie tolerantie: de mogelijkheid van een systeem voor gegevens verwerking om gegevens te blijven verwerken, zelfs als er een partitie fout optreedt.
* Beschik baarheid: een niet-mislukt knoop punt retourneert binnen een redelijke hoeveelheid tijd (zonder fouten of time-outs) een redelijke reactie.
* Consistentie: een lees bewerking is gegarandeerd de meest recente schrijf bewerking voor een bepaalde client te retour neren.

## <a name="partition-tolerance"></a>Partitie tolerantie
Event Hubs is gebaseerd op een gepartitioneerd gegevens model. U kunt het aantal partities in uw Event Hub configureren tijdens de installatie, maar u kunt deze waarde later niet wijzigen. Omdat u partities moet gebruiken met Event Hubs, dient u een beslissing te nemen over de beschik baarheid en consistentie voor uw toepassing.

## <a name="availability"></a>Beschikbaarheid
De eenvoudigste manier om aan de slag te gaan met Event Hubs is het standaard gedrag te gebruiken. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messa ging. Event hubs (5.0.0 of hoger)](#tab/latest)
Als u een nieuw **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient)** -object maakt en de methode **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync)** gebruikt, worden uw gebeurtenissen automatisch gedistribueerd tussen partities in uw event hub. Dit gedrag zorgt voor de grootste hoeveelheid tijd.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Micro soft. Azure. Event hubs (4.1.0 of eerder)](#tab/old)
Als u een nieuw **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** -object maakt en de **[Verzend](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** methode gebruikt, worden uw gebeurtenissen automatisch gedistribueerd tussen partities in uw event hub. Dit gedrag zorgt voor de grootste hoeveelheid tijd.

---

Voor use-cases waarvoor de maximale hoeveelheid tijd is vereist, heeft dit model de voor keur.

## <a name="consistency"></a>Consistentie
In sommige scenario's kan de volg orde van gebeurtenissen belang rijk zijn. Het is bijvoorbeeld mogelijk dat u wilt dat uw back-end-systeem een update opdracht verwerkt vóór een opdracht verwijderen. In dit geval kunt u de partitie sleutel instellen voor een gebeurtenis of een `PartitionSender` object gebruiken (als u de oude bibliotheek van micro soft. Azure. Messa ging gebruikt) om alleen gebeurtenissen naar een bepaalde partitie te verzenden. Dit zorgt ervoor dat wanneer deze gebeurtenissen worden gelezen uit de partitie, ze in de juiste volg orde worden gelezen. 

Zie [code migreren van PartitionSender naar EventHubProducerClient voor het publiceren van gebeurtenissen naar een partitie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition)als u gebruikmaakt van de nieuwere **Azure. Messa ging. Event hubs** -bibliotheek.

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messa ging. Event hubs (5.0.0 of hoger)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Micro soft. Azure. Event hubs (4.1.0 of eerder)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

Houd er bij deze configuratie rekening mee dat als de specifieke partitie waarnaar u wilt verzenden niet beschikbaar is, er een fout bericht wordt weer gegeven. Als u geen affiniteit hebt voor een enkele partitie, stuurt de Event Hubs-service uw gebeurtenis naar de volgende beschik bare partitie.

Een mogelijke oplossing om te zorgen voor bestellingen, terwijl ook de maximale tijd wordt gemaximaliseerd, is het verzamelen van gebeurtenissen als onderdeel van de toepassing voor het verwerken van gebeurtenissen. De eenvoudigste manier om dit te bereiken is door uw gebeurtenis te stem pelen met een aangepaste volgorde nummer eigenschap. In de volgende code ziet u een voorbeeld:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messa ging. Event hubs (5.0.0 of hoger)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Micro soft. Azure. Event hubs (4.1.0 of eerder)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

In dit voor beeld wordt uw gebeurtenis verzonden naar een van de beschik bare partities in uw Event Hub en wordt het overeenkomstige Volg nummer van uw toepassing ingesteld. Voor deze oplossing moet de status worden behouden door de verwerkings toepassing, maar geeft uw afzenders een eind punt dat waarschijnlijk beschikbaar is.

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van Event Hubs-service](./event-hubs-about.md)
* [Een Event Hub maken](event-hubs-create.md)
