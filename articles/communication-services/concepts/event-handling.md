---
title: Gebeurtenisverwerking
titleSuffix: An Azure Communication Services concept document
description: Gebruik Azure Event Grid om processen te activeren op basis van acties die plaatsvinden in een Communication Service.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df882f7a3e9dfbf4c1c30d6bca9a606049f81496
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488933"
---
# <a name="event-handling-in-azure-communication-services"></a>Afhandeling van gebeurtenissen in Azure Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure Communication Services kan worden geïntegreerd met [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) voor het leveren van real-time gebeurtenismeldingen op een betrouwbare, schaalbare en veilige manier. Het doel van dit artikel is om u te helpen uw toepassingen te configureren om te luisteren naar Communication Services-gebeurtenissen. U kunt bijvoorbeeld een database bijwerken, een werk-item maken en een push-melding verzenden wanneer een SMS-bericht wordt ontvangen door een telefoonnummer dat is gekoppeld aan uw Communication Service-resource.

Azure Event Grid is een volledig beheerde service voor gebeurtenisrouting, die gebruikmaakt van een publicatie/abonnementmodel. Event Grid heeft ingebouwde ondersteuning voor Azure-Services, zoals [Azure Functions](../../azure-functions/functions-overview.md) en [Azure Logic-apps](../../azure-functions/functions-overview.md). Het kan gebeurteniswaarschuwingen leveren aan niet-Azure-Services met behulp van webhooks. Zie [Een inleiding tot Azure Event Grid](../../event-grid/overview.md) voor een volledige lijst met gebeurtenisverwerkers die Event Grid ondersteunt.

:::image type="content" source="https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png" alt-text="Diagram waarin het gebeurtenismodel van Azure Event Grid wordt weergegeven.":::

> [!NOTE]
> Ga voor meer informatie over hoe gegevens locatie verband houdt met gebeurtenis afhandeling naar de [conceptuele documentatie van data locatie](./privacy.md)

## <a name="events-types"></a>Gebeurtenistypen

Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees.

Azure Communication Services verzendt de volgende gebeurtenistypen:

| Gebeurtenistype                                                  | Beschrijving                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Gepubliceerd wanneer een SMS wordt ontvangen door een telefoonnummer dat is gekoppeld aan de Communication Service. |
| Microsoft.Communication.SMSDeliveryReportReceived           | Gepubliceerd wanneer een leveringsrapport wordt ontvangen voor een SMS die door de Communication Service wordt verzonden.     |
| Microsoft.Communication.ChatMessageReceived                 | Gepubliceerd wanneer een bericht wordt ontvangen voor een gebruiker in een chat-thread waarvan deze lid is.        |
| Microsoft.Communication.ChatMessageEdited                   | Gepubliceerd wanneer een bericht wordt bewerkt in een chat-thread waarvan de gebruiker lid is.                |
| Microsoft.Communication.ChatMessageDeleted                  | Gepubliceerd wanneer een bericht wordt verwijderd in een chat-thread waarvan de gebruiker lid is.               |
| Microsoft.Communication.ChatThreadCreatedWithUser           | Gepubliceerd wanneer de gebruiker wordt toegevoegd als lid op het moment dat een chat-thread wordt gemaakt.           |
| Microsoft.Communication.ChatThreadWithUserDeleted           | Gepubliceerd wanneer een chat-thread wordt verwijderd waarvan de gebruiker lid is.                           |
| Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser  | Gepubliceerd wanneer de eigenschappen van een chat-thread worden bijgewerkt waarvan de gebruiker lid is.              |
| Microsoft.Communication.ChatMemberAddedToThreadWithUser     | Gepubliceerd wanneer de gebruiker wordt toegevoegd als lid aan een chat-thread.                                   |
| Microsoft.Communication.ChatMemberRemovedFromThreadWithUser | Gepubliceerd wanneer de gebruiker wordt verwijderd uit een chat-thread.                                         |
| Micro soft. Communication. ChatParticipantAddedToThreadWithUser|  Gepubliceerd voor een gebruiker wanneer een nieuwe deel nemer wordt toegevoegd aan een chat-thread waarvan de gebruiker deel uitmaakt.|
| Micro soft. Communication. ChatParticipantRemovedFromThreadWithUser |  Gepubliceerd voor een gebruiker wanneer een deel nemer wordt verwijderd uit een chat thread waarvan de gebruiker deel uitmaakt. |
| Micro soft. Communication. ChatThreadCreated  | Gepubliceerd wanneer een chat-thread wordt gemaakt  |
| Micro soft. Communication. ChatThreadDeleted| Gepubliceerd wanneer een chat thread wordt verwijderd  |
| Micro soft. Communication. ChatThreadParticipantAdded | Gepubliceerd wanneer een nieuwe deel nemer wordt toegevoegd aan een chat thread  |
| Micro soft. Communication. ChatThreadParticipantRemoved | Gepubliceerd wanneer een nieuwe deel nemer wordt toegevoegd aan een chat thread.  |  
| Micro soft. Communication. ChatMessageReceivedInThread | Gepubliceerd wanneer een bericht wordt ontvangen in een chat-thread  |    
| Micro soft. Communication. ChatThreadPropertiesUpdated| Gepubliceerd wanneer het onderwerp eigenschappen van een chat-thread worden bijgewerkt.|    
| Micro soft. Communication. ChatMessageEditedInThread | Gepubliceerd wanneer een bericht wordt bewerkt in een chat-thread |  
| Micro soft. Communication. ChatMessageDeletedInThread | Gepubliceerd wanneer een bericht wordt verwijderd in een chat-thread  |  

