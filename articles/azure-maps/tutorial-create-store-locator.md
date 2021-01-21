---
title: 'Zelfstudie: Een winkelzoekertoepassing maken met behulp van Azure Maps | Microsoft Azure Maps'
description: Zelfstudie over het maken van webtoepassingen voor winkellocators. Gebruik de Azure Maps Web SDK om een webpagina te maken, een query op de zoekservice uit te voeren en de resultaten op een kaart weer te geven.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 801c2fe1710952a12584bf10dd8e5c77de3b839c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625095"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Zelfstudie: Een winkelzoeker maken met behulp van Azure Maps

Deze zelfstudie leidt u door het proces voor het maken van een eenvoudige winkelzoeker met behulp van Azure Maps. Winkelzoekers komen veel voor. Veel van de concepten die worden gebruikt in dit type toepassing zijn van toepassing op veel andere soorten toepassingen. Het aanbieden van een winkelzoeker aan klanten is een must voor de meeste bedrijven die rechtstreeks aan consumenten verkopen. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe webpagina maken met de Azure Map Control-API.
> * Aangepaste gegevens laden uit een bestand en weergeven op een kaart.
> * Gebruik de zoekservice van Azure Maps om een adres te vinden of een query in te voeren.
> * Haal de locatie van de gebruiker op van de browser en laat deze op de kaart zien.
> * Combineer meerdere lagen om aangepaste symbolen te maken op de kaart.  
> * Cluster gegevenspunten.  
> * Voeg besturingselementen voor in- en uitzoomen toe aan de kaart.

<a id="Intro"></a>

Spring vooruit naar het [live-winkelzoekervoorbeeld](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) of de [broncode](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator).

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account met de prijscategorie S1 maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="design"></a>Ontwerp

Voordat u in de code duikt, is het een goed idee om met een ontwerp te beginnen. Uw winkelzoeker kan zo eenvoudig of zo ingewikkeld zijn als u wilt. In deze zelfstudie maken we een eenvoudige winkelzoeker. We nemen onderweg enkele tips op om u te helpen sommige functies uit te breiden als u dat wilt. We maken een winkelzoeker voor het fictieve bedrijf Contoso Coffee. De volgende afbeelding toont een draadmodel van de algemene indeling van de winkelzoeker die we in deze zelfstudie bouwen:

![Draadmodel van een winkelzoekertoepassing voor locaties van Contoso Coffee-bars](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)

Om de bruikbaarheid van deze winkelzoeker te maximaliseren, gebruiken we een responsieve lay-out die wordt aangepast wanneer de schermbreedte van een gebruiker kleiner is dan 700 pixels. Een responsieve lay-out maakt het gemakkelijk om de winkelzoeker op een klein scherm te gebruiken, zoals op een mobiel apparaat. Hier volgt een draadmodel van een lay-out voor een klein scherm:  

