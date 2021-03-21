---
title: 'Snelstart: Azure SignalR Service REST-API'
description: Ontdek hoe u REST API met de volgende Azure SignalR Service-voorbeelden gebruikt. Zoek detail van REST API-specificatie.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874522"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Quickstart: In realtime berichten verzenden via de console-app

Azure SignalR-service biedt [REST-API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) ter ondersteuning van server-naar-client-communicatie, zoals broadcasting. U kunt elke programmeertaal kiezen die een REST-API kan aanroepen. U kunt berichten verzenden naar alle verbonden clients, een specifieke client op basis van naam of een groep clients.

In deze snelstartgids leert u hoe u in C# berichten via een opdrachtregel-app kunt verzenden naar verbonden client-apps.

## <a name="prerequisites"></a>Vereisten

Deze quickstart kan worden uitgevoerd op macOS, Windows of Linux.

* [.NET Core-SDK](https://www.microsoft.com/net/download/core)
* Een teksteditor of code-editor naar keuze.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Laten wij de code klaarmaken terwijl de service wordt geïmplementeerd. Kloon de [voorbeeld-app vanuit GitHub](https://github.com/aspnet/AzureSignalR-samples.git), stel de SignalR Service-verbindingsreeks in en voer de toepassing lokaal uit.

1. Open een nieuw git-terminalvenster. Ga naar een map waarin u het voorbeeldproject wilt klonen.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

Dit voorbeeld is een console-app die het gebruik van de Azure SignalR-service weergeeft. Deze biedt twee modi:

- Servermodus: gebruik eenvoudige opdrachten om de REST-API voor Azure SignalR-service aan te roepen.
- Clientmodus: maak verbinding met de Azure SignalR-service en ontvang berichten van de server.

U kunt ook zien hoe u een toegangstoken kunt genereren voor verificatie met Azure SignalR-service.

### <a name="build-the-executable-file"></a>Het uitvoerbare bestand maken

We gebruiken macOS osx.10.13-x64 als voorbeeld. U kunt [naslaginformatie](/dotnet/core/rid-catalog) vinden over het maken op andere platforms.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Een client starten

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Een server starten

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="run-the-sample-without-publishing"></a>Het voorbeeld uitvoeren zonder te publiceren

U kunt ook de onderstaande opdracht uitvoeren om een server of client te starten

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Gebruikersgeheimen gebruiken om een verbindingsreeks te specificeren

U kunt `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` uitvoeren in de hoofdmap van het voorbeeld. Daarna hebt u de optie `-c "<ConnectionString>"` niet meer nodig.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="usage"></a>Gebruik

Gebruik de opdracht om het bericht te verzenden nadat de server is gestart:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

U kunt meerdere clients met verschillende clientnamen starten.

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integratie met services van derden

Met behulp van de Azure SignalR-service kunt services van derden integreren met het systeem.

### <a name="definition-of-technical-specifications"></a>Definitie van technische specificaties

In de volgende tabel worden alle versies van de ondersteunde REST-API's weergegeven op datum. U kunt er ook het definitiebestand voor elke specifieke versie vinden

Versie | API-status | Door | Specifiek
--- | --- | --- | ---
`1.0-preview` | Beschikbaar | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Beschikbaar | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

De lijst met beschikbare API's voor elke specifieke versie is beschikbaar in de volgende lijst.

API | 1.0-preview | 1.0
--- | --- | ---
[Uitzenden naar alle](#broadcast) | **&#x2713;** | **&#x2713;**
[Uitzenden naar een groep](#broadcast-group) | **&#x2713;** | **&#x2713;**
Uitzenden naar bepaalde groepen | **&#x2713;** (afgeschaft) | `N / A`
[Naar een gebruiker verzenden](#send-user) | **&#x2713;** | **&#x2713;**
Verzenden naar bepaalde gebruikers | **&#x2713;** (afgeschaft) | `N / A`
[Een gebruiker aan een groep toevoegen](#add-user-to-group) | `N / A` | **&#x2713;**
[Een gebruiker uit een groep verwijderen](#remove-user-from-group) | `N / A` | **&#x2713;**
[Het bestaan van de gebruiker controleren](#check-user-existence) | `N / A` | **&#x2713;**
[Een gebruiker uit alle groepen verwijderen](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Naar een verbinding verzenden](#send-connection) | `N / A` | **&#x2713;**
[Een verbinding aan een groep toevoegen](#add-connection-to-group) | `N / A` | **&#x2713;**
[Een verbinding uit een groep verwijderen](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Een clientverbinding sluiten](#close-connection) | `N / A` | **&#x2713;**
[Status van service](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Uitzenden naar iedereen

Versie | API HTTP-methode | Aanvraag-URL | Aanvraagbody
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Hetzelfde als hierboven

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Uitzenden naar een groep

Versie | API HTTP-methode | Aanvraag-URL | Aanvraagbody
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Hetzelfde als hierboven

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Wordt verzonden naar een gebruiker

Versie | API HTTP-methode | Aanvraag-URL | Aanvraagbody
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Hetzelfde als hierboven

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Een gebruiker aan een groep toevoegen

Versie | API HTTP-methode | Aanvraag-URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Een gebruiker uit een groep verwijderen

Versie | API HTTP-methode | Aanvraag-URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Het bestaan van de gebruiker in een groep controleren

API-versie | API HTTP-methode | Aanvraag-URL
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Statuscode respons | Beschrijving
---|---
`200` | Gebruiker bestaat
`404` | Gebruiker bestaat niet

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Een gebruiker uit alle groepen verwijderen

API-versie | API HTTP-methode | Aanvraag-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Een bericht naar een verbinding verzenden

API-versie | API HTTP-methode | Aanvraag-URL | Aanvraagtekst
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Een verbinding aan een groep toevoegen

API-versie | API HTTP-methode | Aanvraag-URL
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Een verbinding uit een groep verwijderen

API-versie | API HTTP-methode | Aanvraag-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Een clientverbinding sluiten

API-versie | API HTTP-methode | Aanvraag-URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Service Health

API-versie | API HTTP-methode | Aanvraag-URL
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Statuscode respons | Beschrijving
---|---
`200` | Service goed
`5xx` | Servicefout

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Ondervindt u problemen? Probeer de [gids voor probleemoplossing](signalr-howto-troubleshoot-guide.md) of [laat het ons weten](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een REST API gebruikt om een realtime bericht van SignalR Service naar clients te verzenden. Krijg vervolgens meer informatie over de manier waarop u Azure-functies ontwikkelt en implementeert met een SignalR Service-binding, die boven op de REST API is gebouwd.

> [!div class="nextstepaction"]
> [Azure-functies ontwikkelen met Azure SignalR Service-bindingen](signalr-quickstart-azure-functions-csharp.md)