U kunt de Azure Portal of Azure CLI gebruiken om u te abonneren op gebeurtenissen die door uw Communication Services-resource worden verzonden. Ga aan de slag met het afhandelen van gebeurtenissen door te kijken naar [Hoe u SMS-gebeurtenissen verwerkt in Communication Services](../quickstarts/telephony-sms/handle-sms-events.md)


## <a name="event-subjects"></a>Gebeurtenisonderwerpen

Het veld `subject` van alle Communication Services-gebeurtenissen identificeert de gebruiker, het telefoonnummer of de entiteit waarop de gebeurtenis is gericht. Veelgebruikte voorvoegsels worden gebruikt om eenvoudige [Event Grid-filtering](../../event-grid/event-filtering.md)toe te staan.

| Onderwerp voorvoegsel                              | Communication Service-entiteit |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN-telefoonnummer            |
| `user/`                                     | Gebruiker van Communication Services  |
| `thread/`                                   | Chat-thread.                 |

In het volgende voorbeeld ziet u een filter voor alle SMS-berichten en bezorgingsrapporten die zijn verzonden naar alle telefoonnummers van 555-netnummers die eigendom zijn van een Communication Services-resource:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="sample-event-responses"></a>Voorbeeld gebeurtenisantwoorden

Wanneer een gebeurtenis wordt geactiveerd, verzendt de Event Grid-service gegevens over die gebeurtenis om eindpunten te abonneren.

Deze sectie bevat een voorbeeld van hoe de gegevens voor elke gebeurtenis eruitzien.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived-gebeurtenis

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived-gebeurtenis

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Microsoft.Communication.ChatMessageReceived-gebeurtenis

