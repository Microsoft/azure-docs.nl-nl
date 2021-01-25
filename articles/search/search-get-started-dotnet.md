---
title: 'Quickstart: Een zoekindex maken in .NET'
titleSuffix: Azure Cognitive Search
description: In deze C#-quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van de Azure.Search.Documents-clientbibliotheek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180095"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Quickstart: Een zoekindex maken met behulp van de Azure.Search.Documents-clientbibliotheek.

Gebruik de nieuwe [Azure.Search.Documents (versie 11)-clientbibliotheek](/dotnet/api/overview/azure/search.documents-readme) om een .NET Core-consoletoepassing te maken in C# waarmee een zoekindex kan worden gemaakt, geladen en opgevraagd.

U kunt [de broncode downloaden](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) om te beginnen met een voltooid project of de stappen in dit artikel volgen om uw eigen project te maken.

> [!NOTE]
> Zoekt u een eerdere versie? Zie [Een zoekindex maken met Microsoft. Azure.Search v10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u beschikken over de volgende hulpprogramma's en services:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt een gratis service voor deze quickstart gebruiken. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), een willekeurige editie. Voorbeeldcode is getest in de gratis Community-editie van Visual Studio 2019.

Bij het instellen van uw project downloadt u het [NuGet-pakket Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/).

Azure SDK voor .NET voldoet aan [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support), wat betekent dat .NET Framework 4.6.1 en .NET Core 2.0 de minimale vereisten zijn.

## <a name="set-up-your-project"></a>Uw project instellen

Voeg serviceverbindingsgegevens samen en start Visual Studio om een nieuw console-app-project te maken dat kan worden uitgevoerd op .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Een sleutel en eindpunt kopiëren

Voor aanroepen naar de service zijn voor elke aanvraag een URL-eindpunt en een toegangssleutel vereist. Als eerste stap zoekt u de API-sleutel en de URL die u aan uw project wilt toevoegen. U geeft beide waarden op bij het maken van de client in een latere stap.

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. In **Instellingen** > **Sleutels**, kunt u een beheerderssleutel ophalen voor volledige rechten op de service. Deze heeft u nodig voor het maken en verwijderen van objecten. Er zijn twee uitwisselbare primaire en secundaire sleutels. U kunt beide gebruiken.

   ![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-rest/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

### <a name="install-the-nuget-package"></a>Installeer het NuGet-pakket van

Nadat het project is gemaakt, voegt u de clientbibliotheek toe. Het [Azure.Search.Documents-pakket](https://www.nuget.org/packages/Azure.Search.Documents/) bestaat uit één clientbibliotheek die alle API's bevat die worden gebruikt om te werken met een zoekservice in .NET.

1. Start Visual Studio en maak een .NET Core-consoletoepassing.

1. Selecteer onder **Hulpprogramma's** > **NuGet-pakketbeheer** de optie **NuGet-pakketten voor oplossing beheren...** . 

1. Klik op **Bladeren**.

1. Zoek `Azure.Search.Documents` en selecteer versie 11.0 of hoger.

1. Klik op **Installeren** aan de rechterkant om de assembly toe te voegen aan uw project en oplossing.

### <a name="create-a-search-client"></a>Een zoekclient maken

1. Wijzig in **Program.cs** de naamruimte in `AzureSearch.SDK.Quickstart.v11` en voeg vervolgens de volgende `using`-instructies toe.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Twee clients maken: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) maakt de index en [SearchClient](/dotnet/api/azure.search.documents.searchclient) laadt een bestaande index en voert er een query op uit. Beide hebben het service-eindpunt en een beheerder-API-sleutel nodig voor verificatie van rechten voor maken/verwijderen.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Een index maken

In deze quickstart wordt een index van hotels gemaakt die u met hotelgegevens laadt en waarop u query's uitvoert. In deze stap definieert u de velden in de index. Elke definitie bevat een naam, gegevenstype en kenmerken die bepalen hoe het veld wordt gebruikt.

In dit voorbeeld worden synchrone methoden van de Azure.Search.Documents-bibliotheek gebruikt voor eenvoud en leesbaarheid. Voor productiescenario's moet u echter asynchrone methoden gebruiken om uw app op een schaalbare en responsieve manier te laten werken. U kunt bijvoorbeeld [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) gebruiken in plaats van [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Voeg een lege klassedefinitie toe aan uw project: **Hotel.cs**

1. Kopieer de volgende code in **Hotel.cs** om de structuur van een hoteldocument te definiëren. Door kenmerken in het veld wordt bepaald hoe het veld wordt gebruikt in een toepassing. Het `IsFilterable`-kenmerk moet bijvoorbeeld worden toegewezen aan elk veld dat een filterexpressie ondersteunt.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   In de clientbibliotheek van Azure.Search.Documents kunt u velddefinities stroomlijnen met behulp van de velden [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) en [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield). Beide zijn afgeleiden van een [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) en kunnen uw code vereenvoudigen:

   + `SimpleField` kan elk gegevenstype zijn, is altijd niet-doorzoekbaar (wordt genegeerd voor zoekopdrachten in volledige tekst) en kan worden opgehaald (is niet verborgen). Andere kenmerken zijn standaard uitgeschakeld, maar kunnen wel worden ingeschakeld. U kunt een `SimpleField` gebruiken voor document-id's of velden die alleen worden gebruikt in filters, facetten of scoreprofielen. Als dat het geval is, moet u ervoor zorgen dat u alle kenmerken toepast die nodig zijn voor het scenario, zoals `IsKey = true` voor een document-id. Zie [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) in broncode voor meer informatie.

   + `SearchableField` moet een tekenreeks zijn die altijd kan worden doorzocht en opgehaald. Andere kenmerken zijn standaard uitgeschakeld, maar kunnen wel worden ingeschakeld. Omdat dit veldtype kan worden doorzocht, worden synoniemen en het volledige gamma van analyse-eigenschappen ondersteund. Zie [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) in broncode voor meer informatie.

   Ongeacht of u de basis-API van `SearchField` of een van de hulpmodellen gebruikt, u moet filter-, facet- en sorteerkenmerken expliciet inschakelen. Bijvoorbeeld [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)en [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) moet expliciet worden voorzien van een kenmerk, zoals weergegeven in het bovenstaande voorbeeld. 

1. Voeg een tweede lege klassedefinitie toe aan uw project: **Address.cs**.  Kopieer de volgende code naar de klasse.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Maak nog twee klassen: **Hotel.Methods.cs** en **Address.Methods.cs** voor ToString ()-overschrijvingen. Deze klassen worden gebruikt voor het weergeven van zoekresultaten in de console-uitvoer.  De inhoud van deze klassen zijn niet in dit artikel opgenomen, maar u kunt de code kopiëren vanuit [bestanden in GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. Maak in **Program.cs** een [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)-object en roep vervolgens de methode [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) aan om de index uit te drukken in uw zoekservice. De index bevat ook een [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) om automatisch aanvullen in te schakelen voor de opgegeven velden.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Azure Cognitive Search doorzoekt inhoud die in de service is opgeslagen. In deze stap laadt u JSON-documenten die overeenkomen met de hotelindex die u zojuist hebt gemaakt.

In Azure Cognitive Search zijn zoekdocumenten gegevensstructuren die zowel de invoer van indexeringen als de uitvoer van query's zijn. Als u de documenten hebt verkregen via een externe gegevensbron, bestaat de documentinvoer mogelijk uit rijen in een database, blobs in Blob Storage of JSON-documenten op een schijf. In dit voorbeeld nemen we de korte route en gaan we JSON-documenten voor vier hotels in de code zelf insluiten. 

Bij het uploaden van documenten moet u een [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1)-object gebruiken. Een `IndexDocumentsBatch`-object bevat een verzameling [acties](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), die elk een document en een eigenschap bevat waarmee Azure Cognitive Search wordt aangestuurd om een actie uit te voeren ([uploaden, samenvoegen, verwijderen en mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. In **Program.cs** maakt u een matrix met documenten en indexacties en vervolgens geeft u de matrix door aan `IndexDocumentsBatch`. De documenten hieronder voldoen aan de index hotel-quickstart, zoals gedefinieerd door de hotelklasse.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Nadat u het [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1)-object hebt geïnitialiseerd, kunt u het naar de index sturen door [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) aan te roepen op uw [SearchClient](/dotnet/api/azure.search.documents.searchclient)-object.

1. Voeg de volgende regels toe aan Main(). Documenten worden geladen met SearchClient, maar voor de bewerking zijn ook beheerdersrechten op de service vereist, die meestal is gekoppeld aan SearchIndexClient. Een manier om deze bewerking in te stellen is het ophalen van SearchClient via SearchIndexClient (adminClient in dit voorbeeld).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Omdat dit een console-app is waarmee alle opdrachten opeenvolgend worden uitgevoerd, moet u een wachttijd van 2 seconden tussen indexeren en query's toevoegen.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    De vertraging van 2 seconden compenseert de indexering (die asynchroon is), zodat alle documenten kunnen worden geïndexeerd voordat de query's worden uitgevoerd. Codering in een vertraging is doorgaans alleen nodig in demo's, testen en voorbeeldtoepassingen.

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

U kunt queryresultaten ophalen zodra het eerste document wordt geïndexeerd, maar wacht met het daadwerkelijk testen van uw index totdat alle documenten zijn geïndexeerd.

In deze sectie worden twee functies toegevoegd: querylogica en resultaten. Gebruik voor query's de methode [Search](/dotnet/api/azure.search.documents.searchclient.search). Deze methode gebruikt zoektekst (de querytekenreeks) en andere [opties](/dotnet/api/azure.search.documents.searchoptions).

De [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)-klasse vertegenwoordigt de resultaten.

1. Maak in **Program.cs** een **WriteDocuments**-methode waarmee zoekresultaten op de console worden afgedrukt.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Maak een **RunQueries**-methode om query's uit te voeren en resultaten te retourneren. Resultaten zijn Hotel-objecten. In dit voorbeeld ziet u de handtekening en de eerste query van de methode. Deze query demonstreert de Select-parameter waarmee u het resultaat kunt samenstellen met behulp van geselecteerde velden uit het document.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. In de tweede query kunt u op een term zoeken, een filter toevoegen waarmee documenten worden geselecteerd met een hogere beoordeling dan 4 en de documenten vervolgens op beoordeling sorteren in aflopende volgorde. Een filter is een Booleaanse uitdrukking die wordt geëvalueerd over [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)-velden in een index. Filterquery's bevatten waarden of sluiten ze uit. Daarom is er geen relevantiescore gekoppeld aan een filterquery. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. De derde query demonstreert searchFields, die wordt gebruikt om een zoekbewerking in volledige tekst naar specifieke velden te bepalen.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. De vierde query demonstreert facetten, die kunnen worden gebruikt voor het structureren van een facetnavigatiestructuur. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. In de vijfde query retourneert u een specifiek document. Een zoekbewerking voor een document is een typisch antwoord op een OnClick-gebeurtenis in een resultatenset.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. De laatste query toont de syntaxis voor automatisch aanvullen, waarbij een gedeeltelijke gebruikersinvoer van 'sa' wordt gesimuleerd die wordt omgezet in twee mogelijke overeenkomsten in de sourceFields die zijn gekoppeld aan de suggester die u in de index hebt gedefinieerd.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Voeg **RunQueries** toe aan Main().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

De vorige query's tonen meerdere [manieren om termen in een query te koppelen](search-query-overview.md#types-of-queries): zoekopdracht in volledige tekst, filters en automatisch aanvullen.

Zoekopdrachten in volledige tekst en filters worden uitgevoerd met behulp van de methode [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search). Een zoekquery kan worden doorgegeven in de `searchText`-tekenreeks, terwijl een filterexpressie kan worden doorgegeven in de eigenschap [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) van de klasse [Search Options](/dotnet/api/azure.search.documents.searchoptions). Als u wilt filteren zonder te zoeken, geeft u `"*"` door voor de `searchText`-parameter van de [Zoekmethode](/dotnet/api/azure.search.documents.searchclient.search). Om te zoeken zonder te filteren, stelt u de eigenschap `Filter` niet in of geeft u niet door aan een `SearchOptions`-instantie.

## <a name="run-the-program"></a>Het programma uitvoeren

Druk op F5 om de app opnieuw te bouwen en het programma in zijn geheel uit te voeren. 

De uitvoer bevat berichten van [Console.WriteLine](/dotnet/api/system.console.writeline), met toevoeging van query-informatie en -resultaten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze C#-quickstart hebt u een reeks taken uitgevoerd om een index te maken, hier documenten in te laden en query's uit te voeren. In verschillende fasen hebben we korte routes genomen om de code te vereenvoudigen voor leesbaarheid en begrijpelijkheid. Als u voldoende bekend bent met de basisconcepten, is het raadzaam het volgende artikel te lezen om alternatieve methoden en concepten te verkennen om uw kennis uit te breiden. 

> [!div class="nextstepaction"]
> [Ontwikkelen in .NET](search-howto-dotnet-sdk.md)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
