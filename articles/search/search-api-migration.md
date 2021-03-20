---
title: REST API versies bijwerken
titleSuffix: Azure Cognitive Search
description: Bekijk de verschillen in API-versies en ontdek welke acties vereist zijn voor het migreren van bestaande code naar de nieuwste versie van de Azure Cognitive Search-service REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929639"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Voer een upgrade uit naar de nieuwste REST API in azure Cognitive Search

Als u een eerdere versie van de [**zoek rest API**](/rest/api/searchservice/)gebruikt, helpt dit artikel bij het bijwerken van uw toepassing naar de nieuwste, algemeen beschik bare API-versie **2020-06-30**.

Versie 2020-06-30 bevat een belang rijke nieuwe functie ([Knowledge Store](knowledge-store-concept-intro.md)) en introduceert een aantal kleine gedrags wijzigingen. Als zodanig is deze versie voornamelijk compatibel met eerdere versies, dus code wijzigingen moeten mini maal zijn als u een upgrade uitvoert van de vorige versie (2019-05-06).

> [!NOTE]
> Een zoek service ondersteunt een reeks REST API-versies, waaronder eerder. U kunt deze API-versies blijven gebruiken, maar we raden u aan uw code te migreren naar de nieuwste versie zodat u toegang hebt tot nieuwe mogelijkheden. Na verloop van tijd worden de meest verouderde versies van de REST API verouderd en [niet meer ondersteund](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Upgrade uitvoeren

Wanneer u een upgrade naar een nieuwe versie uitvoert, hoeft u waarschijnlijk niet veel wijzigingen aan te brengen in uw code, behalve om het versie nummer te wijzigen. De enige situaties waarin u mogelijk code moet wijzigen, zijn als:

* De code mislukt wanneer niet-herkende eigenschappen worden geretourneerd in een API-antwoord. Uw toepassing moet standaard eigenschappen negeren die deze niet begrijpt.

* Uw code persistentie van API-aanvragen en probeert deze opnieuw te sturen naar de nieuwe API-versie. Dit kan bijvoorbeeld gebeuren als uw toepassing aanhoudt dat vervolg tokens worden geretourneerd door de zoek-API (Zie de naslag informatie over `@search.nextPageParameters` zoeken naar de [Zoek opdracht](/rest/api/searchservice/Search-Documents)).

* Uw code verwijst naar een API-versie die is voor 2019-05-06 en is onderhevig aan een of meer van de laatste wijzigingen in die release. De sectie [upgrade naar 2019-05-06](#upgrade-to-2019-05-06) bevat meer details. 

Als een van deze situaties op u van toepassing is, moet u de code mogelijk dienovereenkomstig wijzigen. Anders zijn er geen wijzigingen nodig, maar u kunt ook beginnen met het gebruik van functies die zijn toegevoegd in de nieuwe versie.

## <a name="upgrade-to-2020-06-30"></a>Voer een upgrade uit naar 2020-06-30

Versie 2020-06-30 is de nieuwe, algemeen beschik bare versie van de REST API. Er zijn één belang rijke wijziging en verschillende gedrags verschillen. 

Functies zijn nu algemeen beschikbaar in deze API-versie:

* [Kennis archief](knowledge-store-concept-intro.md), permanente opslag van verrijkte inhoud die is gemaakt via vaardig heden, gemaakt voor downstream-analyse en verwerking via andere toepassingen. Met deze mogelijkheid kan een door de Indexeer functie opgestuurde AI-verrijkings pijplijn een kennis archief vullen naast een zoek index. Als u de preview-versie van deze functie hebt gebruikt, is deze gelijk aan de algemeen beschik bare versie. De enige vereiste code wijziging is het wijzigen van de API-versie.

### <a name="breaking-change"></a>Laatste wijziging

Bestaande code die wordt geschreven ten opzichte van eerdere API-versies, verbreekt op API-Version = 2020-06-30 en hoger als code de volgende functionaliteit bevat:

* Wille keurige EDM. datum-en letterlijke waarden (een datum die bestaat uit jaar-maand-dag, zoals `2020-12-12` ) in filter expressies moeten de notatie EDM. date time offset hebben: `2020-12-12T00:00:00Z` . Deze wijziging is nood zakelijk om onjuiste of onverwachte query resultaten te verwerken vanwege verschillen in de tijd zone.

### <a name="behavior-changes"></a>Gedrags wijzigingen

* [BM25-classificatie algoritme](index-ranking-similarity.md) vervangt het vorige classificatie algoritme met nieuwere technologie. Nieuwe services gebruiken dit algoritme automatisch. Voor bestaande services moet u para meters instellen voor het gebruik van het nieuwe algoritme.

* Geordende resultaten voor Null-waarden zijn gewijzigd in deze versie, waarbij Null-waarden eerst worden weer gegeven als de sorteer bewerking is `asc` en laatste als de sorteer bewerking is `desc` . Als u code hebt geschreven om te verwerken hoe Null-waarden worden gesorteerd, let dan op deze wijziging.

## <a name="upgrade-to-2019-05-06"></a>Voer een upgrade uit naar 2019-05-06

Versie 2019-05-06 is de vorige, algemeen beschik bare versie van de REST API. Functies die algemeen beschikbaar werden in deze API-versie, zijn onder andere:

* [Automatisch aanvullen](index-add-suggesters.md) is een typeahead-functie waarmee een gedeeltelijk opgegeven term invoer wordt voltooid.
* [Complexe typen](search-howto-complex-data-types.md) bieden systeem eigen ondersteuning voor gestructureerde object gegevens in de zoek index.
* Met [JsonLines-parserings modi](search-howto-index-json-blobs.md), onderdeel van Azure Blob-indexering, wordt één Zoek document per JSON-entiteit gemaakt, gescheiden door een nieuwe regel.
* [AI-verrijking](cognitive-search-concept-intro.md) biedt indexeringen die gebruikmaken van de AI-verrijkings engines van Cognitive Services.

### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

Bestaande code die wordt geschreven ten opzichte van eerdere API-versies, verbreekt op API-Version = 2019-05-06 en hoger als code de volgende functionaliteit bevat:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexeer functie voor Azure Cosmos DB-Data Source is nu ' type ': ' cosmosdb '

Als u een [Cosmos DB Indexeer functie](search-howto-index-cosmosdb.md )gebruikt, moet u wijzigen `"type": "documentdb"` in `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Fouten bij de uitvoering van de indexer hebben geen status meer

De fout structuur voor de uitvoering van de Indexeer functie had eerder een `status` element. Dit element is verwijderd omdat het geen bruikbare informatie verstrekt.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>De API voor de gegevens bron van de Indexeer functie retourneert geen verbindings reeksen meer

Vanuit API-versies 2019-05-06 en 2019-05-06-preview-versie retourneert de gegevens bron-API geen verbindings reeksen meer in het antwoord op een REST-bewerking. In eerdere API-versies, voor gegevens bronnen die zijn gemaakt met behulp van POST, heeft Azure Cognitive Search geretourneerd **201** , gevolgd door het OData-antwoord, waarin de Connection String als tekst zonder opmaak is opgenomen.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>De benoemde kwalificatie voor het erkennen van entiteits herkenning is nu stopgezet

Als u de [naam entiteit herkennings](cognitive-search-skill-named-entity-recognition.md) kwalificatie in uw code noemt, mislukt de aanroep. De vervangings functionaliteit is [entiteits herkenning](cognitive-search-skill-entity-recognition.md). U moet de vaardigheids verwijzing zonder andere wijzigingen kunnen vervangen. De API-hand tekening is hetzelfde voor beide versies. 

### <a name="upgrading-complex-types"></a>Complexe typen upgraden

Met API-versie 2019-05-06 is formele ondersteuning voor complexe typen toegevoegd. Als uw code eerder aanbevolen aanbevelingen voor complex type-equivalentie in 2017-11-11-preview of 2016-09-01-preview heeft geïmplementeerd, zijn er enkele nieuwe en gewijzigde limieten vanaf versie 2019-05-06 waarvan u op de hoogte moet zijn:

+ De beperkingen voor de diepte van subvelden en het aantal complexe verzamelingen per index zijn lager. Als u indexen hebt gemaakt die deze limieten overschrijden met behulp van de preview-API-versies, zullen pogingen om deze te wijzigen of opnieuw te maken met API-versie 2019-05-06 mislukken. Als dit van toepassing is op u, moet u uw schema opnieuw ontwerpen zodat het binnen de nieuwe limieten past en de index vervolgens opnieuw bouwt.

+ Er is een nieuwe limiet die begint met API-versie 2019-05-06 op het aantal elementen van complexe verzamelingen per document. Als u indexen hebt gemaakt met documenten die deze limieten overschrijden met behulp van de preview-API-versies, zullen pogingen om die gegevens opnieuw te indexeren met API-versie 2019-05-06, mislukken. Als dit op u van toepassing is, moet u het aantal complexe verzamelings elementen per document verminderen voordat u de gegevens opnieuw indexeert.

Zie [service limieten voor Azure Cognitive Search](search-limits-quotas-capacity.md)voor meer informatie.

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Een oude, complexe type structuur bijwerken

Als uw code complexe typen gebruikt met een van de oudere versies van de preview-API, kunt u gebruikmaken van een index definitie-indeling die er als volgt uitziet:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Een nieuwere structuur achtige indeling voor het definiëren van index velden werd geïntroduceerd in API versie 2017-11-11-preview. In de nieuwe indeling heeft elk complex veld een verzameling velden waarin de subvelden ervan zijn gedefinieerd. In API versie 2019-05-06 wordt deze nieuwe indeling uitsluitend gebruikt en wordt geprobeerd een index met de oude indeling te maken of bij te werken. Als u indexen hebt gemaakt met de oude indeling, moet u API versie 2017-11-11-preview gebruiken om ze bij te werken naar de nieuwe indeling voordat ze kunnen worden beheerd met API-versie 2019-05-06.

U kunt ' platte ' Indexen bijwerken met de nieuwe indeling met de volgende stappen met API versie 2017-11-11-Preview:

1. Voer een GET-aanvraag uit om uw index op te halen. Als deze al in de nieuwe indeling is, bent u klaar.

2. Vertaal de index van de "platte" indeling naar de nieuwe indeling. U moet hiervoor code schrijven, omdat er geen voorbeeld code beschikbaar is op het moment van schrijven.

3. Voer een PUT-aanvraag uit om de index bij te werken naar de nieuwe indeling. Zorg ervoor dat u geen andere details van de index wijzigt, zoals de zoek functie en filterbaarheid van velden, omdat dit niet is toegestaan door de API van de update-index.

> [!NOTE]
> Het is niet mogelijk indexen te beheren die zijn gemaakt met de oude ' platte ' indeling van de Azure Portal. Upgrade uw indexen van de ' vlakke ' weer gave naar de boom structuur van uw eerste gebruik.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over zoek REST API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie voor de Search-service REST API](/rest/api/searchservice/)
