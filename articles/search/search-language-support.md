---
title: Meerdere talen indexeren voor niet-Engelse Zoek query's
titleSuffix: Azure Cognitive Search
description: Een index maken die inhoud in meerdere talen ondersteunt en vervolgens query's maakt die zijn afgestemd op die inhoud.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801601"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Een index maken voor meerdere talen in azure Cognitive Search

Een belang rijke vereiste in een meertalige Zoek toepassing is de mogelijkheid om de resultaten in de eigen taal van de gebruiker te doorzoeken en op te halen. In azure Cognitive Search, een manier om te voldoen aan de taal vereisten van een meertalige app, is het maken van speciale velden voor het opslaan van teken reeksen in een specifieke taal en het beperken van zoek opdrachten in volledige tekst tot deze velden op de query tijd.

+ Stel in veld definities een taal analyse in die de taal kundige regels van de doel taal aanroept. Als u de volledige lijst met ondersteunde analyse functies wilt weer geven, raadpleegt u [taal analyse functies toevoegen](index-add-language-analyzers.md).

+ Stel op de query aanvraag para meters in op bereik zoeken in volledige tekst naar specifieke velden en knip vervolgens de resultaten van alle velden die geen inhoud bieden die compatibel is met de zoek ervaring die u wilt leveren.

Het succes van deze techniek scharniert op de integriteit van de veld inhoud. Azure Cognitive Search converteert geen teken reeksen of voert geen taal detectie uit als onderdeel van de uitvoering van query's. U moet ervoor zorgen dat velden de door u verwachte teken reeksen bevatten.

## <a name="define-fields-for-content-in-different-languages"></a>Velden definiëren voor inhoud in verschillende talen

In azure Cognitive Search worden query's gericht op één index. Ontwikkel aars die taalspecifieke teken reeksen willen opgeven in één zoek opdracht, definiëren meestal specifieke velden voor het opslaan van de waarden: Eén veld voor Engelse teken reeksen, een voor Frans, enzovoort.

De eigenschap ' Analyzer ' voor een veld definitie wordt gebruikt om de [taal analyse](index-add-language-analyzers.md)in te stellen. Deze wordt gebruikt voor het indexeren en uitvoeren van query's.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Een index maken en laden

Een tussenliggende (en mogelijk duidelijke) stap is dat u [de index moet bouwen en vullen](search-get-started-dotnet.md) voordat u een query kunt formuleren. Deze stap wordt hier vermeld voor volledigheid. Een manier om de beschik baarheid van de index te bepalen, is door de lijst indexen te controleren in de [Portal](https://portal.azure.com).

> [!TIP]
> Taal detectie en tekst omzetting worden ondersteund tijdens gegevens opname door middel van [AI-verrijking](cognitive-search-concept-intro.md) en [vaardig heden](cognitive-search-working-with-skillsets.md). Als u een Azure-gegevens bron met gemengde inhoud hebt, kunt u de functies voor taal detectie en-omzetting proberen met behulp van de [wizard gegevens importeren](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>De resultaten van de query en het knippen beperken

De para meters voor de query worden gebruikt om de zoek opdracht te beperken tot specifieke velden en vervolgens de resultaten van velden die niet nuttig zijn voor uw scenario, te verkorten. 

| Parameters | Doel |
|-----------|--------------|
| **searchFields** | Hiermee beperkt u het zoeken in volledige tekst in de lijst met benoemde velden. |
| **$select** | Hiermee verkleint u het antwoord op alleen de velden die u opgeeft. Standaard worden alle ophaalbaar velden geretourneerd. Met de para meter **$Select** kunt u kiezen welke items u wilt retour neren. |

Gezien het doel van het beperken van een zoek opdracht naar velden met Franse teken reeksen, gebruikt u **searchFields** om de query te richten op velden met teken reeksen in die taal.

Het opgeven van het analyse functie voor een query aanvraag is niet nodig. Een taal analyse op de veld definitie wordt altijd gebruikt tijdens de query verwerking. Voor query's waarmee meerdere velden worden opgegeven die verschillende taal analyse functies aanroepen, worden de termen of zinsdelen onafhankelijk verwerkt door de toegewezen analyse functies voor elk veld.

Een zoek opdracht retourneert standaard alle velden die zijn gemarkeerd als ophalen. Daarom wilt u mogelijk velden uitsluiten die niet voldoen aan de taalspecifieke Zoek ervaring die u wilt bieden. Als u de zoek opdracht beperkt tot een veld met Franse teken reeksen, wilt u waarschijnlijk velden met Engelse teken reeksen uitsluiten van uw resultaten. Met behulp van de para meter **$Select** query kunt u bepalen welke velden worden geretourneerd naar de aanroepende toepassing.

#### <a name="example-in-rest"></a>Voor beeld in REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Voor beeld in C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Taalspecifieke velden verhogen

Soms is de taal van de agent waarmee een query wordt uitgegeven niet bekend. in dat geval kan de query worden uitgegeven voor alle velden tegelijk. De voor keur voor IA voor resultaten in een bepaalde taal kan worden gedefinieerd met behulp van [Score profielen](index-add-scoring-profiles.md). In het onderstaande voor beeld worden overeenkomsten die in de beschrijving in het Engels zijn gevonden, hoger beoordeeld ten opzichte van overeenkomsten in andere talen:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Vervolgens voegt u het Score profiel in de zoek opdracht toe:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Volgende stappen

+ [Taalanalyse](index-add-language-analyzers.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API voor documenten zoeken](/rest/api/searchservice/search-documents)
+ [Overzicht van AI-verrijking](cognitive-search-concept-intro.md)
+ [Overzicht van vaardig heden](cognitive-search-working-with-skillsets.md)