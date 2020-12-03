---
title: C#-zelfstudie over het ordenen van resultaten
titleSuffix: Azure Cognitive Search
description: In deze C#-zelfstudie wordt gedemonstreerd hoe u zoekresultaten kunt ordenen. Het is het vervolg van een eerder hotelproject, en gaat over sorteren op primaire en secundaire eigenschappen, en omvat een scoreprofiel om boostingcriteria toe te voegen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/02/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5a55a330f6f4fefb86f2c056cd0ca3b2ba5f4b29
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499591"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Zelfstudie: Zoekresultaten toevoegen met behulp van de .NET SDK

In deze reeks zelfstudies zijn resultaten geretourneerd en weergegeven in een [standaardvolgorde](index-add-scoring-profiles.md#what-is-default-scoring). In deze zelfstudie voegt u primaire en secundaire sorteercriteria toe. Als alternatief voor ordenen op basis van numerieke waarden ziet u in het laatste voorbeeld hoe u resultaten kunt ordenen op basis van een aangepast scoreprofiel. We gaan ook dieper in op de weergave van _complexe typen_.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Resultaten ordenen op basis van één eigenschap
> * Resultaten ordenen op basis van meerdere eigenschappen
> * Resultaten filteren op basis van een afstand tot een geografisch punt
> * Resultaten ordenen op basis van een scoreprofiel

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt het project met oneindig schuiven dat is gemaakt in de zelfstudie [Paginering toevoegen aan zoekresultaten](tutorial-csharp-paging.md) uitgebreid.

Een voltooide versie van de code in deze zelfstudie vindt u in het volgende project:

* [5-order-results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Vereisten

* De oplossing [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll). Dit project kan uw eigen versie zijn die is gebaseerd op de vorige zelfstudie of een kopie van GitHub.

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket [Azure.Search.Documents (versie 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10) voor een eerdere versie van de .NET SDK.

## <a name="order-results-based-on-one-property"></a>Resultaten ordenen op basis van één eigenschap

Wanneer resultaten worden geordend op basis van één eigenschap, bijvoorbeeld hotelclassificaties, hebben we niet alleen de geordende resultaten nodig, maar willen we ook zeker weten dat de volgorde klopt. Als het beoordelingsveld aan de resultaten wordt toegevoegd, kunnen we bevestigen dat de resultaten correct zijn gesorteerd.

In deze oefening voegen we voor elk hotel ook wat meer informatie toe aan de weergave met resultaten: het goedkoopste en het duurste kamertarief.

Het is niet nodig om modellen te wijzigen om ordenen in te schakelen. Alleen de weergave en de controller moeten zijn bijgewerkt. Begin met het openen van de startcontroller.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>De parameter OrderBy toevoegen aan de zoekparameters

1. Voeg de optie **OrderBy** toe aan de naam van de eigenschap. Voeg in de methode **Index(SearchData model)** de volgende regel toe aan de zoekparameters.

    ```cs
    OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > De standaardvolgorde is oplopend, maar u kunt **asc** toevoegen aan de eigenschap om dit duidelijk te maken. Een aflopende volgorde wordt opgegeven door **desc** toe te voegen.

1. Voer de app nu uit en voer een veelvoorkomende zoekterm in. De resultaten kunnen wel of niet de juiste volgorde hebben, aangezien noch u, noch de ontwikkelaar of de gebruiker, over een eenvoudige manier beschikt om de resultaten te controleren!

1. Laten we duidelijk aangeven dat de resultaten zijn geordend op basis van classificatie. Vervang eerst de klassen **vak1** en **vak2** in het bestand hotels.css door de volgende klassen (deze klassen zijn alle nieuwe klassen die we nodig hebben voor deze zelfstudie).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > CSS-bestanden worden meestal opgeslagen in de cache. Dit kan ertoe leiden dat een oud CSS-bestand wordt gebruikt en uw bewerkingen worden genegeerd. Een goede manier om dit te voorkomen is om een querytekenreeks met een versieparameter toe te voegen aan de koppeling. Bijvoorbeeld:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Werk het versienummer bij als u denkt dat de browser een oud CSS-bestand gebruikt.

1. Voeg de eigenschap **Classificatie** toe aan de parameter **Selecteren** in de methode **Index(SearchData model)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

1. Open de weergave (index.cshtml) en vervang de rendering-lus ( **&lt;!-- Show the hotel data. --&gt;** ) door de volgende code.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. De classificatie moet zowel beschikbaar zijn op de eerste zichtbare pagina als op de volgende pagina's die worden aangeroepen via de balk voor oneindig scrollen. Voor de laatste van deze twee situaties moeten we zowel de actie **Volgende** in de controller, als de functie **Gescrold** in de weergave bijwerken. Begin met voor de controller de methode **Volgende** te wijzigen in de volgende code. Met deze code wordt de classificatietekst gemaakt en gecommuniceerd.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Werk nu de functie **Gescrold** in de weergave bij om de classificatietekst weer te geven.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

1. Voer de app nu opnieuw uit. Zoek op een veelvoorkomende term, bijvoorbeeld ‘wifi’, en controleer of de resultaten voor hotelclassificatie in aflopende volgorde zijn geordend.

    ![Ordenen op basis van classificatie](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    U ziet nu dat verschillende hotels dezelfde classificatie hebben. De volgorde waarin deze hotels worden weergegeven is dus wederom de volgorde waarin de gegevens zijn gevonden. Dit is willekeurig.

    Voordat we een tweede volgordeniveau toevoegen, gaan we code toevoegen om het bereik van kamertarieven weer te geven. We voegen deze code toe om het uitpakken van gegevens uit een _complex type_ te demonstreren, en ook zodat we het ordenen van resultaten op basis van prijs kunnen bespreken (bijvoorbeeld: goedkoopste eerst).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Het bereik van kamertarieven toevoegen aan de weergave

1. Voeg eigenschappen met de goedkoopste en de duurste kamer toe aan het Hotel.cs-model.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

1. Bereken de kamertarieven aan het einde van de actie **Index(SearchData model)** in de startcontroller. Voeg de berekeningen toe na het opslaan van tijdelijke gegevens.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

1. Voeg de eigenschap **Kamers** toe aan de parameter **Selecteren** in de actiemethode **Index(SearchData model)** van de controller.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

1. Wijzig de rendering-lus in de weergave om het tariefbereik weer te geven voor de eerste pagina van de resultaten.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Wijzig de methode **Volgende** in de startcontroller om het tariefbereik te communiceren voor de volgende pagina’s met resultaten.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Werk de functie **Gescrold** in de weergave bij om de tekst voor kamertarieven te verwerken.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

1. Voer de app uit en controleer of de bereiken voor kamertarieven worden weergegeven.

    ![Bereiken voor kamertarieven weergeven](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

De eigenschap **OrderBy** van de zoekparameters accepteert geen vermelding zoals **Rooms.BaseRate** om het goedkoopste kamertarief te bieden, zelfs als de kamers al zijn gesorteerd op tarief. In dit geval zijn de kamers niet gesorteerd op tarief. Om hotels in de voorbeeldgegevensset weer te geven geordend op kamertarief, moet u de resultaten ordenen in de startcontroller, en deze resultaten in de gewenste volgorde naar de weergave verzenden.

## <a name="order-results-based-on-multiple-values"></a>Resultaten ordenen op basis van meerdere waarden

De vraag is nu hoe we onderscheid kunnen maken tussen hotels met dezelfde classificatie. Een goede manier is om te ordenen op basis van de laatste keer dat het hotel is gerenoveerd. Met andere woorden, hoe recenter het hotel is gerenoveerd, hoe hoger het hotel wordt weergegeven in de resultaten.

1. Als u een tweede volgordeniveau wilt toevoegen, wijzigt u de eigenschappen **OrderBy** en **Select** in de methode **Index(SearchData model)** om de eigenschap **LastRenovationDate** op te nemen.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Een willekeurig aantal eigenschappen kan worden ingevoerd in de lijst **OrderBy**. Als hotels dezelfde classificatie en renovatiedatum hebben, kan er een derde eigenschap worden ingevoerd om onderscheid te maken.

1. We willen wederom de renovatiedatum zien in de weergave, om er zeker van te zijn dat de volgorde klopt. Voor zoiets als een renovatie is waarschijnlijk alleen het jaar vereist. Wijzig de rendering-lus in de weergave in de volgende code.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

1. Wijzig de methode **Volgende** in de startcontroller om het jaaronderdeel van de laatste renovatiedatum door te sturen.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Wijzig de functie **Gescrold** in de weergave om de renovatietekst weer te geven.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

1. Voer de app uit. Zoek op een veelvoorkomende term, bijvoorbeeld ‘zwembad’ of ‘uitzicht’, en controleer of hotels met dezelfde classificatie nu in aflopende volgorde van renovatiedatum worden weergegeven.

    ![Ordenen op renovatiedatum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Resultaten filteren op basis van een afstand tot een geografisch punt

Classificatie en renovatiedatum zijn voorbeelden van eigenschappen die het beste in aflopende volgorde kunnen worden weergegeven. Een alfabetische lijst is een voorbeeld van een nuttige toepassing van de oplopende volgorde. (Bijvoorbeeld: als er slechts één eigenschap **OrderBy** is en deze is ingesteld op **HotelName**, wordt een alfabetische volgorde weergegeven.) Voor onze voorbeeldgegevens is afstand tot een geografisch punt echter geschikter.

Als u resultaten wilt weergeven op basis van geografische afstand, zijn verschillende stappen vereist.

1. Filter alle hotels die zich buiten een bepaalde omtrek van het opgegeven punt bevinden, door een filter in te voeren met parameters voor lengtegraad, breedtegraad en radius. Lengtegraad wordt het eerst doorgegeven aan de functie PUNT. Radius is in kilometers.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

1. Met het bovenstaande filter worden de resultaten _niet_ geordend op basis van afstand, maar worden alleen de uitschieters verwijderd. Als u de resultaten wilt ordenen, voert u een instelling **OrderBy** in waarmee de methode geoDistance wordt opgegeven.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

1. Hoewel de resultaten worden geretourneerd via Azure Cognitive Search met behulp van een afstandsfilter, wordt de berekende afstand tussen de gegevens en het opgegeven punt _niet_ geretourneerd. Bereken de waarde in de weergave of controller opnieuw als u deze wilt weergeven in de resultaten.

    Met de volgende code wordt de afstand berekend tussen twee lengtegraad/breedtegraad-punten.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

1. Nu moet u deze concepten aan elkaar koppelen. Bij deze codefragmenten stopt onze zelfstudie echter. We laten het aan de lezer zelf om te oefenen met het bouwen van een kaart-app. Als u dit voorbeeld wilt uitbreiden, kunt u overwegen om een plaatsnaam in te voeren met een radius, of een punt op de kaart aan te wijzen en een radius te selecteren. Raadpleeg de volgende resources om deze opties verder te onderzoeken:

* [Azure Maps-documentatie](../azure-maps/index.yml)
* [Een adres vinden met behulp van de Azure Maps-zoekservice](../azure-maps/how-to-search-for-address.md)

## <a name="order-results-based-on-a-scoring-profile"></a>Resultaten ordenen op basis van een scoreprofiel

De voorbeelden in de zelfstudie laten tot dusver zien hoe u kunt ordenen op basis van numerieke waarden (classificatie, renovatiedatum, geografische afstand), wat een _exact_ proces voor ordenen oplevert. Sommige zoekacties en sommige gegevens lenen zich echter niet voor een dergelijke eenvoudige vergelijking tussen twee gegevenselementen. Azure Cognitive Search omvat het concept van _Scoren_. _Scoreprofielen_ kunnen worden opgegeven voor een set gegevens die kunnen worden gebruikt om meer complexe en kwalitatieve vergelijkingen te bieden. Dit is handig wanneer u bijvoorbeeld gegevens op basis van tekst wilt vergelijken om te bepalen wat eerst moet worden weergegeven.

Scoreprofielen worden niet gedefinieerd door gebruikers, maar meestal door beheerders van een gegevensset. Er zijn verschillende scoreprofielen ingesteld voor de hotelgegevens. We gaan nu kijken hoe een scoreprofiel wordt gedefinieerd, en vervolgens gaan we code schrijven om te zoeken met deze profielen.

### <a name="how-scoring-profiles-are-defined"></a>Scoreprofielen definiëren

Hier zijn drie voorbeelden van scoreprofielen. We gaan kijken hoe elk van deze voorbeelden _als het goed is_ de volgorde van resultaten bepaalt. Als app-ontwikkelaar schrijft u deze profielen niet. Ze worden geschreven door de gegevensbeheerder, maar het is wel handig om de syntaxis te bekijken.

1. Dit is het standaardscoreprofiel voor de hotelgegevensset, dat wordt gebruikt wanneer u geen parameter **OrderBy** of **ScoringProfile** opgeeft. Dit profiel zorgt voor een hogere _score_ voor een hotel als de zoektekst aanwezig is in de hotelnaam, beschrijving, of lijst met tags (voorzieningen). U ziet dat de scores voor bepaalde velden zwaarder wegen. Als de zoektekst in een ander veld verschijnt, dat hierboven niet wordt weergegeven, heeft het een gewicht van 1. Hoe hoger de score, hoe eerder een resultaat verschijnt in de weergave.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

1. Het volgende scoreprofiel zorgt voor een aanzienlijk hogere score, als een opgegeven parameter een of meer tags (wat wij voorzieningen noemen) uit de lijst bevat. Het belangrijkste punt van dit profiel is dat een parameter met de tekst _moet_ zijn opgegeven. Als de parameter leeg is of niet is opgegeven, wordt een fout gegenereerd.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

1. In dit derde voorbeeld zorgt de classificatie voor een aanzienlijk hogere score. De laatste renovatiedatum zorgt ook voor een hogere score, maar alleen als deze gegevens binnen 730 dagen (2 jaar) van de huidige datum vallen.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Nu gaan we kijken of deze profielen werken zoals we willen!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Code toevoegen aan de weergave om profielen te vergelijken

1. Open het bestand index.cshtml en vervang de &lt;hoofdsectie&gt; door de volgende code.

    ```html
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Open het bestand SearchData.cs en vervang de klasse **SearchData** door de volgende code.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Open het bestand hotels.css en voeg de volgende HTML-klassen toe.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Code toevoegen aan de controller om een scoreprofiel op te geven

1. Open het bestand voor de homecontroller. Voeg het volgende toe **met behulp van** de instructie (voor hulp bij het maken van lijsten).

    ```cs
    using System.Linq;
    ```

1. Voor dit voorbeeld hebben we de eerste aanroep naar **Index** nodig om iets meer te doen dan alleen de eerste weergave retourneren. Met de methode wordt nu gezocht naar maximaal 20 voorzieningen om weer te geven.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. We hebben twee privémethoden nodig om de facetten op te slaan in een tijdelijke opslag, en om ze op te halen uit de tijdelijke en een model te vullen.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. We moeten de parameters **OrderBy** en **ScoringProfile** instellen als vereist. Vervang de bestaande methode **Index(SearchData model)** door het volgende.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Lees de opmerkingen voor elk van de **switch**-selecties.

5. Er hoeven geen wijzigingen te worden aangebracht in de actie **Volgende**, als u de aanvullende code voor de vorige sectie hebt voltooid voor ordenen op basis van meerdere eigenschappen.

### <a name="run-and-test-the-app"></a>De app uitvoeren en testen

1. Voer de app uit. Als het goed is, ziet u nu een volledige set voorzieningen in de weergave.

1. Als u voor de volgorde de optie Op numerieke classificatie selecteert, krijgt u de numerieke volgorde die u al hebt geïmplementeerd in deze zelfstudie, waarbij de renovatiedatum de beslissende factor is bij hotels met dezelfde classificatie.

   ![De tag ‘strand’ ordenen op basis van classificatie](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Probeer nu de optie Op voorzieningen uit. Maak verschillende selecties met voorzieningen en controleer of hotels met deze voorzieningen hoger worden weergegeven in de lijst met resultaten.

   ![De tag ‘strand’ ordenen op basis van profiel](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Probeer het profiel Op renovatiedatum/Classificatieprofiel uit om te kijken of u de verwachte resultaten te zien krijgt. Alleen onlangs gerenoveerde hotels moeten een hogere score krijgen op basis van _renovatie_.

### <a name="resources"></a>Resources

Raadpleeg [Scoreprofielen toevoegen aan een Azure Cognitive Search-index](./index-add-scoring-profiles.md) voor meer informatie.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Gebruikers verwachten dat zoekresultaten zijn geordend van meest naar minst relevant.
* Gegevens moeten zijn gestructureerd zodat ze eenvoudig kunnen worden geordend. We kunnen niet eenvoudig ordenen op ‘Goedkoopste’ eerst, omdat de gegevens niet zijn gestructureerd om te worden geordend zonder aanvullende code.
* Er kunnen veel volgordeniveaus zijn, om op een hoger volgordeniveau onderscheid te maken tussen resultaten met dezelfde waarde.
* Het is normaal dat sommige resultaten worden geordend in oplopende volgorde (bijvoorbeeld afstand ten opzichte van een bepaald punt), en andere in aflopende volgorde (bijvoorbeeld de classificatie van gasten).
* Scoreprofielen kunnen worden gedefinieerd wanneer numerieke vergelijkingen niet beschikbaar zijn, of niet slim genoeg zijn, voor een gegevensset. Het scoren van alle resultaten helpt bij het intelligent ordenen en weergeven van de resultaten.

## <a name="next-steps"></a>Volgende stappen

U hebt deze reeks C#-zelfstudies voltooid. Als het goed is, hebt u waardevolle kennis opgedaan van de Azure Cognitive Search API’s.

Voor verdere naslaginformatie en zelfstudies, kunt u naar [Microsoft Learn](/learn/browse/?products=azure) gaan, of een van de andere zelfstudies bekijken in de [Azure Cognitive Search-documentatie](./index.yml).