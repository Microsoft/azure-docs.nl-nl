---
title: 'Zelfstudie: Meerdere routes op basis van vervoersoptie | Microsoft Azure Maps'
description: Zelfstudie over het gebruik van Azure Maps om routes voor specifieke reismodi te vinden naar nuttige plaatsen. Ontdek hoe u meerdere routes op kaarten kunt weergeven.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 5e7f38e4bbc95e04bf54f8f7a2dd80e21891e8df
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215490"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Zelfstudie: Routes vinden en weergeven voor verschillende manieren van reizen met Azure Maps

In deze zelfstudie leert u hoe u de [Route-service](/rest/api/maps/route) van Azure Maps en [Map control](./how-to-use-map-control.md) gebruikt om routebeschrijvingen voor zowel privé- als bedrijfsvoertuigen (vrachtwagens) met het ladingtype `USHazmatClass2` weer te geven. Daarnaast leest u hoe u realtime verkeersgegevens op een kaart visualiseert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het kaartbesturingselement maken en weergeven op een webpagina
> * Realtime verkeersgegevens op een kaart weergeven
> * De route van privé- en bedrijfsvoertuigen op een kaart aanvragen en weergeven

## <a name="prerequisites"></a>Vereisten

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. [Maak een Azure Maps-account](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

U kunt de volledige broncode voor het voorbeeld [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) ophalen. Een livevoorbeeld is [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel) te vinden.

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Een nieuwe webpagina maken met de kaartbesturingselement-API

De volgende stappen laten zien hoe u het kaartbesturingselement maakt en weergeeft op een webpagina.

1. Maak een nieuw bestand op uw lokale computer en noem dit **MapTruckRoute.html**.
2. Kopieer de volgende HTML-opmaak en plak deze in het bestand.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

     De HTML-header bevat de CSS- en JavaScript-bronbestanden die worden gehost door de Azure-bibliotheek voor kaartbesturingselementen. De gebeurtenis `onload` van de hoofdtekst roept de functie `GetMap` aan. In de volgende stap voegen we de initialisatiecode van het kaartbesturingselement toe.

3. Voeg de volgende JavaScript-code toe aan de functie `GetMap`. Vervang de tekenreeks `<Your Azure Maps Key>` door de primaire sleutel die u hebt gekopieerd uit uw Maps-account.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

4. Sla het bestand op en open het in uw browser. Er wordt een eenvoudig kaartbesturingselement weergegeven.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Eenvoudige kaartweergave van kaartbesturingselement":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Realtime verkeersgegevens op een kaart weergeven

1. Voeg de volgende JavaScript-code toe aan de functie `GetMap`. Met deze code wordt de gebeurtenis-handler `ready` van het kaartbesturingselement geïmplementeerd. De rest van de code in deze zelfstudie wordt binnen de gebeurtenis-handler `ready` geplaatst.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    In de gebeurtenis-handler voor kaart `ready` is de instelling voor de verkeersstroom op de kaart ingesteld op `relative`, dit is de snelheid van de weg ten opzichte van een situatie met een vrije verkeersstroom. Zie [TrafficOptions-interface](/javascript/api/azure-maps-control/atlas.trafficoptions) voor meer verkeersopties.

2. Sla het bestand **MapTruckRoute.html** op en vernieuw de pagina in uw browser. Als u inzoomt op een stad, zoals Los Angeles, ziet u dat de straten worden weergegeven met de huidige verkeersstroomgegevens.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Verkeer op een kaart weergeven":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Rendering van routeweergave definiëren

In deze zelfstudie worden twee routes berekend en weergegeven op de kaart. De eerste route wordt berekend voor een privévoertuig (auto). De tweede route wordt berekend voor een bedrijfsvoertuig (vrachtwagen) om het verschil tussen de resultaten aan te geven. Samen met de routes wordt een symboolpictogram weergegeven dat het begin- en het eindpunt van elke route aanduidt evenals lijnen met een andere kleur voor elk routepad. Zie [Een lijnlaag toevoegen aan een kaart](map-add-line-layer.md) voor meer informatie over het toevoegen van lijnlagen. Zie [Een symboollaag toevoegen aan een kaart](map-add-pin.md) voor meer informatie over symboollagen.

1. Voeg de volgende code toe aan de gebeurtenis-handler `ready` van het kaartbesturingselement.

    ```JavaScript

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a layer for rendering the route lines and have it render under the map labels.
    map.layers.add(new atlas.layer.LineLayer(datasource, null, {
        strokeColor: ['get', 'strokeColor'],
        strokeWidth: ['get', 'strokeWidth'],
        lineJoin: 'round',
        lineCap: 'round'
    }), 'labels');

    //Add a layer for rendering point data.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: ['get', 'icon'],
            allowOverlap: true
        },
        textOptions: {
            textField: ['get', 'title'],
            offset: [0, 1.2]
        },
        filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
    }));

    ```


    In de gebeurtenis-handler `ready` van Map control wordt een gegevensbron gemaakt waarin de route vanaf het begin- tot het eindpunt wordt opgeslagen. Er wordt gebruikgemaakt van [expressies](data-driven-style-expressions-web-sdk.md) om de lijndikte en kleur op te halen uit de eigenschappen van de routelijnfunctie. Er is een tweede parameter met de waarde `'labels'` doorgegeven om ervoor te zorgen dat de routelijn de labels van de weg niet bedekt.

    Vervolgens wordt er een symboollaag gemaakt en aan de gegevensbron gekoppeld. Deze laag geeft aan hoe de begin- en eindpunten worden weergegeven. In dit geval zijn er expressies toegevoegd om de afbeelding van het pictogram en de tekstlabelgegevens op te halen uit de eigenschappen van elk puntobject. Zie [Gegevensgestuurde stijlexpressies](data-driven-style-expressions-web-sdk.md)voor meer informatie over expressies.

2. Stel het beginpunt in als een fictief bedrijf in Seattle, genaamd Fabrikam, en als eindpunt (bestemming) een kantoor van Microsoft.  Voeg de volgende code toe aan de gebeurtenis-handler `ready` van het kaartbesturingselement.


    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Deze code maakt twee [GeoJSON-puntobjecten](https://en.wikipedia.org/wiki/GeoJSON) die de begin- en eindpunten vertegenwoordigen, die vervolgens aan de gegevensbron worden toegevoegd.

    In het laatste codeblok wordt de cameraweergave ingesteld met de breedtegraad en de lengtegraad van het begin- en eindpunt. De begin- en eindpunten worden toegevoegd aan de gegevensbron. Het begrenzingsvak voor de begin- en eindpunten wordt berekend met behulp van de functie `atlas.data.BoundingBox.fromData`. Dit begrenzingsvak wordt gebruikt om de cameraweergave van de kaart met behulp van de functie `map.setCamera` in te stellen op de hele route. Er wordt opvulling toegevoegd om de grootte van de pixels in de symboolpictogrammen te compenseren. Zie de eigenschap [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) voor meer informatie over de eigenschap setCamera van het kaartbesturingselement.

3. Sla **TruckRoute.html** op en vernieuw de browser. De kaart is nu gecentreerd op Seattle. De blauwe druppelvormige speld duidt het beginpunt aan. De blauwe ronde speld duidt het eindpunt aan.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Kaart met begin- en eindpunt weergeven":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>De route van privé- en bedrijfsvoertuigen op een kaart aanvragen en weergeven

In deze sectie wordt beschreven hoe u de API Azure Maps-routeservice kunt gebruiken om routebeschrijving van het ene naar het andere punt op te halen, op basis van uw vervoersmiddel. We gebruiken twee vervoermiddelen: vrachtwagen en auto.

>[!TIP]
>De Route-service biedt API's om de *snelste*, *kortste*, *zuinigste* of *leukste* route te plannen, waarbij rekening wordt gehouden met afstand, verkeersomstandigheden en vervoersmiddel. Met de service kunnen gebruikers ook toekomstige routes plannen op basis van historische verkeersomstandigheden. Gebruikers kunnen de voorspelling van de duur van de route voor elke gewenste tijd bekijken. Zie [API Routebeschrijving ophalen](/rest/api/maps/route/getroutedirections) voor meer informatie.

1. Voeg in de functie `GetMap` in de gebeurtenis-handler `ready` van het besturingselement het volgende toe aan de JavaScript-code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   De `SubscriptionKeyCredential` maakt een `SubscriptionKeyCredentialPolicy` voor het verifiëren van HTTP-aanvragen voor Azure Maps met de abonnementssleutel. De `atlas.service.MapsURL.newPipeline()` neemt het beleid `SubscriptionKeyCredential` op en maakt een [pijplijn](/javascript/api/azure-maps-rest/atlas.service.pipeline)instantie. De `routeURL` staat voor een URL voor [route](/rest/api/maps/route)bewerkingen van Azure Maps.

2. Nadat u de referenties en de URL hebt ingesteld, voegt u de volgende JavaScript-code toe om een vrachtwagenroute van het begin- tot het eindpunt te maken. Deze route wordt gemaakt en weergegeven voor een vrachtwagen die lading van de klasse `USHazmatClass2` vervoert.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    De bovenstaande code voert een query uit op de Azure Maps Route-service via de [Azure Maps Route Directions-API](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). De routelijn wordt vervolgens uit de verzameling GeoJSON-kenmerken geëxtraheerd en aan de gegevensbron toegevoegd met de methode `geojson.getFeatures()`. Ten slotte wordt de routeregel toegevoegd aan de gegevensbron. We voegen het toe aan de index van 0, om ervoor te zorgen dat de vrachtwagenroute wordt gerenderd vóór eventuele andere regels in de gegevensbron, omdat de berekening van de vrachtwagenroute vaak langzamer is dan de berekening van een autoroute. Als de routelijn voor de vrachtwagen wordt toegevoegd aan de gegevensbron na de autoroute, wordt deze hierboven weergegeven. Er worden twee eigenschappen toegevoegd aan de lijn voor de vrachtwagenroute: een blauwe lijnkleur en een lijndikte van negen pixels.

    >[!TIP]
    > Als u alle mogelijke opties en waarden voor de Azure Maps Route Directions-API wilt weergeven, raadpleegt u [URI-parameters voor Post Route Directions](/rest/api/maps/route/postroutedirections#uri-parameters).

3. Voeg nu de volgende JavaScript-code toe om een route voor een auto te maken.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    De bovenstaande code voert een query uit op de Azure Maps Route-service via de methode [Azure Maps Route Directions-API](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). De routelijn wordt vervolgens uit de verzameling GeoJSON-kenmerken geëxtraheerd en aan de gegevensbron toegevoegd met de methode `geojson.getFeatures()`. Ten slotte wordt de routeregel toegevoegd aan de gegevensbron. Er worden twee eigenschappen toegevoegd aan de lijn voor de vrachtwagenroute: een paarse lijnkleur en een lijndikte van vijf pixels.

4. Sla het bestand **TruckRoute.html** op en vernieuw de webbrowser. De kaart zou nu de vrachtwagen- en auto-routes moeten weergeven.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Routes voor privé- en bedrijfsvoertuigen op een kaart met Azure Route Service":::

    De vrachtwagenroute wordt weergegeven met een dikke blauwe lijn. De autoroute wordt weergegeven met een dunne paarse lijn. De autoroute gaat over Lake Washington via I-90 door tunnels onder woongebieden. Omdat de tunnels zich dicht bij de buurt bevinden, is de lading gevaarlijk afval verboden. De route voor vrachtwagens, met een lading met de Amerikaanse gevarenklasse `USHazmatClass2`, loopt via een andere snelweg.

U kunt de volledige broncode voor het voorbeeld [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html) ophalen. Een livevoorbeeld is [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel) te vinden.

U kunt ook [gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

In de volgende zelfstudie ziet u het proces voor het maken van een eenvoudige winkellocator met behulp van Azure Maps.

> [!div class="nextstepaction"]
> [Een winkellocator maken met behulp van Azure Maps](./tutorial-create-store-locator.md)