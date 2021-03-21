---
title: C#-zelfstudie over het gebruik van facetten bij navigatie
titleSuffix: Azure Cognitive Search
description: Doorgaan vanaf 'Resultaten pagineren' om Facetnavigatie toe te voegen. Meer informatie over hoe facetten kunnen worden gebruikt om eenvoudig een zoekopdracht te beperken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789800"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Zelfstudie: Facetnavigatie toevoegen met behulp van de .NET SDK

Facetten maken zelfgestuurde navigatie mogelijk door een set links te bieden voor het filteren van de resultaten. In deze zelfstudie wordt een facetnavigatiestructuur aan de linkerkant van de pagina geplaatst, met labels en klikbare tekst om de resultaten toe te spitsen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Modeleigenschappen instellen in als _IsFacetable_
> * Facetnavigatie toevoegen aan uw app

## <a name="overview"></a>Overzicht

Facetten worden gebaseerd op velden in uw zoekindex. Een query-aanvraag die facet=[string] bevat, geeft het veld waarop wordt gefacetteerd. Het is gebruikelijk om meerdere facetten toe te voegen, zoals `&facet=category&facet=amenities`, gescheiden door en-tekens(&). Voor het implementeren van een facetnavigatiestructuur moet u zowel facetten als filters opgeven. Het filter wordt gebruikt om resultaten toe te spitsen bij een klikgebeurtenis. Als iemand bijvoorbeeld op 'budget' klikt, worden de resultaten gefilterd op basis van dat criterium.

In deze zelfstudie wordt het pagineringsproject dat is gemaakt in de zelfstudie [Paginering toevoegen aan zoekresultaten](tutorial-csharp-paging.md) uitgebreid.

Een voltooide versie van de code in deze zelfstudie vindt u in het volgende project:

