---
title: Verouderde C#-Snelstartgids
titleSuffix: Azure Cognitive Search
description: Deze Snelstartgids maakt gebruik van de versie 10-client bibliotheek (micro soft. Azure. Search) om een zoek index te maken, te laden en op te vragen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8dc2eb898c12e374bc503c5a05f00eb20667443b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94701837"
---
# <a name="quickstart-create-a-search-index-using-the-legacy-microsoftazuresearch-v10-client-library"></a>Snelstartgids: een zoek index maken met behulp van de verouderde client bibliotheek van micro soft. Azure. Search V10 toevoegen

Dit artikel is de C#-Snelstartgids voor de verouderde client bibliotheek van [**micro soft. Azure. Search**](/dotnet/api/overview/azure/search/client10) (versie 10), die nu wordt vervangen door de uments-client bibliotheek (versie 11) van [**Azure.Search.Doc**](/dotnet/api/overview/azure/search.documents-readme) .

> [!NOTE]
> Als u bestaande of Inflight-ontwikkel projecten hebt, kunt u versie 10 blijven gebruiken. Voor nieuwe projecten of voor het gebruik van nieuwe functies, moet u echter overstappen naar de [nieuwe bibliotheek](/dotnet/api/overview/azure/search.documents-readme).

## <a name="about-this-quickstart"></a>Over deze Snelstartgids

Maak een .NET core-console toepassing in C# die een Azure Cognitive Search-index maakt, laadt en opvraagt met behulp van Visual Studio en de [micro soft. Azure. Search-client bibliotheken](/dotnet/api/overview/azure/search/client10). 

In dit artikel wordt uitgelegd hoe u de toepassing maakt. U kunt ook [de volledige toepassing downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10).

> [!NOTE]
> In de demo code in dit artikel wordt gebruikgemaakt van de synchrone methoden van de Azure Cognitive Search versie 10 .NET SDK voor eenvoud. Voor productiescenario's wordt u echter aangeraden de asynchrone methoden in uw eigen toepassingen te gebruiken, zodat ze schaalbaar en responsief blijven. U kunt bijvoorbeeld `CreateAsync` en `DeleteAsync` gebruiken in plaats van `Create` en `Delete`.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), een willekeurige editie. Voorbeeldcode en instructies zijn getest met de gratis Community-editie.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Voor aanroepen naar de service zijn voor elke aanvraag een URL-eindpunt en een toegangssleutel vereist. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-rest/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Open eerst Visual Studio en maak een nieuw Console App-project dat kan worden uitgevoerd op .NET Core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

Het [pakket micro soft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) bestaat uit een aantal client bibliotheken die worden gedistribueerd als NuGet-pakketten.

Voor dit project gebruikt u versie 10 van het `Microsoft.Azure.Search` NuGet-pakket en het meest recente `Microsoft.Extensions.Configuration.Json` NuGet-pakket.

1. Selecteer onder **Hulpprogramma's** > **NuGet-pakketbeheer** de optie **NuGet-pakketten voor oplossing beheren...** . 

1. Klik op **Bladeren**.

1. Zoek `Microsoft.Azure.Search` en selecteer versie 10.

1. Klik op **Installeren** aan de rechterkant om de assembly toe te voegen aan uw project en oplossing.

1. Herhaal deze stappen voor `Microsoft.Extensions.Configuration.Json`. Selecteer hier versie 2.2.0 of later.


### <a name="add-azure-cognitive-search-service-information"></a>Informatie over de Azure Cognitive Search-service toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op het project en selecteer **Toevoegen** > **Nieuw item...** . 

1. Zoek bij Nieuw item toevoegen naar 'JSON' om een JSON-lijst met itemtypen te retourneren.

1. Kies **JSON-bestand**, geef het bestand de naam 'appsettings.json' en klik op **Toevoegen**. 

1. Voeg het bestand toe aan uw uitvoermap. Klik met de rechtermuisknop op appsettings.json en selecteer **Eigenschappen**. Selecteer bij **Naar uitvoermap kopiëren** de optie **Kopiëren indien nieuwer**.

1. Kopieer de volgende JSON in uw nieuwe JSON-bestand. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Vervang de naam van de zoekservice (YOUR-SEARCH-SERVICE-NAME) en de beheer-API-sleutel (YOUR-ADMIN-API-KEY) door geldige waarden. Als uw service-eind punt is `https://mydemo.search.windows.net` , is de service naam " `mydemo` ".

### <a name="add-class-method-files-to-your-project"></a>'.Method'-klassebestanden toevoegen aan uw project

Deze stap is vereist om zinvolle uitvoer te maken in de console. Wanneer u de resultaten naar het consolevenster afdrukt, moeten afzonderlijke velden van het object Hotel worden geretourneerd als tekenreeksen. Met deze stap wordt [ToString()](/dotnet/api/system.object.tostring) geïmplementeerd om deze taak uit te voeren. U doet dit door de benodigde code naar twee nieuwe bestanden te kopiëren.

