---
title: C#-zelfstudie over Azure SQL-gegevens indexeren
titleSuffix: Azure Cognitive Search
description: In deze C#-zelfstudie maakt u verbinding met Azure SQL Database, extraheert u doorzoekbare gegevens en laadt u deze in een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2ca5f42120661b887d07e697596f41cb7a7fce4
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745764"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Zelfstudie: Azure SQL-gegevens indexeren met de .NET SDK

Configureer een [indexeerfunctie](search-indexer-overview.md) om doorzoekbare gegevens op te halen uit Azure SQL-database en deze te verzenden naar een zoekindex in Azure Cognitive Search. 

In deze zelfstudie wordt gebruikgemaakt van C# en de [.NET SDK](/dotnet/api/overview/azure/search) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een gegevensbron maken dat verbinding maakt met Azure SQL Database
> * Een indexeerfunctie maken
> * Een indexeerfunctie uitvoeren om gegevens in een index te laden
> * Een query uitvoeren op een index als een verificatiestap

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-files"></a>Bestanden downloaden

De broncode voor deze zelfstudie bevindt zich in de map [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) in de GitHub-opslagplaats [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started).

## <a name="1---create-services"></a>1- Services maken

In deze zelfstudie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's en Azure SQL Database als een externe gegevensbron. Maak, indien mogelijk, beide services in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan Azure SQL Database zich in elke regio bevinden.

### <a name="start-with-azure-sql-database"></a>Aan de slag met Azure SQL Database

In deze stap maakt u een externe gegevensbron op Azure SQL Database die een indexeerfunctie kan verkennen. U kunt de Azure-portal en het bestand *hotels.sql* uit de voorbeelddownload gebruiken om de gegevensset in Azure SQL Database te maken. Azure Cognitive Search gebruikt platte rijensets, zoals de sets die worden gegenereerd op basis van een weergave of query. Het SQL-bestand in de voorbeeldoplossing maakt en vult één tabel.

Als u een bestaande Azure SQL Database-resource hebt, kunt u de tabel hotels er desgewenst aan toevoegen vanaf stap 4.

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/).

1. **Een SQL Database** vinden of maken. U kunt de standaardinstellingen en de laagste prijscategorie gebruiken. Eén voordeel van het maken van een server is dat u de gebruikersnaam en het wachtwoord van een beheerder kunt opgeven die in een latere stap nodig zijn om tabellen te maken en te laden.

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="De pagina Nieuwe database" border="false":::

1. Klik op **Beoordelen + maken** om de nieuwe server en database te implementeren. Wacht tot de server en database zijn geïmplementeerd.

1. Klik in het navigatiedeelvenster op **Query-editor (preview)** en voer de gebruikersnaam en het wachtwoord van de serverbeheerder in. 

   Als de toegang wordt geweigerd, kopieert u het IP-adres van de client uit het foutbericht en klikt u op de koppeling **Serverfirewall instellen** om een regel toe te voegen die toegang vanaf uw clientcomputer toestaat met behulp van uw client-IP voor het bereik. Het kan enkele minuten duren voordat de regel is doorgevoerd.

1. In Query-editor klikt u op **Query openen** en navigeert u naar de locatie van bestand *hotels.sql* op uw lokale computer. 

1. Selecteer het bestand en klik op **Openen**. Het script moet er ongeveer uitzien als in de volgende schermafbeelding:

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="SQL-script" border="false":::

1. Klik op **Uitvoeren** om de query uit te voeren. In het resultatenvenster zou u het bericht moeten zien dat de query is gelukt voor 3 rijen.

1. Als u een rijenset uit deze tabel wilt retourneren, kunt u de volgende query uitvoeren als verificatiestap:

    ```sql
    SELECT * FROM Hotels
    ```

1. Kopieer de verbindingsreeks voor ADO.NET voor de database. Kopieer onder **Instellingen** > **Verbindingsreeksen** de ADO.NET-verbindingsreeks, vergelijkbaar met het voorbeeld hieronder.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

U hebt deze verbindingsreeks in de volgende oefening nodig om uw omgeving in te stellen.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het volgende onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search ophalen

Voor API-aanroepen is de service-URL en een toegangssleutel vereist. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Een HTTP-eindpunt en toegangssleutel ophalen" border="false":::

