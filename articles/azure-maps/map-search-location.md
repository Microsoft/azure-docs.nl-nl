---
title: Zoek resultaten weer geven op een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een zoek opdracht kunt uitvoeren met Microsoft Azure Maps Web SDK en de resultaten op de kaart kunt weer geven.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895270"
---
# <a name="show-search-results-on-the-map"></a>Zoek resultaten weer geven op de kaart

Dit artikel laat u zien hoe u kunt zoeken naar interessante locaties en de zoek resultaten op de kaart kunt weer geven.

Er zijn twee manieren om te zoeken naar een locatie van belang. Een van de manieren is een service module te gebruiken voor het maken van een zoek opdracht. De andere manier is om een zoek opdracht te maken om te [Azure Maps fuzzy Search-API](/rest/api/maps/search/getsearchfuzzy) via de [API voor ophalen](https://fetch.spec.whatwg.org/). Beide manieren worden hieronder besproken.

## <a name="make-a-search-request-via-service-module"></a>Een zoek aanvraag indienen via de service module

<iframe height='500' scrolling='no' title='Zoek resultaten weer geven op een kaart (Service module)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Zoek resultaten weer geven op een kaart (Service module)</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt het eerste blok een kaart object en stelt het verificatie mechanisme in voor het gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Het tweede code blok maakt een `TokenCredential` om HTTP-aanvragen te verifiëren voor Azure Maps met het toegangs token. Vervolgens wordt door gegeven `TokenCredential` aan `atlas.service.MapsURL.newPipeline()` en wordt een [pijplijn](/javascript/api/azure-maps-rest/atlas.service.pipeline) instantie gemaakt. De `searchURL` staat voor een URL voor [zoek](/rest/api/maps/search)bewerkingen van Azure Maps.

Met het derde code blok wordt een gegevens bron object gemaakt met behulp van de klasse [Data Source](/javascript/api/azure-maps-control/atlas.source.datasource) en worden er Zoek resultaten aan toegevoegd. Een [symbool laag](/javascript/api/azure-maps-control/atlas.layer.symbollayer) maakt gebruik van tekst of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) zijn verpakt als symbolen op de kaart.  Vervolgens wordt er een symbool laag gemaakt. De gegevens bron wordt toegevoegd aan de Symbol-laag, die vervolgens wordt toegevoegd aan de kaart.

Het vierde code blok maakt gebruik van de methode [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) in de [module service](how-to-use-services-module.md). U kunt met behulp van de zoek actie voor het maken van een vrije tekst zoeken naar interessante [rest API](/rest/api/maps/search/getsearchfuzzy) . Aanvragen ophalen voor de zoek actie voor fuzzy-API kan elke combi natie van fuzzy invoer verwerken. Een GeoJSON-functiecollectie van het antwoord wordt vervolgens geëxtraheerd met de methode `geojson.getFeatures()` en toegevoegd aan de gegevensbron. Dit leidt er automatisch toe dat de gegevens worden weergegeven op de kaart via de symboollaag.

Het laatste code blok past de camera grenzen voor de kaart aan met behulp van de eigenschap [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van de kaart.

De zoek aanvraag, de gegevens bron, de symbool-laag en de camera grenzen bevinden zich in de [gebeurtenislistener](/javascript/api/azure-maps-control/atlas.map#events) van de kaart. We willen er zeker van zijn dat de resultaten worden weer gegeven nadat de kaart volledig is geladen.


## <a name="make-a-search-request-via-fetch-api"></a>Een zoek aanvraag indienen via de API voor ophalen

<iframe height='500' scrolling='no' title='Zoek resultaten weer geven op een kaart' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Zoek resultaten weer geven op een kaart</a> op basis van Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

In de bovenstaande code maakt het eerste code blok een kaart object. Het verificatie mechanisme wordt ingesteld voor het gebruik van het toegangs token. U kunt [een overzicht maken](./map-create.md) voor instructies.

Het tweede code blok maakt een URL voor het maken van een zoek opdracht. Er worden ook twee matrices gemaakt voor het opslaan van grenzen en pincodes voor zoek resultaten.

Het derde code blok maakt gebruik van de [API voor ophalen](https://fetch.spec.whatwg.org/). De [ophaal-API](https://fetch.spec.whatwg.org/) wordt gebruikt om een aanvraag in te stellen voor [Azure Maps fuzzy Search-API](/rest/api/maps/search/getsearchfuzzy) om te zoeken naar de gewenste punten. De fuzzy Search-API kan elke combi natie van fuzzy invoer verwerken. Vervolgens wordt het antwoord verwerkt en geparseerd en worden de resultaten van de zoek opdracht toegevoegd aan de searchPins-matrix.

Met het vierde code blok wordt een gegevens bron object gemaakt met behulp van de klasse [Data Source](/javascript/api/azure-maps-control/atlas.source.datasource) . In de code voegen we Zoek resultaten toe aan het bron object. Een [symbool laag](/javascript/api/azure-maps-control/atlas.layer.symbollayer) maakt gebruik van tekst of pictogrammen voor het weer geven van op punten gebaseerde gegevens die in de [gegevens bron](/javascript/api/azure-maps-control/atlas.source.datasource) zijn verpakt als symbolen op de kaart. Vervolgens wordt er een symbool laag gemaakt. De gegevens bron wordt toegevoegd aan de Symbol-laag, die vervolgens wordt toegevoegd aan de kaart.

Met het laatste code blok wordt een [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) -object gemaakt. Het gebruikt de matrix met resultaten en vervolgens worden de camera grenzen voor de kaart aangepast met behulp van de [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)van de kaart. Vervolgens worden de resultaat pinnen weer gegeven.

De zoek aanvraag, de gegevens bron, de symbool-laag en de grenzen van de camera worden ingesteld in de [gebeurtenislistener van de](/javascript/api/azure-maps-control/atlas.map#events) kaart, om ervoor te zorgen dat de resultaten worden weer gegeven nadat de kaart volledig is geladen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van Zoekservice](how-to-use-best-practices-for-search.md)

Meer informatie over **fuzzy Search**:

> [!div class="nextstepaction"]
> [Azure Maps fuzzy Search-API](/rest/api/maps/search/getsearchfuzzy)

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](/javascript/api/azure-maps-control/atlas.map)

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Informatie ophalen uit een coördinaat](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Routebeschrijving van A naar B](map-route.md)