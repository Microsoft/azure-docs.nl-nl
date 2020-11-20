---
title: Een suggester maken
titleSuffix: Azure Cognitive Search
description: Schakel Type-Ahead query acties in azure Cognitive Search in door Voorst Ellen te maken en aanvragen te formuleren waarmee automatisch aanvullen of automatische suggesties worden aangeroepen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9122d6716aa94a7e0164c9c7774c7c8d85be814a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968006"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Een suggestie maken om automatisch aanvullen en voorgestelde resultaten in een query in te scha kelen

In azure Cognitive Search wordt ' Search-as-u-type ' ingeschakeld via een **suggestie voor suggesties** die is toegevoegd aan een [zoek index](search-what-is-an-index.md). Een suggestie biedt ondersteuning voor twee ervaring: *automatisch aanvullen*, waarmee een gedeeltelijke invoer voor een volledige termijn query wordt voltooid en *suggesties* die door worden uitgenodigd, worden door middel van een bepaalde overeenkomst. Automatisch aanvullen produceert een query. Suggesties maken een overeenkomend document.

De volgende scherm afbeelding van [het maken van uw eerste app in C#](tutorial-csharp-type-ahead-and-suggestions.md) illustreert beide. Bij automatisch aanvullen wordt een mogelijke term verwacht, waarbij ' TW ' wordt voltooid met ' in '. Suggesties zijn de resultaten van een mini maal zoek opdracht, waarbij een veld zoals de naam van het hotel een overeenkomend Hotel Zoek document uit de index vertegenwoordigt. Voor suggesties kunt u elk veld dat beschrijvende informatie bevat, op elk gewenst Opper vlak weer gegeven.

![Visuele vergelijking van automatisch aanvullen en voorgestelde query's](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuele vergelijking van automatisch aanvullen en voorgestelde query's")

U kunt deze functies afzonderlijk of samen gebruiken. Als u dit gedrag wilt implementeren in azure Cognitive Search, is er een index-en query onderdeel. 

+ Een suggestie toevoegen aan een definitie van een zoek index. De rest van dit artikel is gericht op het maken van een suggestie.

+ Roep een query voor suggesties ingeschakeld, in de vorm van een suggestie aanvraag of aanvraag voor automatisch aanvullen, met behulp van een van de [hieronder vermelde api's](#how-to-use-a-suggester).

De ondersteuning voor zoeken naar het type wordt ingeschakeld per veld voor teken reeks velden. U kunt zowel typeahead-gedrag in dezelfde Zoek oplossing implementeren als u een vergelijk bare ervaring wilt hebben als de functie die wordt aangegeven in de scherm opname. Beide aanvragen zijn gericht op de verzameling *documenten* van een specifieke index en reacties worden geretourneerd nadat een gebruiker ten minste een invoer teken reeks van drie tekens heeft opgegeven.

## <a name="what-is-a-suggester"></a>Wat is een suggestieer?

Een suggestie is een interne gegevens structuur die ondersteuning biedt voor zoek bewerkingen in het type door het opslaan van voor voegsels voor het vergelijken van gedeeltelijke query's. Net als bij tokened-voor waarden worden voor voegsels opgeslagen in omgekeerde indexen, één voor elk veld dat is opgegeven in de verzameling suggesties van velden.

## <a name="how-to-create-a-suggester"></a>Een suggestie maken

Als u een suggestie wilt maken, voegt u er een toe aan een [index definitie](/rest/api/searchservice/create-index). Een suggestie haalt een naam en een verzameling velden op waarvoor de typeahead-ervaring is ingeschakeld. en [Stel elke eigenschap](#property-reference)in. Het beste moment om een suggestie te maken, is wanneer u ook het veld definieert dat deze gebruikt.

+ Gebruik alleen teken reeks velden.

+ Als het teken reeks veld deel uitmaakt van een complex type (bijvoorbeeld een veld plaats in adres), neemt u het bovenliggende item op in het veld: `"Address/City"` (rest en C# en python) of `["Address"]["City"]` (Java script).

+ Gebruik de standaard standaard-lucene Analyzer ( `"analyzer": null` ) of een [taal analyse](index-add-language-analyzers.md) (bijvoorbeeld `"analyzer": "en.Microsoft"` ) in het veld.

Als u probeert een suggestie te maken met behulp van bestaande velden, wordt deze niet door de API toegestaan. Voor voegsels worden gegenereerd tijdens het indexeren, wanneer gedeeltelijke termen in twee of meer teken combinaties worden getokend op basis van hele voor waarden. Gezien dat bestaande velden al zijn getokend, moet u de index opnieuw samen stellen als u deze wilt toevoegen aan een suggestie. Zie [een Azure Cognitive search-index opnieuw samen stellen](search-howto-reindex.md)voor meer informatie.

### <a name="choose-fields"></a>Velden kiezen

Hoewel een suggestie meerdere eigenschappen heeft, is het hoofd zakelijk een verzameling teken reeks velden waarvoor u een zoek-als-u-type-ervaring inschakelt. Er is één suggestie voor elke index, dus de suggesties lijst moet alle velden bevatten die inhoud bijdragen voor beide suggesties en automatisch aanvullen.

Automatisch aanvullen heeft voor delen van een grotere groep velden waaruit kan worden getrokken, omdat de extra inhoud meer mogelijkheden heeft voor het volt ooien van termen.

Suggesties, daarentegen, produceren betere resultaten wanneer uw veld keuze selectief is. Houd er rekening mee dat de suggestie een proxy voor een zoek document is, zodat u wilt dat velden die het beste een enkel resultaat vertegenwoordigen. Namen, titels of andere unieke velden die onderscheid maken tussen meerdere overeenkomsten, werken het beste. Als velden bestaan uit herhaalde waarden, bestaan de suggesties uit identieke resultaten en is een gebruiker niet op welke manier u hoeft te klikken.

Als u wilt voldoen aan beide zoek functies, voegt u alle velden toe die u nodig hebt voor automatisch aanvullen, maar gebruikt u vervolgens **$Select**, **$Top**, **$filter** en **searchFields** om de resultaten voor suggesties te beheren.

### <a name="choose-analyzers"></a>Analyse functies kiezen

De keuze van een analyse functie bepaalt hoe velden worden getokend en vervolgens worden voorafgegaan door. Bijvoorbeeld: voor een teken reeks die is afgebroken als ' context gevoelig ', resulteert het gebruik van een taal analyse in deze combi Naties van tokens. ' context ', ' gevoelig ', ' context gevoelig '. Had u de standaard-lucene Analyzer hebt gebruikt, is de teken reeks met afbreek streepjes niet aanwezig. 

Bij het evalueren van analyse functies kunt u de [tekst-API analyseren](/rest/api/searchservice/test-analyzer) gebruiken om inzicht te krijgen in de manier waarop de voor waarden worden verwerkt. Wanneer u een index hebt gemaakt, kunt u verschillende analyse functies op een teken reeks uitproberen om de uitvoer van het token weer te geven.

Velden die gebruikmaken van [aangepaste analyse](index-add-custom-analyzers.md) functies of [vooraf gedefinieerde analyse](index-add-custom-analyzers.md#predefined-analyzers-reference) functies (met uitzonde ring van standaard-lucene), zijn expliciet niet toegestaan om slechte resultaten te voor komen.

> [!NOTE]
> Als u de restrictie beperking wilt omzeilen, bijvoorbeeld als u een tref woord of ngram Analyzer voor bepaalde query scenario's nodig hebt, moet u twee afzonderlijke velden voor dezelfde inhoud gebruiken. Hiermee kan een van de velden een suggestie bieden, terwijl de andere kan worden ingesteld met een aangepaste analyse configuratie.

## <a name="create-using-the-portal"></a>Maken met behulp van de portal

Wanneer u **index toevoegen** of de wizard **gegevens importeren** gebruikt om een index te maken, hebt u de mogelijkheid om een suggestie in te scha kelen:

1. Voer in de definitie van de index een naam in voor de suggestie.

1. Selecteer in elke veld definitie voor nieuwe velden een selectie vakje in de kolom suggestie. Een selectie vakje is alleen beschikbaar voor teken reeks velden. 

Zoals eerder genoteerd, beïnvloedt Analyzer Choice tokening en voor voegsels. Houd rekening met de volledige veld definitie bij het inschakelen van suggesties. 

## <a name="create-using-rest"></a>Maken met REST

Voeg in de REST API suggesties toe via [Create Index](/rest/api/searchservice/create-index) of [update index](/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Maken met .NET

In C# definieert u een [SearchSuggester-object](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` is een verzameling voor een SearchIndex-object, maar kan slechts één item hebben. 

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    var definition = new SearchIndex()
    {
        FieldBuilder builder = new FieldBuilder();
        Fields = builder.Build(typeof(Hotel);
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    }

    await indexClient.CreateIndexAsync(definition);
}
```

## <a name="property-reference"></a>Eigenschappen referentie

|Eigenschap      |Beschrijving      |
|--------------|-----------------|
|`name`        | Opgegeven in de suggestie definitie, maar wordt ook wel een aanvraag voor automatisch aanvullen of suggesties genoemd. |
|`sourceFields`| Opgegeven in de suggestie definitie. Het is een lijst met een of meer velden in de index die de bron van de inhoud voor suggesties zijn. Velden moeten van het type `Edm.String` en zijn `Collection(Edm.String)` . Als er een analyse programma is opgegeven in het veld, moet dit een benoemde lexicale Analyzer zijn in [deze lijst](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (geen aangepaste analyse functie).<p/> Geef als best practice alleen de velden op die aan een verwacht en passend antwoord worden uitgeleend, of het nu gaat om een voltooide teken reeks in een zoek balk of vervolg keuzelijst.<p/>De naam van een hotel is een goede kandidaat omdat deze precisie heeft. Uitgebreide velden, zoals beschrijvingen en opmerkingen, zijn te dicht bij. Zo zijn herhalende velden, zoals categorieën en tags, minder effectief. In de voor beelden bevatten we ' categorie ' om aan te tonen dat u meerdere velden kunt bevatten. |
|`searchMode`  | De para meter alleen-lezen, maar is ook zichtbaar in de portal. Deze para meter is niet beschikbaar in de .NET SDK. Hiermee wordt de strategie aangegeven die wordt gebruikt om te zoeken naar kandidaten zinsdelen. De enige modus die momenteel wordt ondersteund `analyzingInfixMatching` , die momenteel overeenkomt met het begin van een term.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Een suggestie gebruiken

Een suggestie wordt gebruikt in een query. Nadat een suggestie is gemaakt, roept u een van de volgende Api's aan voor een zoek-als-u-type-ervaring:

+ [Suggesties REST API](/rest/api/searchservice/suggestions)
+ [REST API automatisch aanvullen](/rest/api/searchservice/autocomplete)
+ [Methode SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Methode AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

In een zoek toepassing moet de client code gebruikmaken van een bibliotheek zoals de [automatisch aanvullen van de jQuery-gebruikers interface](https://jqueryui.com/autocomplete/) om de gedeeltelijke query te verzamelen en de overeenkomst op te geven. Zie voor meer informatie over deze taak [automatisch aanvullen of voorgestelde resultaten aan client code toevoegen](search-autocomplete-tutorial.md).

Het API-gebruik wordt geïllustreerd in de volgende aanroep van de REST API voor automatisch aanvullen. Er zijn twee Takeaways uit dit voor beeld. Ten eerste, net als bij alle query's wordt de bewerking vergeleken met de documenten verzameling van een index en de query bevat een **Zoek** parameter, in dit geval de gedeeltelijke query. Ten tweede moet u **suggesterName** toevoegen aan de aanvraag. Als een suggestie niet in de index is gedefinieerd, mislukt de aanroep van automatisch aanvullen of suggesties.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Voorbeeldcode

+ [Uw eerste app maken in C# (Les 3-zoek opdracht toevoegen aan het type)](tutorial-csharp-type-ahead-and-suggestions.md) demonstreert aanbevolen query's, automatisch aanvullen en facet navigatie. Dit code voorbeeld wordt uitgevoerd op een sandbox Azure Cognitive Search-service en maakt gebruik van een vooraf geladen Hotels-index met een suggestie die al is gemaakt. u hoeft dus alleen op F5 te drukken om de toepassing uit te voeren. Er is geen abonnement of aanmelding vereist.

## <a name="next-steps"></a>Volgende stappen

We raden u aan het volgende artikel te lezen om meer te weten te komen over de manier waarop aanvragen worden formulering.

> [!div class="nextstepaction"]
> [Automatisch aanvullen en suggesties aan client code toevoegen](search-autocomplete-tutorial.md)