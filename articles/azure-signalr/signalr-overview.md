---
title: Wat is Azure SignalR Service?
description: Een beter inzicht krijgen in de typische gebruiksscenario's voor Azure SignalR en de belangrijkste voordelen van Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 92da400616ace9f24da121d81798d5ebd9222e35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88921882"
---
# <a name="what-is-azure-signalr-service"></a>Wat is Azure SignalR Service?

Met de service Azure SignalR wordt het proces van het toevoegen van realtimewebfunctionaliteit aan toepassingen via HTTP vereenvoudigd. Dankzij deze realtimefunctionaliteit kunnen via de service inhoudsupdates worden gepusht naar verbonden clients, zoals een enkele webpagina of mobiele toepassing. Zo worden clients bijgewerkt zonder de server te hoeven pollen of nieuwe HTTP-bijwerkaanvragen te hoeven verzenden.


In dit artikel vindt u een overzicht van de service Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>Waarvoor wordt de service Azure SignalR gebruikt?

In elk scenario waarin gegevens in realtime van de server naar de client moeten worden gepusht, kan Azure SignalR Service worden gebruikt.

Traditionele realtime functies waarvoor vaak navraag moet worden gedaan bij de server, kunnen ook gebruikmaken van Azure SignalR Service.

Azure SignalR Service wordt in een groot aantal branches gebruikt voor toepassingstypen waarvoor realtime inhoudsupdates zijn vereist. We vermelden enkele voorbeelden waarvoor Azure SignalR Service goed kan worden gebruikt:

* **Gegevensupdates met een hoge frequentie:** games, stemmen, navragen (polling), veilingen.
* **Dashboards en controle:** bedrijfsdashboard, gegevens over de financiële markt, directe verkoopupdates, leaderboard voor games met meerdere spelers en IoT-controle.
* **Chat:** live chatroom, chatbot, online klantondersteuning, realtime winkelassistent, berichtenservice, chatten in games, enzovoort.
* **Realtime locatie op de kaart:** logistieke tracering, bijhouden van de leveringsstatus, updates van de transportstatus, GPS-apps.
* **Realtime gerichte advertenties:** gepersonaliseerde realtime push-advertenties en aanbiedingen, interactieve advertenties.
* **Samenwerkings-apps:** coauteurschap, whiteboard-apps en vergadersoftware voor teams.
* **Pushmeldingen:** sociaal netwerk, e-mail, games, reiswaarschuwingen.
* **Realtime uitzending:** live audio-/video-uitzending, live ondertiteling, vertalen, uitzending van evenementen/nieuws.
* **IoT en verbonden apparaten:** realtime IoT-statistieken, beheer op afstand, realtime status en tracering van locaties.
* **Automatisering:** realtime trigger uit upstreamgebeurtenissen.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Wat zijn de voordelen van het gebruik van Azure SignalR Service?

**Gebaseerd op standaarden:**

SignalR biedt een aantal verschillende technieken voor het bouwen van realtimewebtoepassingen. [WebSockets](https://wikipedia.org/wiki/WebSocket) is de optimale transportmethode, maar als bepaalde opties niet beschikbaar zijn, worden ook andere methoden zoals [SSE (Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) en Long Polling gebruikt. In SignalR wordt automatisch de juiste transportmethode gedetecteerd en geïnitialiseerd, afhankelijk van welke functies op de server en de client worden ondersteund.

**Ondersteuning voor systeemeigen ASP.NET Core:**

SignalR Service biedt een systeemeigen programmeertoepassing met ASP.NET Core en ASP.NET. Voor het ontwikkelen van een nieuwe SignalR-toepassing met SignalR Service of het migreren van een bestaande SignalR-toepassing naar SignalR Service is minimale inspanning vereist.
SignalR Service biedt ook ondersteuning voor de nieuwe functie van ASP.NET Core, Blazor aan serverzijde.

**Uitgebreide client-ondersteuning:**

SignalR Service kan worden gebruikt met een breed scala aan clients, zoals web- en mobiele browsers, desktop-apps, mobiele apps, serverprocessen, IoT-apparaten en gameconsoles. SignalR Service biedt SDK's in verschillende talen. Naast systeemeigen SDK's voor ASP.NET Core of ASP.NET C# biedt SignalR Service ook een SDK voor JavaScript-clients, om webclients te kunnen gebruiken, en vele JavaScript-frameworks. De SDK voor Java-clients wordt ook ondersteund voor Java-toepassingen, waaronder systeemeigen Android-apps. SignalR Service ondersteunt REST API en serverloos via integraties met Azure Functions en Event Grid.

**Verwerk grootschalige clientverbindingen:**

SignalR Service is ontworpen voor grootschalige realtime toepassingen. Bij SignalR Service kunnen meerdere instanties samenwerken om op te schalen naar miljoenen clientverbindingen. De service ondersteunt ook meerdere globale regio's voor sharding, hoge beschikbaarheid of herstel na noodgevallen.

**U hoeft SignalR niet zelf te hosten:**

Vergeleken met zelf gehoste SignalR-toepassingen hoeft u de achtervlakken waarmee de schalen en clientverbindingen worden verwerkt niet te beheren wanneer u naar SignalR Service overschakelt. De volledig beheerde service vereenvoudigt ook webtoepassingen en bespaart kosten voor hosting. SignalR Service biedt een wereldwijd bereik en een datacentrum en netwerk van wereldklasse, schaalt op naar miljoenen verbindingen, garandeert de SLA en biedt alle nalevings- en beveiligingsfunctionaliteit volgens de Azure-norm.

![Beheerde SignalR Service](./media/signalr-overview/managed-signalr-service.png)

**Uitgebreide API's voor verschillende berichtenpatronen:**

SignalR Service stelt de server in staat om berichten te verzenden naar een bepaalde verbinding, alle verbindingen of een subset van verbindingen die horen bij een specifieke gebruiker of die in een willekeurige groep zijn geplaatst.

## <a name="how-to-use-azure-signalr-service"></a>De service Azure SignalR gebruiken

Er zijn veel verschillende manieren om met Azure SignalR Service te programmeren, zoals de voorbeelden die hier worden vermeld:

- **[Een ASP.NET Core SignalR-app schalen](signalr-concept-scale-aspnet-core.md)**: integreer de service Azure SignalR met een ASP.NET Core SignalR-toepassing om uit te schalen naar duizenden verbindingen.
- **[Serverloze realtimeapps bouwen](signalr-concept-azure-functions.md)**: gebruik de integratie van Azure Functions met de service Azure SignalR om serverloze realtimetoepassingen te bouwen in talen zoals JavaScript, C# en Java.
- **[Berichten van de server verzenden naar clients via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)**: de service Azure SignalR biedt REST API waarmee via toepassingen berichten kunnen worden verzonden naar clients die zijn verbonden met de service SignalR, in elke programmeertaal die compatibel is met REST.
