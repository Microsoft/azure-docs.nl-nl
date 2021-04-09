---
title: Automatisch aanvullen toevoegen aan een zoekvak
titleSuffix: Azure Cognitive Search
description: Schakel zoek acties op basis van het type query in azure Cognitive Search door Voorst Ellen te maken en aanvragen te formuleren waarmee automatisch een zoekvak met de voor waarden of zinsdelen wordt ingevuld. U kunt ook voorgestelde overeenkomsten retour neren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6bc45bb6eec9dbf46e039dd1e2c32197820bb09d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626704"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Automatisch aanvullen en suggesties toevoegen aan client-apps met behulp van Azure Cognitive Search

Search-as-u-type is een algemene techniek voor het verbeteren van de query productiviteit. In azure Cognitive Search wordt deze ervaring ondersteund via *automatisch aanvullen*, waardoor een term of woord groep is gebaseerd op gedeeltelijke invoer (het volt ooien van ' micro ' met ' micro soft '). Een tweede gebruikers ervaring is *suggesties* of een korte lijst met overeenkomende documenten (waarbij Boek titels worden geretourneerd met een id zodat u een koppeling kunt maken naar een detail pagina over dat boek). Automatisch aanvullen en suggesties worden voorgesteld op basis van een overeenkomst in de index. De service biedt geen query's die nul resultaten retour neren.

Als u deze ervaringen wilt implementeren in azure Cognitive Search, hebt u het volgende nodig:

+ Een *suggestie* definitie die is inge sloten in het index schema.
+ Een *query* die de API voor [automatisch aanvullen](/rest/api/searchservice/autocomplete) of [suggesties](/rest/api/searchservice/suggestions) voor de aanvraag specificeert.
+ Een *UI-besturings element* voor het afhandelen van interacties die zoeken naar u typen in uw client-app. U kunt het beste een bestaande Java script-bibliotheek gebruiken voor dit doel.

In azure Cognitive Search worden automatisch aangeleverde query's en voorgestelde resultaten opgehaald uit de zoek index, van geselecteerde velden die u hebt geregistreerd met een suggestie. Een suggestie maakt deel uit van de index en geeft aan welke velden inhoud leveren die een query voltooit, een resultaat krijgt of beide. Wanneer de index is gemaakt en geladen, wordt er intern een gegevens structuur voor suggesties gemaakt voor het opslaan van voor voegsels die worden gebruikt voor het vergelijken van gedeeltelijke query's. Voor suggesties, het kiezen van de juiste velden die uniek zijn of die ten minste niet herhalend zijn, is essentieel voor de ervaring. Zie [een suggestie maken](index-add-suggesters.md)voor meer informatie.

De rest van dit artikel is gericht op query's en client code. Java script en C# worden gebruikt voor het illustreren van belang rijke punten. REST API-voor beelden worden gebruikt om elke bewerking beknopt weer te geven. Zie [volgende stappen](#next-steps)voor koppelingen naar end-to-end-code voorbeelden.

## <a name="set-up-a-request"></a>Een aanvraag instellen

De elementen van een aanvraag bevatten een van de Search-as-u-type-Api's, een gedeeltelijke query en een suggestie. Het volgende script illustreert onderdelen van een aanvraag, waarbij de REST API automatisch aanvullen als voor beeld wordt gebruikt.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

De ' suggesterName ' geeft u de velden voor suggesties die worden gebruikt voor het volt ooien van de voor waarden of suggesties. Voor suggesties met name moet de lijst met velden bestaan uit de lijsten die duidelijke keuzes bieden tussen de overeenkomende resultaten. Op een site die computer spellen verkoopt, is het veld mogelijk de titel van het spel.

De para meter ' Search ' biedt de gedeeltelijke query, waarbij tekens worden ingevoerd in de query aanvraag via het jQuery-besturings element voor automatisch aanvullen. In het bovenstaande voor beeld is ' minecraf ' een statische illustratie van wat het besturings element mogelijk heeft door gegeven.

De Api's leggen geen vereisten voor de minimale lengte voor de gedeeltelijke query voor; Dit kan slechts één teken zijn. JQuery automatisch aanvullen biedt echter een minimum lengte. Een minimum van twee of drie tekens is gebruikelijk.

Overeenkomsten bevinden zich aan het begin van een term in een wille keurige plaats in de invoer teken reeks. Op basis van de Quick Brown Fox komen zowel automatisch aanvullen als suggesties overeen op de gedeeltelijke versies van ' de ', ' snel ', ' bruin ' of ' Fox ', maar niet op gedeeltelijke infix-termen zoals ' rij ' of ' Ox '. Daarnaast stelt elke overeenkomst het bereik in voor downstream-uitbrei dingen. Een gedeeltelijke query van ' Quick BR ' komt overeen met ' Quick Brown ' of ' Quick brood ', maar ' bruin ' of ' brood ' is op zichzelf niet hetzelfde, tenzij ' snel ' voor komt.

