---
title: C#-zelfstudie over automatisch aanvullen en suggesties
titleSuffix: Azure Cognitive Search
description: Voeg automatisch aanvullen en suggesties toe om zoektermen te verzamelen van gebruikers met een vervolgkeuzelijst. Deze zelfstudie is gebaseerd op een bestaand hotelproject.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 202a7f6b01423045fe7c72db5b42c29ae58f648d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013833"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Zelfstudie: Automatisch aanvullen en suggesties toevoegen met de .NET SDK

Ontdek hoe u automatisch aanvullen kunt implementeren (typeahead-query's en voorgestelde resultaten) wanneer een gebruiker iets begint te typen in een zoekvak. In deze zelfstudie laten we automatisch aangevulde query's en voorgestelde resultaten eerst apart en vervolgens samen zien. Een gebruiker hoeft slechts twee of drie tekens te typen om alle beschikbare resultaten te zien.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Suggesties toevoegen
> * Markering toevoegen aan de suggesties
> * Automatisch aanvullen toevoegen
> * Automatisch aanvullen en suggesties combineren

## <a name="overview"></a>Overzicht

In deze zelfstudie worden automatisch aanvullen en voorgestelde resultaten toegevoegd aan de vorige zelfstudie [Paginering toevoegen aan zoekresultaten](tutorial-csharp-paging.md).

Een voltooide versie van de code in deze zelfstudie vindt u in het volgende project:

* [3-add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Vereisten

* De oplossing [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Dit project kan uw eigen versie zijn die is gebaseerd op de vorige zelfstudie of een kopie van GitHub.

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket [Azure.Search.Documents (versie 11)](https://www.nuget.org/packages/Azure.Search.Documents/). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10) voor een eerdere versie van de .NET SDK.

## <a name="add-suggestions"></a>Suggesties toevoegen

Laten we beginnen met een eenvoudig geval waarbij we de gebruiker alternatieven bieden: een vervolgkeuzelijst met voorgestelde resultaten.

1. Veranderd in het bestand index.cshtml `@id` van de instructie **TextBoxFor** door **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Voer na deze instructie, achter de afsluitende **&lt;/div&gt;** dit script in. Dit script maakt gebruik van de [widget Automatisch aanvullen](https://api.jqueryui.com/autocomplete/) uit de opensource-jQuery UI-bibliotheek om een vervolgkeuzelijst met voorgestelde resultaten weer te geven.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    De id `"azureautosuggest"` verbindt het bovenstaande script met het zoekvak. De bronoptie van de widget is ingesteld op een methode Suggest die de Suggest-API aanroept met twee queryparameters: **markeringen** en **gedeeltelijke overeenkomsten**. Hier zijn beide uitgeschakeld. Er is ook minimaal twee tekens nodig om de zoekopdracht te activeren.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Verwijzingen naar jQuery-scripts toevoegen aan de weergave

1. Om de jQuery-bibliotheek te openen, wijzigt u het onderdeel &lt;head&gt; van het weergavebestand met de volgende code:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Omdat we een nieuwe jQuery-verwijzing introduceren, moeten we ook de standaard jQuery-verwijzing in het bestand _Layout.cshtml (in de map **Weergaves/Gedeeld**) verwijderen of er een opmerking van maken. Zoek de volgende regels en verander de eerste regel in een opmerking zoals hieronder te zien is. Dit voorkomt conflicterende verwijzingen naar jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Nu kunnen we de vooraf gedefinieerde jQuery-functies voor automatisch aanvullen gebruiken.

### <a name="add-the-suggest-action-to-the-controller"></a>De actie Suggest toevoegen aan de controller

1. Voeg in de begincontroller de actie **SuggestAsync** toe (na de actie **Page**).

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
        var options = new SuggestOptions()
        {
            UseFuzzyMatching = fuzzy,
            Size = 8,
        };

        if (highlights)
        {
            options.HighlightPreTag = "<b>";
            options.HighlightPostTag = "</b>";
        }

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    De parameter **Size** geeft aan hoeveel resultaten er moeten worden geretourneerd (als dit niet is opgegeven, is de standaardwaarde 5). Er wordt een _suggester_ opgegeven in de zoekindex wanneer de index wordt gemaakt. In het voorbeeld van een hotelindex die door Microsoft wordt gehost, is de naam van de suggestie 'sg' en wordt alleen gezocht naar voorgestelde overeenkomsten in het veld **HotelName**.

    Met fuzzy overeenkomsten kunnen 'bijna juiste resultaten' opgenomen worden in de uitvoer, tot één bewerkingsafstand. Als de parameter **markeringen** is ingesteld op waar, dan worden HTML-tags voor vette tekst toegevoegd aan de uitvoer. In het volgende onderdeel stellen we beide parameters in op waar.

2. Mogelijk zijn er enkele syntaxisfouten. Indien dat het geval is, voeg dan bovenaan het bestand de volgende **using**-instructies toe.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Voer de app uit. Krijgt u een reeks opties wanneer u bijvoorbeeld 'po' invoert? Probeer nu 'pa'.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="Wanneer u *po* typt, worden er twee suggesties weergegeven" border="false":::

    Merk op dat de letters die u invoer een woord _moeten_ beginnen, en er niet zomaar deel van mogen uitmaken.

4. Stel in het weergavescript **&fuzzy** in op waard en voer de app opnieuw uit. Voer nu 'po' in. Merk op dat de zoekopdracht ervan uitgaat dat u één letter verkeerd hebt getypt.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="*pa* typen wanneer fuzzy is ingesteld op waar" border="false":::

    Indien u dat wenst, kunt u de [Lucene querysyntaxis in Azure Cognitive Search](./query-lucene-syntax.md) bekijken voor meer gedetailleerde informatie over de logica die gebruikt wordt bij fuzzy zoekopdrachten.

## <a name="add-highlighting-to-the-suggestions"></a>Markering toevoegen aan de suggesties

We kunnen de weergave van de suggesties voor de gebruiker verbeteren door de parameter **markeringen** op waar in te stellen. Eerst moeten we echter code toevoegen om de vette tekst weer te geven.

1. Voeg in de weergave (index.cshtml) het volgende script toe na het script `"azureautosuggest"` dat eerder is beschreven.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Wijzig nu de ID van het tekstvak zodat deze er als volgt uitziet.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Voer de app opnieuw uit. Normaal gezien ziet u dat uw ingevoerde tekst vetgedrukt staat in de suggesties. Probeer 'pa' te typen.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="*pa* typen met markering" border="false":::

   De logica die gebruikt wordt voor het bovenstaande markeringsscript, is niet onfeilbaar. Als u een term opgeeft die twee keer in dezelfde naam voorkomt, dan zijn de vetgedrukte resultaten niet helemaal wat u wilt. Probeer 'mo' te typen.

   Een van de vragen die een ontwikkelaar zich moet stellen is, wanneer een script 'goed genoeg' werkt en wanneer er onvolmaaktheden zijn die moeten worden opgelost. We gaan niet verder met markeringen in deze zelfstudie, maar een nauwkeurig algoritme zoeken is het overwegen waard als markeringen niet geschikt zijn voor uw gegevens. Zie [Treffers markeren](search-pagination-page-layout.md#hit-highlighting) voor meer informatie.

## <a name="add-autocomplete"></a>Automatisch aanvullen toevoegen

Een andere variatie, die lichtjes verschilt van suggesties, is automatisch aanvullen (soms 'type-ahead') genoemd. Hiermee vervolledigt u een queryterm. Ook hier gaan we beginnen met de eenvoudigste implementatie, voordat we de gebruikerservaring gaan verbeteren.

1. Voer het volgende script in bij de weergave, na uw vorige scripts.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Wijzig nu de ID van het tekstvak zodat deze er als volgt uitziet.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Voer in de begincontroller de actie **AutocompleteAsync** toe na de actie **SuggestAsync**.

    ```cs
    public async Task<ActionResult> AutoCompleteAsync(string term)
    {
        InitSearch();

        // Setup the autocomplete parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 6
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

        // Convert the autocompleteResult results to a list that can be displayed in the client.
        List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

        return new JsonResult(autocomplete);
    }
    ```

    Merk op dat we dezelfde functie *suggester*, met de naam 'sg', gebruiken bij automatisch aanvullen als bij suggesties (we proberen dus enkel de hotelnamen automatisch aan te vullen).

    Er zijn verschillende **AutocompleteMode**-instellingen, en wij gebruiken **OneTermWithContext**. Bekijk [Autocomplete API](/rest/api/searchservice/autocomplete) voor een beschrijving van aanvullende opties.

1. Voer de app uit. Merk op dat alle opties die in de vervolgkeuzelijst worden weergegeven, enkele woorden zijn. Probeer woorden te typen die beginnen met 're'. Merk op dat er steeds minder opties zijn wanneer u meer letters typt.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Typen met eenvoudig automatisch aanvullen" border="false":::

    Het script voor suggesties dat u eerder uitvoerde, is waarschijnlijk nuttiger dan dit script voor automatisch aanvullen. Als u automatisch aanvullen gebruikersvriendelijker wilt maken, kunt u het gebruiken met voorgestelde resultaten.

## <a name="combine-autocompletion-and-suggestions"></a>Automatisch aanvullen en suggesties combineren

Automatisch aanvullen en suggesties combineren is onze meest complexe optie, en biedt de beste gebruikservaring. Wat we, naast de tekst die getypt wordt, willen weergeven is het eerste resultaat van Azure Cognitive Search voor het automatisch aanvullen van de tekst. We willen ook een reeks suggesties in een vervolgkeuzelijst.

Er zijn bibliotheken die deze functionaliteit bieden. Dit wordt vaak 'Inline automatisch aanvullen' of iets vergelijkbaar genoemd. We gaan deze functie echter zelf implementeren, zodat u de API's kunt verkennen. In dit voorbeeld gaan we eerst aan de controller beginnen werken.

1. Voeg een actie toe aan de controller die slechts één resultaat voor automatisch aanvullen retourneert, samen met een bepaald aantal suggesties. We noemen deze actie **AutoCompleteAndSuggestAsync**. Voeg in uw begincontroller de volgende actie toe na uw andere nieuwe acties.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    Boven aan de lijst met **resultaten** wordt één optie voor automatisch aanvullen weergegeven, gevolgd door alle suggesties.

1. In de weergave implementeren we eerst een truc, zodat een lichtgrijs automatisch aangevuld woord verschijnt recht onder dikkere tekst die door de gebruiker wordt ingevoerd. HTML bevat hiervoor relatieve positionering. Verander de **TextBoxFor**-instructie (en de omringende &lt;div&gt;-instructies) naar het volgende. Een tweede zoekvak, genaamd **underneath**, bevindt zich recht onder ons normale zoekvak, door dit zoekvak 39 pixels van zijn standaardlocatie te verplaatsen.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Merk op dat we de id opnieuw veranderen, in dit geval in **azureautocomplete**.

1. Voer in de weergave ook het volgende script toe, na alle scripts die u tot nu toe hebt ingevoerd. Het script is lang en complex vanwege de verschillende invoergedragingen die worden afgehandeld.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    De functie **interval** wordt zowel gebruikt om de onderliggende tekst te wissen wanneer deze niet meer overeenkomt met wat de gebruiker typt, als om hoofd- of kleine letters te gebruiken terwijl de gebruiker typt ('pa' komt overeen met 'PA', 'pA' en 'Pa' tijdens het zoeken), zodat de overlappende tekst er netjes uitziet.

    Lees de opmerkingen in het script voor een uitgebreider inzicht.

1. Ten slotte moeten we een kleine aanpassing maken aan twee HTML-klassen om ze transparant te maken. Voeg de volgende regel toe aan de klassen **searchBoxForm** en **searchBox** in het bestand hotels.css.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Voer nu de app uit. Typ 'pa' in het zoekvak. Krijgt u de suggestie 'palace' samen met twee hotels die 'pa' bevatten?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Typen met inline automatisch aanvullen en suggesties" border="false":::

1. Probeer de tabtoets te gebruiken om de suggestie te accepteren, en probeer suggesties te selecteren met de pijltoetsen en de tabtoets of met de muis. Controleer of het script al deze situaties correct verwerkt.

    U kunt ervoor kiezen om een bibliotheek te gebruiken waarbij deze functie is ingebouwd, maar nu kent u ten minste één manier om inline automatisch aanvullen in te stellen.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Automatisch aanvullen en suggesties kunnen de gebruiker in staat stellen om in enkele tikken precies te vinden wat ze zoeken.
* De combinatie van automatisch aanvullen en suggesties kan een uitgebreide gebruikerservaring bieden.
* Probeer de functies voor automatisch aanvullen altijd uit met alle soorten invoer.
* De functie **setInterval** functie kan handig zijn om elementen van de gebruikersinterface te controleren en te corrigeren.

## <a name="next-steps"></a>Volgende stappen

In de volgende zelfstudie ziet u een andere manier om de gebruikerservaring te verbeteren, met behulp van facetten om een zoekopdracht te verfijnen met één klik.

> [!div class="nextstepaction"]
> [C#-zelfstudie: Facetten gebruiken om beter te navigeren - Azure Cognitive Search](tutorial-csharp-facets.md)
