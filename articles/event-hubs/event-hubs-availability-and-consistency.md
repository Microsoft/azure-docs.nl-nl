---
title: Beschik baarheid en consistentie-Azure Event Hubs | Microsoft Docs
description: De maximale hoeveelheid Beschik baarheid en consistentie bieden met Azure Event Hubs met behulp van partities.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425920"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Beschikbaarheid en consistentie in Event Hubs
Dit artikel bevat informatie over de beschik baarheid en consistentie die worden ondersteund door Azure Event Hubs. 

## <a name="availability"></a>Beschikbaarheid
Azure Event Hubs verspreidt het risico op onherstelbare storingen van afzonderlijke computers of zelfs volledige racks voor clusters die meerdere fout domeinen binnen een Data Center omvatten. Het implementeert transparante detectie van fouten en failover-mechanismen, zodat de service binnen de gegarandeerde service niveaus blijft werken en normaal gesp roken zonder merk bare onderbrekingen wanneer dergelijke fouten optreden. 

Als er een Event Hubs naam ruimte is gemaakt met [beschikbaarheids zones](../availability-zones/az-overview.md) ingeschakeld, wordt het uitval risico verder verdeeld over drie fysiek gescheiden faciliteiten en is de service voldoende capaciteits reserves om direct te kunnen omgaan met het volledige, onherstelbare verlies van de volledige faciliteit. Zie [Azure Event hubs-geo-nood herstel](event-hubs-geo-dr.md)voor meer informatie.

Wanneer een client toepassing gebeurtenissen naar een Event Hub verzendt zonder een partitie op te geven, worden gebeurtenissen automatisch gedistribueerd tussen partities in uw Event Hub. Als een partitie om een of andere reden niet beschikbaar is, worden gebeurtenissen verdeeld over de resterende partities. Dit gedrag zorgt voor de grootste hoeveelheid tijd. Voor use-cases waarvoor de maximale hoeveelheid tijd is vereist, is dit model in plaats van het verzenden van gebeurtenissen naar een specifieke partitie. 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>Beschik baarheid van overwegingen bij het gebruik van een partitie-ID of-sleutel
Het gebruik van een partitie-ID of partitie sleutel is optioneel. Overweeg zorgvuldig of u er een moet gebruiken. Als u geen partitie-ID/sleutel opgeeft bij het publiceren van een gebeurtenis, Event Hubs balanceert de belasting tussen de partities. Wanneer u een partitie-ID/sleutel gebruikt, zijn voor deze partities Beschik baarheid op één knoop punt vereist en kunnen er storingen optreden in de loop van de tijd. Reken knooppunten moeten bijvoorbeeld mogelijk opnieuw worden opgestart of worden gerepareerd. Als u dus een partitie-ID/sleutel instelt en deze partitie om een of andere reden niet meer beschikbaar is, mislukt de poging om toegang te krijgen tot de gegevens in die partitie. Als hoge Beschik baarheid het belangrijkst is, geeft u geen partitie-ID/sleutel op. In dat geval worden gebeurtenissen via een intern taakverdelings algoritme verzonden naar partities. In dit scenario maakt u een expliciete keuze tussen Beschik baarheid (geen partitie-ID/sleutel) en consistentie (het vastmaken van gebeurtenissen aan een specifieke partitie). Door gebruik te maken van partitie-ID/sleutel, wordt de beschik baarheid van een Event Hub op partitie niveau gedowngraded. 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>Beschik baarheid van overwegingen bij het verwerken van vertragingen bij het verwerken van gebeurtenissen
Een andere overweging is het afhandelen van een verwerking van consumenten toepassingen bij het verwerken van gebeurtenissen. In sommige gevallen kan het beter zijn voor de consumenten toepassing om gegevens te verwijderen en opnieuw te proberen, in plaats van te proberen de verwerking uit te voeren. Dit kan leiden tot verdere downstream verwerkings vertragingen. Met een aandelen tikker is het echter beter te wachten op de volledige actuele gegevens, maar in een live chat-of VOIP-scenario kunt u in plaats daarvan snel de gegevens gebruiken, zelfs als deze niet volledig zijn.

