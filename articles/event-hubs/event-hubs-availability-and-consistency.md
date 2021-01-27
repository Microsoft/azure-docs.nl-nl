---
title: Beschik baarheid en consistentie-Azure Event Hubs | Microsoft Docs
description: De maximale hoeveelheid Beschik baarheid en consistentie bieden met Azure Event Hubs met behulp van partities.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882192"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Beschikbaarheid en consistentie in Event Hubs
Dit artikel bevat informatie over de beschik baarheid en consistentie die worden ondersteund door Azure Event Hubs. 

## <a name="availability"></a>Beschikbaarheid
Azure Event Hubs verspreidt het risico op onherstelbare storingen van afzonderlijke computers of zelfs volledige racks voor clusters die meerdere fout domeinen binnen een Data Center omvatten. Het implementeert transparante detectie van fouten en failover-mechanismen, zodat de service binnen de gegarandeerde service niveaus blijft werken en normaal gesp roken zonder merk bare onderbrekingen wanneer dergelijke fouten optreden. Als een Event Hubs naam ruimte is gemaakt met de optie ingeschakeld voor [beschikbaarheids zones](../availability-zones/az-overview.md), wordt het uitval risico verder verdeeld over drie fysiek gescheiden faciliteiten en heeft de service voldoende capaciteits reserves om direct te kunnen omgaan met het volledige, onherstelbaar verlies van de volledige faciliteit. Zie [Azure Event hubs-geo-nood herstel](event-hubs-geo-dr.md)voor meer informatie.

Wanneer een client toepassing gebeurtenissen naar een Event Hub verzendt, worden gebeurtenissen automatisch gedistribueerd tussen partities in uw Event Hub. Als een partitie om een of andere reden niet beschikbaar is, worden gebeurtenissen verdeeld over de resterende partities. Dit gedrag zorgt voor de grootste hoeveelheid tijd. Voor use-cases waarvoor de maximale hoeveelheid tijd is vereist, is dit model in plaats van het verzenden van gebeurtenissen naar een specifieke partitie. Zie [Partities](event-hubs-scalability.md#partitions) voor meer informatie.

## <a name="consistency"></a>Consistentie
In sommige scenario's kan de volg orde van gebeurtenissen belang rijk zijn. Het is bijvoorbeeld mogelijk dat u wilt dat uw back-end-systeem een update opdracht verwerkt vóór een opdracht verwijderen. In dit scenario verzendt een client toepassing gebeurtenissen naar een specifieke partitie, zodat de volg orde wordt behouden. Wanneer een consumenten toepassing deze gebeurtenissen van de partitie verbruikt, worden deze in volg orde gelezen. 

Houd er bij deze configuratie rekening mee dat als de specifieke partitie waarnaar u wilt verzenden niet beschikbaar is, er een fout bericht wordt weer gegeven. Als u geen affiniteit hebt voor een enkele partitie, stuurt de Event Hubs-service uw gebeurtenis naar de volgende beschik bare partitie.

Een mogelijke oplossing om te zorgen voor bestellingen, terwijl ook de maximale tijd wordt gemaximaliseerd, is het verzamelen van gebeurtenissen als onderdeel van de toepassing voor het verwerken van gebeurtenissen. De eenvoudigste manier om dit te bereiken is door uw gebeurtenis te stem pelen met een aangepaste volgorde nummer eigenschap.

In dit scenario verzendt de producer-client gebeurtenissen naar een van de beschik bare partities in uw Event Hub en stelt het overeenkomstige Volg nummer van uw toepassing in. Voor deze oplossing moet de status worden behouden door de verwerkings toepassing, maar geeft uw afzenders een eind punt dat waarschijnlijk beschikbaar is.

## <a name="appendix"></a>Bijlage

### <a name="net-examples"></a>.NET-voor beelden

#### <a name="send-events-to-a-specific-partition"></a>Gebeurtenissen verzenden naar een specifieke partitie
Stel de partitie sleutel voor een gebeurtenis in of gebruik een `PartitionSender` object (als u de oude bibliotheek van micro soft. Azure. Messa ging gebruikt) om alleen gebeurtenissen naar een bepaalde partitie te verzenden. Dit zorgt ervoor dat wanneer deze gebeurtenissen worden gelezen uit de partitie, ze in de juiste volg orde worden gelezen. 

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

### <a name="set-a-sequence-number"></a>Een Volg nummer instellen
In het volgende voor beeld wordt uw gebeurtenis stem pels met een aangepaste Sequence Number-eigenschap. 

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
