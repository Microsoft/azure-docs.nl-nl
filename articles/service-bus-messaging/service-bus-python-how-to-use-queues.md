---
title: Azure Service Bus-wachtrijen met Azure Service Bus-pakketversie 7.0.0 voor Python gebruiken
description: In dit artikel wordt beschreven hoe u met Python berichten verzendt naar en ontvangt van Azure Service Bus-wachtrijen.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 0553062032a58ec9eb9cf3c474ee7c8f19fc544d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98631552"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Berichten verzenden naar en berichten ontvangen van Azure Service Bus-wachtrijen (Python)
In dit artikel wordt beschreven hoe u met Python berichten verzendt naar en ontvangt van Azure Service Bus-wachtrijen. 

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken. Noteer de **verbindingsreeks** voor uw Service Bus-naamruimte en de naam van de **wachtrij** die u hebt gemaakt.
- Python 2.7 of hoger, met het [Python Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus)-pakket geïnstalleerd. Raadpleeg de [Python-installatiehandleiding](/azure/developer/python/azure-sdk-install) voor meer informatie. 

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij

1. Voeg de volgende importeerinstructie toe. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Voeg de volgende constanten toe. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - Vervang `<NAMESPACE CONNECTION STRING>` door de verbindingstekenreeks voor uw Service Bus-naamruimte.
    > - Vervang `<QUEUE NAME>` door de naam van het wachtrij. 
3. Voeg een methode voor het verzenden van één bericht toe.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    De afzender is een object dat fungeert als een client voor de wachtrij dat u hebt gemaakt. U maakt en verzendt deze later als een argument voor deze functie. 
4. Voeg een methode voor het verzenden van een lijst met berichten toe.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
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
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Maak een Service Bus-client en vervolgens een object voor de afzender van de wachtrij om berichten te verzenden.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
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
 
## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Voeg de volgende code toe achter de afdrukinstructie. Deze code ontvangt voortdurend nieuwe berichten totdat er geen nieuwe berichten meer worden ontvangen gedurende 5 seconden (`max_wait_time`). 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Volledige code

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

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
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
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

Selecteer de wachtrij op de pagina **Overzicht** om naar de pagina **Service Bus-wachtrij** te gaan. U kunt ook de **binnenkomende** en **uitgaande** berichten op deze pagina zien. U ziet ook andere informatie, zoals de **huidige grootte** van de wachtrij en **aantal actieve berichten**. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Details van wachtrij":::


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden: 

- [Azure Service Bus-clientbibliotheek voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - De map **sync_samples** bevat voorbeelden die laten zien hoe u op synchrone wijze met Service Bus kunt werken. In deze quickstart hebt u deze methode gebruikt. 
    - De map **async_samples** bevat voorbeelden die laten zien hoe u op asynchrone wijze met Service Bus kunt werken. 
- [Referentiedocumentatie voor Azure Service Bus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)