```json
[{
    "id": "02272459-badb-4e2e-b538-4cb8a2f71da6",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}/recipient/{rawId}",
    "data": {
      "messageBody": "Welcome to Azure Communication Services",
      "messageId": "1613694358927",
      "senderId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724"
        }
      },
      "senderDisplayName": "Jhon",
      "composeTime": "2021-02-19T00:25:58.927Z",
      "type": "Text",
      "version": 1613694358927,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d05-83fe-084822000f6d",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d05-83fe-084822000f6d",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d05-83fe-084822000f6d"
        }
      },
      "transactionId": "oh+LGB2dUUadMcTAdRWQxQ.1.1.1.1.1827536918.1.7",
      "threadId": "19:6e5d6ca1d75044a49a36a7965ec4a906@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatMessageReceived",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:25:59.9436666Z"
  }
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Microsoft.Communication.ChatMessageEdited-gebeurtenis

```json
[{
    "id": "93fc1037-b645-4eb0-a0f2-d7bb3ba6e060",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}/recipient/{rawId}",
    "data": {
      "editTime": "2021-02-19T00:28:20.784Z",
      "messageBody": "Let's Chat about new communication services.",
      "messageId": "1613694357917",
      "senderId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724"
        }
      },
      "senderDisplayName": "Bob(Admin)",
      "composeTime": "2021-02-19T00:25:57.917Z",
      "type": "Text",
      "version": 1613694500784,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f"
        }
      },
      "transactionId": "1mL4XZH2gEecu/alk9tOtw.2.1.2.1.1833042153.1.7",
      "threadId": "19:6e5d6ca1d75044a49a36a7965ec4a906@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatMessageEdited",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:28:21.7456718Z"
  }]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Microsoft.Communication.ChatMessageDeleted-gebeurtenis
```json
[{
    "id": "23cfcc13-33f2-4ae1-8d23-b5015b05302b",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}/recipient/{rawId}",
    "data": {
      "deleteTime": "2021-02-19T00:43:10.14Z",
      "messageId": "1613695388152",
      "senderId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e"
        }
      },
      "senderDisplayName": "Bob(Admin)",
      "composeTime": "2021-02-19T00:43:08.152Z",
      "type": "Text",
      "version": 1613695390361,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f"
        }
      },
      "transactionId": "fFs4InlBn0O/0WyhfQZVSQ.1.1.2.1.1867776045.1.4",
      "threadId": "19:48899258eec941e7a281e03edc8f4964@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatMessageDeleted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:43:10.9982947Z"
  }]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Microsoft.Communication.ChatThreadCreatedWithUser-gebeurtenis

```json
[{
    "id": "eba02b2d-37bf-420e-8656-3a42ef74c435",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/createdBy/rawId/recipient/rawId",
    "data": {
      "createdBy": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9",
      "createdByCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9"
        }
      },
      "properties": {
        "topic": "Chat about new commuication services"
      },
      "members": [
        {
          "displayName": "Bob",
          "memberId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9"
        },
        {
          "displayName": "John",
          "memberId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-289b-07fd-0848220015ea"
        }
      ],
      "participants": [
        {
          "displayName": "Bob",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9",
            "communicationUser": {
              "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9"
            }
          }
        },
        {
          "displayName": "John",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-289b-07fd-0848220015ea",
            "communicationUser": {
              "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-289b-07fd-0848220015ea"
            }
          }
        }
      ],
      "createTime": "2021-02-18T23:47:26.91Z",
      "version": 1613692046910,
      "recipientId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286e-84f5-08482200181c",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286e-84f5-08482200181c",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286e-84f5-08482200181c"
        }
      },
      "transactionId": "zbZt+9h/N0em+XCW2QvyIA.1.1.1.1.1737228330.0.1737490483.1.6",
      "threadId": "19:1d594fb1eeb14566903cbc5decb5bf5b@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatThreadCreatedWithUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-18T23:47:34.7437103Z"
  }]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Microsoft.Communication.ChatThreadWithUserDeleted-gebeurtenis

