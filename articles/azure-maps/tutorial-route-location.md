---
title: 'Zelfstudie: Een route naar een locatie zoeken | Microsoft Azure Maps'
description: Zelfstudie over het vinden van een route naar een nuttige plaats. Ontdek hoe u adrescoördinaten instelt en query's uitvoert op de Azure Maps Route-service voor een routebeschrijving naar de plaats.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 1f1647766eeae917f7a2266ebb8f00fdfab3452e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210575"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Zelfstudie: Routebeschrijvingen weergeven met behulp van de Azure Maps-routeservice en een kaartbesturingselement

In deze zelfstudie ziet u hoe u de Azure Maps-[routeservice-API ](/rest/api/maps/route) en een [kaartbesturingselement](./how-to-use-map-control.md) kunt gebruiken om de routebeschrijving van het begin- tot het eindpunt weer te geven. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het kaartbesturingselement maken en weergeven op een webpagina. 
> * Definiëren hoe de route wordt weergegeven door [symboollagen](map-add-pin.md) en [lijnlagen](map-add-line-layer.md) te definiëren.
> * GeoJSON-objecten maken en toevoegen aan de kaart om begin- en eindpunten aan te duiden.
> * Routebeschrijving van de begin- en eindpunten ophalen met behulp van de [API Routebeschrijving ophalen](/rest/api/maps/route/getroutedirections).

U kunt de volledige broncode voor het voorbeeld [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html) ophalen. Een livevoorbeeld is [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination) te vinden.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Het kaartbesturingselement maken en weergeven

De volgende stappen laten zien hoe u het kaartbesturingselement maakt en weergeeft op een webpagina.

1. Maak een nieuw bestand op uw lokale computer en noem dit **MapRoute.html**.
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Eenvoudige kaartweergave van kaartbesturingselement":::

## <a name="define-route-display-rendering"></a>Rendering van routeweergave definiëren

In deze zelfstudie gaat u met behulp van een lijnlaag de route weergeven. De begin- en eindpunten worden weergegeven met behulp van een symboollaag. Zie [Een lijnlaag toevoegen aan een kaart](map-add-line-layer.md) voor meer informatie over het toevoegen van lijnlagen. Zie [Een symboollaag toevoegen aan een kaart](map-add-pin.md) voor meer informatie over symboollagen.

1. Voeg de volgende JavaScript-code toe aan de functie `GetMap`. Met deze code wordt de gebeurtenis-handler `ready` van het kaartbesturingselement geïmplementeerd. De rest van de code in deze zelfstudie wordt binnen de gebeurtenis-handler `ready` geplaatst.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    });
    ```

    In de gebeurtenis-handler `ready` van het kaartbesturingselement wordt een gegevensbron gemaakt waarin de route vanaf het begin- tot het en eindpunt wordt opgeslagen. Er wordt een lijnlaag gemaakt en aan de gegevensbron gekoppeld om te definiëren hoe de routelijn wordt weergegeven.  Er is een tweede parameter met de waarde `'labels'` doorgegeven om ervoor te zorgen dat de routelijn de labels van de weg niet bedekt.

    Vervolgens wordt er een symboollaag gemaakt en aan de gegevensbron gekoppeld. Deze laag geeft aan hoe de begin- en eindpunten worden weergegeven. In dit geval zijn er expressies toegevoegd om de afbeelding van het pictogram en de tekstlabelgegevens op te halen uit de eigenschappen van elk puntobject. Zie [Gegevensgestuurde stijlexpressies](data-driven-style-expressions-web-sdk.md) voor meer informatie over expressies.

2. Stel Microsoft in als beginpunt en een benzinestation in Seattle als eindpunt.  Voeg de volgende code toe aan de gebeurtenis-handler `ready` van het kaartbesturingselement.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Deze code maakt twee [GeoJSON-puntobjecten](https://en.wikipedia.org/wiki/GeoJSON) die de begin- en eindpunten vertegenwoordigen, die vervolgens aan de gegevensbron worden toegevoegd. 

    In het laatste codeblok wordt de cameraweergave ingesteld met de breedtegraad en de lengtegraad van het begin- en eindpunt. De begin- en eindpunten worden toegevoegd aan de gegevensbron. Het begrenzingsvak voor de begin- en eindpunten wordt berekend met behulp van de functie `atlas.data.BoundingBox.fromData`. Dit begrenzingsvak wordt gebruikt om de cameraweergave van de kaart met behulp van de functie `map.setCamera` in te stellen op de hele route. Er wordt opvulling toegevoegd om de grootte van de pixels in de symboolpictogrammen te compenseren. Zie de eigenschap [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) voor meer informatie over de eigenschap setCamera van het kaartbesturingselement.

3. Sla **MapRoute.html** op en vernieuw de browser. De kaart is nu gecentreerd op Seattle. De blauwe druppelvormige speld duidt het beginpunt aan. De blauwe ronde speld duidt het eindpunt aan.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Begin- en eindpunt van routes op de kaart weergeven":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Routebeschrijving ophalen

In deze sectie wordt beschreven hoe u de API voor routebeschrijvingen van Azure Maps kunt gebruiken om routebeschrijvingen van het ene punt naar het andere en de geschatte aankomsttijd op te halen.

>[!TIP]
>De routeservices van Azure Maps bieden API's om routes te plannen op basis van verschillende routetypen zoals *snelste*, *kortste*, *zuinigste* of *leukste*, waarbij rekening wordt gehouden met afstand, verkeersomstandigheden en het gebruikte vervoersmiddel. Met de service kunnen gebruikers ook toekomstige routes plannen op basis van historische verkeersomstandigheden. Gebruikers kunnen de voorspelling van de duur van de route voor elke gewenste tijd bekijken. Zie [API Routebeschrijving ophalen](/rest/api/maps/route/getroutedirections) voor meer informatie.

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

2. Nadat u referenties en de URL hebt ingesteld, voegt u de volgende code toe aan de gebeurtenis-handler `ready` van het besturingselement. Met deze code wordt de route van het beginpunt naar het eindpunt samengesteld. De `routeURL` vraagt de API Azure Maps-routeservice om de routebeschrijving te berekenen. Met behulp van de methode `geojson.getFeatures()` wordt vervolgens uit het antwoord een verzameling GeoJSON-kenmerken geëxtraheerd en aan de gegevensbron toegevoegd.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Sla het bestand **MapRoute.html** op en vernieuw de webbrowser. Op de kaart zou nu de route van het begin- tot het eindpunt moeten zijn weergegeven.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Azure-kaartbesturingselement en routeservice":::

U kunt de volledige broncode voor het voorbeeld [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html) ophalen. Een livevoorbeeld is [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination) te vinden.

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

De volgende zelfstudie laat zien hoe u een routequery maakt met beperkingen, zoals de manier van reizen of het type lading. U kunt vervolgens meerdere routes weergeven op dezelfde kaart.

> [!div class="nextstepaction"]
> [Routes vinden voor verschillende manieren van reizen](./tutorial-prioritized-routes.md)