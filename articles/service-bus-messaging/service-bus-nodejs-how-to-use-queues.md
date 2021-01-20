---
title: Azure Service Bus-wachtrijen gebruiken in JavaScript
description: Meer informatie over het schrijven van een JavaScript-programma dat gebruikmaakt van de nieuwste preview-versie van @azure/service-bus-pakket om berichten te verzenden naar en berichten te ontvangen van een Service Bus-wachtrij.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: ac24d84176f27170648545bc8044c5dcbc77781a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180010"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Berichten verzenden naar en berichten ontvangen van Azure Service Bus-wachtrijen (JavaScript)
In deze zelfstudie leert u hoe u het pakket [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) in een JavaScript-programma kunt gebruiken om berichten van en naar een Service Bus-wachtrij te ontvangen en verzenden.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken. Noteer de **verbindingsreeks** voor uw Service Bus-naamruimte en de naam van de **wachtrij** die u hebt gemaakt.

> [!NOTE]
> - In deze zelfstudie wordt gebruikgemaakt van voorbeelden die u kunt kopiëren en uitvoeren met [Nodejs](https://nodejs.org/). Zie voor instructies over het maken van een Node.js-toepassing [Een Node.js-toepassing maken en implementeren op een Azure-website](../app-service/quickstart-nodejs.md) of [Node.js-cloudservice met Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het npm-pakket voor Service Bus wilt installeren, opent u een opdrachtprompt met `npm` in het bijbehorende pad. Wijzig de directory in de map waar u de voorbeelden wilt maken en voer vervolgens deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
De volgende voorbeeldcode laat zien hoe u een bericht naar een wachtrij verzendt.

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/).
2. Maak een bestand met de naam `send.js` en plak de onderstaande code hierin. Met deze code worden een bericht naar uw wachtrij verzonden. Het bericht bevat een label (Wetenschapper) en hoofdtekst (Einstein).

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
    async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Vervang `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` door de verbindingstekenreeks voor uw Service Bus-naamruimte.
1. Vervang `<QUEUE NAME>` door de naam van het wachtrij. 
1. Voer vervolgens de opdracht in een opdrachtprompt uit om dit bestand uit te voeren.

    ```console
    node send.js 
    ```
1. De volgende uitvoer wordt weergegeven.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `receive.js` en plak hierin de volgende code.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });
    ```
3. Vervang `<CONNECTION STRING TO SERVICE BUS NAMESPACE>` door de verbindingstekenreeks voor uw Service Bus-naamruimte.
1. Vervang `<QUEUE NAME>` door de naam van het wachtrij. 
1. Voer vervolgens de opdracht in een opdrachtprompt uit om dit bestand uit te voeren.

    ```console
    node receive.js
    ```
1. De volgende uitvoer wordt weergegeven.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Op de pagina **Overzicht** voor de Service Bus-naamruimte in Azure Portal ziet u het aantal **inkomende** en **uitgaande** berichten. Mogelijk moet u ongeveer een minuut wachten en de pagina vervolgens vernieuwen om de meest recente waarden te zien. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Aantal inkomende en uitgaande berichten":::

Selecteer de wachtrij op de pagina **Overzicht** om naar de pagina **Service Bus-wachtrij** te gaan. U ziet ook de **inkomende** en **uitgaande** berichten op deze pagina. U ziet ook andere informatie, zoals de **huidige grootte** van de wachtrij, **maximum grootte**, **aantal actieve berichten**, enzovoort. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Details van wachtrij":::
## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden: 

- [Azure Service Bus-clientbibliotheek voor JS](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). De map **javascript** heeft JavaScript-voorbeelden en de map **typescript** heeft TypeScript-voor beelden. 
- [Referentiedocumentatie voor Azure Service Bus](/javascript/api/overview/azure/service-bus)