```json
[{
    "id": "f5d6750c-c6d7-4da8-bb05-6f3fca6c7295",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/deletedBy/{rawId}/recipient/{rawId}",
    "data": {
      "deletedBy": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-6473-83fe-084822000e21",
      "deletedByCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-6473-83fe-084822000e21",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-6473-83fe-084822000e21"
        }
      },
      "deleteTime": "2021-02-18T23:57:51.5987591Z",
      "createTime": "2021-02-18T23:54:15.683Z",
      "version": 1613692578672,
      "recipientId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-647b-e1fe-084822001416",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-647b-e1fe-084822001416",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-647b-e1fe-084822001416"
        }
      },
      "transactionId": "mrliWVUndEmLwkZbeS5KoA.1.1.2.1.1761607918.1.6",
      "threadId": "19:5870b8f021d74fd786bf5aeb095da291@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatThreadWithUserDeleted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-18T23:57:52.1597234Z"
  }]
```

### <a name="microsoftcommunicationchatparticipantaddedtothreadwithuser--event"></a>Micro soft. Communication. ChatParticipantAddedToThreadWithUser-gebeurtenis 
```json
[{
    "id": "049a5a7f-6cd7-43c1-b352-df9e9e6146d1",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantAdded/{rawId}/recipient/{rawId}",
    "data": {
      "time": "2021-02-25T06:37:29.9232485Z",
      "addedByCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d"
        }
      },
      "participantAdded": {
        "displayName": "John Smith",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f",
          "communicationUser": {
            "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f"
          }
        }
      },
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e"
        }
      },
      "createTime": "2021-02-25T06:37:17.371Z",
      "version": 1614235049907,
      "transactionId": "q7rr9by6m0CiGiQxKdSO1w.1.1.1.1.1473446055.1.6",
      "threadId": "19:f1400e1c542f4086a606b52ad20cd0bd@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatParticipantAddedToThreadWithUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-25T06:37:31.4880091Z"
  }]
```

### <a name="microsoftcommunicationchatparticipantremovedfromthreadwithuser-event"></a>Micro soft. Communication. ChatParticipantRemovedFromThreadWithUser-gebeurtenis 
```json
[{
    "id": "e8a4df24-799d-4c53-94fd-1e05703a4549",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantRemoved/{rawId}/recipient/{rawId}",
    "data": {
      "time": "2021-02-25T06:40:20.3564556Z",
      "removedByCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d"
        }
      },
      "participantRemoved": {
        "displayName": "Bob",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f",
          "communicationUser": {
            "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f"
          }
        }
      },
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e"
        }
      },
      "createTime": "2021-02-25T06:37:17.371Z",
      "version": 1614235220325,
      "transactionId": "usv74GQ5zU+JmWv/bQ+qfg.1.1.1.1.1480065078.1.5",
      "threadId": "19:f1400e1c542f4086a606b52ad20cd0bd@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatParticipantRemovedFromThreadWithUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-25T06:40:24.2244945Z"
  }]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser-gebeurtenis

```json
[{
    "id": "d57342ff-264e-4a5e-9c54-ef05b7d50082",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/editedBy/{rawId}/recipient/{rawId}",
    "data": {
      "editedBy": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
      "editedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e"
        }
      },
      "editTime": "2021-02-19T00:28:28.7390282Z",
      "properties": {
        "topic": "Communication in Azure"
      },
      "createTime": "2021-02-19T00:28:25.864Z",
      "version": 1613694508719,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724"
        }
      },
      "transactionId": "WLXPrnJ/I0+LTj2cwMrNMQ.1.1.1.1.1833369763.1.4",
      "threadId": "19:2cc3504c41244d7483208a4f58a1f188@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:28:29.559726Z"
  }]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Microsoft.Communication.ChatMemberAddedToThreadWithUser-gebeurtenis

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/memberAdded/{rawId}/recipient/{rawId}",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:13.2342692Z"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Microsoft.Communication.ChatMemberRemovedFromThreadWithUser-gebeurtenis

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/memberRemoved/{rawId}/recipient/{rawId}",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:51.2244511Z"
}]
```

### <a name="microsoftcommunicationchatthreadcreated-event"></a>Micro soft. Communication. ChatThreadCreated-gebeurtenis

```json
[ {
    "id": "a607ac52-0974-4d3c-bfd8-6f708a26f509",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/createdBy/{rawId}",
    "data": {
      "createdByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "properties": {
        "topic": "Talk about new Thread Events in commuication services"
      },
      "participants": [
        {
          "displayName": "Bob",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
            }
          }
        },
        {
          "displayName": "Scott",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467"
            }
          }
        },
        {
          "displayName": "Shawn",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f6-83fe-084822002337",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f6-83fe-084822002337"
            }
          }
        },
        {
          "displayName": "Anthony",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e3-e1fe-084822002c35",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e3-e1fe-084822002c35"
            }
          }
        }
      ],
      "createTime": "2021-02-20T00:31:54.365+00:00",
      "version": 1613781114365,
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "gK6+kgANy0O1wchlVKVTJg.1.1.1.1.921436178.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadCreated",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:31:54.5369967Z"
  }]
