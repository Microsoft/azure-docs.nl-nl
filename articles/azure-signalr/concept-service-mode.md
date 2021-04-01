---
title: Service modus in azure signalerings service
description: Een overzicht van verschillende service modi in de Azure signalerings service, waarin de verschillen en de toepasselijke gebruikers scenario's worden uitgelegd
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143252"
---
# <a name="service-mode-in-azure-signalr-service"></a>Service modus in azure signalerings service

Service modus is een belang rijk concept in de Azure signalerings service. Wanneer u een nieuwe signalerings bron maakt, wordt u gevraagd een service modus op te geven:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Service modus kiezen bij maken":::

U kunt deze later ook wijzigen in het menu instellingen:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Service modus bijwerken":::

De Azure signalerings service ondersteunt momenteel drie service modi: **standaard**, **serverloos** en **klassiek**. Uw signaal bron werkt anders in verschillende modi. In dit artikel leert u wat de verschillen zijn en hoe u de juiste service modus kiest op basis van uw scenario.

## <a name="default-mode"></a>Standaard modus

De standaard modus is de standaard waarde voor de service modus wanneer u een nieuwe signalerings bron maakt. In deze modus werkt uw toepassing als een typische ASP.NET Core-signaal toepassing (of ASP.NET), waarin u een webserver hebt die als host fungeert voor een hub (hierna hub-server genoemd) en clients in realtime-communicatie met de hub-server kunnen communiceren. Het enige verschil is dat u de client en de server niet rechtstreeks verbindt, de client en server verbinding maken met de signalerings service en de service als een proxy gebruiken. Hieronder ziet u een diagram waarin de typische toepassings structuur in de standaard modus wordt geïllustreerd:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Toepassings structuur in de standaard modus":::

Als u dus een seingevings toepassing hebt en wilt integreren met de seingevings service, moet de standaard modus de juiste keuze zijn voor de meeste gevallen.

### <a name="connection-routing-in-default-mode"></a>Verbindings routering in de standaard modus

In de standaard modus zijn er WebSocket-verbindingen tussen de hub-server en de signaal service (Server verbindingen genoemd). Deze verbindingen worden gebruikt voor het overdragen van berichten tussen de server en de client. Wanneer een nieuwe client is verbonden, stuurt de signaal service de client over naar één hub-server (ervan uitgaande dat u meer dan één server hebt) via bestaande server verbindingen. De client verbinding gaat tijdens de levens duur over op dezelfde hub-server. Wanneer de client berichten verzendt, gaan ze altijd naar dezelfde hub-server. Met dit gedrag kunt u enkele statussen voor afzonderlijke verbindingen op uw hub-server veilig onderhouden. Als u bijvoorbeeld iets tussen de server en de client wilt streamen, hoeft u geen rekening te houden met het geval dat gegevens pakketten naar verschillende servers worden verzonden.

> [!IMPORTANT]
> Dit betekent ook dat in de standaard modus client geen verbinding kan worden gemaakt zonder eerst verbinding te maken met de server. Als alle hub-servers zijn losgekoppeld vanwege netwerk onderbreking of het opnieuw opstarten van de server, wordt door de client verbinding een fout bericht weer gegeven met de melding dat er geen server is verbonden. Daarom is het uw verantwoordelijkheid om ervoor te zorgen dat er ten minste één hub-server is verbonden met de signaal service (bijvoorbeeld meerdere hub-servers hebben en ervoor te zorgen dat ze niet op hetzelfde moment offline worden gezet voor zaken als onderhoud).

Dit routerings model betekent ook wanneer een hub-server offline gaat, de verbindingen die de server omleiden worden verwijderd. U moet er dus voor zorgen dat er verbinding wordt gemaakt wanneer de hub-server offline is voor onderhoud en het opnieuw verbinden van een verbinding, zodat deze geen negatieve gevolgen heeft voor uw toepassing.

## <a name="serverless-mode"></a>Serverloze modus

De serverloze modus, zoals de naam al aangeeft, is een modus die geen enkele hub-server kan hebben. Vergelijken met de standaard modus, in deze modus client is geen hub-server nodig om verbinding te kunnen krijgen. Alle verbindingen zijn verbonden met de service in een ' serverloze ' modus en de service is verantwoordelijk voor het onderhouden van client verbindingen, zoals het verwerken van client pings (in de standaard modus).