* [4-add-facet-navigation (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Vereisten

* De oplossing [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Dit project kan uw eigen versie zijn die is gebaseerd op de vorige zelfstudie of een kopie van GitHub.

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket [Azure.Search.Documents (versie 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10) voor een eerdere versie van de .NET SDK.

## <a name="set-model-properties-as-isfacetable"></a>Modeleigenschappen instellen in als IsFacetable

Een modeleigenschap kan alleen in een facetzoekactie worden gevonden als deze is getagd met **IsFacetable**.

1. Bekijk de klasse **Hotel**. **Categorie** en **Tags** zijn bijvoorbeeld getagd als **IsFacetable**, maar **HotelNaam** en **Beschrijving** niet. 

    ```cs
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
    ```

1. Er worden geen tags gewijzigd als onderdeel van deze zelfstudie. Sluit daarom het hotel.cs-bestand ongewijzigd.

    > [!Note]
    > Bij het zoeken naar facetten wordt een fout gegenereerd als een veld dat in de zoekopdracht is aangevraagd, niet op de juiste wijze is getagd.

## <a name="add-facet-navigation-to-your-app"></a>Facetnavigatie toevoegen aan uw app

In dit voorbeeld gaan we de gebruiker in staat stellen om één categorie hotel of één voorziening te selecteren uit lijsten met koppelingen die links van de resultaten worden wee gegeven. De gebruiker begint met het invoeren van zoektekst, en spitst de zoekresultaten steeds verder toe door een categorie of voorziening te selecteren.

De controller moet de lijst met facetten doorgeven aan de weergave. Om de gebruikersselecties te behouden tijdens de voortgang van het zoeken, gebruiken we tijdelijke opslag als het mechanisme voor het behouden van de status.

![Facetnavigatie gebruiken om een zoekopdracht van 'zwembad' te beperken](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Filterreeksen toevoegen aan het SearchData-model

1. Open het SearchData.cs-bestand en voeg tekenreekseigenschappen toe aan de klasse **SearchData** om de facet filterreeksen op te slaan.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>De Facet-actiemethode toevoegen

De begincontroller heeft een nieuwe actie, **Facet**, nodig, en updates van de bestaande acties **Index** en **Page** en van de methode **RunQueryAsync**.

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Vervang de actiemethode **Index (SearchData model)** .

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Vervang de actiemethode **PageAsync(SearchData model)** .

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. Voeg de actiemethode **FacetAsync(SearchData model)** toe, die moet worden geactiveerd wanneer de gebruiker op een facetkoppeling klikt. Het model bevat een zoekfilter voor categorieën of voor voorzieningen. Voeg het toe na de **PageAsync**-actie.

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>Het zoekfilter instellen

Wanneer een gebruiker een bepaald facet selecteert, bijvoorbeeld op de categorie **Resort en spa**, worden alleen hotels die als deze categorie zijn opgegeven, in de resultaten weergegeven. Als u een zoekopdracht op deze manier wilt beperken, moet u een _filter_ instellen.

1. Vervang de methode **RunQueryAsync** door de volgende code. Deze neemt een filterreeks voor categorieën en een voor voorzieningen, en stelt de **Filter**-parameter van de **SearchOptions** in.

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    Merk op dat de eigenschappen **Category** en **Tags** worden toegevoegd aan de lijst met te retourneren **Selecteer**-items. Deze toevoeging is niet vereist om de facetnavigatie te laten werken, maar we gebruiken deze informatie om te controleren of de filters goed werken.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lijsten met facetkoppelingen toevoegen aan de weergave

Er zijn enkele belangrijke wijzigingen vereist voor de weergave. 

1. Begin met het openen van het bestand hotels.css (in de map wwwroot/css) en voeg de volgende klassen toe.

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

1. Voor de weergave wordt de uitvoer in een tabel ingedeeld, zodat de facetlijsten op een overzichtelijke manier worden uitgelijnd aan de linkerkant en de resultaten aan de rechterkant. Het bestand index.cshtml openen. Vervang de volledige inhoud van de HTML &lt;body&gt;-tags door de volgende code.

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
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    Let op het gebruik van de aanroep **Html.ActionLink**. Met deze aanroep worden geldige filterreeksen naar de controller gecommuniceerd wanneer de gebruiker op een facetkoppeling klikt. 

### <a name="run-and-test-the-app"></a>De app uitvoeren en testen

Het voordeel van facetnavigatie naar de gebruiker is dat de zoekopdracht met één klik kan worden beperkt, wat in de volgende volgorde kan worden weergegeven.

1. Voer de app uit, typ "luchthaven" als zoektekst. Controleer of de lijst met facetten netjes aan de linkerkant wordt weergegeven. Deze facetten zijn allemaal van toepassing op hotels met 'luchthaven' in hun tekstgegevens, met een telling van hoe vaak ze voorkomen.

    ![Facetnavigatie gebruiken om een zoekopdracht van 'luchthaven' te beperken](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. Klik op de categorie **Resort en spa**. Controleer of alle resultaten in deze categorie staan.

    ![De zoekopdracht beperken tot "Resort en spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Klik op de voorziening **continentaal ontbijt**. Controleer of alle resultaten nog in de categorie ‘Resort en spa’ staan met de geselecteerde voorziening.

    ![De zoekopdracht beperken tot ‘continentaal ontbijt’](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Selecteer een andere categorie, vervolgens één voorziening en bekijk de beperkte resultaten. Ga vervolgens omgekeerd, één voorziening en vervolgens één categorie. Verzend een lege zoekopdracht om de pagina opnieuw in te stellen.

    >[!Note]
    > Wanneer een selectie wordt gemaakt in een facetlijst (bijvoorbeeld categorie), wordt een eerdere selectie in de categorielijst overschreven.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Elk facetteerbaar veld moet worden gemarkeerd met de eigenschap **IsFacetable** om het op te nemen in de facetnavigatie.
* Facetten worden gecombineerd met filters om de resultaten in te perken.
* Facetten zijn cumulatief, waarbij elke selectie voortbouwt op de vorige om de resultaten verder toe te spitsen.

## <a name="next-steps"></a>Volgende stappen

In de volgende zelf studie kijken we naar het ordenen van resultaten. Tot nu toe worden de resultaten geordend in de volgorde waarin ze zich bevinden in de database.

> [!div class="nextstepaction"]
> [C#-zelfstudie: De resultaten ordenen - Azure Cognitive Search](tutorial-csharp-orders.md)
