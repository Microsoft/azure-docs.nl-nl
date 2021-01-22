---
title: 'Zelfstudie: Een web-app migreren vanuit Bing Kaarten | Microsoft Azure Maps'
description: Zelfstudie over hoe u een web-app migreert vanuit Bing Kaarten naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: db53e4407674abc1e6c81090dc4a50afa784940d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684822"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>Zelfstudie: Een web-app migreren vanuit Bing Kaarten

Web-apps die Bing Kaarten gebruiken, maken vaak gebruik van de Bing Kaarten v8 JavaScript-SDK. De Azure Maps Web-SDK is de geschikte op Azure gebaseerde SDK om naar te migreren. Met de Azure Maps Web-SDK kunt u interactieve kaarten aanpassen met uw eigen inhoud en beeldmateriaal om weer te geven in uw web- of mobiele toepassingen. Dit besturingselement maakt gebruik van WebGL, zodat u grote gegevenssets kunt weergeven met hoge prestaties. U kunt ontwikkelen met deze SDK met behulp van JavaScript of TypeScript. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een kaart laden
> * Een kaar lokaliseren
> * Punaises, polylijnen en polygonen toevoegen.
> * Informatie weergeven in een pop-up of infobox
> * KML- en GeoJSON-gegevens laden en weergeven
> * Punaises clusteren
> * Een tegellaag als overlay gebruiken
> * Verkeergegevens weergeven
> * Een terrein-overlay toevoegen

Als u een bestaande webtoepassing wilt migreren, controleert u of deze een opensource-bibliotheek voor kaartbesturingselementen gebruikt, zoals Cesium, Leaflet of OpenLayers. Als dat het geval is en u liever deze bibliotheek wilt blijven gebruiken, kunt u deze verbinden met de Azure Maps-tegelservices ([wegtegels](/rest/api/maps/render/getmaptile) \| [satelliettegels](/rest/api/maps/render/getmapimagerytile)). De onderstaande koppelingen bieden meer informatie over het gebruik van Azure Maps in een aantal veelgebruikte opensource-bibliotheken voor kaartbesturingselementen.

