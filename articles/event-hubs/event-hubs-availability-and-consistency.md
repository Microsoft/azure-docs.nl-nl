---
title: Beschik baarheid en consistentie-Azure Event Hubs | Microsoft Docs
description: De maximale hoeveelheid Beschik baarheid en consistentie bieden met Azure Event Hubs met behulp van partities.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6005a51314cff19883fc2a07e4810bd24eb94b24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600952"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Beschikbaarheid en consistentie in Event Hubs
Dit artikel bevat informatie over de beschik baarheid en consistentie die worden ondersteund door Azure Event Hubs. 

## <a name="availability"></a>Beschikbaarheid
Azure Event Hubs verspreidt het risico op onherstelbare storingen van afzonderlijke computers of zelfs volledige racks voor clusters die meerdere fout domeinen binnen een Data Center omvatten. Het implementeert transparante detectie van fouten en failover-mechanismen, zodat de service binnen de gegarandeerde service niveaus blijft werken en normaal gesp roken zonder merk bare onderbrekingen wanneer dergelijke fouten optreden. 

Als er een Event Hubs naam ruimte is gemaakt met [beschikbaarheids zones](../availability-zones/az-overview.md) ingeschakeld, wordt het uitval risico verder verdeeld over drie fysiek gescheiden faciliteiten en is de service voldoende capaciteits reserves om direct te kunnen omgaan met het volledige, onherstelbare verlies van de volledige faciliteit. Zie [Azure Event hubs-geo-nood herstel](event-hubs-geo-dr.md)voor meer informatie.

Wanneer een client toepassing gebeurtenissen naar een Event Hub verzendt zonder een partitie op te geven, worden gebeurtenissen automatisch gedistribueerd tussen partities in uw Event Hub. Als een partitie om een of andere reden niet beschikbaar is, worden gebeurtenissen verdeeld over de resterende partities. Dit gedrag zorgt voor de grootste hoeveelheid tijd. Voor use-cases waarvoor de maximale hoeveelheid tijd is vereist, is dit model in plaats van het verzenden van gebeurtenissen naar een specifieke partitie. 

## <a name="consistency"></a>Consistentie
In sommige scenario's kan de volg orde van gebeurtenissen belang rijk zijn. Het is bijvoorbeeld mogelijk dat u wilt dat uw back-end-systeem een update opdracht verwerkt vóór een opdracht verwijderen. In dit scenario verzendt een client toepassing gebeurtenissen naar een specifieke partitie, zodat de volg orde wordt behouden. Wanneer een consumenten toepassing deze gebeurtenissen van de partitie verbruikt, worden deze in volg orde gelezen. 

Houd er bij deze configuratie rekening mee dat als de specifieke partitie waarnaar u wilt verzenden niet beschikbaar is, er een fout bericht wordt weer gegeven. Als u geen affiniteit hebt voor een enkele partitie, stuurt de Event Hubs-service uw gebeurtenis naar de volgende beschik bare partitie.

Als hoge Beschik baarheid het belangrijkst is, hoeft u daarom niet te richten op een specifieke partitie (met behulp van partitie-ID/sleutel). Door gebruik te maken van partitie-ID/sleutel, wordt de beschik baarheid van een Event Hub op partitie niveau gedowngraded. In dit scenario maakt u een expliciete keuze tussen Beschik baarheid (geen partitie-ID/sleutel) en consistentie (het vastmaken van gebeurtenissen aan een specifieke partitie). Zie [partities](event-hubs-features.md#partitions)voor gedetailleerde informatie over partities in Event hubs.

## <a name="appendix"></a>Bijlage

### <a name="send-events-without-specifying-a-partition"></a>Gebeurtenissen verzenden zonder een partitie op te geven
We raden aan om gebeurtenissen te verzenden naar een Event Hub zonder partitie gegevens in te stellen, zodat de Event Hubs-service de belasting van alle partities kan verdelen. Zie de volgende Quick starts voor meer informatie over hoe u dit doet in verschillende programmeer talen. 

- [Gebeurtenissen verzenden met .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Gebeurtenissen verzenden met Java](event-hubs-java-get-started-send.md)
- [Gebeurtenissen verzenden met Java script](event-hubs-python-get-started-send.md)
- [Gebeurtenissen verzenden met behulp van Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Gebeurtenissen verzenden naar een specifieke partitie
In deze sectie leert u hoe u gebeurtenissen naar een specifieke partitie kunt verzenden met behulp van verschillende programmeer talen. 

### <a name="net"></a>[.NET](#tab/dotnet)
Als u gebeurtenissen naar een specifieke partitie wilt verzenden, maakt u de batch met de methode [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) door de-of-CreateBatchOptions op te geven `PartitionId` `PartitionKey` . [](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions) Met de volgende code wordt een batch gebeurtenissen naar een specifieke partitie verzonden door een partitie sleutel op te geven. Event Hubs zorgt ervoor dat alle gebeurtenissen die een partitie sleutel waarde delen samen worden opgeslagen en worden geleverd in volg orde van aankomst.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

U kunt ook de methode [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) gebruiken door **PartitionId** of **PartitionKey** op te geven in [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Als u gebeurtenissen naar een specifieke partitie wilt verzenden, maakt u de batch met behulp van de methode [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) door de **partitie-id** of de **partitie sleutel** op te geven in [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). Met de volgende code wordt een batch gebeurtenissen naar een specifieke partitie verzonden door een partitie sleutel op te geven. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

U kunt ook de methode [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) gebruiken door de **partitie-id** of **partitie sleutel** op te geven in [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Als u gebeurtenissen wilt verzenden naar een specifieke partitie, [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) geeft u de of de op wanneer u een batch maakt met behulp van de-methode `partition_id` `partition_key` . Gebruik vervolgens de- [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) methode om de batch te verzenden naar de partitie van de Event hub. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

U kunt ook de methode [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) gebruiken door waarden voor `partition_id` or- `partition_key` para meters op te geven.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Als u gebeurtenissen naar een specifieke partitie wilt verzenden, [maakt u een batch](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) met het object [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) door de te gebruiken `partitionId` `partitionKey` . Vervolgens verzendt u de batch naar het Event Hub met behulp van de methode [EventHubProducerClient. methode sendbatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) . 

Zie het volgende voorbeeld

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

U kunt ook de methode [EventHubProducerClient. methode sendbatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) gebruiken door de **partitie-id** of **partitie sleutel** op te geven in [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

- [Overzicht van Event Hubs-service](./event-hubs-about.md)
- [Event Hubs-terminologie](event-hubs-features.md)