![Draadmodel van de Contoso Coffee-winkelzoekertoepassing op een mobiel apparaat](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</

De draadmodellen tonen een redelijk eenvoudige toepassing. De toepassing heeft een zoekvak, een lijst met winkels in de buurt en een kaart met een aantal markeringen, zoals symbolen. En er wordt een pop-upvenster weergegeven met aanvullende informatie wanneer de gebruiker een markering selecteert. Meer specifiek zijn hier de functies die we in deze zelfstudie inbouwen in de winkelzoeker:

* Alle locaties van het geïmporteerde door tabs gescheiden gegevensbestand worden op de kaart geladen.
* De gebruiker kan de kaart verschuiven en zoomen, een zoekopdracht uitvoeren en de GPS-knop Mijn locatie selecteren.
* De pagina-indeling wordt aangepast aan de breedte van het scherm.  
* Er is een header met het winkellogo.  
* De gebruiker kan een zoekvak en zoekknop gebruiken om naar een locatie te zoeken, zoals een adres, postcode of plaats. 
* Een aan het zoekvak toegevoegde `keypress`-gebeurtenis start een zoekopdracht als de gebruiker op Enter drukt. Deze functionaliteit wordt vaak over het hoofd gezien, maar leidt tot een betere gebruikerservaring.
* Wanneer de kaart wordt verplaatst, wordt de afstand van elke locatie tot het midden van de kaart berekend. De resultatenlijst wordt bijgewerkt om de dichtstbijzijnde locaties bovenaan de kaart weer te geven.  
* Wanneer u een resultaat in de resultatenlijst selecteert, wordt de kaart gecentreerd op de geselecteerde locatie en verschijnt er informatie over de locatie in een pop-upvenster.  
* Het selecteren van een specifieke locatie op de kaart activeert ook een pop-upvenster.
* Wanneer de gebruiker uitzoomt, worden locaties gegroepeerd in clusters. Clusters worden afgebeeld als een cirkel met een getal erin. Clusters worden gevormd of lossen op als de gebruiker het zoomniveau wijzigt.
* Door een cluster te selecteren, wordt twee niveaus ingezoomd op de kaart en wordt de locatie van de cluster gecentreerd.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>De gegevensset met winkellocaties maken

Voordat we een winkelzoektoepassing ontwikkelen, moeten we een gegevensset maken van de winkels die we willen weergeven op de kaart. In deze zelfstudie gebruiken we een gegevensset voor een fictieve koffiebar met de naam Contoso Coffee. De gegevensset voor deze eenvoudige winkelzoeker wordt beheerd in een Excel-werkmap. De gegevensset bevat 10.213 locaties van Contoso Coffee-koffiebars, verdeeld over negen landen/regio's: de Verenigde Staten, Canada, het Verenigd Koninkrijk, Frankrijk, Duitsland, Italië, Nederland, Denemarken en Spanje. Hier volgt een schermopname van hoe de gegevens eruitzien:

![Schermopname van de gegevens voor de winkelzoeker in een Excel-werkmap](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)

U kunt [de Excel-werkmap downloaden](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

Als we de schermopname van de gegevens bekijken, zien we het volgende:

* Locatiegegevens worden opgeslagen in de kolommen **AddressLine** (adresregel), **City** (plaats), **Municipality** (gemeente), **AdminDivision** (staat/provincie), **PostCode** (postcode) en **Country** (land).  
* De kolommen **Latitude** (breedtegraad) en **Longitude** (lengtegraad) bevatten de coördinaten van elke locatie van een Contoso Coffee-koffiebar. Als u geen coördinatengegevens hebt, kunt u de zoekservices van Azure Maps gebruiken om de coördinaten van de locaties te bepalen.
* Enkele extra kolommen bevatten metagegevens met betrekking tot de koffiebars: een telefoonnummer, booleaanse kolommen en winkelopenings- en sluitingstijden in 24-uurs notatie. De Booleaanse kolommen zijn voor WiFi-beschikbaarheid en toegankelijkheid voor rolstoelgebruikers. U kunt uw eigen kolommen maken met metagegevens die relevanter zijn voor uw locatiegegevens.

> [!NOTE]
> In Azure Maps worden gegevens weergegeven in de sferische Mercator-projectie EPSG:3857, maar worden de gegevens gelezen in EPSG:4326 dat gebruikmaakt van de datum WGS84.

Er zijn veel manieren om de gegevensset in de toepassing beschikbaar te maken. Eén aanpak is om de gegevens in een database te laden en een webservice beschikbaar te maken die de gegevens opvraagt. U kunt de resultaten vervolgens naar de browser van de gebruiker verzenden. Deze optie is ideaal voor grote gegevenssets of voor gegevenssets die regelmatig worden bijgewerkt. Voor deze optie is echter meer ontwikkelingswerk vereist en heeft hogere kosten.

Een andere benadering is om deze dataset om te zetten in een bestand met platte tekst dat de browser gemakkelijk kan parseren. Het bestand zelf kan worden gehost bij de rest van de toepassing. Deze optie houdt het eenvoudig, maar het is alleen een goede optie voor kleinere gegevenssets, omdat de gebruiker alle gegevens downloadt. We gebruiken het platte-tekstbestand voor deze gegevensset, omdat de bestandsgrootte kleiner is dan 1 MB.  

Als u de werkmap wilt converteren naar een platte-tekstbestand, slaat u de werkmap op als een door tabs gescheiden bestand. De kolommen worden gescheiden door tabtekens, zodat de kolommen gemakkelijk te parseren zijn in onze code. U zou de CSV-indeling kunnen gebruiken (bestand met door komma's gescheiden waarden), maar voor die optie is meer parseringslogica nodig. Elk veld dat door komma's wordt gescheiden, zou dan worden omgeven door aanhalingstekens. Om deze gegevens in Excel te exporteren als een door tabs gescheiden bestand, selecteert u **Opslaan als**. Selecteer in de vervolgkeuzelijst **Opslaan als** de optie **Tekst (tab is scheidingsteken) (*.txt)** . Noem het bestand *ContosoCoffee.txt*.

![Schermopname van het dialoogvenster Opslaan als](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)

Als u het bestand in Kladblok opent, ziet dit eruit als in de volgende afbeelding:

![Schermafbeelding van een Kladblok-bestand met een door tabs gescheiden gegevensset](./media/tutorial-create-store-locator/StoreDataTabFile.png)

## <a name="set-up-the-project"></a>Het project instellen

Voor het maken van het project kunt u [Visual Studio](https://visualstudio.microsoft.com) of de code-editor van uw keuze gebruiken. Maak in de projectmap drie bestanden: *index.html*, *index.css* en *index.js*. Deze bestanden definiëren de lay-out, stijl en logica voor de toepassing. Maak een map met de naam *data* en voeg *ContosoCoffee.txt* toe aan deze map. Maak een andere map met de naam *images* (afbeeldingen). We gebruiken 10 afbeeldingen in deze toepassing, voor pictogrammen, knoppen en markeringen op de kaart. U kunt [deze afbeeldingen downloaden](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Uw projectmap zou er nu uit moeten zien als in de volgende afbeelding:

![Schermafbeelding van de projectmap Simple Store Locator](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)

## <a name="create-the-user-interface"></a>De gebruikersinterface maken

Voor het maken van de gebruikersinterface voegt u code toe aan *index.html*:

1. Voeg de volgende `meta`-tags toe aan de `head` van *index.html*. Met de `charset`-tag wordt de tekenset (UTF-8) gedefinieerd. Met de waarde van `http-equiv` verplicht u de gebruiker om de nieuwste browserversies van Internet Explorer en Microsoft Edge te gebruiken. En met de laatste `meta`-tag geeft u een viewport op die geschikt is voor responsieve indelingen.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Voeg verwijzingen toe naar de JavaScript- en CSS-bestanden voor de Azure Maps-webbesturingselementen :

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Voeg een verwijzing toe naar de servicesmodule van Azure Maps. De module is a JavaScript-bibliotheek die de Azure Maps REST-services verpakt en ze gemakkelijk in JavaScript te gebruiken maakt. De module is handig voor het aandrijven van de zoekfunctionaliteit.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Voeg verwijzingen toe naar *index.js* en *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. Voeg in de hoofdtekst van het document een `header`-tag toe. Voeg in de `header`-tag het logo en de bedrijfsnaam toe.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Voeg een `main`-tag toe en maak een zoekvenster met een tekstvak en zoekknop. Voeg ook `div`-tags toe met verwijzingen naar de kaart, het lijstvenster en de GPS-knop Mijn locatie.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Wanneer u klaar bent zou *index.html* eruit moeten zien als [dit voorbeeldbestand index.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

De volgende stap is het definiëren van de CSS-stijlen. CSS-stijlen definiëren de lay-out van de onderdelen van de toepassing en het uiterlijk van de toepassing. Open *index.css* en voeg de volgende code eraan toe. De `@media`-stijl definieert alternatieve stijlopties om te gebruiken wanneer het scherm minder dan 700 pixels breed is.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Wanneer u de toepassing nu uitvoert, ziet u de header, het zoekvak en de knop Zoeken. Maar de kaart is niet zichtbaar omdat deze nog niet is geladen. Als u een zoekopdracht probeert uit te voeren, gebeurt er niets. We moeten de JavaScript-logica instellen, die wordt beschreven in de volgende sectie. Deze logica heeft toegang tot alle functies van de winkelzoeker.

## <a name="wire-the-application-with-javascript"></a>De toepassing aansluiten met JavaScript

Nu is alles ingesteld in de gebruikersinterface. We moeten nog steeds JavaScript toevoegen om de gegevens te laden en te parseren, en de gegevens vervolgens weergeven op de kaart. Open *index.js* en voeg code toe zoals beschreven in de volgende stappen.

1. Voeg algemene opties toe om het gemakkelijker te maken instellingen bij te werken. Definieer de variabelen voor de kaart, het pop-upvenster, de gegevensbron, de pictogramlaag en de HTML-markering. Stel de HTML-markering in om het midden van een zoekgebied aan te geven. Definieer tevens een exemplaar van de Azure Maps-zoekserviceclient.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Voeg code toe aan *index.js*. Met de volgende code wordt de kaart geïnitialiseerd. Er is een [gebeurtenislistener](/javascript/api/azure-maps-control/atlas.map#events) toegevoegd om te wachten tot de pagina is geladen. Vervolgens zijn er gebeurtenissen samengesteld voor het bewaken van het laden van de kaart, waarbij de zoekknop en de knop Mijn locatie in werking wordt gesteld.

   Wanneer de gebruiker de zoekknop selecteert, of een locatie intypt in het zoekvak en op Enter drukt, wordt een fuzzy zoekopdracht gestart met de zoekopdracht van de gebruiker. Geef een matrix met ISO 2-land-/regiowaarden door aan de `countrySet`-optie om de zoekresultaten te beperken tot die landen/regio's. Het beperken van de te doorzoeken landen/regio's helpt de nauwkeurigheid van de geretourneerde zoekresultaten te verhogen. 
  
   Zodra de zoekopdracht is voltooid, wordt het eerste resultaat genomen en wordt de kaartcamera op dat gebied gezet. Wanneer de gebruiker de knop Mijn locatie selecteert, haalt u de locatie van de gebruiker op met behulp van de HTML5 Geolocation-API. Deze API is ingebouwd in de browser. Centreer vervolgens de kaart boven hun locatie.  

   > [!Tip]
   > Wanneer u een pop-upvensters gebruikt, is het raadzaam één `Popup`-instantie te maken en deze te hergebruiken door de inhoud en positie ervan bij te werken. Voor elke `Popup`-instantie die u toevoegt aan uw code, worden meerdere DOM-elementen toegevoegd aan de pagina. Hoe meer DOM-elementen er op een pagina zijn, hoe meer dingen de browser moet bijhouden. Als er te veel items zijn, kan de browser traag worden.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. Voeg in de `ready`-gebeurtenislistener van de kaart een zoombesturingselement en een HTML-markering toe om het midden van een zoekgebied weer te geven.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. Voeg in de `ready`-gebeurtenislistener van de kaart een gegevensbron toe. Maak vervolgens een aanroep om de gegevensset te laden en te parseren. Schakel clustering voor de gegevensbron in. Door clustering van de gegevensbrongroepen worden overlappende punten samengevoegd tot een cluster. De clusters worden gescheiden in afzonderlijke wanneer de gebruiker inzoomt. Dit gedrag zorgt voor een betere gebruikerservaring en verbetert de prestaties.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Nadat u de gegevensset in de `ready`-gebeurtenislistener van de kaart hebt geladen, definieert u een set lagen om de gegevens weer te geven. Er wordt een bellenlaag gebruikt om geclusterde gegevenspunten weer te geven. Er wordt een symboollaag wordt gebruikt om het aantal punten in elk cluster boven de bellenlaag weer te geven. Met een tweede symboollaag wordt een aangepast pictogram voor afzonderlijke locaties op de kaart weergegeven.

   Voeg `mouseover`- en `mouseout`-gebeurtenissen toe aan de bellen- en pictogramlagen om de muisaanwijzer te wijzigen wanneer de gebruiker een cluster of pictogram op de kaart aanwijst. Voeg een `click`-gebeurtenis toe aan de clusterbellenlaag. Deze `click`-gebeurtenis zoomt twee niveaus in op de kaart en centreert de kaart op een cluster wanneer de gebruiker een cluster selecteert. Voeg een `click`-gebeurtenis toe aan de pictogramlaag. Deze `click`-gebeurtenis geeft een pop-upvenster met de details van een koffiebar weer wanneer een gebruiker een individueel locatiepictogram selecteert. Voeg een gebeurtenis toe aan de kaart om te controleren wanneer de kaart klaar is met bewegen. Wanneer deze gebeurtenis wordt geactiveerd, worden de items in het deelvenster met de lijst bijgewerkt.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Wanneer de gegevensset met koffiebars wordt geladen, moet deze eerst worden gedownload. Vervolgens moet het tekstbestand worden gesplitst in regels. De eerste regel bevat de headerinformatie. Om de code gemakkelijker te volgen te maken, parseren we de header in een object, dat we vervolgens kunnen gebruiken om de celindex van elke eigenschap op te zoeken. Na de eerste regel doorloopt u de resterende regels en maakt u een puntelement. Voeg het puntelement toe aan de gegevensbron. Werk tot slot het lijstvenster bij.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Wanneer de deelvensterlijst wordt bijgewerkt, wordt de afstand berekend. Deze afstand is van het midden van de kaart tot alle puntelementen in de huidige kaartweergave. De punten worden vervolgens gesorteerd op afstand. Er wordt HTML-code gegenereerd om elke locatie in het lijstvenster weer te geven.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Wanneer de gebruiker een item in het lijstvenster selecteert, wordt de vorm waaraan het item is gerelateerd, opgehaald uit de gegevensbron. Er wordt een pop-upvenster gegenereerd op basis van de eigenschapsgegevens die zijn opgeslagen in de vorm. De kaart wordt gecentreerd op de vorm. Als de kaart minder dan 700 pixels breed is, wordt de kaartweergave verschoven zodat het pop-upvenster zichtbaar is.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Nu hebt u een volledig functionele winkelzoeker. Open het *index.html*-bestand van de winkelzoeker in een webbrowser. Wanneer de clusters op de kaart worden weergegeven, kunt u naar een locatie zoeken met behulp van het zoekvak, door de knop Mijn locatie te selecteren, door een cluster te selecteren of door in te zoomen op de kaart om afzonderlijke locaties te bekijken.

De eerste keer dat een gebruiker de knop Mijn locatie selecteert, geeft de browser een beveiligingswaarschuwing weer die om toestemming vraagt ​​voor toegang tot de locatie van de gebruiker. Als de gebruiker ermee instemt om zijn/haar locatie te delen, zoomt de kaart in op de locatie van de gebruiker en worden nabijgelegen koffiebars getoond.

![Schermafbeelding van de vraag van de browser om toegang tot de locatie van de gebruiker](./media/tutorial-create-store-locator/GeolocationApiWarning.png)

Wanneer u sterk genoeg inzoomt op een gebied met koffiebarlocaties, worden de clusters gescheiden in afzonderlijke locaties. Selecteer een van de pictogrammen op de kaart of selecteer een item in het zijpaneel om een ​​pop-upvenster te bekijken. In het pop-upvenster wordt informatie weergegeven voor de geselecteerde locatie.

![Schermopname van de voltooide winkelzoeker](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)

Als u het formaat van het browservenster verkleint tot minder dan 700 pixels breed of de toepassing op een mobiel apparaat opent, verandert de lay-out zodat deze beter geschikt is voor kleinere schermen.

![Schermopname van de versie van de winkelzoeker voor kleine schermen](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)

In deze zelfstudie hebt u geleerd hoe u een eenvoudige winkellocator kunt maken met Azure Maps. De winkelzoeker die u in deze zelfstudie maakt, heeft mogelijk alle functionaliteit die u nodig hebt. U kunt functies toevoegen aan uw winkelzoeker of meer geavanceerde functies gebruiken voor een meer aangepaste gebruikerservaring: 

 * Schakel [suggesties terwijl u typt](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) in het zoekvak in.  
 * Voeg [ondersteuning voor meerdere talen](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization) toe. 
 * Geef de gebruiker de mogelijkheid [locaties langs een route te filteren](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
 * Voeg de mogelijkheid toe om [filters in te stellen](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
 * Voeg ondersteuning toe voor het opgeven van een aanvankelijke zoekwaarde door een queryreeks te gebruiken. Wanneer u deze optie in uw winkelzoeker opneemt, kunnen gebruikers zoekopdrachten markeren met een bladwijzer en delen. Dit biedt ook een eenvoudige methode waarmee u zoekopdrachten naar deze pagina kunt doorgeven vanaf een andere pagina.  
 * Implementeer uw winkelzoeker als een [Azure App Service-web-app](../app-service/quickstart-html.md). 
 * Sla uw gegevens op in een database en zoek naar nabijgelegen locaties. Zie voor meer informatie [SQL Server spatial data types overview](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017) (overzicht van ruimtelijke gegevenstypen in SQL Server) en [Query spatial data for the nearest neighbor](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017) (ruimtelijke gegevens opvragen voor de dichtstbijzijnde buren).

U kunt de [volledige broncode weergeven](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator), een [livevoorbeeld weergeven](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator) en meer informatie bekijken over de dekking en mogelijkheden van Azure Maps met behulp van [zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md). U kunt ook [gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md) die u kunt toepassen op uw bedrijfslogica.

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

Voor meer voorbeelden van code en interactieve codering:

> [!div class="nextstepaction"]
> [Map Control gebruiken](how-to-use-map-control.md)
