---
title: 'Quickstart: Azure Queue Storage-bibliotheek v12 - JavaScript'
description: Ontdek hoe u de Azure Queue JavaScript v12-bibliotheek kunt gebruiken om een wachtrij te maken en berichten aan de wachtrij toe te voegen. Vervolgens leert u hoe u berichten uit de wachtrij kunt lezen en verwijderen. U leert ook hoe u een wachtrij kunt verwijderen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 5f50e42bc33adb8f40520f3f98bf1dcfba190a41
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491907"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Quickstart: Azure Queue Storage-clientbibliotheek v12 voor JavaScript

Ga aan de slag met de Azure Queue Storage-clientbibliotheek versie 12 voor JavaScript. Azure Queue Storage is een service om grote aantallen berichten op te slaan voor latere ophaling en verwerking. Volg deze stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Gebruik de Azure Queue Storage-clientbibliotheek v12 voor JavaScript om:

- Een wachtrij maken
- Berichten aan een wachtrij toevoegen
- Berichten in een wachtrij bekijken
- Een bericht in een wachtrij bijwerken
- Berichten van een wachtrij ontvangen
- Berichten uit een wachtrij verwijderen
- Een wachtrij verwijderen

Aanvullende bronnen:

- [API-referentiedocumentatie](/javascript/api/@azure/storage-queue/)
- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Pakket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-queue)
- [Voorbeelden](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Azure Storage-account: [maak een opslagaccount](../common/storage-account-create.md)
- De huidige versie van [Node.js](https://nodejs.org/en/download/) voor uw besturingssysteem.

## <a name="setting-up"></a>Instellen

In dit gedeelte wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-clientbibliotheek v12 voor JavaScript.

### <a name="create-the-project"></a>Het project maken

Maak een Node.js-toepassing met de naam *queues-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Schakel over naar de zojuist gemaakte map *queues-quickstart-v12*.

    ```console
    cd queues-quickstart-v12
    ```

1. Maak een nieuw tekstbestand met de naam *package.json*. Dit bestand definieert het Node.js-project. Sla dit bestand op in de map *queues-quickstart-v12*. Hier is de inhoud van het bestand:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    U kunt desgewenst uw eigen naam invoeren in het veld `author`.

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de map *queues-quickstart-v12* en installeer de Azure Queue Storage-clientbibliotheek voor het JavaScript-pakket met behulp van de opdracht `npm install`.

```console
npm install
```

 Met deze opdracht wordt het bestand *package.json* gelezen en het pakket Azure Queue Storage-clientbibliotheek v12 voor JavaScript geïnstalleerd, evenals alle bibliotheken waarvan het afhankelijk is.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open nog een nieuw tekstbestand in uw code-editor
1. Voeg `require`-aanroepen toe om Azure- en Node.js-modules te laden
1. Maak de structuur voor het programma, waaronder zeer eenvoudige afhandeling van uitzonderingen

    Hier volgt de code:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Sla het nieuwe bestand op als *queues-quickstart-v12.js* in de map *queues-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot aan de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een voorraad werk te maken dat asynchroon moet worden verwerkt. Queue Storage biedt drie typen resources:

- Het opslagaccount
- Een wachtrij in het opslagaccount
- Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de Queue Storage-architectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende JavaScript-klassen om te communiceren met deze resources:

- [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient): Met de `QueueServiceClient` kunt u alle wachtrijen in uw opslagaccount beheren.
- [QueueClient](/javascript/api/@azure/storage-queue/queueclient): Met de `QueueClient`-klasse kunt u een afzonderlijke wachtrij en de bijbehorende berichten beheren en bewerken.
- [QueueMessage](/javascript/api/@azure/storage-queue/queuemessage): De `QueueMessage`-klasse vertegenwoordigt de afzonderlijke objecten die worden geretourneerd wanneer [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) wordt aangeroepen voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-clientbibliotheek voor JavaScript:

- [De verbindingsreeks ophalen](#get-the-connection-string)
- [Een wachtrij maken](#create-a-queue)
- [Berichten aan een wachtrij toevoegen](#add-messages-to-a-queue)
- [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
- [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
- [Berichten van een wachtrij ontvangen](#receive-messages-from-a-queue)
- [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
- [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op van de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in de functie `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Een wachtrij maken

Verzin een naam voor de nieuwe wachtrij. Met de onderstaande code wordt een UUID-waarde aan de wachtrijnaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en afbreekstreepjes bevatten en moeten beginnen met een letter of cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet bovendien tussen 3 en 63 tekens lang zijn. Zie [Naamgeving van wachtrijen en metagegevens](/rest/api/storageservices/naming-queues-and-metadata) voor meer informatie over de naamgeving van wachtrijen.

Maak een instantie van de klasse [QueueClient](/javascript/api/@azure/storage-queue/queueclient). Roep vervolgens de methode [create](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Berichten aan een wachtrij toevoegen

Met het volgende codefragment worden berichten aan de wachtrij toegevoegd door de methode [sendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) aan te roepen. Ook wordt de [QueueMessage](/javascript/api/@azure/storage-queue/queuemessage) opgeslagen die door de derde `sendMessage`-aanroep wordt geretourneerd. De geretourneerde `sendMessageResponse` wordt gebruikt om de inhoud van een bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) aan te roepen. Met de methode `peekMessages` worden één of meer berichten vooraan in de wachtrij opgehaald, maar wordt de zichtbaarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

Werk de inhoud van een bericht bij door de methode [updateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) aan te roepen. De methode `updateMessage` kan de time-out voor zichtbaarheid en de inhoud van een bericht wijzigen. De inhoud van het bericht moet een UTF-8-gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Geef behalve de nieuwe inhoud ook `messageId` en `popReceipt` uit het antwoord dat eerder in de code was opgeslagen. De `sendMessageResponse`-eigenschappen identificeren welk bericht moet worden bijgewerkt.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

Download eerder toegevoegde berichten door de methode [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) aan te roepen. Geef in het veld `numberOfMessages` het maximumaantal berichten op dat voor deze aanroep moet worden ontvangen.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Verwijder berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval betekent ‘verwerken’ gewoon dat het bericht wordt weergegeven in de console.

Berichten verwijderen door de [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-)-methode aan te roepen. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij, zodat u nog een kans hebt om ze te verwerken.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code worden de resources opgeschoond die de app heeft gemaakt, door de wachtrij te verwijderen met de methode [delete](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-).

Voeg deze code toe aan het einde van het `main`-functie en sla het bestand op:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en aan een Azure-wachtrij toegevoegd. De code toont de berichten in de wachtrij, haalt ze op en verwijdert ze, en verwijdert uiteindelijk de wachtrij.

Navigeer in uw consolevenster naar de map die het bestand *queues-quickstart-v12.js* bevat en voer de volgende `node`-opdracht uit om de app uit te voeren.

```console
node queues-quickstart-v12.js
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Neem de code in uw foutopsporingsprogramma door en controleer uw [Azure-portal](https://portal.azure.com) gedurende de hele procedure. Controleer uw opslagaccount om te controleren of er berichten in de wachtrij zijn gemaakt en verwijderd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maakt en berichten eraan toevoegt met behulp van JavaScript-code. Vervolgens leerde u hoe u berichten kunt bekijken, ophalen en verwijderen. Tot slot leerde u hoe u een berichtenwachtrij verwijdert.

Voor zelfstudies, voorbeelden, quickstarts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Documentatie over Azure voor JavaScript](/azure/developer/javascript/)

- Zie de [Azure Storage Queue-clientbibliotheek voor JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)voor meer informatie.
- Ga door naar [Voorbeelden van Azure Queue Storage-clientbibliotheek v12 voor JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples) om meer Azure Queue Storage-voorbeeld-apps te zien.