1. Voeg twee lege klassedefinities toe aan uw project: Address.Methods.cs, Hotel.Methods.cs

1. In Address.Methods.cs overschrijft u de standaardinhoud met de volgende code, [regels 1-25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25).

1. In Hotel.Methods.cs kopieert u [regels 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1 - Index maken

De Hotels-index bestaat uit eenvoudige en complexe velden. Een eenvoudig veld is bijvoorbeeld 'HotelName' of 'Description' en complexe velden bestaan uit een adres met subvelden, of een verzameling kamers. Wanneer een index complexe typen bevat, isoleert u de complexe velddefinities in gescheiden klassen.

1. Voeg twee lege klassedefinities toe aan uw project: Address.cs, Hotel.cs

1. In Address.cs overschrijft u de standaardinhoud met de volgende code:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. In Hotel.cs wordt met de klasse de algemene structuur van de index gedefinieerd, inclusief verwijzingen naar de adresklasse.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Door kenmerken in het veld wordt bepaald hoe het veld wordt gebruikt in een toepassing. Het kenmerk `IsSearchable` moet bijvoorbeeld worden toegewezen aan elk veld dat moet worden opgenomen in een zoekopdracht naar volledige tekst. 
    
    > [!NOTE]
    > In de .NET SDK moeten velden expliciet worden aangeduid als [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable), [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable), [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable) en [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable). Dit gedrag is het tegengestelde van de REST API, waarmee kenmerken op basis van gegevenstype impliciet worden ingeschakeld (eenvoudige tekenreeksvelden zijn bijvoorbeeld automatisch doorzoekbaar).

    In uw index van het type `string` moet exact één veld worden aangewezen als het *sleutelveld*; hierdoor wordt elk document uniek geïdentificeerd. In dit schema is de sleutel `HotelId`.

    In deze index wordt voor de beschrijvingsvelden het optionele kenmerk [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer) gebruikt. Dit wordt opgegeven wanneer u de Lucene-standaardanalyse wilt overschrijven. Voor het veld `description_fr` wordt de Franse Lucene-analyse ([FrLucene](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene)) gebruikt, omdat hierin Franse tekst wordt opgeslagen. Voor `description` wordt de optionele Microsoft-taalanalyse ([EnMicrosoft](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft)) gebruikt.

1. In Program.cs maakt u een exemplaar van de klasse [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient) om verbinding te maken met de service, met behulp van waarden die zijn opgeslagen in het configuratiebestand van de toepassing (appsettings.json). 

   `SearchServiceClient` beschikt over de eigenschap [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes). Deze eigenschap bevat alle methoden die u nodig hebt om een index voor Azure Cognitive Search te maken, weergeven, bijwerken of verwijderen. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Probeer, indien mogelijk, één exemplaar van `SearchServiceClient` in uw toepassing te delen om te voorkomen dat u te veel verbindingen opent. Klassemethoden zijn thread-safe om delen in te schakelen.

   De klasse heeft verschillende constructors. De gewenste constructor krijgt u de naam van uw zoekservice en een `SearchCredentials`-object als parameters. `SearchCredentials` verpakt uw api-sleutel.

    De eenvoudigste manier om in de indexdefinitie `Field`-objecten te maken is via het aanroepen van de methode `FieldBuilder.BuildForType`. Hierbij wordt een modelklasse doorgegeven voor de typeparameter. Een modelklasse heeft eigenschappen die worden toegewezen aan de velden van uw index. Met deze toewijzing kunt u documenten uit de zoekindex binden aan exemplaren van uw modelklasse.

    > [!NOTE]
    > Als u niet van plan bent om een modelklasse te gebruiken, moet u alsnog de index definiëren door rechtstreeks `Field`-objecten te maken. U kunt de naam van het veld aan de constructor doorgeven, samen met het gegevenstype (of analyse voor tekenreeksvelden). U kunt ook andere eigenschappen instellen, zoals `IsSearchable`, `IsFilterable`, enzovoort.
    >

1. Druk op F5 om de app te bouwen en de index te maken. 

    Als het project is gebouwd, wordt een consolevenster geopend en worden statusbericht naar het scherm geschreven voor het verwijderen en maken van de index. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

In Azure Cognitive Search zijn documenten gegevensstructuren die zowel de invoer van indexeringen als de uitvoer van query's zijn. Als u de documenten hebt verkregen via een externe gegevensbron, bestaat de documentinvoer mogelijk uit rijen in een database, blobs in Blob Storage of JSON-documenten op een schijf. In dit voorbeeld nemen we de korte route en gaan we JSON-documenten voor vier hotels in de code zelf insluiten. 

Wanneer u documenten uploadt, moet u een [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch)-object gebruiken. Een `IndexBatch` bevat een verzameling [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction)-objecten, die elk een document en een eigenschap bevat waarmee Azure Cognitive Search wordt aangestuurd om een actie uit te voeren ([uploaden, samenvoegen, verwijderen en mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. In Program.cs maakt u een matrix met documenten en indexacties en vervolgens geeft u de matrix door aan `IndexBatch`. De documenten hieronder voldoen aan de index hotel-quickstart, zoals gedefinieerd door de hotel- en adresklassen.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Zodra u het `IndexBatch`-object hebt geïnitialiseerd, kunt u het object naar de index sturen door [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) aan te roepen op uw [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient)-object. `Documents` is een eigenschap van `SearchIndexClient` die methoden biedt voor het toevoegen, aanpassen of verwijderen van documenten in uw index of voor het uitvoeren van query's op die documenten.

    De `try`/`catch` rondom de aanroep voor de `Index`-methode vangt indexfouten op. Dit kan zich voordoen bij een zware belasting van uw service. In productiecode kunt u indexering van documenten vertragen en vervolgens proberen de documenten die niet zijn geïndexeerd opnieuw te indexeren, maar u kunt ook een logboek maken en doorgaan zoals in het voorbeeld. U kunt de indexering ook op een andere manier verwerken die aan de vereisten omtrent de gegevensconsistentie van uw toepassing voldoet.

    De vertraging van 2 seconden compenseert de indexering (die asynchroon is), zodat alle documenten kunnen worden geïndexeerd voordat de query's worden uitgevoerd. Codering in een vertraging is doorgaans alleen nodig in demo's, testen en voorbeeldtoepassingen.

1. In Program.cs in de hoofdmap verwijdert u de opmerkingen in de regels voor '2 - Documenten laden'. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Druk op F5 om de app opnieuw te bouwen. 

    Als het project is gebouwd, wordt een consolevenster geopend en worden statusbericht geschreven, dit keer met een bericht over het uploaden van documenten. In Azure Portal, op de pagina **Overzicht** van de zoekservice, bevat de index hotels-quickstart nu als het goed is 4 documenten.

Zie [Verwerking van documenten door de .NET SDK](search-howto-dotnet-sdk-v10.md#how-dotnet-handles-documents) voor meer informatie over de verwerking van documenten.

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

U kunt queryresultaten ophalen zodra het eerste document wordt geïndexeerd, maar wacht met het daadwerkelijk testen van uw index totdat alle documenten zijn geïndexeerd. 

In deze sectie worden twee functies toegevoegd: querylogica en resultaten. Voor query's gebruikt u de methode [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search). Bij deze methode worden zowel zoektekst als andere [parameters](/dotnet/api/microsoft.azure.search.models.searchparameters) gebruikt. 

De klasse [`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) vertegenwoordigt de resultaten.


1. In Program.cs maakt u een WriteDocuments-methode waarmee zoekresultaten op de console worden afgedrukt.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Maak een RunQueries-methode om query's uit te voeren en resultaten te retourneren. Resultaten zijn Hotel-objecten. U kunt de select-parameter gebruiken om afzonderlijke velden te traceren. Als een veld niet is opgenomen in de select-parameter, wordt de bijbehorende Hotel-eigenschap null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Er zijn twee [manieren om termen in een query te vergelijken](search-query-overview.md#types-of-queries): zoeken op volledige tekst en filters. Een query naar volledige tekst zoekt naar een of meer termen in `IsSearchable`-velden in de index. Een filter is een Booleaanse uitdrukking die wordt geëvalueerd over `IsFilterable`-velden in een index. U kunt zoeken op volledige tekst en filters samen of afzonderlijk gebruiken.

    Zoekopdrachten en filters worden allebei uitgevoerd met behulp van de `Documents.Search`-methode. Een zoekopdracht kan worden doorgegeven aan de `searchText`-parameter, terwijl een filterexpressie kan worden doorgegeven in de eigenschap `Filter` van de `SearchParameters`-klasse. Als u wilt filteren zonder te zoeken, geeft u `"*"` door voor de `searchText`-parameter. Om te zoeken zonder te filteren, stelt u de eigenschap `Filter` niet in of geeft u niet door aan een `SearchParameters`-instantie.

1. In Program.cs in de hoofdmap verwijdert u de opmerkingen in de regels voor '3 - Zoeken'. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. De oplossing is nu voltooid. Druk op F5 om de app opnieuw te bouwen en het programma in zijn geheel uit te voeren. 

    De uitvoer bevat dezelfde berichten als voorheen, maar bevat nu ook querygegevens en resultaten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze C#-quickstart hebt u een reeks taken uitgevoerd om een index te maken, hier documenten in te laden en query's uit te voeren. In verschillende fasen hebben we korte routes genomen om de code te vereenvoudigen voor leesbaarheid en begrijpelijkheid. Als u voldoende bekend bent met de basisconcepten, is het raadzaam het volgende artikel te lezen om alternatieve methoden en concepten te verkennen om uw kennis uit te breiden. 

De voorbeeldcode en index zijn uitgebreide versies hiervan. In het volgende voorbeeld wordt de verzameling Kamers toegevoegd, worden verschillende klassen en acties gebruikt en gaan we dieper in op verwerking.

> [!div class="nextstepaction"]
> [Ontwikkelen in .NET](search-howto-dotnet-sdk.md)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)