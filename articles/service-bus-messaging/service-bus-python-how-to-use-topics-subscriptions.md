---
title: Azure Service Bus-onderwerpen en -abonnementen met Azure Service Bus-pakketversie 7.0.0 voor Python gebruiken
description: In dit artikel wordt beschreven hoe u met Python berichten verzendt naar en ontvangt van abonnementen.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 49e80e277c6df5372341293861d5bda0580f3e8c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537158"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Berichten verzenden naar een Azure Service Bus-onderwerp en berichten ontvangen van abonnementen op het onderwerp (Python)
In dit artikel wordt beschreven hoe u met Python berichten verzendt naar een Service Bus-onderwerp en berichten ontvangt van het abonnement op het onderwerp. 

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in de [Quickstart: Gebruik Azure Portal om een Service Bus-onderwerp en abonnementen voor het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md). Noteer de verbindingsreeks, onderwerpnaam en abonnementsnaam. U gebruikt slechts één abonnement voor deze quickstart. 
- Python 2.7 of hoger, met het pakket [Azure Python SDK] [Azure Python package] geïnstalleerd. Raadpleeg de [Python-installatiehandleiding](/azure/developer/python/azure-sdk-install) voor meer informatie.

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp

1. Voeg de volgende importeerinstructie toe. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Voeg de volgende constanten toe. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Vervang `<NAMESPACE CONNECTION STRING>` door de verbindingsreeks voor uw naamruimte.
    > - Vervang `<TOPIC NAME>` door de naam van het onderwerp.
    > - Vervang `<SUBSCRIPTION NAME>` door de naam van het abonnement op het onderwerp. 
3. Voeg een methode voor het verzenden van één bericht toe.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    De afzender is een object dat fungeert als een client voor het onderwerp dat u hebt gemaakt. U maakt en verzendt deze later als een argument voor deze functie. 
4. Voeg een methode voor het verzenden van een lijst met berichten toe.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Voeg een methode voor het verzenden van een batch met berichten toe.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Maak een Service Bus-client en vervolgens een object voor de afzender van het onderwerp om berichten te verzenden.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
Voeg de volgende code toe achter de afdrukinstructie. Deze code ontvangt voortdurend nieuwe berichten totdat er geen nieuwe berichten meer worden ontvangen gedurende 5 seconden (`max_wait_time`). 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Volledige code

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>De app uitvoeren
Wanneer u de toepassing uitvoert, wordt de volgende uitvoer weergegeven: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

Ga in Azure Portal naar uw Service Bus-naamruimte. Controleer op de pagina **Overzicht** of er 16 **inkomende** en **uitgaande** berichten zijn. Als u de tellingen niet ziet, wacht u een paar minuten en vernieuwt u de pagina. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Aantal inkomende en uitgaande berichten":::

Selecteer het onderwerp in het onderste deelvenster om de pagina **Service Bus-onderwerp** voor uw onderwerp weer te geven. Op deze pagina ziet u drie inkomende en drie uitgaande berichten in de grafiek **Berichten**. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Inkomende en uitgaande berichten":::

Als u op deze pagina een abonnement selecteert, krijgt u toegang tot de pagina **Service Bus-abonnement**. Op deze pagina ziet u het aantal actieve berichten, het aantal onbestelbare berichten, en meer. In dit voorbeeld zijn alle berichten ontvangen, waardoor het aantal actieve berichten nul is. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aantal actieve berichten":::

Als u commentaar maakt van de ontvangstcode, verandert het aantal actieve berichten in 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Aantal actieve berichten zonder ontvangst":::

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden: 

- [Azure Service Bus-clientbibliotheek voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - De map **sync_samples** bevat voorbeelden die laten zien hoe u op synchrone wijze met Service Bus kunt werken. In deze quickstart hebt u deze methode gebruikt. 
    - De map **async_samples** bevat voorbeelden die laten zien hoe u op asynchrone wijze met Service Bus kunt werken. 
- [Referentiedocumentatie voor Azure Service Bus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
