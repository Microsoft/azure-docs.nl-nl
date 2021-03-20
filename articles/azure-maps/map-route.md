---
title: Route beschrijving op een kaart weer geven | Microsoft Azure kaarten
description: In dit artikel leert u hoe u de instructies tussen twee locaties op een kaart kunt weer geven met behulp van de Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890714"
---
# <a name="show-directions-from-a-to-b"></a>Routebeschrijving van A naar B

In dit artikel leest u hoe u een route aanvraag maakt en hoe de route op de kaart wordt weer gegeven.

Er zijn twee manieren om dit te doen. De eerste manier is om de [Azure Maps route-API](/rest/api/maps/route/getroutedirections) via een service module op te vragen. De tweede manier is de [Fetch API](https://fetch.spec.whatwg.org/) te gebruiken om een zoek opdracht naar de [Azure Maps route-API](/rest/api/maps/route/getroutedirections)te maken. Beide manieren worden hieronder besproken.

## <a name="query-the-route-via-service-module"></a>Een query uitvoeren op de route via service module

<iframe height='500' scrolling='no' title='Route beschrijving van A naar B weer geven op een kaart (Service module)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/RBZbep/'>route beschrijving van a naar B op een kaart (Service module)</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt de eerste blok kering van een kaart object en stelt het verificatie mechanisme in voor het gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Het tweede code blok maakt een `TokenCredential` om HTTP-aanvragen te verifiëren voor Azure Maps met het toegangs token. Vervolgens wordt door gegeven `TokenCredential` aan `atlas.service.MapsURL.newPipeline()` en wordt een [pijplijn](/javascript/api/azure-maps-rest/atlas.service.pipeline) instantie gemaakt. De `routeURL` staat voor een URL voor [route](/rest/api/maps/route)bewerkingen van Azure Maps.

Met het derde code blok wordt een [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) object gemaakt en toegevoegd aan de kaart.

Het vierde code blok maakt objecten van het begin-en eind [punt](/javascript/api/azure-maps-control/atlas.data.point) en voegt deze toe aan het data source-object.

Een regel is een [functie](/javascript/api/azure-maps-control/atlas.data.feature) voor Lines Tring. Met een [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) worden regel objecten die zijn ingepakt in de  [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) , weer gegeven als lijnen op de kaart. Het vierde code blok maakt een laag en voegt deze toe aan de kaart. Zie Eigenschappen van een laag op [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Een [symbool laag](/javascript/api/azure-maps-control/atlas.layer.symbollayer) maakt gebruik van teksten of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource)zijn verpakt. De tekst of de pictogrammen worden weer gegeven als symbolen op de kaart. Met het vijfde code blok wordt een symbool laag gemaakt en toegevoegd aan de kaart.

Het zesde code blok voert een query uit op de Azure Maps Routing-service, die deel uitmaakt van de [service module](how-to-use-services-module.md). De methode [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) van de RouteURL wordt gebruikt om een route tussen de begin-en eind punten op te halen. Een verzameling geojson-functies uit het antwoord wordt vervolgens geëxtraheerd met de `geojson.getFeatures()` methode en aan de gegevens bron toegevoegd. Vervolgens wordt het antwoord weer gegeven als een route op de kaart. Zie [een regel toevoegen op de kaart](map-add-line-layer.md)voor meer informatie over het toevoegen van een regel aan de kaart.

Met het laatste code blok worden de grenzen van de kaart ingesteld met behulp van de eigenschap [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

De route query, gegevens bron, symbool, lijn lagen en camera grenzen worden in [de gebeurtenislistener gemaakt.](/javascript/api/azure-maps-control/atlas.map#events) Deze code structuur zorgt ervoor dat de resultaten pas worden weer gegeven nadat de kaart volledig is geladen.

## <a name="query-the-route-via-fetch-api"></a>De route opvragen via de API voor ophalen

<iframe height='500' scrolling='no' title='Route beschrijving van A naar B op een kaart weer geven' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>richting van de pen weer geven van a naar B op een kaart</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code bouwt het eerste code blok een kaart object in en stelt het verificatie mechanisme in voor het gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Met het tweede code blok wordt een [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) object gemaakt en toegevoegd aan de kaart.

Het derde code blok maakt de begin-en doel punten voor de route. Vervolgens worden deze toegevoegd aan de gegevens bron. U kunt [een pincode toevoegen op de kaart](map-add-pin.md) zien voor instructies over het gebruik van [addPins](/javascript/api/azure-maps-control/atlas.map).

Met een [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) worden regel objecten die zijn ingepakt in de  [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) , weer gegeven als lijnen op de kaart. Het vierde code blok maakt een laag en voegt deze toe aan de kaart. Zie Eigenschappen van een laag op [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Een [symbool laag](/javascript/api/azure-maps-control/atlas.layer.symbollayer) maakt gebruik van tekst of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) zijn verpakt als symbolen op de kaart. Met het vijfde code blok wordt een symbool laag gemaakt en toegevoegd aan de kaart. Zie Eigenschappen van een Symbol-laag op [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

In het volgende code blok `SouthWest` `NorthEast` worden de begin-en doel punten gemaakt en worden de grenzen van de kaart ingesteld met behulp van de eigenschap [SetCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

In het laatste code blok wordt de [API voor ophalen](https://fetch.spec.whatwg.org/) gebruikt om een zoek opdracht naar de [Azure Maps route-API](/rest/api/maps/route/getroutedirections)te maken. Het antwoord wordt vervolgens geparseerd. Als het antwoord is geslaagd, worden de gegevens over de breedte graad en lengte graad gebruikt voor het maken van een lijn door deze punten te verbinden. De regel gegevens worden vervolgens toegevoegd aan de gegevens bron om de route op de kaart weer te geven. Zie [een regel toevoegen op de kaart](map-add-line-layer.md) voor instructies.

De route query, gegevens bron, symbool, lijn lagen en camera grenzen worden in [de gebeurtenislistener gemaakt.](/javascript/api/azure-maps-control/atlas.map#events) We willen er ook voor zorgen dat de resultaten worden weer gegeven nadat de kaart volledig is geladen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Best practices voor het gebruik van de routeringsservice](how-to-use-best-practices-for-search.md)

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](/javascript/api/azure-maps-control/atlas.map)

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Verkeer weergeven op de kaart](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interactie met de gebeurtenissen van de kaart muis](./map-events.md)