---
title: C#-zelfstudie voor het indexeren van meerdere Azure-gegevensbronnen
titleSuffix: Azure Cognitive Search
description: Meer informatie over het importeren van gegevens uit meerdere gegevensbronnen in één Azure Cognitive Search-index met behulp van indexeerfuncties. In deze zelfstudie en in de voorbeeldcode wordt gebruikgemaakt van C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c9d9c43ae1be755ccb30fc377692257a81332ea8
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593719"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Zelfstudie: Indexeren vanuit meerdere gegevensbronnen met behulp van de .NET-SDK

Met Azure Cognitive Search kunt u gegevens uit meerdere gegevensbronnen importeren, analyseren en indexeren in één geconsolideerde zoekindex. 

In deze zelfstudie worden C# en de clientbibliotheek [Azure.Search.Documents](/dotnet/api/overview/azure/search) gebruikt in de Azure-SDK voor .NET om voorbeeldgegevens voor hotels uit een Azure Cosmos DB te indexeren, en deze samen te voegen met de details over hotelkamers die uit Azure Blob Storage-documenten zijn gehaald. Het resultaat is een gecombineerde zoekindex voor hotels met hoteldocumenten, met kamers als een complex gegevenstype.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Voorbeeldgegevens uploaden en gegevensbronnen maken
> * De documentsleutel identificeren
> * De index definiëren en maken
> * Hotelgegevens uit Azure Cosmos DB indexeren
> * Hotelkamergegevens uit blobopslag samenvoegen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt de nieuwe clientbibliotheek [Azure.Search.Documents](/dotnet/api/overview/azure/search) gebruikt, versie 11.x, om meerdere indexeerfuncties te maken en uit te voeren. In deze zelfstudie stelt u twee Azure-gegevensbronnen in, zodat u een Indexeerfunctie kunt configureren waarmee uit beide bronnen informatie wordt opgehaald om één zoekindex te vullen. De twee gegevenssets moeten een gemeenschappelijke waarde hebben om de samenvoeging te ondersteunen. In dit voorbeeld is dit veld een id. Zolang er een gemeenschappelijk veld is om de toewijzing te ondersteunen, kunnen met een indexeerfunctie gegevens uit verschillende resources worden samengevoegd: gestructureerde gegevens uit Azure SQL, niet-gestructureerde gegevens uit Blob-opslag, of een willekeurige combinatie van [ondersteunde gegevensbronnen](search-indexer-overview.md#supported-data-sources) in Azure.

Een voltooide versie van de code in deze zelfstudie vindt u in het volgende project:

* [multiple-data-sources/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket Azure.Search.Documents (versie 11). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) in GitHub voor een eerdere versie van de .NET SDK.

## <a name="prerequisites"></a>Vereisten

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [NuGet-pakket voor Azure Cognitive Search (versie 11.x)](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="1---create-services"></a>1- Services maken

In deze zelfstudie wordt gebruikgemaakt van Azure Cognitive Search voor indexering en query's, Azure Cosmos DB voor één gegevensset en Azure-blobopslag voor de tweede gegevensset. 

Maak, indien mogelijk, alle services in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kunnen uw services zich in elke regio bevinden.

In dit voorbeeld worden twee kleine gegevenssets gebruikt, waarin zeven fictieve hotels worden beschreven. In één set worden de hotels zelf beschreven, en wordt in een Azure Cosmos DB-database geladen. De andere set bevat details over de hotelkamers en is beschikbaar als zeven afzonderlijke JSON-bestanden die moeten worden geüpload naar de Azure-blobopslag.

### <a name="start-with-cosmos-db"></a>Aan de slag met Cosmos DB

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en ga naar uw Azure Cosmos DB-account. Open vervolgens de pagina Overzicht.

1. Selecteer **Data Explorer** en selecteer **Nieuwe database**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Een nieuwe database maken" border="false":::

1. Voer de naam **hotel-rooms-db** in. Accepteer de standaardwaarden voor de overige instellingen.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Database configureren" border="false":::

1. Een nieuwe container maken. Gebruik de bestaande database die u zojuist hebt gemaakt. Voer **hotels** in als containernaam en gebruik **/HotelId** als partitiesleutel.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Container toevoegen" border="false":::

1. Selecteer **Items** onder **Hotels** en klik vervolgens op **Item uploaden** op de opdrachtbalk. Ga naar en selecteer het bestand **cosmosdb/HotelsDataSubset_CosmosDb.json** in de projectmap.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Uploaden naar een Azure Cosmos DB-verzameling" border="false":::

1. Gebruik de knop Vernieuwen om de weergave van de items in de verzameling hotels te vernieuwen. Er worden zeven nieuwe databasedocumenten weergegeven.

1. Kopieer een verbindingsreeks op de pagina **Sleutels** in Kladblok. U hebt dit nodig voor **appsettings.json** in een latere stap. Als u de voorgestelde databasenaam ‘hotel-rooms-db’ niet hebt gebruikt, kopieert u ook de databasenaam.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Meld u aan bij [Azure Portal](https://portal.azure.com), ga naar uw Azure-opslagaccount, klik op **Blobs** en op **+ Container**.

1. [Maak een blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md) met de naam **hotel-rooms** om de JSON-voorbeeldbestanden voor hotelkamers op te slaan. U kunt het niveau voor openbare toegang instellen op een van de geldige waarden.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Een blobcontainer maken" border="false":::

1. Nadat de container is gemaakt, opent u deze en selecteert u **Uploaden** op de opdrachtbalk. Navigeer naar de map met de voorbeeldbestanden. Selecteer deze allemaal en klik vervolgens op **Uploaden**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Bestanden uploaden" border="false":::

1. Kopieer de opslagaccountnaam en een verbindingsreeks op de pagina **Toegangssleutels** in Kladblok. U hebt in een latere stap beide waarden nodig voor **appsettings.json**.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search kopiëren

Als u uw zoekservice wilt verifiëren, hebt u de service-URL en een toegangssleutel nodig.

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="De naam van de service en de querysleutels voor beheer ophalen" border="false":::

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2 - De omgeving instellen

1. Start Visual Studio, en selecteer in het menu **Extra** de optie **NuGet Package Manager** en **NuGet-pakketten voor oplossing beheren...** . 

1. Ga naar het tabblad **Bladeren**. Zoek en installeer vervolgens **Azure.Search.Documents** (versie 11.0 of hoger). U moet door een paar extra dialoogvensters klikken om de installatie te voltooien.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="NuGet gebruiken om Azure-bibliotheken toe te voegen" border="false":::

1. Ga naar de NuGet-pakketten **Microsoft.Extensions.Configuration** en **Microsoft.Extensions.Configuration.Json** en installeer ze.

1. Open het oplossingsbestand **/v11/AzureSearchMultipleDataSources.sln**.

1. Bewerk het bestand **appsettings.json** in Solution Explorer om verbindingsgegevens op te geven.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

De eerste twee vermeldingen zijn de URL en beheersleutels van een zoekservice. Gebruik het volledige eindpunt, bijvoorbeeld: `https://mydemo.search.windows.net`.

In de volgende vermeldingen worden de accountnamen en de verbindingsreeksgegevens van de Azure-blobopslag- en Azure Cosmos DB-gegevensbronnen opgegeven.

## <a name="3---map-key-fields"></a>3 - Sleutelvelden toewijzen

Voor het samenvoegen van inhoud moeten beide gegevensstromen zijn gericht op dezelfde documenten in de zoekindex. 

In Azure Cognitive Search bevat het sleutelveld een unieke id van elk document. Elke zoekindex moet precies één sleutelveld van het type `Edm.String` bevatten. Het sleutelveld moet aanwezig zijn voor elk document in een gegevensbron dat wordt toegevoegd aan de index. (Dit is zelfs het enige vereiste veld.)

Wanneer u gegevens van meerdere gegevensbronnen indexeert, zorgt u ervoor dat elke binnenkomende rij of elk binnenkomend document een algemene documentsleutel bevat om gegevens van twee fysiek afzonderlijke brondocumenten samen te voegen in een nieuw zoekdocument in de gecombineerde index. 

Vaak moet er op voorhand goed worden gepland om een betekenisvolle documentsleutel voor uw index te verzinnen en ervoor te zorgen dat deze aanwezig is in beide gegevensbronnen. In deze demo is de sleutel `HotelId` van elk hotel in Cosmos DB ook aanwezig in de JSON-kamerblobs in de blobopslag.

De Azure Cognitive Search-indexeerfuncties kunnen veldtoewijzingen gebruiken om gegevensvelden een nieuwe naam te geven en zelfs opnieuw in te delen tijdens het indexeren, zodat de brongegevens naar het juiste indexveld kunnen worden geleid. In Cosmos DB heet de hotel-id bijvoorbeeld **`HotelId`** . In de JSON-blobbestanden voor de hotelkamers is de hotel-id echter **`Id`** . Dit wordt door het programma verwerkt door het veld **`Id`** van de blobs toe te wijzen aan het sleutelveld **`HotelId`** in de indexeerfunctie.

> [!NOTE]
> In de meeste gevallen zijn automatisch gegenereerde documentsleutels, zoals die standaard worden gemaakt door bepaalde indexeerfuncties, geen goede documentsleutels voor gecombineerde indexen. Over het algemeen moet u een betekenisvolle, unieke sleutelwaarde gebruiken die al bestaat in, of eenvoudig kan worden toegevoegd aan, uw gegevensbronnen.

## <a name="4---explore-the-code"></a>4 - De code verkennen

Zodra de gegevens en configuratie-instellingen kloppen, kan het voorbeeldprogramma in **/v11/AzureSearchMultipleDataSources.sln** worden gebouwd en uitgevoerd.

Deze eenvoudige C#-/.NET-console-app voert de volgende taken uit:

* Maakt een nieuwe index op basis van de gegevensstructuur van de C#-hotelklasse (die ook verwijst naar de adres- en kamerklassen).
* Maakt een nieuwe gegevensbron en een indexeerfunctie om Azure Cosmos DB-gegevens toe te wijzen aan indexvelden. Dit zijn beide objecten in Azure Cognitive Search.
* Voert de indexeerfunctie uit zodat hotelgegevens vanuit Cosmos DB worden geladen.
* Maakt een tweede gegevensbron en een indexeerfunctie om JSON-blobgegevens toe te wijzen aan indexvelden.
* Voert de tweede indexeerfunctie uit om kamergegevens uit de blobopslag te laden.

 Neem, voordat u het programma gaat uitvoeren, even een minuut de tijd om de code en de index- en indexeerfunctiedefinities voor dit voorbeeld te bestuderen. De relevante code staat in twee bestanden:

  + **Hotel.cs** bevat het schema dat de index definieert
  + **Program.cs** bevat functies voor het maken van de Azure Cognitive Search-index, -gegevensbronnen en -indexeringen en laadt de gecombineerde resultaten in de index.

### <a name="create-an-index"></a>Een index maken

In dit voorbeeldprogramma wordt de [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) gebruikt om een Azure Cognitive Search-index te definiëren en te maken. Er wordt gebruikgemaakt van de [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder)-klasse om een indexstructuur te genereren op basis van een C#-gegevensmodelklasse.

Het gegevensmodel wordt gedefinieerd door de hotelklasse, die ook verwijzingen bevat naar de adres- en kamerklassen. Met FieldBuilder wordt ingezoomd op meerdere klassedefinities om een complexe gegevensstructuur te genereren voor de index. Tags voor metagegevens worden gebruikt voor het definiëren van de kenmerken van elk veld. Er kan bijvoorbeeld worden aangegeven of een veld doorzoekbaar of sorteerbaar is.

Met het programma worden alle bestaande indexen met dezelfde naam verwijderd, voordat het nieuwe wordt gemaakt (mocht u dit voorbeeld meer dan één keer willen uitvoeren).

In de volgende fragmenten uit het bestand **Hotel.cs** worden enkele velden weergegeven, gevolgd door een verwijzing naar een ander gegevensmodelklasse, Kamer[], die weer wordt gedefinieerd in het bestand **Room.cs** (niet weergegeven).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

In het bestand **Program.cs** wordt een [Zoekindex](/dotnet/api/azure.search.documents.indexes.models.searchindex) gedefinieerd met een naam en een veldverzameling, gegenereerd via de methode `FieldBuilder.Build`. De index wordt hierna als volgt gemaakt:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Een Azure Cosmos DB-gegevensbron en -indexeerfunctie maken

Het hoofdprogramma bevat logica om de Azure Cosmos DB-gegevensbron te maken voor de hotelgegevens.

Eerst wordt de naam van de Azure Cosmos DB-database samengevoegd met de verbindingsreeks. Vervolgens wordt een object [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) gedefinieerd.

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Wanneer de gegevensbron is gemaakt, stelt het programma een Azure Cosmos DB-indexeerfunctie in met de naam **hotel-rooms-cosmos-indexer**.

Met het programma worden eventuele bestaande indexeerfuncties met dezelfde naam bijgewerkt, waarbij de bestaande indexeerfunctie wordt overschreven door de inhoud van de bovenstaande code. Het omvat ook het opnieuw instellen en uitvoeren van acties, voor het geval u dit voorbeeld meer dan één keer wilt uitvoeren.

In het volgende voorbeeld wordt een schema voor de indexeerfunctie gedefinieerd, zodat deze één keer per dag wordt uitgevoerd. U kunt de planningseigenschap uit deze aanroep verwijderen als u niet wilt dat de indexeerfunctie in de toekomst automatisch opnieuw wordt uitgevoerd.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Dit voorbeeld bevat een eenvoudig try-catch-blok om eventuele fouten te rapporteren die tijdens de uitvoering kunnen optreden.

Nadat de Azure Cosmos DB-indexeerfunctie is uitgevoerd, bevat de zoekindex een volledige reeks voorbeelden van hoteldocumenten. Het veld Kamers van elk hotel is echter een lege matrix, omdat de gegevensbron van Azure Cosmos DB geen kamergegevens bevat. Vervolgens haalt het programma gegevens op uit de blobopslag om de kamergegevens te laden en samen te voegen.

### <a name="create-blob-storage-data-source-and-indexer"></a>Gegevensbron op basis van blobopslag en de indexeerfunctie maken

Voor het ophalen van de kamergegevens stelt het programma eerst een gegevensbron voor de blobopslag in, zodat wordt verwezen naar een set afzonderlijke JSON-blob-bestanden.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Wanneer de gegevensbron is gemaakt, stelt het programma een blob-indexeerfunctie in met de naam **hotel-rooms-blob-indexer**, zoals hieronder weergegeven.

De JSON-blobs bevatten een sleutelveld met de naam **`Id`** in plaats van **`HotelId`** . In de code wordt de klasse `FieldMapping` gebruikt om de indexeerfunctie te laten weten dat de veldwaarde **`Id`** moet verwijzen naar de documentsleutel **`HotelId`** in de index.

Indexeerfuncties voor Blob-opslag kunnen [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) gebruiken om een parseermodus op te geven. U moet een andere parseermodus instellen afhankelijk van of blobs een enkel document vertegenwoordigen, of meerdere documenten binnen dezelfde blob. In dit voorbeeld vertegenwoordigt elke blob één JSON-document. In de code wordt dus de parameter `json` gebruikt. Voor meer informatie over het parseren van parameters voor de indexeerfunctie voor JSON-blobs raadpleegt u [JSON-blobs indexeren](search-howto-index-json-blobs.md).

In dit voorbeeld wordt een schema voor de indexeerfunctie gedefinieerd zodat deze een keer per dag wordt uitgevoerd. U kunt de planningseigenschap uit deze aanroep verwijderen als u niet wilt dat de indexeerfunctie in de toekomst automatisch opnieuw wordt uitgevoerd.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Omdat de index al is gevuld met hotelgegevens uit de Azure Cosmos DB-database, werkt de blobindexeerfunctie de bestaande documenten in de index bij en worden de kamergegevens toegevoegd.

> [!NOTE]
> Als u dezelfde niet-sleutelvelden in beide gegevensbronnen hebt en de gegevens in deze velden niet overeenkomen, bevat de index de waarden van de indexeerfunctie die het meest recent is uitgevoerd. In ons voorbeeld bevatten beide gegevensbronnen het veld **HotelName**. Als de gegevens in dit veld om de een of andere reden verschillen, worden voor documenten met dezelfde sleutelwaarde de **HotelName**-gegevens uit de gegevensbron die het laatst is geïndexeerd opgeslagen in de index.

## <a name="5---search"></a>5 - Zoeken

U kunt de gevulde zoekindex onderzoeken nadat het programma is uitgevoerd. Gebruik hiervoor [**Search Explorer**](search-explorer.md) in de portal.

Open in Azure Portal de pagina **Overzicht** van de zoekservice en zoek in de lijst **Indexen** de index **hotel-rooms-sample**.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Lijst met Azure Cognitive Search-indexen" border="false":::

Klik op de index hotel-rooms-sample in de lijst. U ziet een Search Explorer-interface voor de index. Voer een query in voor een term, bijvoorbeeld luxe. Er wordt ten minste één document in de resultaten weergegeven. In dit document staat een lijst met kamerobjecten in de kamermatrix.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeldcode wordt gecontroleerd op bestaande objecten, en worden deze verwijderd of bijgewerkt, zodat u het programma opnieuw kunt uitvoeren.

U kunt de portal gebruiken om indexen, indexeerfuncties en gegevensbronnen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met het opnemen van gegevens uit verschillende bronnen, kunnen we dieper ingaan op de configuratie van de indexeerfunctie, te beginnen met Cosmos DB.

> [!div class="nextstepaction"]
> [Een indexeerfunctie voor Azure Cosmos DB configureren](search-howto-index-cosmosdb.md)
