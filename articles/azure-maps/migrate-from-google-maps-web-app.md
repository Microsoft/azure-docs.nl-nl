---
title: 'Zelfstudie: Een web-app migreren vanuit Google Maps naar Microsoft Azure Maps'
description: Zelfstudie over hoe u een web-app migreert vanuit Google Maps naar Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 4dee8de8f42b78ecdab9d9e15bb277d58fa8ba70
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905057"
---
# <a name="tutorial---migrate-a-web-app-from-google-maps"></a>Zelfstudie - Een web-app migreren uit Google Maps

De meeste web-apps die gebruikmaken van Google Maps, maken gebruik van de Google Maps v3 JavaScript-SDK. De Azure Maps Web-SDK is de geschikte op Azure gebaseerde SDK om naar te migreren. Met de Azure Maps Web-SDK kunt u interactieve kaarten aanpassen met uw eigen inhoud en beelden. U kunt uw app uitvoeren in zowel web-apps als of mobiele apps. Dit besturingselement maakt gebruik van WebGL, zodat u grote gegevenssets kunt weergeven met hoge prestaties. U kunt ontwikkelen met deze SDK met behulp van JavaScript of TypeScript. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een kaart laden
> * Een kaar lokaliseren
> * Markeringen polylijnen en polygonen toevoegen.
> * Informatie weergeven in een pop-up of informatievenster
> * KML- en GeoJSON-gegevens laden en weergeven
> * Clustermarkeringen
> * Een tegellaag als overlay gebruiken
> * Verkeergegevens weergeven
> * Een terrein-overlay toevoegen

U leert ook het volgende: 

> [!div class="checklist"]
> * Hoe u algemene kaarttaken uitvoert met de Azure Maps Web-SDK
> * Best practices om prestaties en gebruikerservaring te verbeteren
> * Tips over hoe u uw toepassing maakt met geavanceerde functies in Azure Maps

Als u een bestaande webtoepassing wilt migreren, controleert u of deze een opensource-bibliotheek voor kaartbesturingselementen gebruikt. Dit zijn voorbeelden van opensource-bibliotheken voor kaartbesturingselementen: Cesium, Leaflet en OpenLayers. U kunt uw toepassing nog steeds migreren, zelfs als deze gebruikmaakt van een opensource-bibliotheek voor kaartbesturingselementen, en u niet de Azure Maps Web-SDK wilt gebruiken. In dat geval verbindt u uw toepassing met de Azure Maps-tegelservices ([wegtegels](/rest/api/maps/render/getmaptile) \| [satelliettegels](/rest/api/maps/render/getmapimagerytile)). Hieronder vindt u meer informatie over het gebruik van Azure Maps in een aantal veelgebruikte opensource-bibliotheken voor kaartbesturingselementen.

