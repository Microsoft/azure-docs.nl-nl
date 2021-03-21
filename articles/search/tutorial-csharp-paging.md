---
title: C#-zelfstudie over de paginering van zoekresultaten
titleSuffix: Azure Cognitive Search
description: Paginerings- en navigatieknoppen toevoegen aan zoekresultaten, waarbij wordt voortgebouwd op een bestaand hotelproject om de knoppen eerste, volgende, vorige, laatste en nummerknoppen toe te voegen. Een tweede pagineringssysteem maakt gebruik van oneindig schuiven, dat wordt geactiveerd door een verticale schuifbalk te verplaatsen naar de ondergrens.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bad4bc4d0016b2898b315bfb9799dc8972be7b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785867"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Zelfstudie: Paginering toevoegen aan zoekresultaten met behulp van de .NET SDK

Meer informatie over het implementeren van twee verschillende pagineringssystemen, het eerste op basis van paginanummers en het tweede op basis van oneindig schuiven. Beide pagineringssystemen worden veel gebruikt en het selecteren van het juiste systeem is afhankelijk van de gebruikerservaring die u voor de resultaten wilt. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Uw app uitbreiden met genummerde paginering
> * Uw app uitbreiden met oneindig schuiven

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt een pagineringssysteem toegevoegd aan een eerder gemaakt project, dat wordt beschreven in de zelfstudie [Uw eerste zoek-app maken](tutorial-csharp-create-first-app.md).

Een voltooide versie van de code die u in deze zelfstudie gaat ontwikkelen, is te vinden in de volgende projecten:

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Vereisten

