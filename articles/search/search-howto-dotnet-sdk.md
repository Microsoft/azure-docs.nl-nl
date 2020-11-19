---
title: Azure.Search.Documents (V11) in .NET gebruiken
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken en beheren van Zoek objecten in een .NET-toepassing met behulp van C# en de V11-client bibliotheek (Azure.Search.Documents). Code fragmenten laten zien hoe u verbinding maakt met de service, indexen maakt en query's uitvoert.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3ceead297ea726e256d806c08c22810b39296793
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917168"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Azure.Search.Documents gebruiken in een C# .NET-toepassing

In dit artikel wordt uitgelegd hoe u Zoek objecten maakt en beheert met behulp van C# en de uments-client bibliotheek (versie 11) van [**Azure.Search.Doc**](/dotnet/api/overview/azure/search) .

## <a name="about-version-11"></a>Over versie 11

De Azure SDK voor .NET voegt een nieuwe [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) -client bibliotheek toe van het Azure SDK-team dat functioneel equivalent is aan [micro soft. Azure. Search](/dotnet/api/overview/azure/search/client10) client libraries, maar maakt gebruik van algemene benaderingen en conventies waar van toepassing. Enkele voor beelden zijn [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) sleutel verificatie en [System.Text.Jsop. Serialisatie](/dotnet/api/system.text.json.serialization) voor JSON-serialisatie.

Net als bij vorige versies kunt u deze bibliotheek gebruiken voor het volgende:

+ Zoek indexen, gegevens bronnen, Indexeer functies, vaardig heden en synoniemen maken en beheren
+ Zoek documenten laden en beheren in een index
+ Query's uitvoeren, allemaal zonder de details van HTTP en JSON te hoeven afhandelen

