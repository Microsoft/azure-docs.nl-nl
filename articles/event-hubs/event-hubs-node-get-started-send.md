---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van JavaScript (meest recente versie)
description: Dit artikel bevat een overzicht van het maken van een JavaScript-toepassing waarmee gebeurtenissen worden verzonden naar/ontvangen van Azure Event Hubs met behulp van het nieuwste pakket azure/event-hubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: c418c13346fb1ec8ba16965fa1020c676ddf3ac6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602550"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs"></a>Gebeurtenissen verzenden naar of gebeurtenissen ontvangen van Event Hubs met behulp van JavaScript (azure/event-hubs)
In deze quickstart ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het JavaScript-pakket **azure/event-hubs**. 


## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Node.js versie 8.x of hoger. Download de meest recente [versie van LTS (Long-Term Support)](https://nodejs.org).  
- Visual Studio Code (aanbevolen) of een andere IDE (Integrated Development Environment).  
- Een actieve Azure Event Hubs-naamruimte en Event Hub. Voer de volgende stappen uit om deze te maken: 

   1. Maak in de [Azure-portal](https://portal.azure.com) een naamruimte van het type *Event Hubs*, en verkrijg vervolgens de beheerreferenties die de toepassing nodig heeft om met de Event Hub te communiceren. 
   1. Als u de naamruimte en Event Hub wilt maken volgt u de instructies in [Quickstart: Een Event Hub maken met behulp van de Azure-portal](event-hubs-create.md).
   1. Ga verder met behulp van de instructies in deze quickstart. 
   1. Volg de instructies in [Verbindingsreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) om de verbindingsreeks voor uw Event Hub-naamruimte op te halen. Leg de verbindingsreeks vast zodat u deze later in deze quickstart kunt gebruiken.
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal vervolgens de **verbindingsreeks voor de Event Hubs-naamruimte** op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze quickstart.

### <a name="install-the-npm-package"></a>Installeer het npm-pakket
Als u het [npm-pakket (Node Package Manager) voor Event Hubs](https://www.npmjs.com/package/@azure/event-hubs) wilt installeren, opent u een opdrachtprompt met *npm* in het bijbehorende pad. Wijzig de directory in de map waar u de voorbeelden wilt opslaan, en voer vervolgens deze opdracht uit:

```shell
npm install @azure/event-hubs
```

Voor deze ontvangende kant moet u nog twee pakketten installeren. In deze quickstart gebruikt u Azure Blob-opslag om controlepunten te behouden, zodat met het programma geen reeds gelezen gebeurtenissen worden gelezen. Er worden in een blob regelmatig controlepunten voor metagegevens uitgevoerd voor ontvangen berichten. Met deze aanpak kunt u later eenvoudig berichten blijven ontvangen vanaf de plek waar u was gebleven.

Voer de volgende opdrachten uit:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Gebeurtenissen verzenden

In deze sectie maakt u een JavaScript-toepassing waarmee gebeurtenissen naar een Event Hub worden verzonden.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com).
1. Maak een bestand met de naam *send.js*, en plak hierin de volgende code:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. Gebruik in de code echte waarden om het volgende te vervangen:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Voer `node send.js` uit om dit bestand uit te voeren. Met deze opdracht verzendt u een batch met drie gebeurtenissen naar uw Event Hub.
1. Controleer in de Azure-portal of de Event Hub de berichten heeft ontvangen. Schakel in de sectie **Metrische gegevens** over naar de weergave **Berichten**. U moet de pagina vernieuwen om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weergegeven dat de berichten zijn ontvangen.

    [![Controleren of de Event Hub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Ga voor de volledige broncode, inclusief aanvullende informatieve opmerkingen, naar de [pagina GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Gefeliciteerd! U hebt nu gebeurtenissen verzonden naar een Event Hub.


## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie ontvangt u gebeurtenissen van een Event Hub met behulp van een controlepuntarchief voor Azure Blob Storage-opslag in een Java script-toepassing. Er worden in een Azure Storage-blob regelmatig controlepunten voor metagegevens uitgevoerd voor ontvangen berichten. Met deze aanpak kunt u later eenvoudig berichten blijven ontvangen vanaf de plek waar u was gebleven.

> [!WARNING]
> Als u deze code op Azure Stack Hub uitvoert, treden er runtimefouten op tenzij u zich richt op een specifieke versie van de Storage-API. Dat komt doordat de Event Hubs-SDK de meest recente Azure Storage-API gebruikt die beschikbaar is in Azure maar die niet beschikbaar is op uw Azure Stack Hub-platform. Azure Stack Hub biedt mogelijk ondersteuning voor een andere versie van de Storage Blob-SDK dan de versies die doorgaans in Azure beschikbaar zijn. Als u Azure Blob-opslag gebruikt als controlepuntopslag, controleert u de [ondersteunde versie van de Azure Storage-API voor uw build van Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) en stelt u die versie in uw code als doel in. 
>
> Als u bijvoorbeeld op Azure Stack Hub versie 2005 uitvoert, is de hoogste beschikbare versie van de Storage-service versie 2019-02-02. De Event Hubs-SDK-clientbibliotheek maakt standaard gebruik van de hoogste beschikbare versie op Azure (2019-07-07 op het moment van de release van de SDK). In dit geval moet u naast de volgende stappen in deze sectie ook code toevoegen om de API-versie van de Storage-service te richten op 2019-02-02. Bekijk de [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js)- en [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)-voorbeelden in GitHub voor een voorbeeld van het instellen van een specifieke versie van de Storage-API. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslagaccount en een blobcontainer maken
Als u een Azure-opslagaccount met een blobcontainer erin wilt maken, voert u de volgende acties uit:

1. [Een Azure-opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Een blobcontainer in het opslagaccount maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [De verbindingsreeks voor het opslagaccount ophalen](../storage/common/storage-configure-connection-string.md)

Zorg ervoor dat u de verbindingsreeks en de containernaam vastlegt voor later gebruik in de receive-code.

### <a name="write-code-to-receive-events"></a>Code schrijven om gebeurtenissen te ontvangen

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com).
1. Maak een bestand met de naam *receive.js*, en plak hierin de volgende code:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            if (events.length === 0) {
              console.log(`No events received within wait time. Waiting for next interval`);
              return;
            }
          
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. Gebruik in de code echte waarden om de volgende waarden te vervangen:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Voer `node receive.js` in een opdrachtprompt uit om dit bestand uit te voeren. In het venster worden nu berichten over ontvangen gebeurtenissen weergegeven.

    > [!NOTE]
    > Ga voor de volledige broncode, inclusief aanvullende informatieve opmerkingen, naar de [pagina GitHub receiveEventsUsingCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsUsingCheckpointStore.js).

Gefeliciteerd! U hebt nu gebeurtenissen ontvangen van uw Event Hub. Het programma voor ontvangen, ontvangt nu gebeurtenissen van alle partities van de standaardconsumentengroep in de Event Hub.

## <a name="next-steps"></a>Volgende stappen
Bekijk deze voorbeelden op GitHub:

- [JavaScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