Er is in deze modus geen server verbinding (als u probeert de Service-SDK te gebruiken om verbinding te maken met de server, wordt een fout bericht weer geven). Daarom is er ook geen verbindings Routering en Server-client persistentie (zoals beschreven in de sectie standaard modus). Maar u kunt nog steeds een toepassing aan de server zijde hebben om berichten naar clients te pushen. U kunt dit op twee manieren doen, [rest-api's](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) gebruiken voor eenmalig verzenden of via een WebSocket-verbinding, zodat u meerdere berichten efficiënter kunt verzenden (Houd er rekening mee dat deze WebSocket-verbinding anders is dan de server verbinding).

> [!NOTE]
> Zowel de REST API als de WebSocket-methode worden ondersteund in de service [Management SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)van signalering. Als u een andere taal dan .NET gebruikt, kunt u ook hand matig de REST-Api's aanroepen volgens deze [specificatie](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md).
>
> Als u gebruikmaakt van Azure Functions, kunt u de [service bindingen van de signalering voor Azure functions](../azure-functions/functions-bindings-signalr-service.md) (hierna functie bindingen genoemd) gebruiken om berichten te verzenden als een uitvoer binding.

Het is ook mogelijk dat uw server toepassing berichten en verbindings gebeurtenissen van clients ontvangt. De service levert berichten en verbindings gebeurtenissen aan vooraf geconfigureerde eind punten (upstream genoemd) met behulp van webhooks. Vergelijken met de standaard modus is het mogelijk dat persistentie en HTTP-aanvragen minder efficiënt zijn dan WebSocket-verbindingen.

Zie dit [document](./concept-upstream.md)voor meer informatie over het configureren van upstream.

Hieronder ziet u een diagram dat laat zien hoe de serverloze modus werkt:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Toepassings structuur in serverloze modus":::

> [!NOTE]
> Let op: in de standaard modus kunt u ook gebruikmaken van REST API/Management SDK/functie binding om berichten rechtstreeks naar de client te sturen als u niet via de hub-server wilt door gaan. Client verbindingen in de standaard modus worden echter nog steeds verwerkt door de hub-servers en de upstream werkt niet in deze modus.

## <a name="classic-mode"></a>Klassieke modus

Klassiek is een gemengde modus met een standaard modus en een serverloos. In deze modus wordt de verbindings modus bepaald door de vraag of er een hub-server is verbonden wanneer de client verbinding tot stand is gebracht. Als er een hub-server is, wordt de client verbinding doorgestuurd naar een hub-server. Anders wordt er een serverloze modus ingevoerd waarbij client-naar-server bericht niet kan worden geleverd aan de hub-server. Dit leidt tot een aantal verschillen, bijvoorbeeld als alle hub-servers voor korte tijd niet beschikbaar zijn, de client verbindingen die tijdens die tijd zijn gemaakt in de serverloze modus en kunnen geen berichten verzenden naar de hub-server.

> [!NOTE]
> De klassieke modus is hoofd zakelijk bedoeld voor achterwaartse compatibiliteit voor de toepassingen die zijn gemaakt voordat de standaard modus en de serverloos worden uitgevoerd. Het wordt ten zeerste aanbevolen deze modus niet meer te gebruiken. Voor nieuwe toepassingen kiest u standaard of op een server gebaseerd op uw scenario. Voor bestaande toepassingen is het ook raadzaam om uw gebruiks voorbeelden te controleren en de juiste service modus te kiezen.

De klassieke modus biedt ook geen ondersteuning voor sommige nieuwe functies zoals upstream in de serverloze modus.

## <a name="choose-the-right-service-mode"></a>De juiste service modus kiezen

U moet nu inzicht hebben in de verschillen tussen service modi en weten hoe u deze kunt kiezen. Zoals u al hebt geleerd in de vorige sectie, wordt de klassieke modus niet aanbevolen en moet u alleen kiezen tussen standaard en serverloos. Hier vindt u een aantal tips die u kunnen helpen bij het kiezen van de juiste keuze voor nieuwe toepassingen en het buiten gebruik stellen van de klassieke modus voor bestaande toepassingen.

* Als u al bekend bent met de werking van de seingevings bibliotheek en wilt overstappen van een zelf-hostende Signa lering om de Azure signalerings service te gebruiken, kiest u standaard modus. De standaard modus werkt op dezelfde manier als de zelf-hostende Signa lering (en u kunt hetzelfde programmeer model gebruiken in de seingevings bibliotheek), de signaal service fungeert alleen als een proxy tussen clients en hub-servers.

* Als u een nieuwe toepassing maakt en geen hub-server-en server verbindingen wilt onderhouden, kiest u serverloze modus. Deze modus werkt meestal samen met Azure Functions, zodat u geen server hoeft te onderhouden. U kunt nog steeds beschikken over duplex communicatie (met REST API/Management SDK/functie binding + upstream), maar het programmeer model wijkt af van de seingevings bibliotheek.

* Als u beide hub-servers hebt voor het uitvoeren van client verbindingen en back-end-toepassingen om berichten rechtstreeks te pushen naar clients (bijvoorbeeld door middel van REST API), moet u altijd de standaard modus kiezen. Houd er wel van uit dat het belangrijkste verschil tussen de standaard modus en de serverloze computer, of u hub-servers hebt en hoe client verbindingen worden gerouteerd. REST API/Management SDK/function binding kan in beide modi worden gebruikt.

* Als u een gemengd scenario hebt, bijvoorbeeld als u twee verschillende hubs hebt op dezelfde Signa lering-resource, die wordt gebruikt als een traditionele seingevings hub en de andere die wordt gebruikt met Azure Functions en geen hub-server heeft, moet u deze in de standaard modus en één in de serverloze modus van elkaar nemen.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over het gebruik van de standaard-en serverloze modus:

* [Interne werking van Azure SignalR Service](signalr-concept-internals.md)

* [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](signalr-concept-serverless-development-config.md)