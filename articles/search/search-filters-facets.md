---
title: Facet filters voor zoek navigatie in apps
titleSuffix: Azure Cognitive Search
description: Filter criteria op beveiligings identiteit van de gebruiker, geo-locatie of numerieke waarden om Zoek resultaten te verminderen voor query's in azure Cognitive Search, een gehoste service voor zoeken in de Cloud op Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9360fc000e01e1c52561cbaa3e2f2968e67e2fa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91740867"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Een facet filter maken in azure Cognitive Search 

Facet navigatie wordt gebruikt voor zelfgestuurde filters op query resultaten in een zoek-app, waarbij uw toepassing UI-besturings elementen biedt voor het bereiken van een zoek opdracht naar groepen documenten (bijvoorbeeld categorieën of Brands), en Azure Cognitive Search voorziet in de gegevens structuur om de werk ervaring te herstellen. In dit artikel worden de basis stappen voor het maken van een facet navigatie structuur die u wilt bieden, snel gecontroleerd. 

> [!div class="checklist"]
> * Velden voor filteren en facetten kiezen
> * Kenmerken voor het veld instellen
> * De index maken en gegevens laden
> * Facet filters toevoegen aan een query
> * Resultaten verwerken

Facetten zijn dynamisch en worden geretourneerd op een query. Zoek reacties nemen de facet categorieën mee die worden gebruikt om de resultaten te navigeren. Als u niet bekend bent met facetten, is het volgende voor beeld een illustratie van een facet navigatie structuur.

:::image type="complex" source="media/search-filters-facets/facet-nav.png" alt-text="gefilterde Zoek resultaten":::
"Een afbeelding met een zoek venster met gefilterde Zoek resultaten, gegroepeerd op zakelijke titel. Een pijl geeft aan dat de resultaten facetten zijn die worden weer gegeven in een facet navigatie structuur. :::image-end:::

