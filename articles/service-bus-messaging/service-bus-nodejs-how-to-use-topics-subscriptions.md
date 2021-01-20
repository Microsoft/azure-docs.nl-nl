---
title: Preview-versie van JavaScript azure/service-bus met onderwerpen en abonnementen gebruiken
description: Meer informatie over het schrijven van een JavaScript-programma dat gebruikmaakt van de nieuwste preview-versie van @azure/service-bus-pakket om berichten naar een Service Bus-onderwerp te verzenden en berichten van een abonnement op het onderwerp te ontvangen.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: devx-track-js
ms.openlocfilehash: a1afe4207ce3833f3bcb55bc7bc2e8e27f393f63
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179993"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Quickstart: Service Bus-onderwerpen en-abonnementen gebruiken met Node.js en het previewpakket azure/service-bus
In deze zelfstudie leert u hoe u het [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-pakket in een JavaScript-programma kunt gebruiken om berichten te versturen naar een Service Bus-onderwerp en berichten te ontvangen van een Service Bus-abonnement naar dat onderwerp.

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) of u [aanmelden voor een gratis proefversie](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in de [Quickstart: Gebruik Azure Portal om een Service Bus-onderwerp en abonnementen voor het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md). Noteer de verbindingsreeks, onderwerpnaam en abonnementsnaam. U gebruikt slechts één abonnement voor deze quickstart. 

> [!NOTE]
> - In deze zelfstudie wordt gebruikgemaakt van voorbeelden die u kunt kopiëren en uitvoeren met [Nodejs](https://nodejs.org/). Zie voor instructies over het maken van een Node.js-toepassing [Een Node.js-toepassing maken en implementeren op een Azure-website](../app-service/quickstart-nodejs.md) of [Node.js-cloudservice](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) met Windows PowerShell.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren
Als u het npm-pakket voor Service Bus wilt installeren, opent u een opdrachtprompt met `npm` in het bijbehorende pad. Wijzig de directory in de map waar u de voorbeelden wilt maken en voer vervolgens deze opdracht uit.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
De volgende voorbeeldcode laat zien hoe u een batch berichten naar een Service Bus-onderwerp verzendt. Zie opmerkingen bij de code voor meer informatie. 

1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam `sendtotopic.js` en plak de onderstaande code hierin. Met deze code worden een bericht naar uw onderwerp verzonden.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
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
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
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
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
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
3. Vervang `<SERVICE BUS NAMESPACE CONNECTION STRING>` door de verbindingstekenreeks voor uw Service Bus-naamruimte.
1. Vervang `<TOPIC NAME>` door de naam van het onderwerp. 
1. Voer vervolgens de opdracht in een opdrachtprompt uit om dit bestand uit te voeren.

    ```console
    node sendtotopic.js 
    ```
1. De volgende uitvoer wordt weergegeven.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
1. Open uw favoriete editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/)
2. Maak een bestand met de naam **receivefromsubscription.js**, en plak hierin de volgende code. Zie opmerkingen bij de code voor meer informatie. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
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
3. Vervang `<SERVICE BUS NAMESPACE CONNECTION STRING>` door de verbindingsreeks naar uw naamruimte. 
1. Vervang `<TOPIC NAME>` door de naam van het onderwerp. 
1. Vervang `<SUBSCRIPTION NAME>` door de naam van het abonnement op het onderwerp. 
1. Voer vervolgens de opdracht in een opdrachtprompt uit om dit bestand uit te voeren.

    ```console
    node receivefromsubscription.js
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

Navigeer in het Azure Portal naar uw Service Bus-naamruimte en selecteer het onderwerp in het onderste deelvenster om de pagina **Service Bus-onderwerp** te bekijken voor uw onderwerp. Op deze pagina ziet u drie inkomende en drie uitgaande berichten in de grafiek **Berichten**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Inkomende en uitgaande berichten":::

Als u de volgende keer op de pagina **Service Bus-onderwerp** een app verzenden uitvoert, ziet u zes inkomende berichten (3 nieuw), maar drie uitgaande berichten. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Bijgewerkte onderwerppagina":::

Als u op deze pagina een abonnement selecteert, krijgt u toegang tot de pagina **Service Bus-abonnement**. Op deze pagina ziet u het aantal actieve berichten, het aantal onbestelbare berichten, en meer. In dit voorbeeld zijn er drie actieve berichten die nog niet zijn ontvangen door een ontvanger. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aantal actieve berichten":::

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden: 

- [Azure Service Bus-clientbibliotheek voor Python](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). De map **javascript** heeft JavaScript-voorbeelden en de map **typescript** heeft TypeScript-voor beelden. 
- [Referentiedocumentatie voor Azure Service Bus](/javascript/api/overview/azure/service-bus)