- Cesium: een 3D-kaartbesturingselement voor het web. [Codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentatie](https://cesiumjs.org/)
- Leaflet: een lichtgewicht 2D-kaartbesturingselement voor het web. [Codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentatie](https://leafletjs.com/)
- OpenLayers: een 2D-kaartbesturingselement voor het web dat projecties ondersteunt. [Codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentatie](https://openlayers.org/)

Bij het ontwikkelen met behulp van een JavaScript-framework kan een van de volgende opensource-projecten nuttig zijn:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): Angular 10-wrapper rond Azure-kaarten.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): een Azure Maps Blazor-onderdeel.
- [Azure Maps React-onderdeel](https://github.com/WiredSolutions/react-azure-maps): een react-wrapper voor het Azure Maps-besturingselement.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps): een Azure Maps-onderdeel voor de Vue-toepassing.

## <a name="prerequisites"></a>Vereisten 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="key-features-support"></a>Belangrijkste ondersteunde functies

De tabel bevat een overzicht van de belangrijkste API-functies in de Google Maps v3 JavaScript-SDK en de ondersteunde API-functie in de Azure Maps Web-SDK.

| Google Maps-functie     | Ondersteuning in Azure Maps Web-SDK |
|-------------------------|:--------------------------:|
| Markeringen                 | ✓                          |
| Clusteren van markeringen       | ✓                          |
| Polylijnen en veelhoeken    | ✓                          |
| Gegevenslagen             | ✓                          |
| Overlays voor terreinen         | ✓                          |
| Heatmaps               | ✓                          |
| Tegellagen             | ✓                          |
| KML-laag               | ✓                          |
| Tekenprogramma's           | ✓                          |
| Service voor geocodering        | ✓                          |
| Service voor aanwijzingen      | ✓                          |
| Service voor afstandsmatrix | ✓                          |
| Service voor hoogte-indicatie       | ✓                          |

## <a name="notable-differences-in-the-web-sdks"></a>Belangrijke verschillen tussen de web-SDK's

Dit zijn enkele van de belangrijkste verschillen tussen de Google Maps-SDK en de Azure Maps Web-SDK waarmee u rekening moet houden:

- Naast het leveren van een gehost eindpunt om toegang te krijgen tot de Azure Maps Web-SDK, is er een NPM-pakket beschikbaar. Sluit het Web SDK-pakket in apps in. Raadpleeg deze [documentatie](how-to-use-map-control.md) voor meer informatie. Dit pakket bevat ook TypeScript-definities.
- U moet eerst een instantie van de Map-klasse maken in Azure Maps. Wacht tot de gebeurtenis `ready` of `load` van een kaart is geactiveerd voordat u programmatisch interactie heeft met de kaart. Deze volgorde zorgt ervoor dat alle kaartresources zijn geladen en klaar zijn voor gebruik.
- Beide platforms gebruiken een soortgelijk tegelsysteem voor de basiskaarten. De tegels in Google Maps zijn 256 pixels groot, terwijl de tegels in Azure Maps een omvang hebben van 512 pixels. Om dezelfde kaartweergave te krijgen in Azure Maps en Google Maps, moet u het zoomniveau van Google Maps met één verlagen in Azure Maps.
- Coördinaten in Google Maps worden aangeduid als `latitude,longitude` terwijl in Azure Maps `longitude,latitude` wordt gebruikt. De indeling van Azure Maps is afgestemd op de standaard `[x, y]`, die wordt gevolgd door de meeste GIS-platforms.
- Vormen in de Web-SDK van Azure Maps zijn gebaseerd op het GeoJSON-schema. Helperklassen worden weergegeven via de [*naamruimte* atlas.data](/javascript/api/azure-maps-control/atlas.data). Er is ook de klasse [*atlas.Shape*](/javascript/api/azure-maps-control/atlas.shape). Gebruik deze klasse om GeoJSON-objecten te verpakken, zodat u de gegevens eenvoudig en op een bindbare manier kunt bijwerken en onderhouden.
- Coördinaten in Azure Maps worden gedefinieerd als Position-objecten. Een coördinaat wordt opgegeven als een numerieke matrix in de notatie `[longitude,latitude]`. Een andere manier is via new atlas.data.Position(longitude, latitude).
    > [!TIP]
    > De klasse Position heeft een statische helpermethode voor het importeren van coördinaten met de notatie 'breedtegraad, lengtegraad'. De methode [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) kan vaak worden vervangen door de methode `new google.maps.LatLng` in Google Maps-code.
- In plaats van stijlgegevens op te geven voor elke vorm die wordt toegevoegd aan de kaart, houdt Azure Maps stijlen gescheiden van de gegevens. Gegevens worden opgeslagen in een gegevensbron en verbonden met renderinglagen. Azure Maps-code maakt gebruik van gegevensbronnen om de gegevens weer te geven. Deze aanpak leidt tot verbeterde prestaties. Daarnaast ondersteunen veel lagen gegevensgestuurde stijlen, waarbij bedrijfslogica kan worden toegevoegd aan opties voor laagstijlen. Deze ondersteuning verandert hoe afzonderlijke vormen worden weergegeven op een laag op basis van eigenschappen die in de vorm zijn gedefinieerd.

## <a name="web-sdk-side-by-side-examples"></a>Voorbeelden van Web SDK naast elkaar gelegd

Deze verzameling bevat codevoorbeelden voor elk platform en elk voorbeeld heeft betrekking op een algemeen gebruiksscenario. Een en ander is bedoeld om u te helpen bij het migreren van uw webtoepassing van de Google Maps v3 JavaScript-SDK naar de Azure Maps Web-SDK. Codevoorbeelden die betrekking hebben op webtoepassingen zijn geschreven in JavaScript. Azure Maps biedt echter ook TypeScript-definities als een extra optie via een [NPM-module](how-to-use-map-control.md).

**Onderwerpen**

- [Een kaart laden](#load-a-map)
- [Lokaliseren van de kaart](#localizing-the-map)
- [De kaartweergave instellen](#setting-the-map-view)
- [Een markering toevoegen](#adding-a-marker)
- [Een aangepaste markering toevoegen](#adding-a-custom-marker)
- [Een polylijn toevoegen](#adding-a-polyline)
- [Een veelhoek toevoegen](#adding-a-polygon)
- [Een informatievenster weergeven](#display-an-info-window)
- [Een GeoJSON-bestand importeren](#import-a-geojson-file)- 
- [Clusteren van markeringen](#marker-clustering)
- [Een heatmap toevoegen](#add-a-heat-map)
- [Een tegellaag als overlay gebruiken](#overlay-a-tile-layer)
- [Verkeergegevens weergeven](#show-traffic-data)
- [Een terrein-overlay toevoegen](#add-a-ground-overlay)
- [KML-gegevens toevoegen aan de kaart](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>Een kaart laden

Beide SDK's hanteren dezelfde stappen om een kaart te laden:

- Voeg een verwijzing toe naar de kaart-SDK.
- Voeg een tag `div` toe aan de hoofdtekst van de pagina, die als tijdelijke aanduiding voor de kaart fungeert.
- Maak een JavaScript-functie die wordt aangeroepen wanneer de pagina is geladen.
- Maak een instantie van de betreffende Map-klasse.

**Enkele belangrijke verschillen**

- Voor Google Maps moet een accountsleutel worden opgegeven in de scriptverwijzing van de API. Verificatiereferenties voor Azure Maps worden opgegeven als opties van de Map-klasse. Deze referentie kan bestaan uit een abonnementssleutel of Azure Active Directory-gegevens.
- Google Maps accepteert een callback-functie in de scriptverwijzing van de API, die wordt gebruikt om een initialisatiefunctie aan te roepen voor het laden van de kaart. Met Azure Maps moet de gebeurtenis onload van de pagina worden gebruikt.
- Bij het verwijzen naar het element `div` waarin de kaart wordt weergegeven, is voor de `Map`-klasse in Azure Maps alleen de waarde `id` vereist terwijl Google Maps een object `HTMLElement` vereist.
- Coördinaten in Azure Maps worden gedefinieerd als Position-objecten, die kunnen worden opgegeven als een eenvoudige getallenmatrix in de notatie `[longitude, latitude]`.
- Het zoomniveau in Azure Maps is één niveau lager dan het zoomniveau in Google Maps. Dit verschil komt door het verschil in grootte van het tegelsysteem van beide platforms.
- Azure Maps voegt geen navigatiebesturingselementen toe aan het kaartcanvas. Daarom heeft een kaart standaard geen knoppen voor zoomen en kaartstijl. Er zijn echter wel opties beschikbaar voor het toevoegen van een kaartstijlkiezer, zoomknoppen, en besturingselementen voor kompas- of rotatieregeling en kantelregeling.
- Er wordt een gebeurtenis-handler toegevoegd in Azure Maps om de gebeurtenis `ready` van de kaartinstantie te controleren. Deze gebeurtenis wordt geactiveerd wanneer het laden van de WebGL-context voor de kaart en alle benodigde resources is voltooid. Voeg aan deze gebeurtenis-handler de code toe die u wilt uitvoeren nadat het laden van de kaart is voltooid.

In de onderstaande basisvoorbeelden wordt Google Maps gebruikt voor het laden van een kaart die op coördinaten is gecentreerd over New York. De lengtegraad:-73.985, de breedtegraad: 40.747 en de kaart heeft het zoomniveau 12.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Een kaart van Google Maps weergeven die is gecentreerd en ingezoomd op een locatie.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

![Eenvoudige kaart van Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Laad een kaart met dezelfde weergave in Azure Maps, samen met een besturingselement voor de kaartstijl en zoomknoppen.

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

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
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

![Eenvoudige kaart van Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)

Klik [hier](how-to-use-map-control.md) voor uitgebreide informatie over het instellen en gebruiken van het Azure Maps-kaartbesturingselement in een web-app.

> [!NOTE]
> In tegenstelling tot Google Maps vereist Azure Maps geen middelpunt en zoomniveau nodig om de kaart te laden. Als deze informatie niet wordt opgegeven wanneer de kaart wordt geladen, probeert Azure Maps de plaats van de gebruiker te bepalen. Vervolgens wordt de kaart daar gecentreerd en ingezoomd.

**Aanvullende informatie:**

- Azure Maps biedt ook navigatiebesturingselementen voor het draaien en kantelen van de kaartweergave, zoals [hier](map-add-controls.md) wordt beschreven.

### <a name="localizing-the-map"></a>Lokaliseren van de kaart

Lokalisatie is belangrijk als uw doelgroep zich in meerdere landen/regio's bevindt of verschillende talen spreekt.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Als u kaarten van Google Maps wilt lokaliseren, voegt u de parameters language en region toe.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Hier ziet u een voorbeeld van Google Maps waarbij de taal is ingesteld op 'fr-FR'.

![Lokalisatie bij Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Azure Maps biedt twee verschillende manieren om de taal en de regionale weergave van de kaart in te stellen. De eerste optie is om deze informatie toe te voegen aan de algemene naamruimte *atlas*. Hierdoor worden in alle exemplaren van kaartbesturingselementen in uw app standaard deze instellingen gebruikt. Met de volgende code stelt u de taal in op Frans ('fr-FR') en de regionale weergave op 'auto':

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

De tweede optie is om deze informatie door te geven aan de kaartopties bij het laden van de kaart. Dat werkt als volgt:

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
> Met Azure Maps kunt u op dezelfde pagina meerdere kaartexemplaren laden met verschillende taal-en regio-instellingen. Het is ook mogelijk om deze instellingen bij te werken in de kaart nadat deze is geladen. 

Bekijk [hier](supported-languages.md) een gedetailleerde lijst met ondersteunde talen in Azure Maps.

Hier volgt een voorbeeld van Azure Maps waarbij de taal is ingesteld op 'fr-FR' en de gebruikersregio op 'fr'.

![Lokalisatie bij Azure Maps](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>De kaartweergave instellen

Dynamische kaarten in zowel Azure Maps als Google Maps kunnen programmatisch worden verplaatst naar nieuwe geografische locaties. Dit doet u door de juiste functies aan te roepen in JavaScript. De voorbeelden laten zien hoe we satellietluchtfoto's kunnen weergeven op de kaart, de kaart kunnen centreren op een locatie en het zoomniveau kunnen wijzigen in 15 in Google Maps. Hierbij worden de volgende locatiecoördinaten gebruikt: lengtegraad: -111.0225 en breedtegraad: 35.0272.

> [!NOTE]
> In Google Maps worden tegels van 256 pixels gebruikt, terwijl in Azure Maps een grotere tegel van 512 pixels wordt gebruikt. Hierdoor zijn er in Azure Maps minder netwerkaanvragen nodig dan in Google Maps om hetzelfde kaartgebied te laden. Als gevolg van de manier waarop tegelpiramides werken in kaartbesturingselementen, moet u het zoomniveau dat wordt gebruikt in Google Maps met één verlagen bij gebruik in Azure Maps. Deze rekenkundige bewerking zorgt ervoor dat grotere tegels in Azure Maps hetzelfde kaartgebied weergeven als in Google Maps.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Verplaats het kaartbesturingselement Google Maps met behulp van de methode `setOptions`. Met deze methode kunt u het middelpunt van de kaart en een zoomniveau opgeven.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Ingestelde weergave in Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Wijzig in Azure Maps de kaartpositie met behulp van de methode `setCamera` en wijzig de kaartstijl met behulp van de methode `setStyle`. De coördinaten in Azure Maps hebben de notatie 'lengtegraad, breedtegraad', en de waarde voor het zoomniveau wordt met één verlaagd.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Ingestelde weergave in Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**Aanvullende informatie:**

- [Een kaartstijl kiezen](choose-map-style.md)
- [Ondersteunde kaartstijlen](supported-map-styles.md)

### <a name="adding-a-marker"></a>Een markering toevoegen

In Azure Maps kunt u op verschillende manieren puntgegevens weergeven op de kaart:

- **HTML-markeringen**: punten weergeven met behulp van traditionele DOM-elementen. HTML-markeringen ondersteunen slepen.
- **Symboollaag**: punten weergeven met een pictogram of tekst binnen de WebGL-context.
- **Bellenlaag**: punten weergeven als cirkels op de kaart. De stralen van de cirkels kunnen worden geschaald op basis van eigenschappen in de gegevens.

Symbool en bellenlagen moet u renderen in de WebGL-context. Op beide lagen kunnen grote sets met punten op de kaart worden weergegeven. Voor deze lagen moeten gegevens worden opgeslagen in een gegevensbron. Gegevensbronnen en renderinglagen moeten worden toegevoegd aan de kaart nadat de gebeurtenis `ready` is geactiveerd. HTML-markeringen worden op de pagina weergegeven als DOM-elementen en maken geen gebruik van een gegevensbron. Hoe meer DOM-elementen er op een pagina staan, des te langzamer de pagina wordt. Als er meer dan een paar honderd punten moeten worden weergegeven op een kaart, is het beter om een van de renderinglagen te gebruiken.

Laten we een markering toevoegen aan de kaart met het nummer 10 als label. Lengtegraad: -0.2 en breedtegraad: 51.5.

#### <a name="before-google-maps"></a>Vóór: Google Maps

In Google Maps voegt u markeringen aan de kaart toe met behulp van de klasse `google.maps.Marker` en geeft u de kaart op als een van de opties.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Markering in Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)

**Na: Azure Maps met HTML-markeringen**

Gebruik in Azure Maps HTML-markeringen om een punt op de kaart weer te geven. HTML-markeringen worden aanbevolen voor apps waarin slechts een klein aantal punten op de kaart hoeft te worden weergegeven. Als u een HTML-markering wilt gebruiken, maakt u een instantie van de klasse `atlas.HtmlMarker`. Stel de opties text en position in en voeg de markering toe aan de kaart met behulp van de methode `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![HTML-markering in Azure Maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**Na: Azure Maps met een symboollaag**

Voor een symboollaag voegt u de gegevens toe aan een gegevensbron. Koppel de gegevensbron aan de laag. Daarnaast moeten de gegevensbron en laag worden toegevoegd aan de kaart nadat de gebeurtenis `ready` is geactiveerd. Als u een unieke tekstwaarde wilt weergeven boven een symbool, moeten de tekstgegevens worden opgeslagen als een eigenschap van het gegevenspunt. Er moet naar de eigenschap worden verwezen in de optie `textField` van de laag. Deze benadering betekent iets meer werk dan het gebruik van HTML-markeringen, maar geeft wel betere prestaties.

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

![Symboollaag in Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**Aanvullende informatie:**

- [Een gegevensbron maken](create-data-source-web-sdk.md)
- [Een symboollaag toevoegen](map-add-pin.md)
- [Een bellenlaag toevoegen](map-add-bubble-layer.md)
- [Puntgegevens clusteren](clustering-point-data-web-sdk.md)
- [HTML-markeringen toevoegen](map-add-custom-html.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)
- [Pictogramopties voor symboollaag](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Tekstoptie voor symboollaag](/javascript/api/azure-maps-control/atlas.textoptions)
- [HTMLMarker-klasse](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opties voor HTML-markeringen](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>Een aangepaste markering toevoegen

U kunt aangepaste afbeeldingen gebruiken om punten op een kaart voor te stellen. Op de kaart hieronder wordt een aangepaste afbeelding gebruikt om een punt op de kaart weer te geven. Het punt wordt weer gegeven met de breedtegraad: 51.5 en de lengtegraad -0.2. Met het ankerpunt (anchor) wordt de positie van de markering verschoven, zodat de punt van de punaise de juiste positie op de kaart aangeeft.

<center>

![afbeelding van een gele punaise](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>


#### <a name="before-google-maps"></a>Vóór: Google Maps

Maak een aangepaste markering door een `Icon`-object op te geven dat de `url` van de afbeelding bevat. Geef een `anchor`-punt op om de punt van de punaise uit te lijnen met de coördinaat op de kaart. De waarde van anchor in Google Maps is relatief ten opzichte van de linkerbovenhoek van de afbeelding.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```


![Aangepaste markering in Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**Na: Azure Maps met HTML-markeringen**

Als u een HTML-markering wilt aanpassen, geeft u een HTML-`string` of `HTMLElement` door aan de optie `htmlContent` van de markering. Gebruik de optie `anchor` om de relatieve positie van de markering op te geven, ten opzichte van de coördinaten bij position. Wijs een van de negen gedefinieerde referentiepunten toe aan de optie `anchor`. U kunt deze punten gebruiken: 'center', 'top', 'bottom', 'left', 'right', 'top-left', 'top-right', 'bottom-left', 'bottom-right'. De inhoud wordt standaard in het midden aan de onderzijde aan de HTML-inhoud verankerd. Om het gemakkelijker te maken code te migreren vanuit Google Maps, stelt u `anchor` in op 'top-left' en gebruikt u vervolgens de optie `pixelOffset` met de offset die wordt gebruikt in Google Maps. De offsets in Azure Maps worden in de tegenovergestelde richting verplaatst van de offsets in Google Maps. Vermenigvuldig de offsets daarom met min één.

> [!TIP]
> Voeg `pointer-events:none` als stijl toe aan de html-inhoud om het standaardgedrag voor slepen in Microsoft Edge uit te schakelen, waardoor een ongewenst pictogram wordt weergegeven.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Aangepaste HTML-markering in Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**Na: Azure Maps met een symboollaag**

Symboollagen in Azure Maps bieden ook ondersteuning voor aangepaste afbeeldingen. Laad eerst de afbeelding in de kaartresources en wijs er een unieke id aan toe. Verwijs naar de afbeelding in de symboollaag. Gebruik de optie `offset` om de afbeelding op het juiste punt op de kaart uit te lijnen. Gebruik de optie `anchor` om de relatieve positie van het symbool op te geven, ten opzichte van de coördinaten bij position. Gebruik een van de negen gedefinieerde referentiepunten. U kunt deze punten gebruiken: 'center', 'top', 'bottom', 'left', 'right', 'top-left', 'top-right', 'bottom-left', 'bottom-right'. De inhoud wordt standaard in het midden aan de onderzijde aan de HTML-inhoud verankerd. Om het gemakkelijker te maken code te migreren vanuit Google Maps, stelt u `anchor` in op 'top-left' en gebruikt u vervolgens de optie `offset` met de offset die wordt gebruikt in Google Maps. De offsets in Azure Maps worden in de tegenovergestelde richting verplaatst van de offsets in Google Maps. Vermenigvuldig de offsets daarom met min één.

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

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

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

![Symboollaag met aangepast pictogram in Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> Als u geavanceerde aangepaste punten wilt weergeven, gebruikt u meerdere renderinglagen tegelijk. Stel dat u meerdere punaises wilt gebruiken, maar met hetzelfde pictogram op verschillende gekleurde cirkels. In plaats van een aantal afbeeldingen te maken voor elke kleur-overlay, kunt u een symboollaag op een bellenlaag plaatsen. Laat de punaises naar dezelfde gegevensbron verwijzen. Deze benadering is efficiënter dan het maken en onderhouden van een aantal verschillende afbeeldingen.

**Aanvullende informatie:**

- [Een gegevensbron maken](create-data-source-web-sdk.md)
- [Een symboollaag toevoegen](map-add-pin.md)
- [HTML-markeringen toevoegen](map-add-custom-html.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)
- [Pictogramopties voor symboollaag](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Tekstoptie voor symboollaag](/javascript/api/azure-maps-control/atlas.textoptions)
- [HTMLMarker-klasse](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opties voor HTML-markeringen](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Een polylijn toevoegen

Polylijnen worden gebruikt om een lijn of traject op de kaart aan te geven. Laten we een onderbroken polylijn maken op de kaart.

#### <a name="before-google-maps"></a>Vóór: Google Maps

De klasse Polyline accepteert een set opties. Geef een matrix van coördinaten door via de optie `path` van de polylijn.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Polylijn in Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Polylijnen worden `LineString`- of `MultiLineString`-objecten genoemd. Deze objecten kunnen worden toegevoegd toe aan een gegevensbron en weergegeven met behulp van een lijnlaag. Voeg `LineString` toe aan een gegevensbron en voeg vervolgens de gegevensbron toe aan een `LineLayer` om deze weer te geven.

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
![Polylijn in Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**Aanvullende informatie:**

- [Lijnen toevoegen aan de kaart](map-add-line-layer.md)
- [Opties voor lijnlaag](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Een veelhoek toevoegen

Azure Maps en Google Maps bieden vergelijkbare ondersteuning voor veelhoeken. Veelhoeken worden gebruikt om een gebied op de kaart aan te geven. In de volgende voorbeelden ziet u hoe u een veelhoek maakt die een driehoek vormt op basis van de middelste coördinaat van de kaart.

#### <a name="before-google-maps"></a>Vóór: Google Maps

De klasse Polygon accepteert een set opties. Geef een matrix van coördinaten door via de optie `paths` van de veelhoek.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Veelhoek in Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Voeg een object `Polygon` of `MultiPolygon` toe aan een gegevensbron. Geef het object met behulp van lagen weer op de kaart. Geef het gebied van een veelhoek weer met behulp van een polygoonlaag. Geef ten slotte de omtrek van een veelhoek weer met behulp van een lijnlaag.

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
![Veelhoek in Azure Maps](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**Aanvullende informatie:**

- [Een veelhoek toevoegen aan de kaart](map-add-shape.md)
- [Een cirkel toevoegen aan de kaart](map-add-shape.md#add-a-circle-to-the-map)
- [Opties voor polygoonlaag](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [Opties voor lijnlaag](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Een informatievenster weergeven

U kunt aanvullende informatie voor een entiteit weergeven op de kaart als een klasse `google.maps.InfoWindow` in Google Maps. In Azure Maps kan deze functionaliteit worden bereikt met behulp van de klasse `atlas.Popup`. In de volgende voorbeelden wordt een markering toegevoegd aan de kaart. Als een gebruiker op de markering klikt, wordt er een informatievenster of -pop-up weergegeven.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Geef een informatievenster weer met behulp van de constructor `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```
![Pop-up in Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

We gaan ene pop-up toevoegen om aanvullende informatie over de locatie weer te geven. Geef een HTML-`string` of `HTMLElement`-object door aan de optie `content` van de pop-up. Als u wilt, kunnen pop-ups onafhankelijk van elke vorm worden weergegeven. U moet voor pop-ups daarom een waarde opgeven voor `position`. Geef de waarde voor `position` op. Als u een pop-up wilt weergeven, roept u de methode `open` aan en geeft u de `map` door waarin de pop-up moet worden weergegeven.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```
![Pop-up in Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> U kunt hetzelfde doen met een symbool-, bellen-, lijn-of polygoonlaag door de gekozen laag door te geven aan de events-code van de kaart in plaats van een markering.

**Aanvullende informatie:**

- [Een pop-up toevoegen](map-add-popup.md)
- [Pop-up met media-inhoud](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Pop-ups op vormen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Pop-up opnieuw gebruiken met verschillende punaises](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup-klasse](/javascript/api/azure-maps-control/atlas.popup)
- [Opties voor pop-up](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>Een GeoJSON-bestand importeren

Google Maps ondersteunt het laden van GeoJSON-gegevens via de klasse `google.maps.Data` evenals het dynamisch toepassen van stijlen op dergelijke gegevens. De functionaliteit van deze klasse lijkt veel meer op de gegevensgestuurde stijl van Azure Maps. Maar er is wel een belangrijk verschil. Met Google Maps geeft u een callback-functie op. De bedrijfslogica voor het toepassen van een stijl op elke functie wordt afzonderlijk verwerkt in de UI-thread. Maar in Azure Maps ondersteunen lagen het opgeven van gegevensgestuurde expressies als stijlopties. Deze expressies worden op het moment van rendering in een afzonderlijke thread verwerkt. De aanpak van Azure Maps resulteert in een betere rendering. Dit voordeel is merkbaar wanneer grote gegevenssets snel moeten worden weergegeven.

In de volgende voorbeelden wordt een GeoJSON-feed geladen die alle aardbevingen bevat die gedurende de afgelopen zeven dagen door de USGS (United States Geological Survey) zijn geregistreerd. De gegevens van de aardbevingen worden als cirkels van een bepaalde grootte weergegeven op de kaart. De kleur en grootte van elke cirkel is gebaseerd op de kracht van de aardbeving, die wordt opgeslagen in de eigenschap `"mag"` van elke functie in de gegevensset. Als de kracht groter is dan of gelijk is aan vijf op de schaal van Richter, is de cirkel rood. Als de kracht groter is dan of gelijk is aan drie, maar minder dan vijf, is de cirkel oranje. Als de kracht minder is dan drie, is de cirkel groen. De straal van elke cirkel is exponentieel aan de kracht vermenigvuldigd met 0,1.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Geef een callback-functie op in de methode `map.data.setStyle`. Pas binnen de callback-functie bedrijfslogica toe op elke functie. Laad de GeoJSON-feed met de methode `map.data.loadGeoJson`.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![GeoJSON in Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

GeoJSON is het basisgegevenstype in Azure Maps. Importeer het gegevenstype in een gegevensbron met behulp van de methode `datasource.importFromUrl`. Gebruik een bellenlaag. De bellenlaag biedt functionaliteit voor het weergeven van cirkels van verschillende grootten, op basis van de eigenschappen van de functies in een gegevensbron. In plaats van een callback-functie te gebruiken, wordt de bedrijfslogica omgezet in een expressie die wordt doorgegeven aan de stijlopties. Expressies definiëren hoe de bedrijfslogica werkt. Expressies kunnen worden doorgegeven aan een andere thread en worden geëvalueerd aan de hand van de functiegegevens. Er kunnen meerdere gegevensbronnen en lagen worden toegevoegd aan Azure Maps, elk met hun eigen bedrijfslogica. Deze functie maakt het mogelijk om verschillende gegevenssets op verschillende manieren weer te geven op de kaart.

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
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
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



![GeoJSON in Azure Maps](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**Aanvullende informatie:**

- [Een symboollaag toevoegen](map-add-pin.md)
- [Een bellenlaag toevoegen](map-add-bubble-layer.md)
- [Puntgegevens clusteren](clustering-point-data-web-sdk.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clusteren van markeringen

Wanneer er veel punten worden gevisualiseerd op de kaart, bestaat de kans dat punten elkaar overlappen. Hierdoor ziet de kaart er rommelig uit en is de kaart lastig te lezen en gebruiken. Het clusteren van puntgegevens is het proces van het combineren van gegevenspunten die zich dicht bij elkaar bevinden en deze als één geclusterd gegevenspunt weer te geven op de kaart. Als de gebruiker inzoomt op de kaart, vallen de clusters uiteen in de afzonderlijke gegevenspunten. Cluster gegevenspunten om niet alleen de gebruikerservaring te verbeteren, maar ook de prestaties van de kaart.

In de volgende voorbeelden wordt met de code een GeoJSON-feed geladen met gegevens van aardbevingen in de afgelopen week en worden de gegevens toegevoegd aan de kaart. Clusters worden weergegeven als gekleurde cirkels van verschillende grootten. De grootte en kleur van de cirkels is afhankelijk van het aantal punten in het cluster.

> [!NOTE]
> Google Maps en Azure Maps gebruiken algoritmen voor clustering die iets van elkaar verschillen. Dit kan betekenen dat de verdeling van punten in de clusters soms verschilt.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Gebruik de bibliotheek MarkerCluster om markeringen te clusteren. U kunt alleen bepaalde afbeeldingen gebruiken voor clusterpictogrammen. Deze hebben de nummers één tot en met vijf als hun naam en ze staan in dezelfde map.

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Clustering in Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Voeg gegevens toe aan een gegevensbron en beheer de gegevens. Verbind gegevensbronnen en lagen, en geef vervolgens de gegevens weer. De klasse `DataSource` in Azure Maps biedt verschillende opties voor clustering.

- `cluster`: geeft de gegevensbron opdracht om puntgegevens te clusteren.
- `clusterRadius`: de straal in pixels waarbinnen punten moeten worden geclusterd.
- `clusterMaxZoom`: het maximale zoomniveau voor de clustering. Als u verder inzoomt dan dit niveau, worden alle punten weergegeven als symbolen.
- `clusterProperties`: aangepaste eigenschappen die worden berekend met behulp van expressies voor alle punten binnen elk cluster en die worden toegevoegd aan de eigenschappen van elk clusterpunt.

Wanneer clustering is ingeschakeld, verstuurt de gegevensbron geclusterde en niet-geclusterde gegevenspunten naar lagen om daar te worden weergegeven. De gegevensbron kan honderdduizenden gegevenspunten clusteren. Een geclusterd gegevenspunt heeft de volgende eigenschappen:

| Naam van eigenschap             | Type    | Beschrijving   |
|---------------------------|---------|---------------|
| `cluster`                 | booleaans | Geeft aan of de functie een cluster vertegenwoordigt. |
| `cluster_id`              | tekenreeks  | Een unieke id voor het cluster die kan worden gebruikt met de methoden `getClusterExpansionZoom`, `getClusterChildren` en `getClusterLeaves` van DataSource. |
| `point_count`             | getal  | Het aantal punten dat het cluster bevat.  |
| `point_count_abbreviated` | tekenreeks  | Een tekenreeks voor het afkorten van de waarde van `point_count` als deze te lang is (4000 wordt bijvoorbeeld 4K).  |

De klasse `DataSource` ondersteunt de volgende helperfunctie om toegang te krijgen tot aanvullende informatie over een cluster met behulp van de `cluster_id`.

| Methode | Retourtype | Beschrijving |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Matrix&lt;Functie&lt;Geometrie, willekeurig&gt; \| Vorm&gt;&gt; | Hiermee worden de onderliggende elementen opgehaald van het opgegeven cluster op het volgende zoomniveau. Deze elementen kunnen een combinatie zijn van vormen en subclusters. De subclusters bestaan uit functies met eigenschappen die overeenkomen met ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;getal&gt; | Hiermee wordt een zoomniveau berekend waarbij het cluster gaat uitbreiden of uiteenvalt. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Matrix&lt;Functie&lt;Geometrie, willekeurig&gt; \| Vorm&gt;&gt; | Hiermee worden alle punten in een cluster opgehaald. Stel de `limit` in voor het retourneren van een subset van de punten en gebruik de `offset` om door de punten te bladeren. |

Wanneer u geclusterde gegevens gaat weergeven op de kaart, kunt u vaak het beste twee of meer lagen gebruiken. In het volgende voorbeeld worden drie lagen gebruikt. Een bellenlaag voor het tekenen van gekleurde cirkels van verschillende grootten op basis van de grootte van de clusters, een symboollaag om de clustergrootte als tekst weer te geven en ten slotte een tweede symboollaag voor het weergeven van de niet-geclusterde punten. Er zijn overigens nog allerlei andere manieren om geclusterde gegevens weer te geven. Raadpleeg voor meer informatie de documentatie [over het clusteren van puntgegevens](clustering-point-data-web-sdk.md).

U kunt GeoJSON-gegevens rechtstreeks importeren met behulp van de functie `importDataFromUrl` voor de klasse `DataSource`, binnen de kaart van Azure Maps.

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
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



![Clustering in Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**Aanvullende informatie:**

- [Een symboollaag toevoegen](map-add-pin.md)
- [Een bellenlaag toevoegen](map-add-bubble-layer.md)
- [Puntgegevens clusteren](clustering-point-data-web-sdk.md)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Een heatmap toevoegen

Een heatmap, heel soms ook wel een puntdichtheidskaart genoemd, is een type gegevensvisualisatie. Heatmaps worden gebruikt om de dichtheid van gegevens voor te stellen aan de hand van een bereik van kleuren. Ze worden vaak gebruikt om de 'hotspots' van gegevens op een kaart aan te geven. Heatmaps zijn een uitstekende manier om grote sets met puntgegevens weer te geven.

In de volgende voorbeelden wordt een GeoJSON-feed geladen van alle aardbevingen die gedurende de afgelopen maand door de USGS zijn geregistreerd, waarna deze worden weergegeven als een gewogen heatmap. De eigenschap `"mag"` wordt als de weging gebruikt.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Als u een heatmap wilt maken, laadt u de bibliotheek met visualisaties door `&libraries=visualization` toe te voegen aan de URL van het API-script. De laag voor heatmaps in Google Maps biedt geen rechtstreekse ondersteuning voor GeoJSON-gegevens. Download eerst de gegevens en converteer deze naar een matrix van gewogen gegevenspunten:

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

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Heatmap in Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Laad de GeoJSON-gegevens in een gegevensbron en verbind de gegevensbron met een heatmap-laag. De eigenschap die wordt gebruikt voor de weging kan met behulp van een expressie worden doorgegeven aan de optie `weight`. U kunt GeoJSON-gegevens rechtstreeks importeren in Azure Maps met behulp van de functie `importDataFromUrl` voor de klasse `DataSource`.

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
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                    weight: ['get', 'mag'],
                    intensity: 0.005,
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



![Heatmap in Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**Aanvullende informatie:**

- [Een heatmap-laag toevoegen](map-add-heat-map-layer.md)
- [HeatMapLayer-klasse](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [Opties voor heatmap-laag](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Een tegellaag als overlay gebruiken

Tegellagen in Azure Maps worden afbeeldingsoverlays genoemd in Google Maps. Gebruik tegellagen om grote afbeeldingen die zijn opgesplitst in kleinere afbeeldingstegels, over de kaart heen te leggen. Deze tegels volgen het tegelsysteem van de kaart. Deze aanpak wordt vaak gebruikt om grote afbeeldingen of grote gegevenssets over de kaart heen te leggen.

In de volgende voorbeelden wordt een tegellaag met een weerradarafbeelding van Iowa Environment Mesonet van de Iowa State University boven de kaart weergegeven.

#### <a name="before-google-maps"></a>Vóór: Google Maps

In Google Maps kunnen tegellagen worden gemaakt met behulp van de klasse `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```



![Tegellaag in Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Voeg een tegellaag toe aan de kaart, net zoals u dat met elke andere laag doet. Gebruik een opgemaakte URL met de tijdelijke aanduidingen `{x}`, `{y}` en `{z}` voor respectievelijk x, y en het zoomniveau om aan te geven waar de tegels voor de laag moeten worden opgehaald. Tegellagen in Azure Maps ondersteunen ook de tijdelijke aanduidingen `{quadkey}`, `{bbox-epsg-3857}` en `{subdomain}`.

> [!TIP]
> In Azure Maps kunnen lagen eenvoudig onder andere lagen worden weergegeven, waaronder basiskaartlagen. Vaak is het wenselijk om tegellagen onder de kaartlabels weer te geven, zodat ze goed leesbaar zijn. Voor de methode `map.layers.add` is een tweede parameter nodig: de id van de laag waaronder de nieuwe laag moet worden geplaatst. Als u een tegellaag onder de kaartlabels wilt invoegen, gebruikt u deze code: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```



![Tegellaag in Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> Tegelaanvragen kunnen worden vastgelegd met behulp van de optie `transformRequest` van de kaart. Op deze manier kunt u desgewenst kopteksten in de aanvraag wijzigen of eraan toevoegen.

**Aanvullende informatie:**

- [Tegellagen toevoegen](map-add-tile-layer.md)
- [TileLayer-klasse](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [Opties voor tegellaag](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Verkeergegevens weergeven

Verkeersgegevens kunnen worden weergegeven over kaarten van zowel Azure Maps als Google Maps.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Gebruik de verkeerslaag om verkeersgegevens over de kaart heen te leggen.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```



![Verkeer in Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Azure Maps biedt verschillende opties voor het weergeven van verkeer. U kunt verkeersincidenten zoals wegafzettingen en ongelukken als pictogrammen weergeven op de kaart. De verkeersstroom en met kleuren gemarkeerde wegen kunt u over de kaart heen leggen. De kleuren kunnen worden gewijzigd op basis van de ingestelde snelheidslimiet, de gebruikelijke verwachte vertraging of de absolute vertraging. Incidentgegevens in Azure Maps worden elke minuut bijgewerkt en gegevens van verkeersstromen worden om de twee minuten bijgewerkt.

Wijs de gewenste waarden voor `setTraffic`-opties toe.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```



![Verkeer in Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Als u op een van de verkeerspictogrammen in Azure Maps klikt, wordt er extra informatie weergegeven in een pop-up.



![Verkeersincident in Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**Aanvullende informatie:**

- [Verkeer weergeven op de kaart](map-show-traffic.md)
- [Opties voor verkeersgegevens als overlay](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Een terrein-overlay toevoegen

Zowel Azure Maps als Google Maps biedt ondersteuning voor het weergeven van afbeeldingen met georeferenties op de kaart. Afbeeldingen met georeferenties worden verplaatst en verkleind/vergroot tijdens het pannen en zoomen van de kaart. In Google Maps worden afbeeldingen met georeferenties aangeduid als 'terrein- of wegdekoverlays' terwijl ze in Azure Maps worden aangeduid als afbeeldingslagen. Ze zijn ideaal voor het op een kaart weergeven van bouwtekeningen, oude kaarten of beelden van een drone.

#### <a name="before-google-maps"></a>Vóór: Google Maps

Geef de URL op van de afbeelding die u op de kaart wilt weergeven evenals een begrenzingsvak om de afbeelding aan de kaart te binden. In dit voorbeeld wordt een kaartafbeelding van [Newark New Jersey uit 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) over de kaart heen gelegd.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

![Overlay van afbeelding in Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

Gebruik de klasse `atlas.layer.ImageLayer` om afbeeldingen met georeferenties op een kaart weer te geven. Deze klasse vereist een URL van een afbeelding en een set coördinaten voor de vier hoeken van de afbeelding. De afbeelding moet worden gehost in hetzelfde domein of CORs moet zijn ingeschakeld voor de afbeelding.

> [!TIP]
> Als u alleen de gegevens voor north, south, east, west en rotation hebt en u niet voor elke hoek van de afbeelding coördinaten hebt, kunt u de statische methode [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) gebruiken.

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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



![Overlay van afbeelding in Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**Aanvullende informatie:**

- [Een afbeeldingslaag toevoegen aan een kaart](map-add-image-layer.md)
- [ImageLayer-klasse](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>KML-gegevens toevoegen aan de kaart

U kunt in zowel Azure Maps als Google Maps KML-, KMZ-en GeoRSS-gegevens importeren en weergeven op een kaart. Azure Maps biedt ook ondersteuning voor GPX, GML, ruimtelijke CSV-bestanden, GeoJSON, WKT (Well Known Text), WMS (Web-Mapping Services), WMTS (Web-Mapping Tile Services) en WFS (Web Feature Services). Azure Maps leest de bestanden lokaal in het geheugen en kan in de meeste gevallen veel grotere KML-bestanden verwerken. 

#### <a name="before-google-maps"></a>Vóór: Google Maps


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Als deze code wordt uitgevoerd in een browser, ziet u een kaart die eruitziet als de volgende afbeelding:

![Google Maps KML](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>Na: Azure Maps

In Azure Maps is GeoJSON de belangrijkste gegevensindeling die wordt gebruikt in de Web-SDK, maar met behulp van het pakket [azure-maps-spatial-io](/javascript/api/azure-maps-spatial-io/) kunt u ook eenvoudig aanvullende indelingen voor ruimtelijke gegevens integreren. Dit pakket bevat functies voor het lezen en schrijven van ruimtelijke gegevens en bevat ook een eenvoudige gegevenslaag waarmee u eenvoudig gegevens met een van deze indelingen voor ruimtelijke gegevens kunt weergeven. Als u de gegevens in een bestand met ruimtelijke gegevens wilt lezen, geeft u een URL of onbewerkte gegevens als een tekenreeks of blob door aan de functie `atlas.io.read`. Het resultaat bestaat uit alle geparseerde gegevens uit het bestand, die vervolgens aan de kaart kunnen worden toegevoegd. KML is iets ingewikkelder dan de meeste indelingen voor ruimtelijke gegevens aangezien deze indeling veel meer informatie over de stijl bevat. De klasse `SpatialDataLayer` ondersteunt het weergeven van het merendeel van deze stijlen, maar pictogramafbeeldingen moeten in de kaart worden geladen voordat de functiegegevens worden geladen en overlays voor terreinen moeten afzonderlijk als lagen aan de kaart worden toegevoegd. Wanneer u gegevens laadt via een URL, moet deze worden gehost op een eindpunt waarvoor CORs is ingeschakeld. In alle andere gevallen moet er een proxyservice worden doorgegeven als optie voor de functie read. 

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
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


![Azure Maps-KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>


**Aanvullende informatie:**

- [De functie atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Aanvullende codevoorbeelden

Dit zijn enkele aanvullende codevoorbeelden met betrekking tot de migratie van Google Maps:

- [Tekenhulpmiddelen](map-add-drawing-toolbar.md)
- [Slepen van map alleen met twee vingers](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning) (Engelstalige informatie)
- [Zoomen met schuifwiel beperken](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom) (Engelstalige informatie)
- [Een besturingselement voor het volledig scherm maken](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control) (Engelstalige informatie)

**Services:**

- [De Azure Maps Services-module gebruiken](how-to-use-services-module.md)
- [Zoeken naar bezienswaardigheden](map-search-location.md)
- [Informatie ophalen uit een coördinaat (omgekeerde geocode)](map-get-information-from-coordinate.md)
- [Routebeschrijving van A naar B](map-route.md)
- [Automatische suggesties zoeken met JQuery-gebruikersinterface](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Toewijzing van klassen van Google Maps v3 JavaScript-SDK aan klassen van Azure Maps Web-SDK

De volgende bijlage bevat een koppeling van de veelgebruikte klassen in de Google Maps v3 JavaScript-SDK en de equivalente Azure Maps Web-SDK.

### <a name="core-classes"></a>Core-klassen

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [atlas.Popup](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[atlas.CameraBoundsOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[atlas.ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[atlas.StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[atlas.UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [atlas.Pixel](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>Overlay-klassen

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[atlas.data.Point](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[atlas.layer.SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[atlas.SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[atlas.IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[atlas.TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[atlas.layer.BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[atlas.BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [atlas.data.Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [atlas.PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [atlas.LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [atlas.data.LineString](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[atlas.LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | Zie [Een cirkel toevoegen aan de kaart](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[atlas.ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>Serviceklassen

De Azure Maps Web-SDK bevat een services-module, die afzonderlijk kan worden geladen. Deze module verpakt de Azure Maps REST-services met een web-API en kan worden gebruikt in JavaScript-, TypeScript- en Node.js-toepassingen.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[atlas.SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[atlas.SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[atlas.SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[atlas.SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[atlas.SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[atlas.SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[atlas.SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[atlas.SearchPOIOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[atlas.SearchPOICategoryOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>Bibliotheken

Bibliotheken voegen extra functionaliteit toe aan de kaart. Veel van deze bibliotheken bevinden zich in de core-SDK van Azure Maps. Dit zijn enkele equivalente klassen die u kunt gebruiken in plaats van deze Google Maps-bibliotheken

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| Tekeningenbibliotheek       | [Module voor tekenprogramma's](set-drawing-options.md) |
| Geometriebibliotheek      | [atlas.math](/javascript/api/azure-maps-control/atlas.math)   |
| Visualisatiebibliotheek | [Heatmap-laag](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Maps Web-SDK:

> [!div class="nextstepaction"]
> [Map Control gebruiken](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [De module voor tekenprogramma's gebruiken](set-drawing-options.md)

> [!div class="nextstepaction"]
> [De services-module gebruiken](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [De ruimtelijke IO-module gebruiken](how-to-use-spatial-io-module.md)