* Cesium: een 3D-kaartbesturingselement voor het web. [Codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentatie](https://cesiumjs.org/)
* Leaflet: een lichtgewicht 2D-kaartbesturingselement voor het web. [Codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentatie](https://leafletjs.com/)
* OpenLayers: een 2D-kaartbesturingselement voor het web dat projecties ondersteunt. [Codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentatie](https://openlayers.org/)

Bij het ontwikkelen met behulp van een JavaScript-framework kan een van de volgende opensource-projecten nuttig zijn:

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): Angular 10-wrapper rond Azure-kaarten.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): een Azure Maps Blazor-onderdeel.
* [Azure Maps React-onderdeel](https://github.com/WiredSolutions/react-azure-maps): een react-wrapper voor het Azure Maps-besturingselement.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps): een Azure Maps-onderdeel voor de Vue-toepassing.

## <a name="prerequisites"></a>Vereisten

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="key-features-support"></a>Belangrijkste ondersteunde functies

De volgende tabel bevat een overzicht van de belangrijkste API-functies in de Bing Kaarten v8 JavaScript-SDK en de ondersteuning van een vergelijkbare API in de Azure Maps Web-SDK.

| Bing Kaarten-functie        | Ondersteuning in Azure Maps Web-SDK                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Punaises                 | ✓                                                                                      |
| Clustering van punaises       | ✓                                                                                      |
| Polylijnen en veelhoeken     | ✓                                                                                      |
| Overlays voor terreinen          | ✓                                                                                      |
| Heatmaps                | ✓                                                                                      |
| Tegellagen              | ✓                                                                                      |
| KML-laag                | ✓                                                                                      |
| Omtreklaag            | [Voorbeelden](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Gegevens-binninglaag       | [Voorbeelden](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| Laag voor tegels met animatie      | Opgenomen in de opensource-module Azure Maps [Animation](https://github.com/Azure-Samples/azure-maps-animations) |
| Tekenprogramma's            | ✓                                                                                      |
| Service voor geocodering         | ✓                                                                                      |
| Service voor aanwijzingen       | ✓                                                                                      |
| Service voor afstandsmatrix  | ✓                                                                                      |
| Service voor ruimtelijke gegevens     | N.v.t.                                                                                    |
| Satelliet-/luchtfoto's | ✓                                                                                      |
| Overzichtsbeelden         | Gepland                                                                                |
| Straatbeelden       | Gepland                                                                                |
| GeoJSON-ondersteuning          | ✓                                                                                      |
| GeoXML-ondersteuning           | ✓                                                                                      |
| Well-Known Text-ondersteuning  | ✓                                                                                      |
| Aangepaste kaartstijlen        | Gedeeltelijk                                                                                |

Azure Maps heeft ook veel extra [opensource-modules voor de web-SDK](open-source-projects.md#open-web-sdk-modules) waarmee de mogelijkheden worden uitgebreid.

## <a name="notable-differences-in-the-web-sdks"></a>Belangrijke verschillen tussen de web-SDK's

Dit zijn enkele van de belangrijkste verschillen tussen de Bing Kaarten- en Azure Maps Web-SDK waarmee u rekening moet houden:

* Naast het leveren van een gehost eindpunt om toegang te krijgen tot de Azure Maps Web-SDK is er een NPM-pakket beschikbaar om de Web-SDK desgewenst in apps in te sluiten. Raadpleeg deze [documentatie](./how-to-use-map-control.md) voor meer informatie. Dit pakket bevat ook TypeScript-definities.
* Bing Kaarten biedt twee gehoste vertakkingen van de SDK: Release en Experimental. De Experimental-vertakking kan meerdere updates per dag ontvangen wanneer er nieuwe ontwikkelingen plaatsvinden. Azure Maps host alleen een releasevertakking. Er worden echter experimentele functies als aangepaste modules gemaakt in het opensource-project met codevoorbeelden voor Azure Maps. Ook had Bing Kaarten een bevroren vertakking die minder vaak werd bijgewerkt, waardoor er minder kans was dat door wijzigingen vanwege de release fouten werden veroorzaakt. In Azure Maps kunt u de NPM-module gebruiken en verwijzen naar een eerdere release van een secundaire versie.

> [!TIP]
> Met Azure Maps worden zowel geminimaliseerde versies van de SDK gepubliceerd als versies waarvan de minimalisatie ongedaan is gemaakt. Verwijder gewoon `.min` uit de bestandsnamen. De versie waarbij de minimalisatie ongedaan is gemaakt, is handig bij het oplossen van problemen, maar zorg ervoor dat u de geminimaliseerde versie in productie gebruikt om van de kleinere bestandsgrootte te profiteren.

* Nadat u een exemplaar van de Map-klasse hebt gemaakt in Azure Maps, moet de code wachten op de gebeurtenis `ready` of `load` van de kaart om te worden gestart voordat er interactie met de kaart kan plaatsvinden. Deze gebeurtenissen zorgen ervoor dat alle kaartresources zijn geladen en klaar zijn voor gebruik.
* Beide platforms gebruiken een soortgelijk tegelsysteem voor de basiskaarten, maar de tegels in Bing Kaarten zijn 256 pixels groot terwijl de tegels in Azure Maps 512 pixels groot zijn. Als u dezelfde kaartweergave in Azure Maps en Bing Kaarten wilt hebben, moet van het zoomniveau in Bing Kaarten 1 worden afgetrokken van het zoomniveau in Azure Maps.
* Coördinaten in Bing Kaarten worden aangeduid als `latitude, longitude` terwijl in Azure Maps `longitude, latitude` wordt gebruikt. Deze notatie komt overeen met het standaard gebruikte `[x, y]` dat wordt gevolgd door de meeste GIS-platforms.

* Vormen in de Web-SDK van Azure Maps zijn gebaseerd op het GeoJSON-schema. Helperklassen worden weergegeven via de [naamruimte atlas.data](/javascript/api/azure-maps-control/atlas.data). Ook bestaat er een klasse [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) die kan worden gebruikt om GeoJSON-objecten te verpakken en om deze gemakkelijk te kunnen bijwerken en onderhouden op een manier waarop gegevens kunnen worden verbonden.
* Coördinaten in Azure Maps worden gedefinieerd als Position-objecten die kunnen worden opgegeven als een eenvoudige getallenmatrix in de notatie `[longitude, latitude]` of `new atlas.data.Position(longitude, latitude)`.

> [!TIP]
> De klasse Position heeft een statische helperfunctie voor het importeren van coördinaten met de notatie `latitude, longitude`. De [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position)-functie kan vaak de `new Microsoft.Maps.Location`-functie in de Bing Kaarten-code vervangen.

* In plaats van stijlgegevens op te geven voor elke vorm die wordt toegevoegd aan de kaart, houdt Azure Maps stijlen gescheiden van de gegevens. Gegevens worden opgeslagen in gegevensbronnen en worden verbonden met renderinglagen die door Azure Maps-code worden gebruikt om de gegevens weer te geven. Deze aanpak leidt tot verbeterde prestaties. Daarnaast ondersteunen veel lagen gegevensgestuurde stijlen, waarbij bedrijfslogica kan worden toegevoegd aan laagstijlopties waarmee wordt gewijzigd hoe afzonderlijke vormen worden weergegeven in een laag op basis van de eigenschappen die in de vorm zijn gedefinieerd.
* Azure Maps biedt een aantal handige ruimtelijke wiskundige functies in de `atlas.math`-naamruimte, maar deze verschillen van die in de ruimtelijke wiskundige Bing Kaarten-module. Het belangrijkste verschil is dat Azure Maps geen ingebouwde functies biedt voor binaire bewerkingen zoals union en doorsnede, maar omdat Azure Maps is gebaseerd op de open standaard GeoJSON, zijn veel opensource-bibliotheken beschikbaar. Een populaire optie die goed werkt voor Azure Maps en die heel veel ruimtelijke wiskundige mogelijkheden biedt, is [turf js](http://turfjs.org/).

Zie ook de [Azure Maps-woordenlijst](./glossary.md) voor een gedetailleerd overzicht van de terminologie voor Azure Maps.

## <a name="web-sdk-side-by-side-examples"></a>Voorbeelden van Web SDK naast elkaar gelegd

Hier volgen diverse codevoorbeelden voor elk platform die u in algemene scenario's helpen bij het migreren van uw webtoepassing vanuit de Bing Kaarten v8 JavaScript SDK naar de Azure Maps Web-SDK. Codevoorbeelden die betrekking hebben op webtoepassingen, worden geleverd in JavaScript. Azure Maps biedt echter ook TypeScript-definities als een extra optie via een [NPM-module](./how-to-use-map-control.md).

**Onderwerpen**

* [Een kaart laden](#load-a-map)
* [Lokaliseren van de kaart](#localizing-the-map)
* [De kaartweergave instellen](#setting-the-map-view)
* [Een punaise toevoegen](#adding-a-pushpin)
* [Een aangepaste punaise toevoegen](#adding-a-custom-pushpin)
* [Een polylijn toevoegen](#adding-a-polyline)
* [Een veelhoek toevoegen](#adding-a-polygon)
* [Een informatievenster weergeven](#display-an-infobox)
* [Clustering van punaises](#pushpin-clustering)
* [Een heatmap toevoegen](#add-a-heat-map)
* [Een tegellaag als overlay gebruiken](#overlay-a-tile-layer)
* [Verkeergegevens weergeven](#show-traffic-data)
* [Een terrein-overlay toevoegen](#add-a-ground-overlay)
* [KML-gegevens toevoegen aan de kaart](#add-kml-data-to-the-map)
* [Tekenhulpmiddelen toevoegen](#add-drawing-tools)

### <a name="load-a-map"></a>Een kaart laden

Voor het laden van een kaart worden in beide SDK's dezelfde stappen gevolgd;

* Voeg een verwijzing toe naar de kaart-SDK.
* Voeg een tag `div` toe aan de hoofdtekst van de pagina die als tijdelijke aanduiding voor de kaart fungeert.
* Maak een JavaScript-functie die wordt aangeroepen wanneer de pagina is geladen.
* Maak een instantie van de betreffende Map-klasse.

**Enkele belangrijke verschillen**

* Voor Bing Kaarten moet een accountsleutel worden opgegeven in de scriptverwijzing van de API of als kaartoptie. Verificatiereferenties voor Azure Maps worden opgegeven als opties van de Map-klasse. Deze kunnen bestaan uit een abonnementssleutel of Azure Active Directory-gegevens.
* Bing Kaarten maakt gebruik van een callback-functie in de scriptverwijzing van de API die wordt gebruikt om een initialisatiefunctie aan te roepen voor het laden van de kaart. Met Azure Maps moet de gebeurtenis onload van de pagina worden gebruikt.
* Wanneer u een id gebruikt om te verwijzen naar het `div`-element waarin de kaart wordt weergegeven, gebruikt Bing Kaarten een HTML-selector (dat wil zeggen `#myMap`), terwijl Azure Maps alleen de id-waarde gebruikt (bijvoorbeeld `myMap`).
* Coördinaten in Azure Maps worden gedefinieerd als Position-objecten die kunnen worden opgegeven als een eenvoudige getallenmatrix in de notatie `[longitude, latitude]`.
* Het zoomniveau in Azure Maps is één niveau lager dan het Bing Kaarten-voorbeeld vanwege het verschil in tegelsysteemgrootten tussen de platforms.
* Met Azure Maps worden standaard geen navigatiebesturingselementen, zoals zoomknoppen en knoppen voor kaartstijlen, toegevoegd aan het kaartcanvas. Er zijn echter wel besturingselementen beschikbaar voor het toevoegen van een kaartstijlkiezer, zoomknoppen, besturingselementen voor kompas- of rotatieregeling en kantelregeling.
* Er wordt een gebeurtenis-handler toegevoegd in Azure Maps om de gebeurtenis `ready` van de kaartinstantie te controleren. Deze wordt geactiveerd wanneer het laden van de WebGL-context voor de kaart en alle benodigde resources is voltooid. In deze gebeurtenis-handler kunt u alle gewenste code voor na het laden toevoegen.

In de onderstaande voorbeelden ziet u hoe u een basiskaart zo kunt laden dat New York in het midden wordt weergegeven (op lengtegraad -73,985, breedtegraad 40,747) en het zoomniveau in Bing Kaarten 12 is.

**Vóór: Bing Kaarten**

De volgende code is een voorbeeld van hoe u een Bing-kaart gecentreerd en ingezoomd op een locatie weergeeft.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

![Kaart van Bing Kaarten](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**Na: Azure Maps**

In de volgende code is te zien hoe een kaart wordt geladen met dezelfde weergave in Azure Maps, samen met een besturingselement voor de kaartstijl en zoomknoppen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

![Kaart van Azure Maps](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

U vindt [hier](how-to-use-map-control.md) uitgebreide informatie over het instellen en gebruiken van het Azure Maps-kaartbesturingselement in een web-app.

> [!TIP]
> Met Azure Maps worden zowel geminimaliseerde versies van de SDK gepubliceerd als versies waarvan de minimalisatie ongedaan is gemaakt. Verwijder `.min` uit de bestandsnamen. De versie waarbij de minimalisatie ongedaan is gemaakt, is handig bij het oplossen van problemen, maar zorg ervoor dat u de geminimaliseerde versie in productie gebruikt om van de kleinere bestandsgrootte te profiteren.

**Aanvullende bronnen**

* Azure Maps biedt ook navigatiebesturingselementen voor het draaien en kantelen van de kaartweergave, zoals [hier](map-add-controls.md) wordt beschreven.

### <a name="localizing-the-map"></a>Lokaliseren van de kaart

Lokalisatie is belangrijk als uw doelgroep zich in meerdere landen/regio's bevindt of verschillende talen spreekt.

**Vóór: Bing Kaarten**

Voor het lokaliseren van Bing Kaarten worden de taal en regio opgegeven met behulp van de parameters `setLang` en `UR` die worden toegevoegd aan de `<script>`-tagverwijzing naar de API. Bepaalde functies in Bing Kaarten zijn alleen beschikbaar in bepaalde markten. Om die reden wordt de markt van de gebruiker opgegeven met behulp van de parameter `setMkt`.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Hier volgt een voorbeeld van Bing Kaarten waarbij de taal is ingesteld op fr-FR.

![Gelokaliseerde kaart van Bing Kaarten](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**Na: Azure Maps**

Azure Maps biedt alleen opties voor het instellen van de taal en de regionale weergave van de kaart. Er wordt geen marktparameters gebruikt om functies te beperken. Er zijn twee verschillende manieren om de taal en de regionale weergave van de kaart in te stellen. De eerste optie is om deze informatie toe te voegen aan de globale `atlas`-naamruimte. Dit leidt ertoe dat voor alle kaartbesturingselementen in uw app standaard deze waarden worden ingesteld. Met de volgende code stelt u de taal in op Frans (fr-FR) en de regionale weergave op `"Auto"`:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

De tweede optie is om deze informatie door te geven aan de kaartopties bij het laden van de kaart, zoals:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Met Azure Maps kunt u op dezelfde pagina meerdere kaartexemplaren laden met verschillende taal- en regio-instellingen. Verder is het ook mogelijk om deze instellingen bij te werken in de kaart nadat deze is geladen. Bekijk [hier](./supported-languages.md) een gedetailleerde lijst met ondersteunde talen in Azure Maps.

Hier volgt een voorbeeld van Azure Maps waarbij de taal is ingesteld op 'fr-FR' en de gebruikersregio op 'fr'.

![Gelokaliseerde kaart van Azure Maps](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>De kaartweergave instellen

Dynamische kaarten in zowel Bing Kaarten als Azure Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties door de juiste functies in JavaScript aan te roepen. De onderstaande voorbeelden laten zien hoe we satellietluchtfoto's kunnen weergeven op de kaart, de kaart op een locatie kunnen centreren met coördinaten (lengtegraad: -111,0225, breedtegraad: 35,0272) en het zoomniveau kunnen wijzigen in 15 in Bing Kaarten.

> [!NOTE]
> In Bing Kaarten worden tegels van 256 pixels gebruikt, terwijl in Azure Maps een grotere tegel van 512 pixels wordt gebruikt. Hierdoor zijn er in Azure Maps minder netwerkaanvragen nodig dan in Bing Kaarten om hetzelfde kaartgebied te laden. Als gevolg van de manier waarop tegelpiramides werken in kaartbesturingselementen, moet u bij de grotere tegels in Azure Maps het zoomniveau dat in Bing Kaarten wordt gebruikt met 1 verminderen om hetzelfde zichtbare gebied te krijgen.

**Vóór: Bing Kaarten**

Het Bing Kaarten-kaartbesturingselement kan programmatisch worden verplaatst met behulp van de functie `setView` waarmee u het midden van de kaart en een zoomniveau kunt opgeven.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Bing Kaarten - kaartweergave instellen](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**Na: Azure Maps**

In Azure Maps kan de kaartpositie programmatisch worden gewijzigd met behulp van de functie `setCamera` van de kaart en de kaartstijl kan worden gewijzigd met behulp van de functie `setStyle`. Vergeet niet dat de coördinaten in Azure Maps de notatie 'lengtegraad, breedtegraad' hebben en dat de waarde voor het zoomniveau met 1 wordt verlaagd.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Azure Maps - kaartweergave instellen](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**Aanvullende bronnen**

-   [Een kaartstijl kiezen](./choose-map-style.md)
-   [Ondersteunde kaartstijlen](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Een punaise toevoegen

In Azure Maps kunt u op verschillende manieren puntgegevens weergeven op de kaart;

* HTML-markeringen: punten weergeven met behulp van traditionele DOM-elementen. HTML-markeringen ondersteunen slepen.
* Symboollaag: punten weergeven met een pictogram en/of tekst binnen de WebGL-context.
* Bellenlaag: punten weergeven als cirkels op de kaart. De stralen van de cirkels kunnen worden geschaald op basis van eigenschappen in de gegevens.

Zowel symbool- als bellenlagen worden weergegeven binnen de WebGL-context en kunnen zeer grote hoeveelheden punten op de kaart weergeven. Voor deze lagen moeten gegevens worden opgeslagen in een gegevensbron. Gegevensbronnen en renderinglagen moeten worden toegevoegd aan de kaart nadat de gebeurtenis `ready` is geactiveerd. HTML-markeringen worden op de pagina weergegeven als DOM-elementen en maken geen gebruik van een gegevensbron. Hoe meer DOM-elementen er op een pagina staan, des te langzamer de pagina wordt. Als er meer dan een paar honderd punten moeten worden weergegeven op een kaart, is het beter om een van de renderinglagen te gebruiken.

In de onderstaande voorbeelden wordt een markering aan de kaart toegevoegd op lengtegraad -0,2, breedtegraad 51,5 met het cijfer 10 als label.

**Vóór: Bing Kaarten**

Bij Bing Kaarten worden markeringen aan de kaart toegevoegd met behulp van de `Microsoft.Maps.Pushpin`-klasse*. Vervolgens worden er punaises aan de kaart toegevoegd met een van de twee mogelijke functies.

De eerste functie is het maken van een laag, het invoegen van de punaise in de laag en de laag vervolgens toevoegen aan de `layers`-eigenschap van de kaart.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

Bij de tweede functie wordt deze toegevoegd met de `entities`-eigenschap van de kaart. Deze functie is gemarkeerd als afgeschaft in de documentatie voor Bing Kaarten v8, maar de functie kan nog wel worden gebruikt voor basisscenario's.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![Bing Kaarten - punaise toevoegen](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**Na: Azure Maps met HTML-markeringen**

In Azure Maps kunnen HTML-markeringen worden gebruikt om eenvoudig een punt op de kaart weer te geven. HTML-markeringen worden aanbevolen voor eenvoudige apps waarin alleen een klein aantal punten op de kaart hoeft te worden weergegeven. Als u een HTML-markering wilt gebruiken, maakt u een exemplaar van de klasse `atlas.HtmlMarker`, stelt u de tekst- en positie-opties in en voegt u de markering aan de kaart toe met behulp van de functie `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps - markering toevoegen](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Na: Azure Maps met een symboollaag**

Wanneer u een symboollaag gebruikt, moeten de gegevens worden toegevoegd aan een gegevensbron en moet de gegevensbron aan de laag worden gekoppeld. Daarnaast moeten de gegevensbron en laag worden toegevoegd aan de kaart nadat de gebeurtenis `ready` is geactiveerd. Als u een unieke tekstwaarde wilt weergeven boven een symbool, moeten de tekstgegevens worden opgeslagen als een eigenschap van het gegevenspunt en moet naar die eigenschap worden verwezen in de optie `textField` van de laag. Deze benadering betekent iets meer werk dan het gebruik van HTML-markeringen, maar geeft wel betere prestaties.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps - symboollaag toevoegen](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Aanvullende bronnen**

-   [Een gegevensbron maken](./create-data-source-web-sdk.md)
-   [Een symboollaag toevoegen](./map-add-pin.md)
-   [Een bellenlaag toevoegen](./map-add-bubble-layer.md)
-   [Puntgegevens clusteren](./clustering-point-data-web-sdk.md)
-   [HTML-markeringen toevoegen](./map-add-custom-html.md)
-   [Gegevensgestuurde stijlexpressies gebruiken](./data-driven-style-expressions-web-sdk.md)
-   [Pictogramopties voor symboollaag](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Tekstoptie voor symboollaag](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTMLMarker-klasse](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opties voor HTML-markeringen](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Een aangepaste punaise toevoegen

Er kunnen aangepaste afbeeldingen worden gebruikt om punten op een kaart weer te geven. De volgende afbeelding wordt gebruikt in de onderstaande voorbeelden. Hierbij wordt gebruikgemaakt van een aangepaste afbeelding om een punt op de kaart weer te geven op breedtegraad 51,5, lengtegraad -0,2 waarbij de positie van de markering zo wordt verschoven dat het punt van de punaise de juiste positie op de kaart aangeeft.

| ![Azure Maps - punaise toevoegen](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**Vóór: Bing Kaarten**

In Bing Kaarten wordt een aangepaste markering gemaakt door een URL door te geven aan een afbeelding in de `icon`-opties van een punaise. De optie `anchor` wordt gebruikt om de punt van de punaise uit te lijnen met de coördinaat op de kaart. De ankerwaarde in Bing Kaarten is relatief ten opzichte van de linkerbovenhoek van de afbeelding.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![Bing Kaarten - aangepaste punaise toevoegen](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**Na: Azure Maps met HTML-markeringen**

Als u een HTML-markering in Azure Maps wilt aanpassen, geeft u een HTML-`string` of `HTMLElement` door aan de optie `htmlContent` van de markering. In Azure Maps wordt een `anchor`-optie gebruikt om de relatieve positie van de markering ten opzichte van de positiecoördinaat op te geven met behulp van een van de negen gedefinieerde referentiepunten: center, top, bottom, left, right, top-left, top-right, bottom-left en bottom-right. De inhoud is verankerd en is standaard ingesteld op bottom (het midden aan de onderzijde aan de HTML-inhoud). Om het gemakkelijker te maken code te migreren vanuit Bing Kaarten, stelt u het anker in op top-left en gebruikt u vervolgens de optie `offset` met de offset die wordt gebruikt in Bing Kaarten. De offsets in Azure Maps worden in de tegenovergestelde richting verplaatst van de offsets in Bing Kaarten. Vermenigvuldig deze dus met -1.

> [!TIP]
> Voeg `pointer-events:none` als stijl toe aan de HTML-inhoud om het standaardgedrag voor slepen in Microsoft Edge uit te schakelen, waardoor een ongewenst pictogram wordt weergegeven.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps - aangepaste markering toevoegen](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**Na: Azure Maps met een symboollaag**

In de symboollagen van Azure Maps worden ook aangepaste afbeeldingen ondersteund, maar eerst moet de afbeelding worden geladen in de kaartresources en moet er een unieke id aan worden toegewezen. Vervolgens kan de symboollaag naar deze id verwijzen. Het symbool kan worden verschoven naar het juiste punt op de afbeelding met behulp van de optie `offset` van het pictogram. In Azure Maps wordt een `anchor`-optie gebruikt om de relatieve positie van het symbool ten opzichte van de positiecoördinaat op te geven met behulp van een van de negen gedefinieerde referentiepunten: center, top, bottom, left, right, top-left, top-right, bottom-left en bottom-right. De inhoud is standaard verankerd en ingesteld op bottom (het midden aan de onderzijde aan de HTML-inhoud). Om het gemakkelijker te maken code te migreren vanuit Bing Kaarten, stelt u het anker in op top-left en gebruikt u vervolgens de optie `offset` met de offset die wordt gebruikt in Bing Kaarten. De offsets in Azure Maps worden in de tegenovergestelde richting verplaatst van de offsets in Bing Kaarten. Vermenigvuldig deze dus met -1.

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Kaarten - aangepaste symboollaag toevoegen](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> Als u een geavanceerde aangepaste weergave van punten wilt maken, gebruikt u meerdere renderinglagen tegelijk. Als u bijvoorbeeld meerdere punaises met hetzelfde pictogram op verschillende gekleurde cirkels wilt hebben, kunt u in plaats van een aantal afbeeldingen te maken voor elke kleur, een symboollaag weergeven boven op een bellenlaag en deze naar dezelfde gegevensbron laten verwijzen. Deze benadering is efficiënter dan het maken en onderhouden van een aantal verschillende afbeeldingen.

**Aanvullende bronnen**

-   [Een gegevensbron maken](./create-data-source-web-sdk.md)
-   [Een symboollaag toevoegen](./map-add-pin.md)
-   [HTML-markeringen toevoegen](./map-add-custom-html.md)
-   [Gegevensgestuurde stijlexpressies gebruiken](./data-driven-style-expressions-web-sdk.md)
-   [Pictogramopties voor symboollaag](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Tekstoptie voor symboollaag](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTMLMarker-klasse](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opties voor HTML-markeringen](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Een polylijn toevoegen

Polylijnen worden gebruikt om een lijn of pad op de kaart aan te geven. In de onderstaande voorbeelden ziet u hoe u een gestippelde polylijn op de kaart maakt.

**Vóór: Bing Kaarten**

In Bing Kaarten worden bij de klasse Polyline een matrix van locaties en een set opties gebruikt.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Bing Kaarten - polylijn](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**Na: Azure Maps**

In Azure Maps wordt met polylijnen verwezen naar de meer voorkomende georuimtelijke termen `LineString`- of `MultiLineString`-objecten. Deze objecten kunnen worden toegevoegd toe aan een gegevensbron en weergegeven met behulp van een lijnlaag. De opties voor de kleur, breedte en streepjesmatrix van de kwaststreek zijn bijna identiek tussen de platforms.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Azure Maps - lijn](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**Aanvullende bronnen**

-   [Lijnen toevoegen aan de kaart](./map-add-line-layer.md)
-   [Opties voor lijnlaag](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Gegevensgestuurde stijlexpressies gebruiken](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Veelhoeken worden gebruikt om een gebied op de kaart weer te geven. Azure Maps en Bing Kaarten bieden vrijwel dezelfde ondersteuning voor veelhoeken. In de onderstaande voorbeelden ziet u hoe u een veelhoek maakt die een driehoek vormt op basis van de middelste coördinaat van de kaart.

**Vóór: Bing Kaarten**

In Bing Kaarten worden voor de klasse Polygon een matrix van coördinaten of coördinaatringen en een set opties gebruikt.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Bing Kaarten - veelhoek](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Na: Azure Maps**

In Azure Maps kunnen Polygon- en MultiPolygon-objecten worden toegevoegd aan een gegevensbron en met behulp van lagen worden weergegeven op de kaart. Het gebied van een veelhoek kan worden weergegeven in een polygoonlaag. De omtrek van een veelhoek kan worden weergegeven met behulp van een lijnlaag.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Azure Maps - veelhoek](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Aanvullende bronnen**

-   [Een veelhoek toevoegen aan de kaart](./map-add-shape.md#use-a-polygon-layer)
-   [Een cirkel toevoegen aan de kaart](./map-add-shape.md#add-a-circle-to-the-map)
-   [Opties voor polygoonlaag](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Opties voor lijnlaag](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Gegevensgestuurde stijlexpressies gebruiken](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Een informatievenster weergeven

Aanvullende informatie voor een entiteit kan worden weergegeven op de kaart als een `Microsoft.Maps.Infobox`-klasse in Bing Kaarten. In Azure Maps kan dit worden bereikt met behulp van de `atlas.Popup`-klasse. In de onderstaande voorbeelden wordt een punaise/markering aan de kaart toegevoegd. Wanneer erop wordt geklikt, wordt een informatievenster/pop-up weergegeven.

**Vóór: Bing Kaarten**

Met Bing Kaarten wordt een informatievenster gemaakt met behulp van de `Microsoft.Maps.Infobox`-constructor.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Bing Kaarten - informatievenster](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**Na: Azure Maps**

In Azure Maps kunt u een pop-up gebruiken om aanvullende informatie weer te geven voor een locatie. Een HTML-`string` of `HTMLElement`-object kan worden doorgegeven aan de optie `content` van de pop-up. Pop-ups kunnen eventueel onafhankelijk van vormen worden weergegeven en vereisen daarom dat een `position`-waarde wordt opgegeven. Als u een pop-up wilt weergeven, roept u de functie `open` aan en geeft u de kaart door waarop de pop-up moet worden weergegeven.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Pop-up in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> U kunt hetzelfde doen met een symbool-, bellen-, lijn-of polygoonlaag door de laag door te geven aan de gebeurteniscode van de kaart in plaats van een markering.

**Aanvullende bronnen**

-   [Een pop-up toevoegen](./map-add-popup.md)
-   [Pop-up met media-inhoud](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Pop-ups op vormen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Pop-up opnieuw gebruiken met verschillende punaises](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Popup-klasse](/javascript/api/azure-maps-control/atlas.popup)
-   [Opties voor pop-up](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Clustering van punaises

Wanneer een groot aantal gegevenspunten op de kaart wordt gevisualiseerd, overlappen punten elkaar. De kaart wordt dan onoverzichtelijk en kan minder gemakkelijk worden bekeken en gebruikt. Het clusteren van puntgegevens kan worden gebruikt om deze gebruikerservaring te verbeteren en ook de prestaties te verbeteren. Het clusteren van puntgegevens is het proces van het combineren van puntgegevens die zich dicht bij elkaar bevinden en deze als één geclusterd gegevenspunt weer te geven op de kaart. Als de gebruiker inzoomt op de kaart, vallen de clusters uiteen in de afzonderlijke gegevenspunten.

In de onderstaande voorbeelden wordt een GeoJSON-feed geladen met gegevens van aardbevingen in de afgelopen week en worden de gegevens toegevoegd aan de kaart. Clusters worden weergegeven als geschaalde en gekleurde cirkels, afhankelijk van het aantal punten dat ze bevatten.

> [!NOTE]
> Er worden verschillende algoritmen gebruikt voor het clusteren van punaises. Bing Kaarten maakt gebruik van een eenvoudige functie op basis van rasters, terwijl Azure Maps gebruikmaakt van geavanceerdere en visueel aantrekkelijke clusteringsmethoden op basis van punten.

**Vóór: Bing Kaarten**

In Bing Kaarten kunnen GeoJSON-gegevens worden geladen met behulp van de GeoJSON-module. Punaises kunnen worden geclusterd door te laden in de clusteringsmodule en de clusteringslaag ervan te gebruiken.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Kaarten - clustering](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**Na: Azure Maps**

In Azure Maps worden gegevens toegevoegd en beheerd door een gegevensbron. Lagen maken verbinding met gegevensbronnen en geven de gegevens hierin weer. De klasse `DataSource` in Azure Maps biedt verschillende opties voor clustering.

* `cluster`: geeft de gegevensbron opdracht om puntgegevens te clusteren. 
* `clusterRadius`: de straal in pixels waarbinnen punten moeten worden geclusterd.
* `clusterMaxZoom`: het maximale zoomniveau voor de clustering. Als u verder inzoomt, worden alle punten weergegeven als symbolen.
* `clusterProperties`: aangepaste eigenschappen die worden berekend met behulp van expressies voor alle punten binnen elk cluster en die worden toegevoegd aan de eigenschappen van elk clusterpunt.

Wanneer clustering is ingeschakeld, verstuurt de gegevensbron geclusterde en niet-geclusterde gegevenspunten naar lagen om daar te worden weergegeven. De gegevensbron kan honderdduizenden gegevenspunten clusteren. Een geclusterd gegevenspunt heeft de volgende eigenschappen:

| Naam van eigenschap               | Type    | Beschrijving                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | booleaans | Geeft aan of de functie een cluster vertegenwoordigt.     |
| `cluster_id`                | tekenreeks  | Een unieke id voor het cluster die kan worden gebruikt met de functies `getClusterExpansionZoom`, `getClusterChildren` en `getClusterLeaves` van de `DataSource`-klassen. |
| `point_count`               | getal  | Het aantal punten dat het cluster bevat.     |
| `point_count_abbreviated`   | tekenreeks  | Een tekenreeks voor het afkorten van de waarde van `point_count` als deze te lang is (4000 wordt bijvoorbeeld 4K). |

De klasse `DataSource` ondersteunt de volgende helperfunctie om toegang te krijgen tot aanvullende informatie over een cluster met behulp van de `cluster_id`.

| Functie       | Retourtype        | Beschrijving     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Hiermee worden de onderliggende elementen opgehaald van het opgegeven cluster op het volgende zoomniveau. Deze elementen kunnen een combinatie zijn van vormen en subclusters. De subclusters bestaan uit functies met eigenschappen die overeenkomen met clustereigenschappen. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Hiermee wordt het zoomniveau berekend waarbij het cluster gaat uitbreiden of uiteenvalt.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Hiermee worden alle punten in een cluster opgehaald. Stel de `limit` in voor het retourneren van een subset van de punten en gebruik de `offset` om door de punten te bladeren.    |

Wanneer u geclusterde gegevens gaat weergeven op de kaart, kunt u vaak het gemakkelijkste twee of meer lagen gebruiken. In het onderstaande voorbeeld worden drie lagen gebruikt: een bellenlaag voor het tekenen van geschaalde gekleurde cirkels op basis van de grootte van de clusters, een symboollaag om de clustergrootte als tekst weer te geven en een tweede symboollaag voor het weergeven van niet-geclusterde punten. In de documentatie [Gegevens van clusterpunt](./clustering-point-data-web-sdk.md) worden veel andere manieren voor het weergeven van geclusterde gegevens in Azure Maps beschreven.

GeoJSON-gegevens kunnen rechtstreeks worden geïmporteerd in Azure Maps met behulp van de functie `importDataFromUrl` voor de klasse `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Clustering in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**Aanvullende bronnen**

-   [Een symboollaag toevoegen](./map-add-pin.md)
-   [Een bellenlaag toevoegen](./map-add-bubble-layer.md)
-   [Puntgegevens clusteren](./clustering-point-data-web-sdk.md)
-   [Gegevensgestuurde stijlexpressies gebruiken](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Een heatmap toevoegen

Heatmaps (kaarten voor puntdichtheid) zijn een type gegevensvisualisatie waarmee de dichtheid van gegevens wordt weergegeven met behulp van een reeks kleuren. Ze worden vaak gebruikt om de hotspots voor gegevens op een kaart weer te geven en zijn een uitstekende manier om grote puntgegevenssets weer te geven.

In de onderstaande voorbeelden wordt een GeoJSON-feed geladen van alle aardbevingen die gedurende de afgelopen maand door de USGS zijn geregistreerd, waarna deze worden weergegeven als een heatmap.

**Vóór: Bing Kaarten**

In Bing Kaarten kunt u een heatmap maken door de heatmapmodule te laden. Op dezelfde manier wordt de GeoJSON-module geladen om ondersteuning toe te voegen voor GeoJSON-gegevens.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Kaarten - heatmap](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**Na: Azure Maps**

Laad de GeoJSON-gegevens in Azure Maps in een gegevensbron en verbind de gegevensbron met een heatmaplaag. GeoJSON-gegevens kunnen rechtstreeks worden geïmporteerd in Azure Maps met behulp van de functie `importDataFromUrl` voor de klasse `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps-heatmap](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**Aanvullende bronnen**

-   [Een heatmap-laag toevoegen](./map-add-heat-map-layer.md)
-   [HeatMapLayer-klasse](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Opties voor heatmap-laag](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Gegevensgestuurde stijlexpressies gebruiken](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Een tegellaag als overlay gebruiken

Gebruik tegellagen om grote afbeeldingen die zijn opgesplitst in kleinere afbeeldingstegels, over de kaart heen te leggen. Deze tegels volgen het tegelsysteem van de kaart. Deze aanpak wordt vaak gebruikt om grote afbeeldingen of zeer grote gegevenssets over de kaart heen te leggen.

In de onderstaande voorbeelden wordt een tegellaag met een weerradarafbeelding van Iowa Environment Mesonet van de Iowa State University boven de kaart weergegeven waarbij een tegelnaamgevingsschema X, Y, Zoom wordt gebruikt.

**Vóór: Bing Kaarten**

In Bing Kaarten kunnen tegellagen worden gemaakt met behulp van de klasse `Microsoft.Maps.TileLayer`.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![Bing Kaarten - gewogen heatmap](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**Na: Azure Maps**

In Azure Maps kan een tegellaag op vrijwel dezelfde manier als elke andere laag aan de kaart worden toegevoegd. Een opgemaakte URL met voor de tijdelijke aanduidingen `{x}`, `{y}` en `{z}` wordt gebruikt om de laag te laten weten waar toegang tot de tegels kan worden verkregen. Tegellagen in Azure Maps ondersteunen ook de tijdelijke aanduidingen `{quadkey}`, `{bbox-epsg-3857}` en `{subdomain}`.

> [!TIP]
> In Azure Maps kunnen lagen eenvoudig onder andere lagen worden weergegeven, waaronder basiskaartlagen. Vaak is het wenselijk om tegellagen onder de kaartlabels weer te geven, zodat ze goed leesbaar zijn. Voor de functie `map.layers.add` is een tweede parameter nodig (de id van een tweede laag) om de nieuwe laag eronder in te voegen. Als u een tegellaag onder de kaartlabels wilt invoegen, kunt u de volgende code gebruiken:
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Azure Maps - gewogen heatmap](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> Tegelaanvragen kunnen worden vastgelegd met behulp van de optie `transformRequest` van de kaart. Op deze manier kunt u desgewenst kopteksten in de aanvraag wijzigen of eraan toevoegen.

**Aanvullende bronnen**

-   [Tegellagen toevoegen](./map-add-tile-layer.md)
-   [TileLayer-klasse](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Opties voor tegellaag](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Verkeergegevens weergeven

Verkeersgegevens kunnen worden weergegeven over zowel Bing- als Azure-kaarten.

**Vóór: Bing Kaarten**

In Bing Kaarten kunnen verkeersgegevens op de kaart worden weergegeven met de verkeersmodule.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Bing Kaarten - verkeer](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**Na: Azure Maps**

Azure Maps biedt verschillende opties voor het weergeven van verkeer. Verkeersincidenten, zoals wegafzettingen en ongelukken, kunnen als pictogrammen op de kaart worden weer gegeven. Het is mogelijk verkeersstromen (wegen met kleurcode) op de kaart weer te geven waarbij de kleuren kunnen worden aangepast aan de ingediende snelheidslimieten ten opzichte van de normale verwachte vertraging of absolute vertraging. Incidentgegevens in Azure Maps worden elke minuut bijgewerkt en stroomgegevens worden elke twee minuten bijgewerkt.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Verkeer in Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Als u op een van de verkeerspictogrammen in Azure Maps klikt, wordt er extra informatie weergegeven in een pop-up.

![Azure Maps - pop-up voor verkeer](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**Aanvullende bronnen**

-   [Verkeer weergeven op de kaart](./map-show-traffic.md)
-   [Opties voor verkeersgegevens als overlay](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Beheer van verkeer](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Een terrein-overlay toevoegen

Zowel Bing Kaarten als Azure Maps ondersteunt het plaatsen van afbeeldingen met georeferenties op de kaart, zodat ze kunnen worden verplaatst en geschaald wanneer u de kaart verschuift of in- of uitzoomt. In Bing Kaarten worden afbeeldingen met georeferenties aangeduid als terrein-overlays terwijl ze in Azure Maps worden aangeduid als afbeeldingslagen. Deze zijn ideaal voor het op een kaart weergeven van bouwtekeningen, oude kaarten of beelden van een drone.

**Vóór: Bing Kaarten**

Wanneer u een terrein-overlay in Bing Kaarten maakt, geeft u de URL op naar de afbeelding die u als overlay wilt weergeven, en een begrenzingsvak om de afbeelding aan de kaart te koppelen. In dit voorbeeld wordt een kaartafbeelding van [Newark New Jersey uit 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) over de kaart heen gelegd.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

![Bing Kaarten - terrein-overlay](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**Na: Azure Maps**

In Azure Maps is een overlay van afbeeldingen met georeferenties mogelijk met behulp van de `atlas.layer.ImageLayer`-klasse. Deze klasse vereist een URL van een afbeelding en een set coördinaten voor de vier hoeken van de afbeelding. De afbeelding moet worden gehost in hetzelfde domein of CORs moet zijn ingeschakeld voor de afbeelding.

> [!TIP]
> Als u alleen de gegevens voor north, south, east, west en rotation hebt en u niet voor elke hoek van de afbeelding coördinaten hebt, kunt u de statische functie [atlas.layer.ImageLayer.getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) gebruiken.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps - terrein-overlay](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**Aanvullende bronnen**

-   [Een afbeeldingslaag toevoegen aan een kaart](./map-add-image-layer.md)
-   [ImageLayer-klasse](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>KML-gegevens toevoegen aan de kaart

Zowel bij Azure- als Bing-kaarten is het mogelijk KML-, KMZ-, GeoRSS-, GeoJSON- als Well-Known Text (WKT)-gegevens te importeren en op de kaart weer te geven. Azure Maps biedt ook ondersteuning voor GPX, GML, ruimtelijke CSV-bestanden, WMS (Web-Mapping Services), WMTS (Web Mapping Tile Services) en WFS (Web Feature Services).

**Vóór: Bing Kaarten**

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing Kaarten - kml](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**Na: Azure Maps**

In Azure Maps is GeoJSON de belangrijkste gegevensindeling die wordt gebruikt in de Web-SDK, maar met behulp van het pakket [azure-maps-spatial-io](/javascript/api/azure-maps-spatial-io/) kunt u ook eenvoudig aanvullende indelingen voor ruimtelijke gegevens integreren. Deze module bevat functies voor het lezen en schrijven van ruimtelijke gegevens en bevat ook een eenvoudige gegevenslaag waarmee u gemakkelijk gegevens met een van deze indelingen voor ruimtelijke gegevens kunt weergeven. Als u de gegevens in een bestand met ruimtelijke gegevens wilt lezen, geeft u een URL of onbewerkte gegevens als een tekenreeks of blob door aan de functie `atlas.io.read`. Het resultaat bestaat uit alle geparseerde gegevens uit het bestand, die vervolgens aan de kaart kunnen worden toegevoegd. KML is iets ingewikkelder dan de meeste indelingen voor ruimtelijke gegevens aangezien deze indeling veel meer informatie over de stijl bevat. De klasse `SpatialDataLayer` ondersteunt het weergeven van het merendeel van deze stijlen, maar pictogramafbeeldingen moeten in de kaart worden geladen voordat de functiegegevens worden geladen en overlays voor terreinen moeten afzonderlijk als lagen aan de kaart worden toegevoegd. Wanneer u gegevens laadt via een URL, moet deze worden gehost op een eindpunt waarvoor CORs is ingeschakeld. In alle andere gevallen moet er een proxyservice worden doorgegeven als optie voor de functie read.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps-kml](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**Aanvullende bronnen**

-   [De functie atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Tekenhulpmiddelen toevoegen

Bing Kaarten en Azure Maps bieden beide een module waarmee de gebruiker vormen op de kaart kan tekenen en bewerken met de muis of een ander invoerapparaat. Bij beide wordt het tekenen van punaises, lijnen en veelhoeken ondersteund. Azure Maps biedt bovendien opties voor het tekenen van cirkels en rechthoeken.

**Vóór: Bing Kaarten**

In Bing Kaarten wordt de module `DrawingTools` geladen met behulp van de functie `Microsoft.Maps.loadModule`. Nadat deze is geladen, kunt u een exemplaar van de klasse DrawingTools maken en wordt de functie `showDrawingManager` aangeroepen om een werkbalk aan de kaart toe te voegen.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Bing Kaarten - tekenhulpmiddelen](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**Na: Azure Maps**

In Azure Maps moet de module voor tekenhulpmiddelen worden geladen door de JavaScript- en CSS-bestanden te laden waarnaar moet worden verwezen in de app. Wanneer de kaart is geladen, kan een exemplaar van de klasse `DrawingManager` worden gemaakt en een `DrawingToolbar`-exemplaar worden gekoppeld.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Azure Maps - tekenhulpmiddelen](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> In Azure Maps-lagen bieden de tekenhulpmiddelen meerdere manieren waarop gebruikers vormen kunnen tekenen. Bij het tekenen van een veelhoek bijvoorbeeld kan de gebruiker klikken om een punt toe te voegen of de linkermuisknop ingedrukt houden en de muis slepen om een pad te tekenen. Dit kan worden gewijzigd met behulp van de `interactionType`-optie van de `DrawingManager`.

**Aanvullende bronnen**

-   [Documentatie](./set-drawing-options.md)
-   [Codevoorbeelden](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>Aanvullende bronnen

Bekijk de [opensource-modules van de Azure Maps Web-SDK](open-source-projects.md#open-web-sdk-modules). Deze modules bieden veel extra functionaliteit en zijn volledig aanpasbaar.

Bekijk codevoorbeelden die betrekking hebben op het migreren van andere Bing Kaarten-functies:

**Gegevensvisualisaties**

> [!div class="nextstepaction"]
> [Omtreklaag](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Gegevens-binning](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Services**

> [!div class="nextstepaction"]
> [De Azure Maps Services-module gebruiken](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Zoeken naar bezienswaardigheden](./map-search-location.md)

> [!div class="nextstepaction"]
> [Informatie ophalen uit een coördinaat (omgekeerde geocode)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Routebeschrijving van A naar B](./map-route.md)

> [!div class="nextstepaction"]
> [Automatische suggesties zoeken met JQuery-gebruikersinterface](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Lees hier meer over de Azure Maps Web-SDK.

> [!div class="nextstepaction"]
> [Map Control gebruiken](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [De services-module gebruiken](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [De module voor tekenprogramma's gebruiken](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Naslagdocumentatie over de API voor de web-SDK-service van Azure Maps](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources om op te schonen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over migreren van Bing Kaarten naar Azure Maps.

> [!div class="nextstepaction"]
> [Een webservice migreren](migrate-from-bing-maps-web-services.md)