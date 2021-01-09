---
title: Upgrade uitvoeren naar .NET SDK versie 11
titleSuffix: Azure Cognitive Search
description: Code migreren naar Azure Cognitive Search .NET SDK versie 11 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: c5f070f59df69bb186041af450e6ca922469d960
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98043741"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Upgrade uitvoeren naar Azure Cognitive Search .NET SDK versie 11

Als u versie 10,0 of ouder van de [.NET SDK](/dotnet/api/overview/azure/search)gebruikt, helpt dit artikel bij het upgraden naar versie 11 en de **Azure.Search.Documents** -client bibliotheek.

Versie 11 is een volledig opnieuw ontworpen client bibliotheek, uitgebracht door het team van Azure SDK Development (eerdere versies zijn gemaakt door het Azure Cognitive Search Development-Team). De tape wisselaar is opnieuw ontworpen voor een grotere consistentie met andere Azure-client Bibliotheken, met een afhankelijkheid van [Azure. core](/dotnet/api/azure.core) en [System.Text.Jsop](/dotnet/api/system.text.json)en het implementeren van bekende benaderingen voor algemene taken.

Enkele belang rijke verschillen in de nieuwe versie zijn:

+ Eén pakket en bibliotheek, in tegens telling tot meerdere
+ Een nieuwe pakket naam: `Azure.Search.Documents` in plaats van `Microsoft.Azure.Search` .
+ Drie clients in plaats van twee: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ Benoemt verschillen over een reeks Api's en kleine structurele verschillen die sommige taken vereenvoudigen

Naast dit artikel, kunt u het [wijzigings logboek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) controleren op een lijst met wijzigingen in .NET SDK versie 11.

## <a name="package-and-library-consolidation"></a>Pakket-en bibliotheek consolidatie

Met versie 11 worden meerdere pakketten en bibliotheken samengevoegd tot één. Na de migratie kunt u minder bibliotheken beheren.