Nieuw in facet navigatie en wilt u meer details? Zie [facet navigatie implementeren in Azure Cognitive Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Velden kiezen

Facetten kunnen worden berekend op basis van velden met één waarde en verzamelingen. Velden die het beste in facet navigatie werken, hebben een laag kardinaliteit: een klein aantal afzonderlijke waarden die in documenten in uw zoek verzameling worden herhaald (bijvoorbeeld een lijst met kleuren, landen/regio's of merk namen). 

Facetatie is ingeschakeld voor een veld per veld wanneer u de index maakt door het `facetable` kenmerk in te stellen op `true` . Normaal gesp roken moet u het `filterable` kenmerk ook instellen op `true` voor dergelijke velden, zodat de zoek toepassing kan filteren op deze velden op basis van facetten die de eind gebruiker selecteert. 

Wanneer u een index maakt met behulp van de REST API, wordt elk [veld type](/rest/api/searchservice/supported-data-types) dat mogelijk in facet navigatie kan worden gebruikt, `facetable` standaard als volgt gemarkeerd:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numerieke veld typen: `Edm.Int32` , `Edm.Int64` , `Edm.Double`
+ Verzamelingen van de bovenstaande typen (bijvoorbeeld `Collection(Edm.String)` of `Collection(Edm.Double)` )

U kunt geen `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` velden gebruiken in facet navigatie. Facetten werken het beste bij velden met een lage kardinaliteit. Als gevolg van de omzetting van geo-coördinaten, komt het zelden voor dat twee sets van co-niveaus gelijk zijn aan een bepaalde gegevensset. Als zodanig worden facetten niet ondersteund voor geo-coördinaten. U hebt een veld plaats of regio nodig om te facetten per locatie.

## <a name="set-attributes"></a>Kenmerken instellen

Index kenmerken die bepalen hoe een veld wordt gebruikt, worden toegevoegd aan afzonderlijke veld definities in de index. In het volgende voor beeld zijn velden met een lage kardinaliteit, die nuttig zijn voor facetten, bestaan uit: `category` (Hotel, Motel, Hostel), `tags` en `rating` . In deze velden zijn de `filterable` en- `facetable` kenmerken expliciet ingesteld in het volgende voor beeld voor illustratie doeleinden. 

> [!Tip]
> Als best practice voor de prestaties en Optima Lise ring van de opslag kunt u facetatie uitschakelen voor velden die nooit als een facet moeten worden gebruikt. In het bijzonder moeten teken reeks velden voor unieke waarden, zoals een ID of product naam, worden ingesteld op `"facetable": false` om te voor komen dat ze per ongeluk (en oneffectief) gebruikmaken van facet navigatie.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Deze index definitie wordt gekopieerd van [een Azure Cognitive search-index maken met behulp van de rest API](./search-get-started-powershell.md). Het is identiek, met uitzonde ring van de Opper vlakke verschillen in de veld definities. De `filterable` `facetable` kenmerken en worden expliciet toegevoegd aan `category` de `tags` velden,,, `parkingIncluded` `smokingAllowed` en `rating` . In de praktijk `filterable` en `facetable` standaard ingeschakeld op deze velden wanneer u de rest API gebruikt. Wanneer u de .NET SDK gebruikt, moeten deze kenmerken expliciet worden ingeschakeld.

## <a name="build-and-load-an-index"></a>Een index maken en laden

Een tussenliggende (en mogelijk duidelijke) stap is dat u [de index moet bouwen en vullen](./search-get-started-dotnet.md#1---create-an-index) voordat u een query kunt formuleren. Deze stap wordt hier vermeld voor volledigheid. Een manier om te bepalen of de index beschikbaar is, is door de lijst indexen te controleren in de [Portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Facet filters toevoegen aan een query

In toepassings code maakt u een query waarmee alle onderdelen van een geldige query worden opgegeven, met inbegrip van zoek expressies, facetten, filters, Score profielen, waarmee alles wordt gebruikt om een aanvraag te formuleren. In het volgende voor beeld wordt een aanvraag gebouwd waarmee facet navigatie wordt gemaakt op basis van het type accommodatie, classificatie en andere voorzieningen.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Gefilterde resultaten retour neren bij gebeurtenissen klikken

Wanneer de eind gebruiker op een facet waarde klikt, moet de handler voor de gebeurtenis Click een filter expressie gebruiken om de intentie van de gebruiker te realiseren. Op basis `category` van een facet wordt het klikken op de categorie Motel geïmplementeerd met een `$filter` expressie die de accommodaties van dat type selecteert. Wanneer een gebruiker op ' Motel ' klikt om aan te geven dat alleen motels moeten worden weer gegeven, wordt de volgende query die de toepassing verzendt, opgenomen `$filter=category eq 'motel'` .

Met het volgende code fragment wordt een categorie aan het filter toegevoegd als een gebruiker een waarde uit het facet van de categorie selecteert.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Als de gebruiker klikt op een facet waarde voor een veld verzameling als `tags` bijvoorbeeld de waarde pool, moet uw toepassing de volgende filter syntaxis gebruiken: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tips en tijdelijke oplossingen

### <a name="initialize-a-page-with-facets-in-place"></a>Een pagina met facetten op locatie initialiseren

Als u een pagina wilt initialiseren waarop zich facetten bevindt, kunt u een query als onderdeel van de pagina-initialisatie verzenden om de pagina met een eerste facet structuur te seeden.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Een facet navigatie structuur asynchroon behouden van gefilterde resultaten

Een van de uitdagingen met facet navigatie in azure Cognitive Search is dat facetten alleen bestaan voor huidige resultaten. In de praktijk is het gebruikelijk om een statische set facetten te bewaren, zodat de gebruiker in omgekeerde volg orde kan navigeren en de stappen voor het verkennen van alternatieve paden door de zoek inhoud kan worden getraceerd. 

Hoewel dit een veelvoorkomende use-case is, is het niet duidelijk dat de facet navigatie structuur momenteel out-of-the-box bevat. Ontwikkel aars die statische facetten willen, kunnen de beperking meestal omzeilen door twee gefilterde query's uit te geven: één scoped to the results, de andere die wordt gebruikt voor het maken van een statische lijst met facetten voor navigatie doeleinden.

## <a name="see-also"></a>Zie ook

+ [Filters in azure Cognitive Search](search-filters.md)
+ [Index REST API maken](/rest/api/searchservice/create-index)
+ [REST API voor documenten zoeken](/rest/api/searchservice/search-documents)