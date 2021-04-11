---
title: Spelling controle toevoegen
titleSuffix: Azure Cognitive Search
description: Koppel de spelling correctie aan de query pijplijn om typfouten te herstellen voor query termen voordat de query wordt uitgevoerd.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625905"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Spelling controle toevoegen aan query's in Cognitive Search

> [!IMPORTANT]
> De spelling correctie bevindt zich in de open bare preview, die alleen beschikbaar is via de preview-versie REST API. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden. Tijdens de eerste preview-versie worden er geen kosten in rekening gebracht voor de spelling controle. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

U kunt de intrekking verbeteren door de spelling van afzonderlijke zoek termen te corrigeren voordat de zoek machine wordt bereikt. De para meter **speller** wordt ondersteund voor alle query typen: [eenvoudig](query-simple-syntax.md), [volledig](query-lucene-syntax.md)en de nieuwe [semantische](semantic-how-to-query-request.md) optie die momenteel beschikbaar is in de open bare preview.

## <a name="prerequisites"></a>Vereisten

+ Een bestaande zoek index die Engelse inhoud bevat. De spelling correctie werkt momenteel niet met [synoniemen](search-synonyms.md). Vermijd het gebruik ervan voor indexen waarmee een synoniemen toewijzing in een veld definitie wordt opgegeven.

+ Een Search-client voor het verzenden van query's

  De Search-client moet de preview REST-Api's voor de query aanvraag ondersteunen. U kunt [postman](search-get-started-rest.md), [Visual Studio code](search-get-started-vs-code.md)of code gebruiken die u hebt gewijzigd om rest-aanroepen naar de preview-api's te maken.

+ [Een query aanvraag](/rest/api/searchservice/preview-api/search-documents) die gebruikmaakt van spelling correctie, heeft de API-Version = 2020-06 -30-preview, ' speller = lexicon ' en ' queryLanguage = nl-US '.

  De queryLanguage is vereist voor de spelling controle en momenteel is ' nl-nl ' de enige geldige waarde.

> [!Note]
> De para meter speller is beschikbaar op alle lagen, in dezelfde regio's die een semantische zoek opdracht bieden. U hoeft zich niet aan te melden voor toegang tot deze preview-functie. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

## <a name="spell-correction-with-simple-search"></a>Spelling correctie met eenvoudige zoek actie

In het volgende voor beeld wordt gebruikgemaakt van de ingebouwde Hotels-voorbeeld index voor het demonstreren van spelling correctie op een eenvoudige formulier tekst query. Zonder spelling correctie retourneert de query nul resultaten. Met een correctie retourneert de query één resultaat voor de familie gerichte redmiddel van Johnson.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Spelling correctie met volledige lucene

Spelling correctie vindt plaats op afzonderlijke query termen die worden geanalyseerd. Daarom kunt u de para meter speller gebruiken bij sommige lucene-query's, maar niet op andere.

+ Incompatibele query formulieren die tekst analyse overs Laan zijn: Joker teken, regex, fuzzy
+ Compatibele query formulieren zijn: doorzoeken op zoek functie, Proximity, term Boosting

In dit voor beeld wordt gebruikgemaakt van een zoek opdracht in het veld categorie, met volledige lucene-syntaxis en een verkeerd gespelde query term. Door de Spellings controle uit te voeren, wordt de type fout ' Suiite ' gecorrigeerd en wordt de query geslaagd.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Spelling correctie met semantische zoek actie

Met deze query, met type fouten in elke term, met uitzonde ring van één, onderneemt de spelling correcties om relevante resultaten te retour neren. Zie [een semantische query maken](semantic-how-to-query-request.md)voor meer informatie.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Taal overwegingen

De para meter queryLanguage die is vereist voor de spelling controle moet consistent zijn met alle [taal analyse](index-add-language-analyzers.md) functies die zijn toegewezen aan veld definities in het index schema. 

+ queryLanguage bepaalt welke lexicons worden gebruikt voor de spelling controle en wordt ook gebruikt als invoer voor het [algoritme voor semantische classificatie](semantic-answers.md) als u ' query type = semantiek ' gebruikt.

+ Taal analysen worden gebruikt tijdens het indexeren en uitvoeren van query's om overeenkomende documenten te vinden in de zoek index. Een voor beeld van een veld definitie die gebruikmaakt van een taal analyse is `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Voor de beste resultaten bij het gebruik van de Spellings controle, als queryLanguage "en-US" is, moeten alle taal analysen ook een Engelse variant ("en. micro soft" of "en. lucene") zijn.

> [!NOTE]
> Taal neutraal-analyse functies (zoals sleutel woord, eenvoudig, standaard, stoppen, spatie of `standardasciifolding.lucene` ) veroorzaken geen conflict met queryLanguage-instellingen.

In een query aanvraag gelden de queryLanguage die u hebt ingesteld gelijk aan de spelling, antwoorden en bijschriften. Er zijn geen onderdrukkingen voor afzonderlijke onderdelen.

Terwijl inhoud in een zoek index in meerdere talen kan worden samengesteld, is de query-invoer waarschijnlijk in één taal. De zoek machine controleert niet op compatibiliteit van queryLanguage, language Analyzer en de taal waarin inhoud is samengesteld. Zorg er daarom voor dat u query's moet uitvoeren om te voor komen dat er onjuiste resultaten worden geproduceerd.

## <a name="next-steps"></a>Volgende stappen

+ [Een semantische query maken](semantic-how-to-query-request.md)
+ [Een basisquery maken](search-query-create.md)
+ [Volledige lucene-query syntaxis gebruiken](query-Lucene-syntax.md)
+ [Eenvoudige query syntaxis gebruiken](query-simple-syntax.md)
+ [Overzicht van semantisch zoeken](semantic-search-overview.md)