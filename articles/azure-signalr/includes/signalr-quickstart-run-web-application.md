---
title: bestand opnemen
description: bestand opnemen
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "84317783"
---
## <a name="run-the-web-application"></a>De webtoepassing uitvoeren

1. Om gemakkelijkers clients te testen, opent u onze voorbeeldwebtoepassing van één pagina, [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/), in uw browser. 

    > [!NOTE]
    > De bron van het HTML-bestand bevindt zich in [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html). Als u zelf de HTML wilt hosten, start dan een lokale HTTP-server zoal [http-server](https://www.npmjs.com/package/http-server) in de map */docs/demo/chat-v2*. Zorg ervoor dat de oorsprong is toegevoegd aan de `CORS`-instelling in *local.settings.json* zoals in het voorbeeld.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. Wanneer u om de basis-URL van de functie-app wordt gevraagd, voert u `http://localhost:7071` in.

1. Voer een gebruikersnaam in wanneer hierom wordt gevraagd.

1. De webtoepassing roept de functie *GetSignalRInfo* in de functie-app aan om de verbindingsgegevens voor het verbinden met Azure SignalR Service op te halen. Wanneer de verbinding is voltooid, wordt het invoervak voor het chat-bericht weergegeven.

1. Typ een bericht en druk op Enter. Het bericht wordt verzonden naar de functie *SendMessage* in de Azure-functie-app die vervolgens de SignalR-uitvoerbinding gebruikt om het bericht naar alle verbonden clients te verzenden. Als alles goed werkt, wordt het bericht weergegeven in de toepassing.

    ![De toepassing uitvoeren](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Open een ander exemplaar van de webtoepassing in een ander browservenster. U ziet dat alle verzonden berichten in alle exemplaren van de toepassing worden weergegeven.