```
### <a name="microsoftcommunicationchatthreadpropertiesupdated-event"></a>Micro soft. Communication. ChatThreadPropertiesUpdated-gebeurtenis

```json
[{
    "id": "cf867580-9caf-45be-b49f-ab1cbfcaa59f",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/editedBy/{rawId}",
    "data": {
      "editedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-9e35-07fd-084822002264",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-9e35-07fd-084822002264"
        }
      },
      "editTime": "2021-02-20T00:04:07.7152073+00:00",
      "properties": {
        "topic": "Talk about new Thread Events in commuication services"
      },
      "createTime": "2021-02-20T00:00:40.126+00:00",
      "version": 1613779447695,
      "threadId": "19:9e8eefe67b3c470a8187b4c2b00240bc@thread.v2",
      "transactionId": "GBE9MB2a40KEWzexIg0D3A.1.1.1.1.856359041.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdated",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:04:07.8410277Z"
  }]
```
### <a name="microsoftcommunicationchatthreaddeleted-event"></a>Micro soft. Communication. ChatThreadDeleted-gebeurtenis

```json
[
{
    "id": "1dbd5237-4823-4fed-980c-8d27c17cf5b0",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/deletedBy/{rawId}",
    "data": {
      "deletedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-a300-07fd-084822002266",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-a300-07fd-084822002266"
        }
      },
      "deleteTime": "2021-02-20T00:00:42.109802+00:00",
      "createTime": "2021-02-20T00:00:39.947+00:00",
      "version": 1613779241389,
      "threadId": "19:c9e9f3060b884e448671391882066ac3@thread.v2",
      "transactionId": "KibptDpcLEeEFnlR7cI3QA.1.1.2.1.848298005.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadDeleted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:00:42.5428002Z"
  }
  ]