## <a name="2---set-up-your-environment"></a>2 - De omgeving instellen

1. Start Visual Studio en open **DotNetHowToIndexers.sln**.

1. Open **appsettings.json** in Solution Explorer om verbindingsgegevens op te geven.

1. Voor `SearchServiceEndPoint` , als de volledige URL op de pagina service overzicht is " https://my-demo-service.search.windows.net ", is de waarde die moet worden verstrekt de URL.

1. Voor `AzureSqlConnectionString` is de tekenreeksindeling vergelijkbaar met het volgende: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceEndPoint": "<placeholder-search-url>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-search-service>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Controleer in de verbindingsreeks of deze een geldig wachtwoord bevat. Terwijl de database en gebruikersnamen worden gekopieerd, moet het wachtwoord handmatig worden ingevoerd.

## <a name="3---create-the-pipeline"></a>3: Maak de pijplijn

Voor indexeerfuncties is een gegevensbronobject en een index vereist. Relevante code staat in twee bestanden:

  + **hotel.cs**, dat een schema bevat dat de index definieert
  + **Program.cs**, dat functies bevat die de structuren in uw service maken en beheren

### <a name="in-hotelcs"></a>In hotel.cs

Het indexschema definieert de verzameling met velden, met inbegrip van kenmerken die toegestane bewerkingen opgeven, bijvoorbeeld of de volledige tekst van een veld kan worden doorzocht, gefilterd of gesorteerd zoals wordt weergegeven in de volgende velddefinitie voor HotelName. Een [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) is zoek opdrachten in volledige tekst per definitie. Andere kenmerken worden expliciet toegewezen.

```csharp
. . . 
[SearchableField(IsFilterable = true, IsSortable = true)]
[JsonPropertyName("hotelName")]
public string HotelName { get; set; }
. . .
```

Een schema kan ook andere elementen bevatten, zoals scoreprofielen om een zoekscore te verbeteren, aangepaste analysefuncties en andere constructies. In dit geval is het schema echter beperkt gedefinieerd en bestaat het alleen uit velden in de voorbeeldgegevenssets.

### <a name="in-programcs"></a>In Program.cs

Het hoofd programma bevat logica voor het maken van [een Indexeer functie-client](/dotnet/api/azure.search.documents.indexes.models.searchindexer), een index, een gegevens bron en een Indexeer functie. De code controleert op en verwijdert bestaande resources met dezelfde naam, waarbij ervan wordt uitgegaan dat u dit programma meerdere keren uitvoert.

Het gegevensbronobject is geconfigureerd met instellingen die specifiek zijn voor Azure SQL Database-resources, waaronder [gedeeltelijke of incrementele indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) voor het gebruik van de ingebouwde [functies voor het detecteren van veranderingen](/sql/relational-databases/track-changes/about-change-tracking-sql-server) van Azure SQL. De resource-demo database in Azure SQL heeft een kolom ' voorlopig verwijderen ' met de naam **IsDeleted**. Als deze kolom is ingesteld op True in de database, verwijdert de indexeerfunctie het bijbehorende document uit de Azure Cognitive Search-index.

```csharp
Console.WriteLine("Creating data source...");

var dataSource =
      new SearchIndexerDataSourceConnection(
         "hotels-sql-ds",
         SearchIndexerDataSourceType.AzureSql,
         configuration["AzureSQLConnectionString"],
         new SearchIndexerDataContainer("hotels"));

indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
```

Een indexeerfunctie-object is platform-neutraal, waarbij de configuratie, planning en aanroep hetzelfde zijn, ongeacht de bron. Dit voorbeeld bevat een schema, een hersteloptie waarmee de indexeringsgeschiedenis wordt gewist en een methode wordt aangeroepen om de indexeerfunctie direct te maken en uit te voeren. Gebruik [CreateOrUpdateIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.createorupdateindexerasync)om een Indexeer functie te maken of bij te werken.

