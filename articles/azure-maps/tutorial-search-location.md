---
title: 'Zelfstudie: Zoeken naar nabijgelegen locaties op een kaart | Microsoft Azure Maps'
description: Zelfstudie over het zoeken naar nuttige plaatsen op een kaart. Bekijk hoe u de Web-SDK van Azure Maps kunt gebruiken om zoekmogelijkheden en interactieve pop-upvakken toe te voegen aan een kaart.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 31dd1c06b0f17b469454593131ccdc93b45b2446
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624964"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Zelfstudie: Zoeken naar nuttige plaatsen in de buurt met behulp van Azure Maps

Deze zelfstudie laat zien hoe u een account van Azure Maps instelt en vervolgens de API's van Maps gebruikt om te zoeken naar een nuttige plaats. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Maps-account maken
> * De primaire sleutel voor uw Maps-account ophalen
> * Een nieuwe webpagina maken met de kaartbesturingselement-API
> * De zoekservice van Maps gebruiken om een nuttige plaats in de buurt te vinden

## <a name="prerequisites"></a>Vereisten

<a id="createaccount"></a>
<a id="getkey"></a>

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Een nieuwe kaart maken

De Map Control-API is een handige clientbibliotheek. Met deze API kunt u eenvoudig kaarten integreren in uw webtoepassing. Het verbergt de complexiteit van het aanroepen van de REST-service en verhoogt de productiviteit met aanpasbare onderdelen. Gebruik de volgende stappen voor het maken van een statische HTML-pagina, ingesloten met de Map Control-API.

1. Maak een nieuw bestand op uw lokale computer en noem dit **MapSearch.html**.
2. Voeg de volgende HTML-onderdelen toe aan het bestand:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   U ziet dat de HTML-header de CSS- en JavaScript-bronbestanden bevat, gehost door de Azure Map Control-bibliotheek. Let op de gebeurtenis `onload` in het hoofdtekstgedeelte van de pagina. Deze zorgt ervoor dat de functie `GetMap` wordt aangeroepen nadat het hoofdtekstgedeelte van de pagina is geladen. De functie `GetMap` bevat de inline JavaScript-code voor toegang tot de API's van Azure Maps.

3. Voeg de volgende JavaScript-code toe aan de functie `GetMap` van het HTML-bestand. Vervang de tekenreeks `<Your Azure Maps Key>` door de primaire sleutel die u hebt gekopieerd uit uw Maps-account.

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

   Dit segment initialiseert de Map Control-API voor de sleutel van uw Azure Maps-account. `atlas` is de naamruimte die de API en gerelateerde visuele onderdelen bevat. `atlas.Map` biedt het besturingselement voor een visuele en interactieve webkaart.

4. Sla de wijzigingen op in het bestand en open de HTML-pagina in een browser. De getoonde kaart is de eenvoudigste kaart die u kunt maken door `atlas.Map` aan te roepen en de sleutel van uw account op te geven.

   ![De kaart weergeven](./media/tutorial-search-location/basic-map.png)

5. Voeg de volgende JavaScript-code toe aan de functie `GetMap` nadat de kaart is geïnitialiseerd.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   In dit code segment wordt een gebeurtenis `ready` toegevoegd aan de kaart, die wordt geactiveerd wanneer de kaartresources zijn geladen en de kaart gereed is om te worden geopend. In de gebeurtenis-handler voor kaart `ready` wordt een gegevensbron gemaakt om resultaatgegevens in op te slaan. Er wordt een symboollaag gemaakt en aan de gegevensbron gekoppeld. Deze laag geeft aan hoe de resultaatgegevens in de gegevensbron moeten worden gerenderd. In dit geval wordt het resultaat weergegeven met een donkerblauw rond speldpictogram, gecentreerd op de resultatencoördinaat en andere pictogrammen kunnen elkaar overlappen. De laag van het resultaat wordt toegevoegd aan de kaartlagen.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Zoekmogelijkheden toevoegen

In dit gedeelte ziet u hoe u met de [Maps Search-API](/rest/api/maps/search) een nuttige plaats vindt op de kaart. Het is een RESTful-API die is bedoeld voor ontwikkelaars om te zoeken naar adressen, nuttige plaatsen en andere geografische informatie. De Search-service wijst een breedtegraad en lengtegraad toe aan een opgegeven adres. Met de **servicemodule** die hieronder wordt beschreven, kunt u zoeken naar een locatie met de Kaarten zoeken-API.