### <a name="apis-for-search-as-you-type"></a>Api's voor Search-as-u-type

Volg deze koppelingen voor de REST-en .NET SDK-referentie pagina's:

+ [Suggesties REST API](/rest/api/searchservice/suggestions) 
+ [REST API automatisch aanvullen](/rest/api/searchservice/autocomplete) 
+ [Methode SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Methode AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Een reactie structureren

Antwoorden voor automatisch aanvullen en suggesties zijn wat u mogelijk verwacht voor het patroon: door [AutoAanvullen](/rest/api/searchservice/autocomplete#response) een lijst met termen te [retour neren,](/rest/api/searchservice/suggestions#response) worden voor waarden en een document-id geretourneerd, zodat u het document kunt ophalen (met behulp van de [opzoek document](/rest/api/searchservice/lookup-document) -API om het specifieke document voor een detail pagina op te halen).

Antwoorden worden gevormd door de para meters in de aanvraag:

+ Stel voor automatisch aanvullen de [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) in om te bepalen of de tekst is voltooid op basis van een of twee voor waarden. 

+ Stel [$Select](/rest/api/searchservice/suggestionse#query-parameters) voor suggesties in om velden te retour neren die unieke of afdoende waarden bevatten, zoals namen en beschrijvingen. Vermijd velden die dubbele waarden bevatten (zoals een categorie of plaats).

De volgende aanvullende para meters zijn van toepassing op zowel AutoAanvullen als suggesties, maar zijn mogelijk meer nodig voor suggesties, met name wanneer een suggestie meerdere velden bevat.

| Parameter | Gebruik |
|-----------|-------|
| searchFields | De query beperken tot specifieke velden. |
| $filter | Overeenkomst criteria Toep assen op de resultatenset ( `$filter=Category eq 'ActionAdventure'` ). |
| $top | De resultaten beperken tot een bepaald aantal ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Gebruikers interactie code toevoegen

Voor het automatisch aanvullen van een query voorwaarde of het omlaag plaatsen van een lijst met overeenkomende koppelingen is gebruikers interactie code vereist, meestal java script, waarmee u aanvragen van externe bronnen kunt gebruiken, zoals automatisch aanvullen of suggestie query's voor een Azure Search cognitieve index.

Hoewel u deze code systeem eigen kunt schrijven, is het veel eenvoudiger om functies uit de bestaande Java script-bibliotheek te gebruiken, zoals een van de volgende. 

+ De [widget automatisch aanvullen (jQuery gebruikers interface)](https://jqueryui.com/autocomplete/) wordt weer gegeven in het code fragment voor suggesties. U kunt een zoekvak maken en hiernaar verwijzen in een Java script-functie die gebruikmaakt van de widget automatisch aanvullen. Eigenschappen van de widget stellen de bron (een functie voor automatisch aanvullen of suggesties), de minimum lengte van invoer tekens voordat actie wordt uitgevoerd en plaatsing.

+ De [XDSoft automatisch aanvullen-invoeg toepassing](https://xdsoft.net/jqplugins/autocomplete/) wordt weer gegeven in het code fragment automatisch aanvullen.

+ [suggesties](https://www.npmjs.com/package/suggestions) worden weer gegeven in de [zelf studie java script](tutorial-javascript-overview.md) en code sample.

Gebruik deze bibliotheken in de-client om een zoekvak te maken dat zowel suggesties als automatisch aanvullen ondersteunt. In het zoekvak verzamelde invoer kan vervolgens worden gekoppeld aan suggesties en acties voor automatisch aanvullen in de zoek service.

## <a name="suggestions"></a>Suggesties

In deze sectie wordt uitgelegd hoe u de voorgestelde resultaten implementeert, beginnend met de definitie van het zoekvak. Daarnaast ziet u hoe en script dat de eerste Java script-bibliotheek aanroept waarnaar in dit artikel wordt verwezen.

### <a name="create-a-search-box"></a>Een zoekvak maken

Uitgaande van de [JQUERY UI automatisch aanvullen-bibliotheek](https://jqueryui.com/autocomplete/) en een MVC-project in C#, kunt u het zoekvak definiëren met behulp van Java script in het bestand **index. cshtml** . De-bibliotheek voegt de zoek actie toe aan het zoekvak door asynchrone aanroepen naar de MVC-controller te maken om suggesties op te halen.

In **index. cshtml** onder de map \Views\Home ziet u een regel voor het maken van een zoekvak er als volgt uit:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Dit voor beeld is een eenvoudig invoer tekstvak met een klasse voor opmaak, een ID waarnaar wordt verwezen door Java script en tekst van tijdelijke aanduiding.  

Sluit in hetzelfde bestand java script in dat verwijst naar het zoekvak. Met de volgende functie wordt de API Voorst Ellen aangeroepen, waarmee suggesties voor voorgestelde overeenkomende documenten worden aangevraagd op basis van invoer van een gedeeltelijke term:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Hiermee wordt de `source` functie automatisch aanvullen van de jQuery-gebruikers interface aangegeven, waar de lijst met items wordt weer gegeven onder het zoekvak. Aangezien dit project een MVC-project is, wordt de functie **suggereren** aangeroepen in **HomeController. cs** die de logica bevat voor het retour neren van query suggesties. Deze functie geeft ook enkele para meters door aan het beheren van hooglichten, fuzzy matching en term. Door de JavaScript-API voor automatisch aanvullen wordt de parameter 'term' toegevoegd.

Hiermee `minLength: 3` wordt gegarandeerd dat aanbevelingen alleen worden weer gegeven wanneer het zoekvak ten minste drie tekens bevat.

### <a name="enable-fuzzy-matching"></a>Fuzzy matching inschakelen

Zoeken bij benadering zorgt ervoor dat u resultaten krijgt te zien op basis van treffers bij benadering, zelfs als de gebruiker een woord in het zoekvak onjuist heeft gespeld. De bewerkings afstand is 1, wat betekent dat er een maximum verschil is tussen een teken tussen de invoer van de gebruiker en een overeenkomst. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Markeren inschakelen

Met markeren wordt lettertype stijl toegepast op de tekens in het resultaat dat overeenkomt met de invoer. Als de gedeeltelijke invoer bijvoorbeeld ' micro ' is, wordt het resultaat weer gegeven als **micro** Soft, **micro** bereik, enzovoort. Markeren is gebaseerd op de para meters HighlightPreTag en HighlightPostTag, gedefinieerd inline met de functie Voorst Ellen.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Functie Voorst Ellen

Als u gebruikmaakt van C# en een MVC-toepassing, **HomeController. cs** -bestand onder de map controllers, kunt u een klasse maken voor de voorgestelde resultaten. In .NET is een functie Voorst Ellen gebaseerd op de [methode SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Zie [Azure Cognitive Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor meer informatie over de .NET SDK.

`InitSearch`Met de-methode wordt een geverifieerde HTTP-index client naar de Azure Cognitive Search-service gemaakt. De eigenschappen van de [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) -klasse bepalen welke velden worden doorzocht en geretourneerd in de resultaten, het aantal overeenkomsten en of fuzzy matching wordt gebruikt. 

Voor automatisch aanvullen is het niet meer dan één bewerkings afstand (één wegge laten of verkeerd geplaatst teken) beperkt. Houd er rekening mee dat bij benadering van automatisch aanvullen onverwachte resultaten kunnen worden gegenereerd, afhankelijk van de index grootte en de wijze waarop deze Shard. Zie voor meer informatie de [concepten Partition en sharding](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

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

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

De functie SuggestAsync heeft twee para meters die bepalen of treffer-en fuzzy-hooglichten worden geretourneerd, naast de invoer van de zoek term. U kunt Maxi maal acht overeenkomsten opnemen in de voorgestelde resultaten. De methode maakt een [SuggestOptions-object](/dotnet/api/azure.search.documents.suggestoptions), dat vervolgens wordt door gegeven aan de API Voorst Ellen. Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

## <a name="autocomplete"></a>Automatisch aanvullen

Tot nu toe is de zoek UX-code gecentreerd op suggesties. In het volgende code blok wordt automatisch aanvullen weer gegeven met de functie voor automatisch aanvullen van de gebruikers interface van XDSoft jQuery, waarbij een aanvraag voor Azure Cognitive Search automatisch aanvullen wordt door gegeven. Net als bij de suggesties, in een C#-toepassing, code die gebruikers interactie ondersteunt, gaat u naar **index. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Functie automatisch aanvullen

Automatisch aanvullen is gebaseerd op de [methode AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Net als bij suggesties gaat dit code blok in het bestand **HomeController. cs** .

```csharp
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

De functie automatisch aanvullen neemt de invoer van de zoek term. De methode maakt een [AutoCompleteParameters-object](/rest/api/searchservice/autocomplete). Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor end-to-end instructies of code die zowel zoek-als-u-type-ervaringen demonstreert. In het voor beeld wordt de hybride implementatie van suggesties en automatisch aanvullen gedemonstreerd.

+ Het toevoegen van een [Zoek opdracht aan een website (Java script)](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) maakt gebruik van een open source Suggestions-pakket voor een gedeeltelijke termijn voltooiing in de client-app.
+ [Zelf studie: uw eerste app maken in C# (Les 3)](tutorial-csharp-type-ahead-and-suggestions.md) met het bijbehorende  [C#-code voorbeeld: Azure-Search-DotNet-samples/Create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) demonstred support voor typeahead.