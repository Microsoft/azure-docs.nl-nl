---
title: C#-zelfstudie voor het maken van uw eerste app
titleSuffix: Azure Cognitive Search
description: Meer informatie over het stapsgewijs maken van uw eerste C#-zoek-app. De zelf studie biedt zowel een download koppeling naar een werkende app op GitHub als het volledige proces voor het helemaal samen bouwen van de app.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695121"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Zelfstudie: Uw eerste zoek-app maken met behulp van de .NET SDK

In deze zelf studie leert u hoe u een web-app maakt waarmee de resultaten van een zoek index worden opgevraagd en geretourneerd met behulp van Azure Cognitive Search en Visual Studio.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een ontwikkelomgeving instellen
> * Gegevensstructuren modelleren
> * Een webpagina maken voor het verzamelen van query invoer en het weer geven van resultaten
> * Een zoek methode definiëren
> * De app testen

U leert ook hoe eenvoudig een zoekaanroep is. De belangrijkste instructies in de code die u ontwikkelt, zijn in de volgende paar regels ingekapseld.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Slechts één aanroep voert een query uit op de index en retourneert resultaten.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Zoeken naar '*pool*'" border="true":::

## <a name="overview"></a>Overzicht

In deze zelf studie wordt gebruikgemaakt van de hotels-voor beeld-index, die u snel kunt maken in uw eigen zoek service door de Snelstartgids voor het [importeren van gegevens](search-get-started-portal.md)stapsgewijs te door lopen. De index bevat fictieve Hotel gegevens, beschikbaar als ingebouwde gegevens bron in elke zoek service.

In de eerste les in deze zelf studie maakt u een eenvoudige query structuur en zoek pagina, die u in volgende lessen kunt uitbreiden met paginering, facetten en een type-ahead ervaring.

Een voltooide versie van de code kunt u vinden in het volgende project:

* [1-basis-zoeken-pagina (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket Azure.Search.Documents (versie 11). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10) voor een eerdere versie van de .NET SDK.

## <a name="prerequisites"></a>Vereisten

* [Een bestaande zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [maken](search-create-service-portal.md) of zoeken.

* Het hotels-voor beeld-index maken met behulp van de instructies in [Quick Start: een zoek index maken](search-get-started-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Cognitive Search-client bibliotheek voor Azure (versie 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Het project installeren en uitvoeren vanuit GitHub

Als u verder wilt gaan naar een werkende app, volgt u de onderstaande stappen om de voltooide code te downloaden en uit te voeren.

1. Zoek het voorbeeld op GitHub: [Eerste app maken](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. Selecteer in de [hoofdmap](https://github.com/Azure-Samples/azure-search-dotnet-samples) **code**, gevolgd door een **kloon** of **zip downloaden** om uw persoonlijke lokale kopie van het project te maken.

1. Ga in Visual Studio naar en open de oplossing voor de pagina basis zoeken (' 1-Basic-Search-Page ') en selecteer **start zonder fout opsporing** (of druk op F5) om het programma te bouwen en uit te voeren.

1. Dit is een Hotels index. Typ dus enkele woorden die u kunt gebruiken om te zoeken naar hotels (bijvoorbeeld ' WiFi ', ' weer gave ', ' balk ', ' parkeren ') en Bekijk de resultaten.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Zoeken naar '*wifi*'" border="true":::

Hopelijk werkt dit project soepel en wordt er een web-app uitgevoerd. Veel van de essentiële onderdelen voor complexere zoekopdrachten zijn opgenomen in deze ene app. Daarom is het een goed idee om deze te doorlopen en stap voor stap opnieuw te maken. In de volgende secties worden deze stappen besproken.

## <a name="set-up-a-development-environment"></a>Een ontwikkelomgeving instellen

Begin met een Visual Studio-project om dit project helemaal zelf te maken en zodoende de concepten van Azure Cognitive Search in uw gedachte te versterken.

1. Selecteer in Visual Studio **New**  >  **project** en **ASP.net core Web Application**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Een cloudproject maken" border="true":::

1. Geef het project een naam zoals "FirstSearchApp" en stel de locatie in. Selecteer **Maken**.

1. Kies de project sjabloon **Webtoepassing (model-view-controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Een MVC-project maken" border="true":::

1. Installeer de client bibliotheek. In **hulpprogram ma's**  >  **NuGet package manager**  >  **beheert u NuGet-pakketten voor oplossing...** selecteert u **Bladeren** en zoekt u naar "azure.search.documents". Installeer **Azure.Search.Documents** (versie 11 of hoger) en accepteer de licentie overeenkomsten en afhankelijkheden.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="NuGet gebruiken om Azure-bibliotheken toe te voegen" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search initialiseren

Voor dit voor beeld gebruikt u openbaar beschik bare Hotel gegevens. Deze gegevens zijn een willekeurige verzameling van 50 fictieve hotelnamen en beschrijvingen die alleen zijn gemaakt voor het leveren van demogegevens. Geef een naam en API-sleutel op om toegang te krijgen tot deze gegevens.

1. Open **appsettings.jsop** en vervang de standaard regels door de URL van de zoek service (in de indeling `https://<service-name>.search.windows.net` ) en een [beheerder-of query-API-sleutel](search-security-api-keys.md) van uw zoek service. Omdat u geen index hoeft te maken of bij te werken, kunt u de query sleutel gebruiken voor deze zelf studie.

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. In Solution Explorer selecteert u het bestand en wijzigt u in eigenschappen de instelling **kopiëren naar uitvoer Directory** naar **kopiëren indien nieuwer**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="De app-instellingen naar de uitvoer kopiëren" border="true":::

## <a name="model-data-structures"></a>Gegevensstructuren modelleren

Modellen (C#-klassen) worden gebruikt om gegevens te communiceren tussen de client (de weergave), de server (de controller) en ook de Azure-cloud met behulp van de MVC-architectuur (model, view, controller). Normaal gesproken wordt in deze modellen de structuur weergegeven van de gegevens die worden geopend.

In deze stap modelt u de gegevens structuren van de zoek index en de zoek teken reeks die wordt gebruikt in weer gave/controller-communicatie. In de hotels-index heeft elk hotel veel kamers en elk hotel heeft een adres met meerdere delen. Samen is de volledige weer gave van een hotel een hiërarchische en geneste gegevens structuur. U hebt drie klassen nodig om elk onderdeel te maken.

De set met **Hotels**, **adres** en **room** -klassen heet [*complexe typen*](search-howto-complex-data-types.md), een belang rijke functie van Azure Cognitive Search. Complexe typen kunnen bestaan uit een groot aantal klassen en subklassen, en veel complexere gegevensstructuren weergeven dan *eenvoudige typen* (een klasse met alleen primitieve leden).

1. Klik in Solution Explorer met de rechter muisknop op **modellen**  >  **toevoegen**  >  **Nieuw item**.

1. Selecteer **klasse** en noem het item Hotel. cs. Vervang alle inhoud van Hotel.cs door de volgende code. Let op het **adres** en de **kamer** leden van de klasse. deze velden zijn zelf klassen zelf, zodat u er ook modellen voor nodig hebt.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. Herhaal hetzelfde proces voor het maken van een model voor de **adres** klasse, waarbij u het bestands adres. cs een naam geeft. Vervang de inhoud door het volgende.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. Volg opnieuw hetzelfde proces om de klasse **Room** te maken, en geef het bestand de naam Room.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Het laatste model dat u in deze zelf studie maakt, is een klasse met de naam **SearchData** en vertegenwoordigt de invoer van de gebruiker (**brons**) en de uitvoer van de zoek opdracht (**resultList**). Het type uitvoer is kritiek, **searchresults &lt; Hotel &gt;**, omdat dit type precies overeenkomt met de resultaten van de zoek opdracht, en u deze verwijzing wilt door geven naar de weer gave. Vervang de standaard sjabloon door de volgende code.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Een webpagina maken

Project sjablonen worden geleverd met een aantal client weergaven die zich in de map **views** bevinden. De exacte weer gaven zijn afhankelijk van de versie van Core .NET die u gebruikt (3,1 wordt in dit voor beeld gebruikt). Voor deze zelf studie wijzigt u **index. cshtml** zodat de elementen van een zoek pagina worden meegenomen.

Verwijder de inhoud van Index.cshtml in zijn geheel en bouw het bestand opnieuw op in de volgende stappen.

1. In de zelf studie worden twee kleine afbeeldingen in de weer gave gebruikt: een Azure-logo en een pictogram voor zoeken in vergroot glas (azure-logo.png en search.png). Kopieer over de installatie kopieën van het project GitHub naar de map **wwwroot/afbeeldingen** in uw project.

1. De eerste regel van index. cshtml moet verwijzen naar het model dat wordt gebruikt om gegevens te communiceren tussen de client (de weer gave) en de server (de-controller), het **SearchData** -model dat eerder is gemaakt. Voeg deze regel toe aan het bestand Index.cshtml file.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Het is gebruikelijk om een titel voor de weergave in te voeren. De volgende regels moeten dus als volgt zijn:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Na de titel voert u een verwijzing in naar een HTML-opmaak model, dat u binnenkort gaat maken.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. In de hoofd tekst van de weer gave worden twee use-cases verwerkt. Eerst moet het een lege pagina bevatten bij het eerste gebruik voordat de Zoek tekst wordt ingevoerd. Ten tweede moeten de resultaten naast het zoekvak worden verwerkt voor herhaalde query's.

   Als u beide gevallen wilt afhandelen, moet u controleren of het model dat aan de weer gave is gegeven, null is. Een null-model duidt op de eerste use-case (de eerste keer dat de app wordt uitgevoerd). Voeg het volgende toe aan het bestand Index.cshtml en lees de opmerkingen.

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Voeg het opmaak model toe. Selecteer in Visual Studio in **bestand** >  **Nieuw**  >  **bestand** de optie **stijl blad** (met **Algemeen** gemarkeerd).

   Vervang de code door het volgende. We gaan niet meer uitgebreid in op dit bestand. De stijlen zijn standaard-HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. Sla het opmaak model bestand op als hotels. CSS, in de map **wwwroot/CSS** , naast het standaard bestand site. CSS.

Hiermee is de weergave voltooid. Op dit moment worden de modellen en weer gaven voltooid. Alleen de controller blijft over om alles aan elkaar te koppelen.

## <a name="define-methods"></a>Methoden definiëren

In deze stap gaat u naar de inhoud van de **Start controller**.

1. Open het HomeController.cs-bestand en vervang de **using**-instructies door het volgende.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Index-methoden toevoegen

In een MVC-app is de methode **index ()** een standaard actie methode voor elke controller. De index-HTML-pagina wordt geopend. De standaard methode, die geen para meters accepteert, wordt in deze zelf studie gebruikt voor het gebruik van de toepassing: een lege zoek pagina weer geven.

In deze sectie wordt de methode voor het ondersteunen van een tweede use-case uitgebreid: de pagina wordt weer gegeven wanneer een gebruiker de Zoek tekst heeft ingevoerd. Ter ondersteuning van dit geval wordt de index methode uitgebreid om een model als para meter te maken.

1. Voeg de volgende methode toe na de standaard **Index()** -methode.

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Let op de **async**-declaratie van de methode en de **await**-aanroep naar **RunQueryAsync**. Deze sleutel woorden zorgen ervoor dat er asynchrone aanroepen worden gedaan, waardoor het blok keren van threads op de server wordt voor komen.

    Het blok **Catch** maakt gebruik van het fout model dat standaard is gemaakt.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>De foutafhandeling en andere standaardweergaven en -methoden bekijken

Afhankelijk van welke versie van .NET core u gebruikt, wordt standaard een iets andere set standaardweergaven gemaakt. Voor .NET Core 3,1 zijn de standaard weergaven index, privacy en fout beschikbaar. U kunt deze standaard pagina's weer geven tijdens het uitvoeren van de app en onderzoeken hoe deze worden verwerkt in de controller.

U gaat de fout weergave later in deze zelf studie testen.

In het GitHub-voor beeld worden ongebruikte weer gaven en de bijbehorende acties verwijderd.

### <a name="add-the-runqueryasync-method"></a>De methode RunQueryAsync toevoegen

De aanroep van Azure Cognitive Search is ingekapseld in onze **RunQueryAsync**-methode.

1. Voeg eerst enkele statische variabelen toe om de Azure-service in te stellen en een aanroep om deze te initiëren.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Voeg nu de **RunQueryAsync**-methode zelf toe.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    In deze methode moet u eerst controleren of de Azure-configuratie is gestart en vervolgens een aantal Zoek opties instellen. Met de optie **selecteren** geeft u op welke velden in resultaten moeten worden geretourneerd en dus overeenkomen met de eigenschaps namen in de categorie **Hotel** . Als u het **selectie vakje niet inschakelt**, worden alle niet-verborgen velden geretourneerd. Dit kan inefficiënt zijn als u alleen geïnteresseerd bent in een subset van alle mogelijke velden.

    Met de asynchrone aanroep voor zoeken wordt de aanvraag geformuleerd (gemodelleerd als **brons**) en het antwoord (gemodelleerd als **searchResult**). Als u fouten opspoort voor deze code, is de klasse **SearchResult** een goede kandidaat voor het instellen van een breek punt als u de inhoud van **model. resultList** wilt controleren. U ziet dat dit intuïtief is, en u de gegevens krijgt die u hebt gevraagd en niet veel meer.

### <a name="test-the-app"></a>De app testen

Nu gaan we controleren of de app correct wordt uitgevoerd.

1. Selecteer **fout opsporing**  >  **starten zonder fout opsporing** of druk op **F5**. Als de app wordt uitgevoerd zoals verwacht, moet u de initiële index weergave ophalen.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="De app openen" border="true":::

1. Voer een query reeks in, zoals ' strand ' (of een tekst die in aanmerking komt), en klik op het zoek pictogram om de aanvraag te verzenden.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Zoeken naar '*beach*'" border="true":::

1. Probeer 'five star' in te voeren. U ziet dat deze query geen resultaten oplevert. Een geavanceerdere zoekopdracht zou 'five star' behandelen als synoniem voor 'luxury' en deze resultaten retourneren. Ondersteuning voor [synoniemen](search-synonyms.md) is beschikbaar in azure Cognitive Search, maar wordt niet behandeld in deze reeks zelf studies.

1. Voer 'hot' in als zoektekst. Er worden _geen_ vermeldingen met het woord 'hotel' opgehaald. Onze zoekopdracht zoekt alleen naar hele woorden, maar er worden wel enkele resultaten opgehaald.

1. Probeer andere woorden: 'pool','sunshine', 'view', enzovoort. U ziet dat Azure Cognitive Search heel eenvoudig werkt, maar wel overtuigend is.

## <a name="test-edge-conditions-and-errors"></a>Voorwaarden en fouten aan de rand testen

Het is belangrijk om te controleren of de functies voor foutafhandeling werken zoals zou moeten, zelfs wanneer alles perfect werkt. 

1. Voer in de **Index**-methode, na de aanroep **try {** de regel **Throw new Exception()** in. Deze uitzonde ring zorgt voor een fout wanneer u zoekt op tekst.

2. Voer de app uit, voer 'bar' in als zoektekst en klik op het zoekpictogram. De uitzondering moet de foutweergave tot gevolg hebben.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Een fout afdwingen" border="true":::

    > [!Important]
    > Het wordt beschouwd als een beveiligingsrisico om interne foutnummers op foutpagina's te retourneren. Doe als uw app bedoeld is voor algemeen gebruik onderzoek naar veilige en beste procedures voor wat er moet worden geretourneerd als er een fout optreedt.

3. Verwijder **Throw new Exception()** wanneer u tevreden bent over de foutafhandeling.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Een Azure Cognitive Search-aanroep is beknopt en het is eenvoudig om de resultaten te interpreteren.
* Asynchrone aanroepen voegen enige complexiteit toe aan de controller, maar zijn de best practice als u van plan bent om kwaliteits-apps te ontwikkelen.
* Deze app heeft een zoek opdracht voor eenvoudig tekst uitgevoerd, gedefinieerd door wat is ingesteld in **Search Options worden**. Deze ene klasse kan echter worden gevuld met veel leden die verfijning toevoegen aan een zoekopdracht. Er is niet veel extra werk nodig om deze app aanzienlijk krachtiger te maken.

## <a name="next-steps"></a>Volgende stappen

Als u de gebruikers ervaring wilt verbeteren, kunt u meer functies toevoegen, met name paginering (met behulp van pagina nummers of oneindig schuiven), en automatisch aanvullen/suggesties. U kunt ook meer geavanceerde zoek opties overwegen (bijvoorbeeld geografische Zoek opdrachten op hotels binnen een opgegeven straal van een bepaald punt en de volg orde van de zoek resultaten).

Deze volgende stappen worden behandeld in de resterende zelf studies. Laten we beginnen met paginering.

> [!div class="nextstepaction"]
> [C#-zelfstudie: Zoekresultaten pagineren: Azure Cognitive Search](tutorial-csharp-paging.md)