### <a name="service-module"></a>Servicemodule

1. Maak in de gebeurtenis-handler voor de kaart `ready` de zoekservice-URL door de volgende Javascript-code toe te voegen.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   De `SubscriptionKeyCredential` maakt een `SubscriptionKeyCredentialPolicy` voor het verifiëren van HTTP-aanvragen voor Azure Maps met de abonnementssleutel. De `atlas.service.MapsURL.newPipeline()` neemt het beleid `SubscriptionKeyCredential` op en maakt een [pijplijn](/javascript/api/azure-maps-rest/atlas.service.pipeline)instantie. De `searchURL` staat voor een URL voor [zoek](/rest/api/maps/search)bewerkingen van Azure Maps.

2. Voeg vervolgens het volgende scriptblok toe om de zoekquery te maken. Hierin wordt de service Fuzzy zoeken gebruikt, een basiszoek-API van de Search Service. Via de service Fuzzy zoeken wordt de meeste fuzzy invoer verwerkt, zoals adressen, plaatsen en nuttige plaatsen. Met deze code wordt gezocht naar tankstations in de buurt, binnen een straal van de opgegeven breedtegraad en lengtegraad. Een GeoJSON-functiecollectie van het antwoord wordt vervolgens geëxtraheerd met de methode `geojson.getFeatures()` en toegevoegd aan de gegevensbron. Dit leidt er automatisch toe dat de gegevens worden weergegeven op de kaart via de symboollaag. In het laatste deel van het script wordt de cameraweergave van de kaarten weergegeven met behulp van het selectiekader van de resultaten met behulp van de eigenschap [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) van Maps.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Sla het bestand **MapSearch.html** op en vernieuw de browser. U ziet dat kaart gecentreerd rond Seattle en dat ronde blauwe spelden de locaties van tankstations in het gebied aangeven.

   ![De kaart met zoekresultaten weergeven](./media/tutorial-search-location/pins-map.png)

4. U kunt de onbewerkte gegevens die op de kaart worden weergegeven, zien door de volgende HTTPRequest in uw browser te typen. Vervang \<Your Azure Maps Key\> door uw primaire sleutel.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Op dit moment kunnen op de pagina MapSearch de locaties worden weergegeven van nuttige plaatsen die worden geretourneerd door een fuzzy zoekquery. Laten we enkele interactieve mogelijkheden en meer informatie over de locaties toevoegen.

## <a name="add-interactive-data"></a>Interactieve gegevens toevoegen

De kaart die we tot nu toe hebben gemaakt, is uitsluitend gebaseerd op de gegevens voor lengtegraad/breedtegraad voor de zoekresultaten. De onbewerkte JSON die de Search-service van Maps retourneert, bevat echter aanvullende informatie over elk station. Inclusief de naam en het adres. U kunt die gegevens opnemen in de kaart met behulp van interactieve pop-upvakken.

1. Voeg de volgende regels code toe aan de gebeurtenis-handler voor kaart `ready` na de code om een query uit te voeren op de service voor fuzzy zoeken. Met deze code wordt een exemplaar van een pop-upvenster gemaakt en een mouse-overgebeurtenis toegevoegd aan de symboollaag.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    De API `*atlas.Popup` voorziet in een informatievenster dat is verankerd op de juiste positie op de kaart. 

2. Voeg met de functie `GetMap` de volgende code toe om de resultaatgegevens van de mouse-overgebeurtenis in het pop-upvenster weer te geven.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Sla het bestand op en vernieuw de browser. De kaart in de browser toont nu informatie in pop-ups wanneer u de muisaanwijzer over een van de spelden beweegt.

    ![Azure Map Control en Search Service](./media/tutorial-search-location/popup-map.png)

Om de volledige code voor deze zelfstudie te zien, klik [hier](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html). Als u het live voorbeeld wilt weergeven, klikt u [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

De volgende zelfstudie laat zien hoe u een route tussen twee locaties weergeeft.

> [!div class="nextstepaction"]
> [Route naar een bestemming](./tutorial-route-location.md)