+ [ Uments-pakketAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [API-verwijzing voor de client bibliotheek](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>Client verschillen

Indien van toepassing, wijst de volgende tabel de client bibliotheken toe tussen de twee versies.

| Bereik van bewerkingen | Micro soft. Azure. Search &nbsp; (v10 toevoegen) | Azure.Search.Documents &nbsp; (V11) |
|---------------------|------------------------------|------------------------------|
| Client die wordt gebruikt voor query's en voor het vullen van een index. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Client die wordt gebruikt voor indexen, analyse functies, synoniemen | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Client die wordt gebruikt voor Indexeer functies, gegevens bronnen, vaardig heden | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**Nieuw**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` bestaat in beide versies, maar ondersteunt verschillende dingen. Maak in versie 10 `SearchIndexClient` indexen en andere objecten. In versie 11 `SearchIndexClient` werkt met bestaande indexen. Om Verwar ring te voor komen bij het bijwerken van code, moet u mindful van de volg orde waarin de client verwijzingen worden bijgewerkt. Als u de volg orde van de [stappen voor het uitvoeren](#UpgradeSteps) van een upgrade volgt, moet u problemen met de vervanging van teken reeksen oplossen.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Naamgeving en andere API-verschillen

Naast de verschillen tussen de client (eerder vermeld en daarom wegge laten), hebben meerdere andere Api's een andere naam gekregen en in sommige gevallen opnieuw ontworpen. Hieronder vindt u een overzicht van de namen van klassenaam. Deze lijst is niet volledig, maar groeps-API-wijzigingen per taak, wat nuttig kan zijn voor revisies van specifieke code blokken. Zie het [wijzigingslog bestand](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) voor op github voor een lijst met updates van de API `Azure.Search.Documents` .

### <a name="authentication-and-encryption"></a>Verificatie en versleuteling

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (bestond in de [Preview-SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) als een algemeen beschik bare functie) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indexen, analyse functies, synoniemen kaarten

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Veld](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [Param1](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Procedures](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (ook `AnalyzerName` naar `LexicalAnalyzerName` ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (ook `StandardTokenizerV2` naar `LuceneStandardTokenizerV2` ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (ook `TokenizerName` naar `LexicalTokenizerName` ) |
| [SynonymMap. Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Geen. Verwijder verwijzingen naar `Format` . |

Veld definities worden gestroomlijnd: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) zijn nieuwe api's voor het maken van veld definities.

### <a name="indexers-datasources-skillsets"></a>Indexeer functies, gegevens bronnen, vaardig heden

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [Indexeerfunctie](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [Bron](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Vaardigheid](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Vaardig heden](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Data Source Type](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Gegevens importeren

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Query definities en resultaten

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) of [searchresults](/dotnet/api/azure.search.documents.models.searchresults-1), afhankelijk van het feit of het resultaat één document of meerdere is. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [Search Options worden](/dotnet/api/azure.search.documents.searchoptions)  |

### <a name="json-serialization"></a>JSON-serialisatie

De Azure SDK maakt standaard gebruik van [System.Text.Js](/dotnet/api/system.text.json) voor JSON-serialisatie, afhankelijk van de mogelijkheden van deze api's voor het verwerken van tekst transformaties die eerder zijn geïmplementeerd via een systeem eigen [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) -klasse, die geen equivalent in de nieuwe bibliotheek heeft.

Als u eigenschaps namen wilt serialiseren in camelCase, kunt u de [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) gebruiken (vergelijkbaar met [dit voor beeld](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)).

U kunt ook een [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) instellen die is opgegeven in [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions). De volgende System.Text.Jsin code voorbeeld van het Leesmij- [bestand micro soft. Azure. core. ruimtelijk](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents) demonstreert het gebruik van camelCase zonder dat u elke eigenschap hoeft te kenmerken:

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

Als u Newtonsoft.Jsgebruikt voor JSON-serialisatie, kunt u globaal naamgevings beleid door geven met vergelijk bare kenmerken of met behulp van eigenschappen op [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm). Zie het voor beeld voor het [deserialiseren van documenten](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) in de Newtonsoft.Jsin Leesmij voor een voor beeld dat overeenkomt met het bovenstaande.


<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Wat is er in versie 11

Elke versie van een Azure Cognitive Search-client bibliotheek is gericht op een bijbehorende versie van de REST API. De REST API wordt beschouwd als basis voor de service, waarbij afzonderlijke Sdk's een versie van de REST API inpakken. Als .NET-ontwikkelaar kan het nuttig zijn om [rest API documentatie](/rest/api/searchservice/) te controleren als u meer achtergrond informatie wilt over specifieke objecten of bewerkingen.

Versie 11 streeft [naar de 2020-06-30-zoek service](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Omdat versie 11 ook een nieuwe client bibliotheek is die is gebouwd op basis van het begin, is het grootste deel van de ontwikkelings inspanning gericht op equivalentie met versie 10, met een aantal REST API functie-ondersteuning nog in behandeling.

Versie 11,0 biedt volledige ondersteuning voor de volgende objecten en bewerkingen:

+ Index maken en beheren
+ Synoniemen voor het maken en beheren van kaarten
+ Alle query typen en-syntaxis (met uitzonde ring van geo-ruimtelijke filters)
+ Indexeer objecten en bewerkingen voor het indexeren van Azure-gegevens bronnen, waaronder gegevens bronnen en vaardig heden

Versie 11,1 voegt het volgende toe:

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (toegevoegd in 11,1)
+ [Serializer-eigenschap](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (toegevoegd in 11,1) ter ondersteuning van aangepaste serialisatie

### <a name="pending-features"></a>Functies in behandeling

De volgende versie 10-functies zijn nog niet beschikbaar in versie 11. Als u deze functies nodig hebt, moet u de migratie uitschakelen totdat deze worden ondersteund.

+ georuimtelijke typen
+ [Knowledge Store](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade

Met de volgende stappen kunt u aan de slag gaan met een code migratie door de eerste set vereiste taken door lopen, met name met betrekking tot client verwijzingen.

1. Installeer het [Azure.Search.Documents-pakket](https://www.nuget.org/packages/Azure.Search.Documents/) door met de rechter muisknop op uw project verwijzingen te klikken en vervolgens NuGet-pakketten beheren te selecteren. in Visual Studio.

1. Vervang met behulp van de instructies voor micro soft. Azure. Search door het volgende:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Voor klassen waarvoor JSON-serialisatie is vereist, vervangt u door `using Newtonsoft.Json` `using System.Text.Json.Serialization` .

1. Client verificatie code wijzigen. In vorige versies zou u eigenschappen op het client object gebruiken om de API-sleutel in te stellen (bijvoorbeeld de eigenschap [SearchServiceClient. credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) ). Gebruik in de huidige versie de [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) -klasse om de sleutel als referentie door te geven, zodat u, indien nodig, de API-sleutel kunt bijwerken zonder nieuwe client objecten te maken.

   Client eigenschappen zijn gestroomlijnd tot alleen `Endpoint` , `ServiceName` en `IndexName` (indien van toepassing). In het volgende voor beeld wordt de systeem- [URI](/dotnet/api/system.uri) -klasse gebruikt om het eind punt en de [omgevings](/dotnet/api/system.environment) klasse op te geven voor het lezen van de sleutel waarde:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Voeg nieuwe client referenties toe voor Indexeer functie-gerelateerde objecten. Als u Indexeer functies, gegevens bronnen of vaardig heden gebruikt, wijzigt u de client verwijzingen naar [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Deze client is nieuw in versie 11 en heeft geen ante cedent.

1. Verzamelingen en lijsten reviseren. In de nieuwe SDK zijn alle lijsten alleen-lezen om downstream-problemen te voor komen als de lijst Null-waarden bevat. De code moet worden gewijzigd om items toe te voegen aan een lijst. In plaats van teken reeksen toe te wijzen aan een eigenschap Select, voegt u deze bijvoorbeeld als volgt toe:

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   SELECT, Facets, SearchFields, SourceFields, ScoringParameters en OrderBy zijn alle lijsten die nu opnieuw moeten worden samengesteld.

1. Client Referenties bijwerken voor query's en gegevens import. Instanties van [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) moeten worden gewijzigd in [SearchClient](/dotnet/api/azure.search.documents.searchclient). Zorg ervoor dat u alle exemplaren onderschept voordat u verdergaat met de volgende stap om naam Verwar ring te voor komen.

1. Update client verwijzingen voor index, synoniemen kaart en analyse-objecten. Instanties van [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) moeten worden gewijzigd in [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Voor de rest van uw code werkt u klassen, methoden en eigenschappen bij om de Api's van de nieuwe bibliotheek te gebruiken. De sectie [naam verschillen](#naming-differences) is een plek waar u begint, maar u kunt ook het [wijzigings logboek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)controleren.

   Als u problemen ondervindt bij het vinden van gelijkwaardige Api's, wordt een probleem in een logboek vastgelegd, [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) zodat de documentatie kan worden verbeterd of het probleem kan worden onderzocht.

1. Bouw de oplossing opnieuw op. Na het oplossen van eventuele build-fouten of-waarschuwingen, kunt u aanvullende wijzigingen aanbrengen in uw toepassing om te profiteren van de [nieuwe functionaliteit](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

Gezien de veranderingen in bibliotheken en Api's, is een upgrade naar versie 11 niet-trivial en vormt het een belang rijke wijziging in de zin dat uw code niet langer compatibel is met versie 10 en eerder. Zie het [wijzigingslog bestand](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) voor voor een grondige beoordeling van de verschillen `Azure.Search.Documents` .

In termen van versie-updates van de service, waar de code wijzigingen in versie 11 zijn gerelateerd aan de bestaande functionaliteit (en niet alleen een refactorie van de Api's), vindt u de volgende gedrags wijzigingen:

+ [BM25-classificatie algoritme](index-ranking-similarity.md) vervangt het vorige classificatie algoritme met nieuwere technologie. Nieuwe services gebruiken dit algoritme automatisch. Voor bestaande services moet u para meters instellen voor het gebruik van het nieuwe algoritme.

+ [Geordende resultaten](search-query-odata-orderby.md) voor Null-waarden zijn gewijzigd in deze versie, waarbij Null-waarden eerst worden weer gegeven als de sorteer bewerking is `asc` en laatste als de sorteer bewerking is `desc` . Als u code hebt geschreven om te verwerken hoe Null-waarden worden gesorteerd, moet u deze code controleren en mogelijk verwijderen als deze niet langer nodig is.

## <a name="next-steps"></a>Volgende stappen

+ [ Uments-pakketAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Voorbeelden op GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API-naslag informatie](/dotnet/api/overview/azure/search.documents-readme)