Gezien deze beschikbaarheids overwegingen, in deze scenario's, kan de consumenten toepassing een van de volgende strategieën voor het afhandelen van fouten kiezen:

- Stoppen (stoppen met het lezen van de Event Hub totdat de problemen zijn opgelost)
- Drop (berichten zijn niet belang rijk, verwijder ze)
- Probeer het opnieuw (probeer de berichten opnieuw uit te voeren zoals u ziet)


## <a name="consistency"></a>Consistentie
In sommige scenario's kan de volg orde van gebeurtenissen belang rijk zijn. Het is bijvoorbeeld mogelijk dat u wilt dat uw back-end-systeem een update opdracht verwerkt vóór een opdracht verwijderen. In dit scenario verzendt een client toepassing gebeurtenissen naar een specifieke partitie, zodat de volg orde wordt behouden. Wanneer een consumenten toepassing deze gebeurtenissen van de partitie verbruikt, worden deze in volg orde gelezen. 

Houd er bij deze configuratie rekening mee dat als de specifieke partitie waarnaar u wilt verzenden niet beschikbaar is, er een fout bericht wordt weer gegeven. Als u geen affiniteit hebt voor een enkele partitie, stuurt de Event Hubs-service uw gebeurtenis naar de volgende beschik bare partitie.


## <a name="appendix"></a>Bijlage

### <a name="send-events-to-a-specific-partition"></a>Gebeurtenissen verzenden naar een specifieke partitie
In deze sectie wordt beschreven hoe u gebeurtenissen naar een specifieke partitie verzendt met C#, Java, python en Java script. 

### <a name="net"></a>[.NET](#tab/dotnet)
Zie [gebeurtenissen verzenden naar en ontvangen van azure Event hubs-.net (Azure. Messa ging. Event hubs)](event-hubs-dotnet-standard-getstarted-send.md)voor de volledige voorbeeld code waarin wordt uitgelegd hoe u een gebeurtenis batch naar een event hub verzendt (zonder instelling van de partitie-id/-sleutel).

Als u gebeurtenissen naar een specifieke partitie wilt verzenden, maakt u de batch met de methode [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) door de-of-CreateBatchOptions op te geven `PartitionId` `PartitionKey` . [](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions) Met de volgende code wordt een batch gebeurtenissen naar een specifieke partitie verzonden door een partitie sleutel op te geven. 

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
Zie [Java gebruiken om gebeurtenissen te verzenden naar of ontvangen van azure Event hubs (Azure-Messa ging-Event hubs)](event-hubs-java-get-started-send.md)voor de volledige voorbeeld code die laat zien hoe u een gebeurtenis batch naar een event hub verzendt (zonder instelling van partitie-id/sleutel).

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
Zie [gebeurtenissen verzenden naar of ontvangen van Event hubs met behulp van python (Azure-eventhub)](event-hubs-python-get-started-send.md)voor de volledige voorbeeld code waarin wordt uitgelegd hoe u een gebeurtenis batch naar een event hub verzendt (zonder instelling van de partitie-id/-sleutel).

Als u gebeurtenissen wilt verzenden naar een specifieke partitie, [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) geeft u de of de op wanneer u een batch maakt met behulp van de-methode `partition_id` `partition_key` . Gebruik vervolgens de- [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) methode om de batch te verzenden naar de partitie van de Event hub. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

U kunt ook de methode [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) gebruiken door waarden voor `partition_id` or- `partition_key` para meters op te geven.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
Zie [gebeurtenissen verzenden naar of ontvangen van Event hubs met behulp van Java script (Azure/Event-hubs)](event-hubs-node-get-started-send.md)voor de volledige voorbeeld code die laat zien hoe u een gebeurtenis batch naar een event hub verzendt (zonder instelling van partitie-id/sleutel).

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

* [Overzicht van Event Hubs-service](./event-hubs-about.md)
* [Een Event Hub maken](event-hubs-create.md)