De bibliotheek wordt gedistribueerd als één [Azure.Search.DocUment NuGet-pakket](https://www.nuget.org/packages/Azure.Search.Documents/), dat alle api's bevat die worden gebruikt om toegang te krijgen tot een zoek service.

De client bibliotheek definieert klassen zoals `SearchIndex` , `SearchField` , en `SearchDocument` , evenals bewerkingen zoals `SearchIndexClient.CreateIndex` en `SearchClient.Search` op de `SearchIndexClient` `SearchClient` klassen and. Deze klassen zijn ingedeeld in de volgende naam ruimten:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (versie 11) streeft naar [ `2020-06-30` de versie van de Azure Cognitive Search rest API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

De client bibliotheek biedt geen [Service beheer bewerkingen](/rest/api/searchmanagement/), zoals het maken en schalen van zoek services en het beheren van API-sleutels. Als u uw Zoek resources vanuit een .NET-toepassing wilt beheren, gebruikt u de bibliotheek [micro soft. Azure. Management. Search](/dotnet/api/overview/azure/search/management) in de Azure SDK voor .net.

## <a name="upgrade-to-v11"></a>Upgrade uitvoeren naar V11

Als u de vorige versie van de .NET SDK hebt gebruikt en u een upgrade wilt uitvoeren naar de huidige algemeen beschik bare versie, raadpleegt u [upgrade naar Azure Cognitive Search .NET SDK versie 11](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>SDK-vereisten

+ Visual Studio 2019 of hoger.

+ Uw eigen Azure Cognitive Search-service. Als u de SDK wilt gebruiken, hebt u de naam van uw service en een of meer API-sleutels nodig. [Maak een service in de portal](search-create-service-portal.md) als u er nog geen hebt.

+ Down load het [Azure.Search.Documents-pakket](https://www.nuget.org/packages/Azure.Search.Documents) met **hulpprogram ma's**  >  **NuGet package manager**  >  **NuGet-pakketten beheren voor oplossing** in Visual Studio. Zoek de naam van het pakket `Azure.Search.Documents` .

Azure SDK voor .NET voldoet aan [.NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), wat betekent dat .NET Framework 4.6.1 en .net Core 2,0 als minimale vereisten.

## <a name="example-application"></a>Voorbeeld toepassing

In dit artikel leert u hoe u het [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) -code voorbeeld op github kunt gebruiken om basis concepten te illustreren in azure Cognitive Search-specifiek, hoe u een zoek index maakt, laadt en er query's op uitvoert.

Voor de rest van dit artikel wordt ervan uitgegaan dat er een nieuwe index met de naam "Hotels" is gevuld met een paar documenten, met verschillende query's die overeenkomen met de resultaten.

Hieronder ziet u het hoofd programma, waarin de algehele stroom wordt weer gegeven:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Hierna ziet u een deel scherm afbeelding van de uitvoer, ervan uitgaande dat u deze toepassing uitvoert met een geldige service naam en API-sleutels:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Console. WriteLine uitvoer van het voorbeeld programma":::

### <a name="client-types"></a>Client typen

In de client bibliotheek worden drie client typen gebruikt voor verschillende bewerkingen: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) om indexen te maken, bij te werken of te verwijderen, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) om een index te laden of op te vragen, en [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) om te werken met Indexeer functies en vaardig heden. Dit artikel is gericht op de eerste twee. 

Alle clients hebben ten minste de service naam of het eind punt nodig en een API-sleutel. Het is gebruikelijk om deze informatie op te geven in een configuratie bestand, vergelijkbaar met wat u vindt in het `appsettings.json` bestand van de [voorbeeld toepassing DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Als u wilt lezen uit het configuratie bestand, voegt u dit toe `using Microsoft.Extensions.Configuration;` aan uw programma.

Met de volgende instructie maakt u de index-client die wordt gebruikt om indexen te maken, bij te werken of te verwijderen. Er worden een zoek eindpunt en een beheer-API-sleutel gebruikt.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

Met de volgende instructie maakt u de Search-client die wordt gebruikt om documenten te laden of query's uit te voeren. `SearchClient` vereist een index. U hebt een API-sleutel van de beheerder nodig voor het laden van documenten, maar u kunt een query-API-sleutel gebruiken om query's uit te voeren. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Als u een ongeldige sleutel voor de import bewerking opgeeft (bijvoorbeeld een query sleutel waarin een beheerders sleutel is vereist), `SearchClient` wordt het `CloudException` fout bericht ' verboden ' weer gegeven wanneer u de eerste keer een bewerkings methode aanroept. Als dit het geval is, controleert u de API-sleutel.
>

### <a name="deleting-the-index"></a>De index verwijderen

In de vroege stadia van de ontwikkeling wilt u mogelijk een instructie toevoegen [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) om een werk-in-uitvoering-index te verwijderen, zodat u deze opnieuw kunt maken met een bijgewerkte definitie. De voorbeeld code voor Azure Cognitive Search bevat vaak een verwijderings stap, zodat u het voor beeld opnieuw kunt uitvoeren.

De volgende regel wordt aangeroepen `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Deze methode gebruikt de opgegeven `SearchIndexClient` om te controleren of de index bestaat, en als dit het geval is, verwijdert u deze:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> In de voorbeeld code in dit artikel wordt gebruikgemaakt van de synchrone methoden voor eenvoud, maar u moet de asynchrone methoden in uw eigen toepassingen gebruiken om ze schaalbaar en responsief te houden. U kunt bijvoorbeeld in de bovenstaande methode gebruiken in [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) plaats van [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Een index maken

U kunt gebruiken [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) om een index te maken. 

Met de onderstaande methode wordt een nieuw [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) object gemaakt met een lijst met [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) objecten die het schema van de nieuwe index definiëren. Elk veld heeft een naam, gegevens type en verschillende kenmerken waarmee het zoek gedrag wordt gedefinieerd. 

Velden kunnen worden gedefinieerd vanuit een model klasse met behulp van [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . De `FieldBuilder` klasse gebruikt reflectie om een lijst met `SearchField` objecten voor de index te maken door de open bare eigenschappen en kenmerken van de gegeven `Hotel` model klasse te controleren. We gaan de `Hotel` cursus later nader bekijken.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Naast velden kunt u ook Score profielen, Voorst Ellen of CORS-opties toevoegen aan de index (deze para meters worden wegge laten uit het voor beeld voor de boog). U kunt meer informatie vinden over het SearchIndex-object en de onderdelen daarvan in de [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) lijst met eigenschappen, evenals in de [rest API verwijzing](/rest/api/searchservice/).

> [!NOTE]
> U kunt altijd de lijst met `Field` objecten direct maken in plaats van te gebruiken `FieldBuilder` als dat nodig is. Het is bijvoorbeeld mogelijk dat u een model klasse niet wilt gebruiken of dat u een bestaande model klasse moet gebruiken die u niet wilt wijzigen door kenmerken toe te voegen.
>

### <a name="call-createindex-in-main"></a>CreateIndex aanroepen in Main ()

`Main` Hiermee maakt u een nieuwe index "Hotels" door de bovenstaande methode aan te roepen:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Een model klasse gebruiken voor het weer geven van gegevens

Het DotNetHowTo-voor beeld maakt gebruik van model klassen voor de gegevens structuren [Hotel](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [adres](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)en [room](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) . `Hotel` verwijst naar `Address` een complex type op Enkelvoudig niveau (een veld met meerdere delen) en `Room` (een verzameling velden met meerdere delen).

U kunt deze typen gebruiken om de index te maken en te laden, en om de reactie van een query te structureren:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Een alternatieve methode is het rechtstreeks toevoegen van velden aan een index. In het volgende voor beeld worden slechts enkele velden weer gegeven.

   ```csharp
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };
   ```

### <a name="field-definitions"></a>Veld definities

Uw gegevens model in .NET en het bijbehorende index schema moeten ondersteuning bieden voor de zoek ervaring die u aan uw eind gebruiker wilt geven. Elk object op het hoogste niveau in .NET, zoals een zoek document in een zoek index, komt overeen met een Zoek resultaat dat u in uw gebruikers interface zou weer geven. U kunt bijvoorbeeld in een toepassing voor het zoeken in een hotel zoeken op de naam van het Hotel, de functies van het hotel of de kenmerken van een bepaalde kamer. 

Binnen elke klasse wordt een veld gedefinieerd met een gegevens type en kenmerken die bepalen hoe het wordt gebruikt. De naam van elke open bare eigenschap in elke klasse wordt toegewezen aan een veld met dezelfde naam in de index definitie. 

Bekijk het volgende code fragment waarin verschillende veld definities worden opgehaald uit de klasse hotel. U ziet dat adres en ruimten C#-typen zijn met hun eigen klassedefinities (Raadpleeg de voorbeeld code als u deze wilt weer geven). Beide typen zijn complex. Zie voor meer informatie [complex typen model leren](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Een veld klasse kiezen

Bij het definiëren van velden kunt u de basis [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) klasse gebruiken, of u kunt afgeleide hulp modellen gebruiken die als "Sjablonen" fungeren, met vooraf geconfigureerde eigenschappen.

Precies één veld in de index moet als document sleutel () dienen `IsKey = true` . Dit moet een teken reeks zijn en het moet een unieke identificatie vormen voor elk document. Het is ook vereist `IsHidden = true` , wat betekent dat het niet kan worden weer gegeven in Zoek resultaten.

| Veldtype | Beschrijving en gebruik |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Basis klasse, waarbij de meeste eigenschappen zijn ingesteld op NULL, met uitzonde ring van `Name` wat vereist is, en de standaard- `AnalyzerName` lucene. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Hulp model. Dit kan elk gegevens type zijn. Dit kan altijd niet doorzoekbaar zijn (wordt genegeerd voor Zoek opdrachten in volledige tekst) en kan worden opgehaald (niet verborgen). Andere kenmerken zijn standaard uitgeschakeld, maar kunnen wel worden ingeschakeld. U kunt een `SimpleField` gebruiken voor document-id's of velden die alleen worden gebruikt in filters, facetten of scoreprofielen. Als dat het geval is, moet u ervoor zorgen dat u alle kenmerken toepast die nodig zijn voor het scenario, zoals `IsKey = true` voor een document-id. Zie [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) in broncode voor meer informatie. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Hulp model. Moet een teken reeks zijn die altijd kan worden doorzocht en opgehaald. Andere kenmerken zijn standaard uitgeschakeld, maar kunnen wel worden ingeschakeld. Omdat dit veldtype kan worden doorzocht, worden synoniemen en het volledige gamma van analyse-eigenschappen ondersteund. Zie [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) in broncode voor meer informatie. |

Ongeacht of u de basis-API van `SearchField` of een van de hulpmodellen gebruikt, u moet filter-, facet- en sorteerkenmerken expliciet inschakelen. Bijvoorbeeld [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)en [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) moet expliciet worden voorzien van een kenmerk, zoals weergegeven in het bovenstaande voorbeeld.

#### <a name="adding-field-attributes"></a>Veld kenmerken toevoegen

U ziet hoe elk veld wordt gedecoreerd met kenmerken als `IsFilterable` ,, `IsSortable` `IsKey` en `AnalyzerName` . Deze kenmerken worden rechtstreeks aan de [corresponderende veld kenmerken in een Azure Cognitive search-index](/rest/api/searchservice/create-index)toegewezen. De `FieldBuilder` klasse gebruikt deze eigenschappen om veld definities voor de index te maken.

#### <a name="field-type-mapping"></a>Toewijzing van veld type

De .NET-typen van de eigenschappen worden toegewezen aan de equivalente veld typen in de index definitie. De tekenreekseigenschap `Category` is bijvoorbeeld toegewezen aan het veld `category` van type `Edm.String`. Er zijn Vergelijk bare type toewijzingen tussen `bool?` , `Edm.Boolean` , `DateTimeOffset?` en, `Edm.DateTimeOffset` enzovoort. 

Hebt u de eigenschap genoteerd `SmokingAllowed` ?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Het `JsonIgnore` kenmerk voor deze eigenschap geeft `FieldBuilder` aan dat het niet wordt geserialiseerd in de index als een veld.  Dit is een uitstekende manier om berekende eigenschappen aan client zijde te maken die u als helpers in uw toepassing kunt gebruiken.  In dit geval geeft de `SmokingAllowed` eigenschap aan of er `Room` in de `Rooms` verzameling roken is toegestaan. Als alles onwaar is, geeft het aan dat het hele hotel geen roken toestaat.

## <a name="load-an-index"></a>Een index laden

De volgende stap in `Main` vult de zojuist gemaakte "Hotels"-index. Deze index populatie wordt uitgevoerd in de volgende methode: (sommige code is vervangen door '... ' voor illustratie doeleinden. Bekijk de volledige voorbeeld oplossing voor de volledige gegevens populatie code.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Deze methode heeft vier delen. Met de eerste maakt u een matrix van drie `Hotel` objecten met drie `Room` objecten die fungeren als de invoer gegevens die moeten worden geüpload naar de index. Deze gegevens worden in code vastgelegd voor eenvoud. In een daad werkelijke toepassing worden gegevens waarschijnlijk opgehaald uit een externe gegevens bron, zoals een SQL database.

Met het tweede deel wordt een [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) document gemaakt dat de documenten bevat. U geeft de bewerking op die u wilt Toep assen op de batch op het moment dat u deze maakt, in dit geval door aan te roepen [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . De batch wordt vervolgens geüpload naar de Azure Cognitive Search-index op basis van de [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) methode.

> [!NOTE]
> In dit voor beeld worden alleen documenten geüpload. Als u wijzigingen wilt samen voegen in bestaande documenten of documenten wilt verwijderen, kunt u batches maken door `IndexDocumentsAction.Merge` in te roepen, `IndexDocumentsAction.MergeOrUpload` of `IndexDocumentsAction.Delete` in plaats daarvan. U kunt ook verschillende bewerkingen in één batch combi neren door `IndexBatch.New` aan te roepen, waarmee een verzameling `IndexDocumentsAction` objecten wordt opgehaald, waarmee wordt aangegeven dat er in azure Cognitive Search een bepaalde bewerking moet worden uitgevoerd op een document. U kunt elk `IndexDocumentsAction` met een eigen bewerking maken door de overeenkomstige methode `IndexDocumentsAction.Merge` , zoals, `IndexAction.Upload` ,, enzovoort aan te roepen.
> 

Het derde deel van deze methode is een catch-blok dat een belang rijke fout bij het indexeren afhandelt. Als de zoek service sommige documenten in de batch niet kan indexeren, wordt een fout `IndexBatchException` gegenereerd door `IndexDocuments` . Deze uitzonde ring kan optreden als u documenten indexeert terwijl uw service zwaar wordt belast. **Wij raden u aan deze aanvraag expliciet in uw code te behandelen.** U kunt de indexering van documenten die niet zijn geïndexeerd vertragen en vervolgens opnieuw uitvoeren, maar u kunt ook een logboek maken en doorgaan zoals in het voorbeeld. U kunt ook een andere bewerking uitvoeren, afhankelijk van de vereisten omtrent de gegevensconsistentie van de toepassing.

Ten slotte wordt de `UploadDocuments` methode twee seconden traag. Het indexeren vindt asynchroon plaats in uw zoek service. de voorbeeld toepassing moet daarom een korte tijd wachten om ervoor te zorgen dat de documenten kunnen worden doorzocht. Dergelijke vertragingen zijn doorgaans alleen nodig is demo’s, testen en voorbeeldtoepassingen.

### <a name="call-uploaddocuments-in-main"></a>UploadDocuments aanroepen in Main ()

Het volgende code fragment stelt een exemplaar van [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) het gebruik [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) van de methode van indexClient. De indexClient maakt gebruik van een API-sleutel van de beheerder voor de aanvragen die zijn vereist voor het laden of vernieuwen van documenten.

Een alternatieve methode is het rechtstreeks aanroepen `SearchClient` van de door gegeven in een beheer-API-sleutel op `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Query's uitvoeren

Stel eerst een waarde in `SearchClient` die het zoek eindpunt en de query-API-sleutel leest uit **appsettings.jsop**:

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Definieer ten tweede een methode waarmee een query aanvraag wordt verzonden. 

Telkens wanneer met de methode een query wordt uitgevoerd, wordt er een nieuw [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) object gemaakt. Dit object wordt gebruikt om aanvullende opties voor de query op te geven, zoals sorteren, filteren, paginering en facetten. In deze methode stellen we de `Filter` `Select` eigenschap, en in `OrderBy` voor verschillende query's. Voor meer informatie over de syntaxis van de zoek query-expressie, [eenvoudige query syntaxis](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

De volgende stap is om de zoek query werkelijk uit te voeren. Het uitvoeren van de zoek opdracht wordt uitgevoerd met behulp van de- `SearchClient.Search` methode. Geef voor elke query de Zoek tekst door om te gebruiken als een teken reeks (of `"*"` als er geen Zoek tekst is), plus de zoek opties die u eerder hebt gemaakt. We geven ook `Hotel` de type parameter voor `SearchClient.Search` , waarmee de SDK de documenten in de zoek resultaten in objecten van het type deserialiseren `Hotel` .

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Een derde, definieert u een methode die het antwoord schrijft, waarbij u elk document afdrukt naar de-console:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>RunQueries aanroepen in Main ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Query constructies verkennen

Laten we eens kijken naar elk van de query's. Hier volgt de code voor het uitvoeren van de eerste query:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

In dit geval doorzoeken we de volledige index voor het woord "Motel" in elk Doorzoek bare veld en willen we alleen de namen van hotels ophalen, zoals opgegeven door de `Select` optie. Dit zijn de resultaten:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Gebruik in de tweede query een filter om een ruimte te selecteren met een nacht frequentie van minder dan $100. Alleen de Hotel-ID en beschrijving retour neren in de resultaten:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

De bovenstaande query maakt gebruik van een OData `$filter` -expressie, `Rooms/any(r: r/BaseRate lt 100)` waarmee de documenten in de index worden gefilterd. Dit maakt gebruik van de [operator alle](./search-query-odata-collection-operators.md) om de ' BaseRate lt 100 ' toe te passen op elk item in de verzameling ruimtes. Zie [OData-filter syntaxis](./query-odata-filter-orderby-syntax.md)voor meer informatie.

Zoek in de derde query de bovenste twee hotels die het meest recent zijn renovated en geef de naam van het hotel en de datum van de laatste renovatie weer. Dit is de code: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

Zoek in de laatste query alle hotels namen die overeenkomen met het woord ' Hotel ':

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

In deze sectie wordt deze inleiding tot de .NET SDK afgesloten, maar niet meer. In de volgende sectie vindt u aanvullende bronnen voor meer informatie over Program meren met Azure Cognitive Search.

## <a name="next-steps"></a>Volgende stappen

+ Blader door de API-referentie documentatie voor [Azure.Search.Documents](/dotnet/api/azure.search.documents) en [rest API](/rest/api/searchservice/)

+ Blader door andere code voorbeelden op basis van Azure.Search.Documents in [Azure-Search-DotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) en [Search-Getting-Started-DotNet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Bekijk [naam conventies](/rest/api/searchservice/Naming-rules) voor meer informatie over de regels voor het benoemen van verschillende objecten

+ [Ondersteunde gegevens typen](/rest/api/searchservice/Supported-data-types) controleren