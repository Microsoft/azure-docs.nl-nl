---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509213"
---
## <a name="prerequisites"></a>Vereisten
Als u geen [Azure-abonnement](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken
Volg de instructies in deze zelfstudie: [Snelstart: De Azure-portal gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp ](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)om de volgende taken uit te voeren:

- Een **premium** Service Bus-naamruimte maken. 
- De verbindingsreeks ophalen. 
- Een Service Bus-onderwerp maken.
- Een abonnement op het onderwerp maken. In deze zelfstudie hebt u slechts één abonnement nodig. U hoeft de abonnementen S2 en S3 dus niet te maken. 

## <a name="send-messages-to-the-service-bus-topic"></a>Berichten kunt verzenden naar Service Bus-onderwerp
In deze stap gebruikt u een voorbeeldtoepassing om berichten te verzenden naar het Service Bus-onderwerp dat u in de vorige stap hebt gemaakt. 

1. Kloon de [GitHub azure-service-bus repository](https://github.com/Azure/azure-service-bus/) (opslagplaats van GitHub-azure-service-bus).
2. Ga in Visual Studio naar de map *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* en open het bestand *SBEventGridIntegration.sln*.
3. Vouw in het venster Solution Explorer het project **MessageSender** uit en selecteer **Program.cs**.
4. Vervang `<SERVICE BUS NAMESPACE - CONNECTION STRING>` door de verbindingstekenreeks voor uw Service Bus-naamruimte en `<TOPIC NAME>` door de naam van het onderwerp. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Bouw en voer het programma uit om vijf testberichten (`const int numberOfMessages = 5;`) te verzenden naar het Service Bus-onderwerp. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Uitvoer console-app":::
