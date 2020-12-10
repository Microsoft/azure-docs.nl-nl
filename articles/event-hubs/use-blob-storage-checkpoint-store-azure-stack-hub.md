---
title: Blob Storage gebruiken als controlepuntopslag in Azure Stack Hub
description: In dit artikel wordt beschreven hoe u Blob Storage als controlepunt opslag in Event Hubs kunt gebruiken op Azure Stack hub.
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 07d7cf844480a9a88468c17cecc7ca38cca5d176
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007820"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub"></a>Blob Storage als controlepunt archief gebruiken Event Hubs op Azure Stack hub
Als u Azure Blob Storage gebruikt als controlepunt opslag in een omgeving die ondersteuning biedt voor een andere versie van de Storage BLOB SDK dan die welke meestal beschikbaar zijn in azure, moet u code gebruiken om de API-versie van de opslag service te wijzigen in de specifieke versie die wordt ondersteund door die omgeving. Als u bijvoorbeeld werkt met [Event hubs op een Azure stack hub-versie 2002](/azure-stack/user/event-hubs-overview), is de hoogste beschik bare versie van de opslag service versie 2017-11-09. In dit geval moet u code gebruiken om de API-versie van de Storage-service te richten op 2017-11-09. Zie voor een voor beeld van het richten op een specifieke opslag-API-versie de volgende voor beelden op GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) of  [type script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python- [synchroon](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchroon](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

Als u Event Hubs ontvanger uitvoert die gebruikmaakt van Blob Storage als het controle punt archief zonder de versie te richten die Azure Stack hub ondersteunt, wordt het volgende fout bericht weer gegeven:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Voorbeeld fout bericht in python
Voor python wordt een fout van `azure.core.exceptions.HttpResponseError` wordt door gegeven aan de foutafhandelingsroutine `on_error(partition_context, error)` van `EventHubConsumerClient.receive()` . Maar de methode `receive()` veroorzaakt geen uitzonde ring. `print(error)` de volgende uitzonderings gegevens worden afgedrukt:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

In de logboek registratie worden twee waarschuwingen geregistreerd, zoals de volgende:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over partitioneren en controle punten: de [belasting van partities verdelen over meerdere exemplaren van uw toepassing](event-processor-balance-partition-load.md)
