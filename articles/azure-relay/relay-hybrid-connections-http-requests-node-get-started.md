---
title: Hybride verbindingen-HTTP-aanvragen in een knoop punt Azure Relay
description: Hier leert u hoe u een consoletoepassing schrijft in Node.js voor HTTP-aanvragen voor hybride verbindingen van Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 249b4fa231cd54a1a8054b32985ed0e48fcc16f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263756"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Aan de slag met HTTP-aanvragen voor hybride verbindingen van Relay in Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In deze snelstart maakt u Node.js-toepassingen voor afzenders en ontvangers waarmee berichten worden verzonden en ontvangen met behulp van het HTTP-protocol. De toepassingen maken gebruik van de functie Hybride verbindingen van Azure Relay. Zie [Azure Relay](relay-what-is-it.md) voor meer informatie over Azure Relay in het algemeen. 

In deze snelstart voert u de volgende stappen uit:

1. Een Relay-naamruimte maken met behulp van Azure Portal.
2. Een hybride verbinding in die naamruimte maken met behulp van Azure Portal.
3. Een serverconsoletoepassing (listener) schrijven om berichten te ontvangen.
4. Een clientconsoletoepassing (afzender) schrijven om berichten te verzenden.
5. Toepassingen uitvoeren.

## <a name="prerequisites"></a>Vereisten
- [Node.js](https://nodejs.org/en/).
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-namespace-using-the-azure-portal"></a>Een naamruimte maken met de Azure-portal
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Een hybride verbinding maken met behulp van Azure Portal
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Een servertoepassing (listener) maken
Maak een Node.js-consoletoepassing om berichten van de Relay te beluisteren en te ontvangen.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Een clienttoepassing maken (afzender)

Om berichten te versturen naar de Relay, kunt u een HTTP-client gebruiken of een consoletoepassing schrijven in Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>De toepassingen uitvoeren

1. Voer de servertoepassing uit: via een Node.js-opdrachtprompt van het type `node listener.js`.
2. Voer de clienttoepassing uit: via een Node.js-opdrachtprompt van het type `node sender.js`, en voer tekst in.
3. Zorg ervoor dat de servertoepassingsconsole de tekst uitvoert die in de clienttoepassing is ingevoerd.

Gefeliciteerd, u hebt een end-to-endtoepassing met hybride verbindingen gemaakt met behulp van Node.js!

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u Node.js-toepassingen gemaakt voor clients en servers waarmee berichten worden verzonden en ontvangen met behulp van HTTP. De functie Hybride verbindingen van Azure Relay ondersteunt tevens WebSockets voor het verzenden en ontvangen van berichten. Zie de [snelstart over WebSockets](relay-hybrid-connections-node-get-started.md) voor informatie over het gebruik van WebSockets met hybride verbindingen van Azure Relay.

In deze snelstart hebt u Node.js gebruikt om client- en servertoepassingen te maken. Zie de [snelstart over WebSockets in .NET](relay-hybrid-connections-dotnet-get-started.md) of de [snelstart over HTTP in .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md) voor informatie over het schrijven van client- en servertoepassingen in .Net Framework.