* Het project [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page). Dit project kan uw eigen versie zijn die is gebaseerd op de vorige zelfstudie, of een kopie van GitHub.

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket [Azure.Search.Documents (versie 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10) voor een eerdere versie van de .NET SDK.

## <a name="extend-your-app-with-numbered-paging"></a>Uw app uitbreiden met genummerde paginering

Een genummerd pagineringssysteem is de keuze van de belangrijkste commerciële zoekmachines op internet en van veel andere zoekwebsites. Genummerde pagineringssystemen bevatten doorgaans een optie 'volgende' en 'vorige' naast een bereik van paginanummers. Ook zijn de optie 'eerste pagina' en 'laatste pagina' mogelijk beschikbaar. Deze opties geven een gebruiker de controle over het navigeren door op pagina’s gebaseerde resultaten.

In deze zelfstudie voegt u een systeem toe met de opties 'eerste', 'vorige', 'volgende' en 'laatste', samen met de paginanummers die niet vanaf 1 worden gestart, maar in plaats daarvan de huidige pagina waarop de gebruiker zich bevindt (als de gebruiker op pagina 10 zoekt, worden bijvoorbeeld mogelijk de paginanummers 8, 9, 10, 11 en 12 weergegeven).

Het systeem is flexibel genoeg om het aantal zichtbare paginanummers in een globale variabele in te stellen.

Het systeem behandelt de meest linkse en meest rechtse paginanummerknoppen als speciaal, wat betekent dat deze het wijzigen van het bereik van de weergegeven paginanummers kunnen activeren. Als bijvoorbeeld paginanummers 8, 9, 10, 11 en 12 worden weergegeven en de gebruiker op 8 klikt, wordt het bereik van de paginanummers gewijzigd in 6, 7, 8, 9 en 10. En er is een vergelijkbare verschuiving naar rechts als 12 wordt geselecteerd.

### <a name="add-paging-fields-to-the-model"></a>Pagineringsvelden toevoegen aan het model

Zorg dat u de oplossing voor eenvoudige zoekpagina's open hebt.

1. Open het modelbestand SearchData.cs.

1. Voeg globale variabelen toe om paginering te ondersteunen. In MVC worden globale variabelen in hun eigen statische klasse gedeclareerd. Met **ResultsPerPage** wordt het aantal resultaten per pagina ingesteld. Met **MaxPageRange** wordt het aantal zichtbare paginanummers in de weergave bepaald. Met **PageRangeDelta** wordt bepaald hoeveel pagina's naar links of rechts moet worden gegaan wanneer het meest linkse of het meest rechtse paginanummer wordt geselecteerd. Dit laatste nummer is doorgaans ongeveer de helft van **MaxPageRange**. Voeg de volgende code toe aan de naamruimte.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Als u dit project uitvoert op een apparaat met een kleiner scherm, zoals een laptop, overweeg dan om **ResultsPerPage** te wijzigen in 2.

1. Voeg pagineringseigenschappen toe aan de **SearchData**-klasse, na de eigenschap **searchText**.

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Een tabel met pagineringsopties toevoegen aan de weergave

1. Open het bestand index. cshtml en voeg de volgende code toe vóór de &lt;/body&gt;-tag voor sluiten. Deze nieuwe code vertegenwoordigt een tabel met pagineringsopties: eerste, vorige, 1, 2, 3, 4, 5, volgende, laatste.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    We gebruiken een HTML-tabel om alles netjes uit te lijnen. Alle actie is echter afkomstig uit de @Html.ActionLink-instructies, die elk de controller aanroepen met een **nieuw** model dat is gemaakt met verschillende vermeldingen voor de **pagineringseigenschap** die u eerder hebt toegevoegd.

    De opties voor de eerste en laatste pagina verzenden geen tekenreeksen zoals 'eerste' en 'laatste', maar de juiste paginanummers.

1. Voeg pagineringsklassen toe aan de lijst met HTML-stijlen in het bestand hotels.css. De **pageSelected**-klasse is geeft de pagina aan die de gebruiker momenteel bekijkt (door het nummer vetgedrukt te maken) in de lijst met paginanummers.

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Een actie Page toevoegen aan de controller

1. Open het bestand HomeController.cs en voeg de actie **PageAsync** toe. Deze actie reageert op elk van de geselecteerde paginaopties.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

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

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    De **RunQueryAsync**-methode geeft nu een syntaxisfout weer, vanwege de derde parameter, waar we het straks over gaan hebben.

    > [!Note]
    > Door de **TempData**-aanroepen wordt een waarde (een **object**) opgeslagen in de tijdelijke opslag, maar deze opslag is voor _slechts_ één aanroep. Als er iets wordt opgeslagen in tijdelijke gegevens, is het beschikbaar voor de volgende aanroep van een controlleractie, maar is het na die aanroep verdwenen. Als gevolg van deze korte levensduur slaan we de zoektekst en pagineringseigenschappen opnieuw op in de tijdelijke opslag bij elke aanroep van **PageAsync**.

1. Werk de actie **Index(model)** bij om tijdelijke variabelen op te slaan, en om de parameter voor de meest linkse pagina aan de aanroep van **RunQueryAsync** toe te voegen.

    ```csharp
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
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. De **RunQueryAsync**-methode die in de vorige les is geïntroduceerd, moet worden gewijzigd om de syntaxisfout op te lossen. We gebruiken de velden **Skip**, **Size** en **IncludeTotalResultCount** van de klasse [**SearchOptions**](/dotnet/api/azure.search.documents.searchoptions) om slechts één pagina met resultaten op te vragen, beginnend bij de instelling **Skip**. We moeten ook de pagineringsvariabelen voor onze weergave berekenen. Vervang de volledige methode door de volgende code.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

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

        return View("Index", model);
    }
    ```

1. Breng tot slot een kleine wijziging aan in de weergave. De variabele **resultList.Results.TotalCount** bevat nu het aantal resultaten dat op één pagina wordt opgehaald (3 in ons voorbeeld), niet het totale aantal. Omdat we **IncludeTotalCount-** op 'true' instellen, bevat de variabele **resultList.TotalCount** nu het totale aantal resultaten. Zoek naar de plaats waar het aantal resultaten wordt weergegeven in de weergave en wijzig dit in de volgende code.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > Er is een klein prestatieverlies door **IncludeTotalCount** in te stellen op 'true', omdat dit totaal door Azure Cognitive Search moet worden berekend. Bij complexe gegevenssets is er een waarschuwing dat de geretourneerde waarde een _benadering_ is. Omdat het te doorzoeken hotelcorpus klein is, is de waarde nauwkeurig.

### <a name="compile-and-run-the-app"></a>De app compileren en uitvoeren

Selecteer nu **Starten zonder foutopsporing** (of druk op de toets F5).

1. Zoek naar een tekenreeks die veel resultaten oplevert (bijvoorbeeld 'wifi'). Kunt u de resultaten op de pagina overzichtelijk doorlopen?

    ![Genummerde paginering via resultaten voor 'pool'](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. Klik op het meest rechtse, en later, op het meest linkse paginanummer. Worden de paginanummers op de juiste wijze aangepast om de pagina waarop u zich bevindt te centreren?

1. Zijn de opties 'eerste' en 'laatste' nuttig? Sommige commerciële zoekprogramma's gebruiken deze opties, en andere niet.

1. Ga naar de laatste pagina met resultaten. De laatste pagina is de enige pagina die minder dan **ResultsPerPage**-resultaten kan bevatten.

    ![De laatste pagina van 'wifi' wordt onderzocht](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. Typ 'town' en klik op de zoekoptie. Er worden geen pagineringsopties weergegeven als er minder dan één paginavol aan resultaten is.

    ![Zoeken naar 'town'](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Sla dit project op en ga door naar de volgende sectie voor een alternatieve vorm van paginering.

## <a name="extend-your-app-with-infinite-scrolling"></a>Uw app uitbreiden met oneindig schuiven

Oneindig schuiven wordt geactiveerd wanneer een gebruiker met een verticale schuifbalk naar de laatste van de weergegeven resultaten schuift. In dit geval wordt een aanroep naar de zoekservice gedaan voor de volgende pagina met resultaten. Als er geen resultaten meer zijn, wordt er niets opgehaald en verandert de verticale schuifbalk niet. Als er meer resultaten zijn, worden deze toegevoegd aan de huidige pagina en wordt de schuifbalk gewijzigd om aan te geven dat er meer resultaten beschikbaar zijn.

Een belangrijk punt om te weten dat de huidige pagina niet wordt vervangen, maar wordt uitgebreid om de aanvullende resultaten weer te geven. Een gebruiker kan altijd weer omhoog schuiven naar de eerste resultaten van de zoekopdracht.

Om oneindig schuiven te implementeren, beginnen we met het project voordat een van de elementen voor schuiven door paginanummers is toegevoegd. Op GitHub is dit de oplossing [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page).

### <a name="add-paging-fields-to-the-model"></a>Pagineringsvelden toevoegen aan het model

1. Voeg eerst een **paginerings** eigenschap toe aan de klasse **SearchData** (in het modelbestand SearchData.cs).

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Deze variabele is een tekenreeks die 'next' bevat als de volgende pagina met resultaten moet worden verzonden of null is voor de eerste pagina van een zoekopdracht.

1. Voeg in hetzelfde bestand, en binnen de naamruimte, een globale variabeleklasse met één eigenschap toe. In MVC worden globale variabelen in hun eigen statische klasse gedeclareerd. Met **ResultsPerPage** wordt het aantal resultaten per pagina ingesteld. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Een verticale schuifbalk aan de weergave toevoegen

1. Zoek de sectie van het bestand index.cshtml op waarin de resultaten worden weergegeven (deze begint met **@if (Model != null)** ).

1. Vervang de sectie door de onderstaande code. De nieuwe sectie **&lt;div&gt;** bevindt zich rond het gebied dat schuifbaar moet worden, en voegt zowel een **overflow-y-** -kenmerk als een aanroep naar een **onscroll**-functie genaamd 'scrolled ()' toe, zoals volgt.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Voeg direct onder de lus, na de tag &lt;/div&gt;, de functie **scrolled** toe.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    Door de **if**-instructie in het script hierboven wordt getest of de gebruiker naar de onderkant van de verticale schuifbalk is geschoven. Als dit het geval is, wordt er een aanroep naar de controller **Home** gedaan naar een actie met de naam **NextAsync**. Er is geen andere informatie nodig voor de controller. Hiermee wordt de volgende pagina met gegevens geretourneerd. Deze gegevens worden vervolgens opgemaakt met dezelfde identieke HTML-stijlen als de oorspronkelijke pagina. Als er geen resultaten worden geretourneerd, wordt er niets toegevoegd en blijft alles ongewijzigd.

### <a name="handle-the-next-action"></a>De actie Next afhandelen

Er zijn maar drie acties die naar de controller moeten worden verzonden: de eerste keer dat de app wordt uitgevoerd, die **Index()** aanroept, de eerste zoekopdracht van de gebruiker, die **Index(Model)** aanroept en vervolgens de volgende aanroepen voor meer resultaten via **Next(model)** .

1. Open het bestand van de begincontroller en verwijder de methode **RunQueryAsync** uit de oorspronkelijke zelfstudie.

1. Vervang de actie **Index(model)** door de volgende code. Nu wordt het veld **paging** verwerkt wanneer dit is ingesteld op null of 'next', en wordt de aanroep naar Azure Cognitive Search verwerkt.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Net als bij de genummerde pagineringsmethode gebruiken we de zoekinstellingen **Skip** en **Size** om alleen de gegevens die we nodig hebben op te vragen.

1. Voeg de actie **NextAsync** toe aan de begincontroller. U ziet dat er een lijst wordt geretourneerd, waarbij elk hotel twee elementen aan de lijst toevoegt: de naam van het hotel en een beschrijving. Deze indeling is zo ingesteld dat deze overeenkomt met het gebruik van de functie **scrolled** van de opgehaalde gegevens in de weergave.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Als er een syntaxisfout optreedt in **List&lt;string&gt;** , voegt u de volgende **using**-instructie boven aan het controllerbestand toe.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Uw project compileren en uitvoeren

Selecteer nu **Starten zonder foutopsporing** (of druk op de toets F5).

1. Voer een term in die veel resultaten levert (zoals 'pool') en test vervolgens de verticale schuifbalk. Wordt een nieuwe pagina met resultaten geactiveerd?

    ![Oneindig schuiven door resultaten met 'pool'](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Om ervoor te zorgen dat op de eerste pagina een schuifbalk wordt weergegeven, moet de eerste pagina met de resultaten iets groter zijn dan de hoogte van het gebied waarin ze worden weergegeven. In ons voorbeeld heeft **.box1** een hoogte van 30 pixels, **.box2** een hoogte van 100 pixels _en_ een ondermarge van 24 pixels. Elk item gebruikt dus 154 pixels. De drie items samen zijn 3 x 154 = 462 pixels. Om ervoor te zorgen dat een verticale schuifbalk wordt weergegeven, moet er een hoogte voor het weergavegebied worden ingesteld die kleiner is dan 462 pixels, 461 werkt ook. Dit probleem doet zich alleen voor op de eerste pagina, nadat zeker is dat er een schuifbalk wordt weergegeven. De regel die moet worden bijgewerkt is: **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

1. Schuif helemaal omlaag naar de onderkant van de resultaten. U ziet hoe alle informatie nu op één weergavepagina staat. U kunt weer helemaal naar boven schuiven zonder serveraanroepen te activeren.

Meer geavanceerde systemen voor oneindig schuiven kunnen gebruikmaken van het muiswiel of een soortgelijk ander mechanisme om het laden van een nieuwe pagina met resultaten te activeren. Oneindig schuiven wordt verder niet behandeld in deze zelfstudies, maar het heeft een zeker voordeel aangezien het extra muisklikken voorkomt, en mogelijk wilt u ook andere opties verder onderzoeken.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Genummerde paginering is geschikt voor zoekopdrachten waarbij de volgorde van de resultaten enigszins willekeurig is, wat inhoudt dat er mogelijk iets van belang is voor uw gebruikers dat op de volgende pagina's staat.
* Oneindig schuiven is nuttig wanneer de volgorde van de resultaten bijzonder belangrijk is. Bijvoorbeeld als de resultaten zijn geordend op basis van de afstand van het centrum van een plaats van bestemming.
* Genummerde paginering biedt een betere navigatie. Een gebruiker kan bijvoorbeeld onthouden dat er een interessant resultaat was op pagina 6. Bij oneindig schuiven bestaat er niet zo'n handige referentie.
* Oneindig schuiven is aantrekkelijk: omhoog en omlaag schuiven zonder paginanummers om op te klikken.
* Een belangrijke functie van oneindig schuiven is dat er resultaten worden toegevoegd aan een bestaande pagina, waardoor die pagina niet wordt vervangen. Dit is efficiënt.
* Tijdelijke opslag blijft behouden voor slechts één aanroep en moet opnieuw worden ingesteld als er nog meer aanroepen zijn.

## <a name="next-steps"></a>Volgende stappen

Paginering is essentieel voor een zoekervaring. Als de paginering goed geregeld is, is de volgende stap het optimaliseren van de gebruikerservaring door zoekopdrachten met automatisch aanvullen toe te voegen.

> [!div class="nextstepaction"]
> [C#-zelfstudie: Automatisch aanvullen en suggesties toevoegen - Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)