```
### <a name="microsoftcommunicationchatthreadparticipantadded-event"></a>Micro soft. Communication. ChatThreadParticipantAdded-gebeurtenis

```json
[
{
    "id": "3024eb5d-1d71-49d1-878c-7dc3165433d9",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantadded/{rawId}",
    "data": {
      "time": "2021-02-20T00:54:42.8622646+00:00",
      "addedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "participantAdded": {
        "displayName": "Bob",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f3-88f7-084822002454",
          "communicationUser": {
            "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f3-88f7-084822002454"
          }
        }
      },
      "createTime": "2021-02-20T00:31:54.365+00:00",
      "version": 1613782482822,
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "9q6cO7i4FkaZ+5RRVzshVw.1.1.1.1.974913783.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadParticipantAdded",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:54:43.9866454Z"
  }
]
```
### <a name="microsoftcommunicationchatthreadparticipantremoved-event"></a>Micro soft. Communication. ChatThreadParticipantRemoved-gebeurtenis

```json
[
{
    "id": "6ed810fd-8776-4b13-81c2-1a0c4f791a07",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantremoved/{rawId}",
    "data": {
      "time": "2021-02-20T00:56:18.1118825+00:00",
      "removedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "participantRemoved": {
        "displayName": "Shawn",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467",
          "communicationUser": {
            "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467"
          }
        }
      },
      "createTime": "2021-02-20T00:31:54.365+00:00",
      "version": 1613782578096,
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "zGCq8IGRr0aEF6COuy7wSA.1.1.1.1.978649284.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadParticipantRemoved",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:56:18.856721Z"
  }
]
```
### <a name="microsoftcommunicationchatmessagereceivedinthread-event"></a>Micro soft. Communication. ChatMessageReceivedInThread-gebeurtenis

```json
[
{
    "id": "4f614f97-c451-4b82-a8c9-1e30c3bfcda1",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cdb-4916-07fd-084822002624",
    "data": {
      "messageBody": "Talk about new Thread Events in commuication services",
      "messageId": "1613783230064",
      "type": "Text",
      "version": "1613783230064",
      "senderDisplayName": "Bob",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cdb-4916-07fd-084822002624",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cdb-4916-07fd-084822002624"
        }
      },
      "composeTime": "2021-02-20T01:07:10.064+00:00",
      "threadId": "19:5b3809e80e4a439d92c3316e273f4a2b@thread.v2",
      "transactionId": "foMkntkKS0O/MhMlIE5Aag.1.1.1.1.1004077250.1"
    },
    "eventType": "Microsoft.Communication.ChatMessageReceivedInThread",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T01:07:10.5704596Z"
  }
]
```
### <a name="microsoftcommunicationchatmessageeditedinthread-event"></a>Micro soft. Communication. ChatMessageEditedInThread-gebeurtenis

```json
[
  {
    "id": "7b8dc01e-2659-41fa-bc8c-88a967714510",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}",
    "data": {
      "editTime": "2021-02-20T00:59:10.464+00:00",
      "messageBody": "8effb181-1eb2-4a58-9d03-ed48a461b19b",
      "messageId": "1613782685964",
      "type": "Text",
      "version": "1613782750464",
      "senderDisplayName": "Scott",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "composeTime": "2021-02-20T00:58:05.964+00:00",
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "H8Gpj3NkIU6bXlWw8WPvhQ.2.1.2.1.985333801.1"
    },
    "eventType": "Microsoft.Communication.ChatMessageEditedInThread",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:59:10.7600061Z"
  }
]
```

### <a name="microsoftcommunicationchatmessagedeletedinthread-event"></a>Micro soft. Communication. ChatMessageDeletedInThread-gebeurtenis

```json
[
 {
    "id": "17d9c39d-0c58-4ed8-947d-c55959f57f75",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}",
    "data": {
      "deleteTime": "2021-02-20T00:59:10.464+00:00",
      "messageId": "1613782685440",
      "type": "Text",
      "version": "1613782814333",
      "senderDisplayName": "Scott",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "composeTime": "2021-02-20T00:58:05.44+00:00",
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "HqU6PeK5AkCRSpW8eAbL0A.1.1.2.1.987824181.1"
    },
    "eventType": "Microsoft.Communication.ChatMessageDeletedInThread",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T01:00:14.8518034Z"
  }
]
```


## <a name="quickstarts-and-how-tos"></a>Snelstart en uitleg

| Titel | Beschrijving |
| --- | --- |
| [Hoe handelen bij SMS-gebeurtenissen in Communication Services](../quickstarts/telephony-sms/handle-sms-events.md) | Verwerken van alle SMS-gebeurtenissen die door uw Communication Service worden ontvangen via webhook. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](../../event-grid/overview.md) voor een inleiding tot Azure Event Grid.
* Zie voor een inleiding tot Azure Event Grid concepten, de [Concepten in Event Grid?](../../event-grid/concepts.md)
* Voor een inleiding tot Azure Event Grid SystemTopics raadpleegt u [Systeem onderwerpen in Azure Event Grid?](../../event-grid/system-topics.md)