```csharp
Console.WriteLine("Creating Azure SQL indexer...");

var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
      StartTime = DateTimeOffset.Now
};

var parameters = new IndexingParameters()
{
      BatchSize = 100,
      MaxFailedItems = 0,
      MaxFailedItemsPerBatch = 0
};

// Indexer declarations require a data source and search index.
// Common optional properties include a schedule, parameters, and field mappings
// The field mappings below are redundant due to how the Hotel class is defined, but 
// we included them anyway to show the syntax 
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
      Description = "Data indexer",
      Schedule = schedule,
      Parameters = parameters,
      FieldMappings =
      {
         new FieldMapping("_id") {TargetFieldName = "HotelId"},
         new FieldMapping("Amenities") {TargetFieldName = "Tags"}
      }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Indexeer functie-uitvoeringen worden doorgaans gepland, maar tijdens de ontwikkeling kunt u de Indexeer functie direct uitvoeren met behulp van [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync).

```csharp
Console.WriteLine("Running Azure SQL indexer...");

try
{
      await indexerClient.RunIndexerAsync(indexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

## <a name="4---build-the-solution"></a>4 - De oplossing bouwen

Druk op F5 om de oplossing te bouwen en uit te voeren. Het programma wordt uitgevoerd in de foutopsporingsmodus. De status van elke bewerking wordt weergegeven in een consolevenster.

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="Console-uitvoer" border="false":::

Uw code wordt lokaal uitgevoerd in Visual Studio en maakt verbinding met uw zoekservice in Azure, die op zijn beurt verbinding maakt met Azure SQL Database en de gegevensset op te halen. Met dit aantal bewerkingen zijn er verschillende mogelijke foutpunten. Als er een fout optreedt, controleert u eerst de volgende voorwaarden:

+ De verbindings gegevens van de zoek service die u opgeeft, zijn de volledige URL. Als u alleen de service naam hebt ingevoerd, worden de bewerkingen gestopt bij het maken van de index, waarbij de fout niet kan worden gemaakt.

+ Gegevens over de databaseverbinding in **appsettings.json**. Dit moet de ADO.NET-verbindingsreeks zijn die u hebt verkregen via de portal en die is gewijzigd, zodat deze een gebruikersnaam en wachtwoord bevat die geldig zijn voor uw database. Het gebruikersaccount moet machtigingen hebben om gegevens op te halen. Het IP-adres van uw lokale client moet binnenkomende toegang hebben via de firewall.

+ Bronlimieten. Onthoud dat voor de Gratis-laag een limiet van 3 indexen, indexeerfuncties en gegevensbronnen geldt. Een service met de maximale limiet kan geen nieuwe objecten maken.

## <a name="5---search"></a>5 - Zoeken

Gebruik de Azure-portal om het maken van objecten te controleren en gebruik vervolgens **Search Explorer** om de index op te vragen.

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en open op de pagina **Overzicht** van de zoekservice de lijst om te controleren of het object is gemaakt. **Indexen**, **Indexeerfuncties** en **Gegevensbronnen** hebben respectievelijk ‘hotels’, ‘azure-sql-indexer’, and ‘azure-sql’.

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="Tegels met indexeerfuncties en gegevensbronnen" border="false":::

1. Selecteer de index hotels. **Search Explorer** is het eerste tabblad op de pagina hotels. 

1. Klik op **Zoeken** om een lege query uit te voeren. 

   De drie vermeldingen in de index worden geretourneerd als JSON-documenten. Search Explorer retourneert documenten in JSON, zodat u de volledige structuur kunt bekijken.

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="Een query op uitvoeren op een index" border="false":::
   
1. Geef vervolgens een zoekreeks op: `search=river&$count=true`. 

   Deze query roept een zoekopdracht aan die in de volledige tekst zoekt naar de term `river` en het resultaat bevat het aantal overeenkomende documenten. Het aantal overeenkomende documenten retourneren kan handig zijn in testscenario's wanneer u een grote index met duizenden of miljoenen documenten hebt. In dit geval komt slechts één document overeen met de query.

1. Voer ten slotte een zoektekenreeks in die de JSON-uitvoer beperkt tot de gewenste velden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   De respons van de query wordt beperkt tot geselecteerde velden, wat resulteert in een beknoptere uitvoer.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeldcode voor deze zelfstudie wordt gecontroleerd op bestaande objecten. Deze worden verwijderd, zodat u de code opnieuw kunt uitvoeren.

U kunt de portal gebruiken om indexen, indexeerfuncties en gegevensbronnen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basisprincipes van het indexeren van SQL Database, gaan we de configuratie van de indexeerfunctie nader bekijken.

> [!div class="nextstepaction"]
> [Een SQL Database-indexeerfunctie configureren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)