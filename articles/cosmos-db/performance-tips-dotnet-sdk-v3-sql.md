---
title: Tips voor Azure Cosmos DB prestaties voor .NET SDK v3
description: Meer informatie over client configuratie opties voor het verbeteren van Azure Cosmos DB .NET v3 SDK-prestaties.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: f503f132794f6d04b587a78b8f838acba26f9ac3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032011"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tips voor betere prestaties van Azure Cosmos DB en .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](performance-tips-async-java.md)
> * [Sync Java-SDK v2](performance-tips-java.md)

Azure Cosmos DB is een snelle, flexibele, gedistribueerde data base die naadloos kan worden geschaald met gegarandeerde latentie en doorvoer niveaus. U hoeft geen grote architectuur wijzigingen aan te brengen of complexe code te schrijven om uw data base te schalen met Azure Cosmos DB. Omhoog en omlaag schalen is net zo eenvoudig als het maken van één API-aanroep. Zie [container doorvoer inrichten](how-to-provision-container-throughput.md) of [Data Base-door Voer inrichten](how-to-provision-database-throughput.md)voor meer informatie. 

Omdat Azure Cosmos DB via netwerk aanroepen wordt geopend, kunt u optimalisaties aan de client zijde maken om piek prestaties te bereiken wanneer u de [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)gebruikt.

Als u de prestaties van uw Data Base wilt verbeteren, moet u rekening houden met de opties die in de volgende secties worden weer gegeven.

## <a name="hosting-recommendations"></a>Aanbevelingen hosten

**Gebruik Windows 64-bits in plaats van Linux of Windows 32-bits host processing voor query-intensieve werk belastingen.**

U wordt aangeraden Windows 64-bits host te verwerken voor betere prestaties. De SQL SDK bevat een systeem eigen ServiceInterop.dll om query's lokaal te parseren en te optimaliseren. ServiceInterop.dll wordt alleen ondersteund op het Windows x64-platform. 

Voor Linux en andere niet-ondersteunde platforms waarbij ServiceInterop.dll niet beschikbaar is, wordt er een extra netwerk aanroep naar de gateway verzonden om de geoptimaliseerde query te krijgen. 

De vier hier vermelde toepassings typen gebruiken standaard 32-bits host verwerking. Ga als volgt te werk om de verwerking van de host te wijzigen in 64-bits verwerking voor uw toepassings type:

- **Voor uitvoer bare toepassingen**: Stel in het venster **project eigenschappen** in het deel venster voor het **maken** van het [platform doel](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) in op **x64**.

- **Voor op VSTest gebaseerde test projecten**: Selecteer in het menu **test** van Visual Studio test  >  **instellingen** testen en stel de **standaard architectuur** van de processor in op **x64**.

- **Voor lokaal geïmplementeerde ASP.NET-webtoepassingen**: Selecteer **extra**  >  **Opties**  >  **projecten en oplossingen**  >  **webprojecten**, en selecteer vervolgens **de 64-bits versie van IIS Express voor websites en projecten gebruiken**.

- **Voor ASP.NET-webtoepassingen die zijn geïmplementeerd op Azure**: Selecteer in het Azure Portal in **toepassings instellingen** het **64-bits** platform.

> [!NOTE] 
> Nieuwe Visual Studio-projecten worden standaard ingesteld op **elke CPU**. We raden u aan om uw project in te stellen op **x64** zodat het niet wordt overgeschakeld naar **x86**. Een project dat is ingesteld op **een CPU** kan eenvoudig overschakelen naar **x86** als een afhankelijkheid van alleen een x86-processor is toegevoegd.<br/>
> Het ServiceInterop.dll-bestand moet zich in de map bevindt waarin de SDK-DLL wordt uitgevoerd. Dit is alleen een probleem als u hand matig Dll's kopieert of aangepaste build-of implementatie systemen hebt.
    
**Garbage Collection aan server zijde inschakelen**

Het verminderen van de frequentie van garbagecollection kan in sommige gevallen helpen. Stel in .NET [gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) in op `true` .

**De werk belasting van uw client uitschalen**

Als u op hoge doorvoer niveaus test of op tarieven die groter zijn dan 50.000 aanvraag eenheden per seconde (RU/s), kan de client toepassing een knel punt in de werk belasting worden. Dit komt doordat de computer een CPU-of netwerk gebruik kan opleveren. Als u dit punt bereikt, kunt u het Azure Cosmos DB-account nog verder pushen door uw client toepassingen op meerdere servers te schalen.

> [!NOTE] 
> Hoog CPU-gebruik kan leiden tot grotere latentie en time-outuitzonderingen voor aanvragen.

## <a name="networking"></a>Netwerken
<a id="direct-connection"></a>

**Verbindings beleid: modus directe verbinding gebruiken**

De standaard verbindings modus voor .NET v3 SDK is direct. U configureert de verbindings modus wanneer u het `CosmosClient` exemplaar maakt in `CosmosClientOptions` .  Zie het artikel [connectiviteits modi](sql-sdk-connection-modes.md) voor meer informatie over de verschillende connectiviteits opties.

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Tijdelijke poortuitputting**

Als u een hoog verbindings volume of een hoog poort gebruik voor uw instanties ziet, controleert u eerst of uw client exemplaren Singleton zijn. Met andere woorden, de client exemplaren moeten uniek zijn voor de levens duur van de toepassing.

Wanneer het wordt uitgevoerd op het TCP-protocol, optimaliseert de client voor latentie door gebruik te maken van de langdurige verbindingen. Dit is in tegens telling tot het HTTPS-protocol, waarmee de verbindingen worden beëindigd na twee minuten van inactiviteit.

In scenario's waarin u sparse-toegang hebt, en als u een hoger aantal verbindingen ziet in vergelijking met de toegang tot de gateway modus, kunt u het volgende doen:

* Configureer de eigenschap [CosmosClientOptions. PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) `PrivatePortPool` (effectief met de Framework-versies 4.6.1 en hoger en .net Core-versies 2,0 en hoger). Met deze eigenschap kan de SDK gebruikmaken van een kleine groep tijdelijke poorten voor verschillende Azure Cosmos DB bestemmings eindpunten.
* Configureer de eigenschap [CosmosClientOptions. IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) in een waarde die groter is dan of gelijk is aan 10 minuten. De aanbevolen waarden zijn 20 minuten tot 24 uur.

<a id="same-region"></a>

**Voor prestaties, termijnen-clients in dezelfde Azure-regio**

Als dat mogelijk is, plaatst u toepassingen die Azure Cosmos DB aanroepen in dezelfde regio als de Azure Cosmos DB-Data Base. Dit is een geschatte vergelijking: aanroepen naar Azure Cosmos DB binnen dezelfde regio eindigen binnen 1 milliseconde (MS) tot 2 MS, maar de latentie tussen de West-en Oost kust van de Verenigde Staten is meer dan 50 MS. Deze latentie kan variëren van aanvraag tot aanvraag, afhankelijk van de route die door de aanvraag wordt genomen, terwijl deze van de client wordt door gegeven aan de grens van het Azure-Data Center. 

U kunt de laagst mogelijke latentie verkrijgen door ervoor te zorgen dat de aanroepende toepassing zich in dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eind punt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschik bare regio's.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Termijnen-clients in dezelfde regio." border="false":::

   <a id="increase-threads"></a>

**Het aantal threads/taken verhogen**

Omdat aanroepen naar Azure Cosmos DB via het netwerk worden gemaakt, moet u mogelijk de mate van gelijktijdigheid van uw aanvragen variëren, zodat de client toepassing mini maal tijd nodig heeft om te wachten tussen aanvragen. Als u bijvoorbeeld de [parallelle bibliotheek](/dotnet/standard/parallel-programming/task-parallel-library-tpl)van .net-taak gebruikt, maakt u op volg orde van honderden taken die worden gelezen van of schrijven naar Azure Cosmos db.

**Versneld netwerken inschakelen**
 
Om latentie en CPU-jitter te verminderen, raden we u aan om versnelde netwerken in te scha kelen op de virtuele machines van de client. Zie [een virtuele Windows-machine maken met versneld netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) of [een virtuele Linux-machine maken met versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md)voor meer informatie.

## <a name="sdk-usage"></a>SDK-gebruik

**De meest recente SDK installeren**

De Azure Cosmos DB Sdk's worden voortdurend verbeterd om de beste prestaties te leveren. Zie [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md)om de meest recente SDK en verbeteringen van de beoordeling te bepalen.

**Stream-Api's gebruiken**

[.NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) bevat stream-api's die gegevens kunnen ontvangen en retour neren zonder serialisatie. 

Middle-tier-toepassingen die geen reacties rechtstreeks van de SDK gebruiken, maar door sturen naar andere toepassings lagen, kunnen profiteren van de stream-Api's. Voor voor beelden van verwerking van streams, zie de voor beelden van [artikel beheer](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Een singleton Azure Cosmos DB-client gebruiken voor de levens duur van uw toepassing**

Elk `CosmosClient` exemplaar is thread-safe en voert efficiënt verbindings beheer en adres caching uit wanneer het in de directe modus werkt. Om efficiënt verbindings beheer en betere prestaties van de SDK-client mogelijk te maken, wordt u aangeraden één exemplaar te gebruiken per `AppDomain` voor de levens duur van de toepassing.

Wanneer u aan Azure Functions werkt, moeten instanties ook de bestaande [richt lijnen](../azure-functions/manage-connections.md#static-clients) volgen en één exemplaar onderhouden.

<a id="max-connection"></a>

**Reactie op inhoud voor schrijf bewerkingen uitschakelen**

Voor workloads met zware nettoladingen maken stelt `EnableContentResponseOnWrite` u de aanvraag optie in op `false` . De service zal de gemaakte of bijgewerkte resource niet langer naar de SDK retour neren. Normaal gesp roken is het niet nodig om de service te retour neren, omdat de toepassing het object heeft dat wordt gemaakt. De header waarden zijn nog steeds toegankelijk, zoals aanvragen in rekening gebracht. Het uitschakelen van het inhouds antwoord kan helpen de prestaties te verbeteren, omdat de SDK niet langer geheugen hoeft toe te wijzen of de hoofd tekst van de reactie te serialiseren. Het vermindert ook het gebruik van de netwerk bandbreedte om de prestaties te verbeteren.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Bulk inschakelen om te optimaliseren voor door Voer in plaats van latentie**

Schakel *bulksgewijs* in voor scenario's waarbij de werk belasting een grote hoeveelheid door Voer vereist en latentie niet zo belang rijk is. Zie [Introduction to bulksgewijze support](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk)(Engelstalig) voor meer informatie over het inschakelen van de bulk functie en voor informatie over de scenario's waarvoor deze optie moet worden gebruikt.

**Verg Root System.Net MaxConnections per host wanneer u de gateway modus gebruikt**

Azure Cosmos DB aanvragen worden gedaan via HTTPS/REST wanneer u de gateway modus gebruikt. Deze zijn onderhevig aan de standaard verbindings limiet per hostnaam of IP-adres. Mogelijk moet u `MaxConnections` een hogere waarde instellen (van 100 tot 1.000), zodat de client bibliotheek meerdere gelijktijdige verbindingen met Azure Cosmos db kan gebruiken. In .NET SDK 1.8.0 en hoger is de standaard waarde voor [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 50. Als u de waarde wilt wijzigen, kunt u [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) een hogere waarde instellen.

**Parallelle query's voor gepartitioneerde verzamelingen afstemmen**

SQL .NET SDK ondersteunt parallelle query's, waarmee u parallel een gepartitioneerde container kunt doorzoeken. Zie [code voorbeelden](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) met betrekking tot het werken met de sdk's voor meer informatie. Parallelle query's zijn ontworpen om betere latentie en door Voer van query's te bieden dan hun seriële tegen hanger. 

Parallelle query's bieden twee para meters die u aan uw vereisten kunt aanpassen: 

- **MaxConcurrency**: Hiermee bepaalt u het maximum aantal partities waarmee gelijktijdig query's kunnen worden uitgevoerd.

   Parallelle query werkt door meerdere partities parallel te doorzoeken. Maar gegevens van een afzonderlijke partitie worden serieel opgehaald ten opzichte van de query. De instelling `MaxConcurrency` in [SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) voor het aantal partities is de beste kans om de meest uitvoerende query te bereiken, op voor waarde dat alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u de mate van parallelle uitvoering instellen op een hoog getal. Het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de mate van parallelle uitvoering.

    Parallelle query's produceren het meest voor deel als de gegevens gelijkmatig worden verdeeld over alle partities met betrekking tot de query. Als de gepartitioneerde verzameling is gepartitioneerd zodat alle of de meeste gegevens die door een query zijn geretourneerd, in een paar partities worden geconcentreerd (één partitie is het ergste geval), kunnen deze partities de prestaties van de query opsporen.
   
- **MaxBufferedItemCount**: bepaalt het aantal vooraf opgehaalde resultaten.

   Parallelle query is ontworpen om de resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Deze vooraf ophalen helpt de algehele latentie van een query te verbeteren. De `MaxBufferedItemCount` para meter beperkt het aantal vooraf opgehaalde resultaten. Stel `MaxBufferedItemCount` in op het verwachte aantal geretourneerde resultaten (of een hoger getal), zodat de query het maximale voor deel van het vooraf ophalen kan ontvangen.

   Het vooraf ophalen werkt op dezelfde manier, ongeacht de mate van parallellisme, en er is één buffer voor de gegevens van alle partities.  

**Uitstel implementeren met RetryAfter-intervallen**

Tijdens de prestatie tests moet u de belasting verg Roten tot een klein aantal aanvragen wordt beperkt. Als aanvragen worden beperkt, moet de client toepassing uitvallen voor het door de server opgegeven interval voor nieuwe pogingen. Door de uitstel te respecteren, weet u zeker dat u een minimale hoeveelheid tijd moet wachten tussen nieuwe pogingen. 

Zie [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_CosmosException_RetryAfter)voor meer informatie.
    
Er is een mechanisme voor het vastleggen van aanvullende diagnostische gegevens en latentie problemen, zoals wordt weer gegeven in het volgende voor beeld. U kunt de diagnostische teken reeks registreren voor aanvragen met een hogere lees latentie. De vastgelegde diagnostische teken reeks helpt u te begrijpen hoe vaak u een *429* -fout voor een bepaalde aanvraag hebt ontvangen.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Het aantal threads/taken verhogen**

Zie [het aantal threads/taken](#increase-threads) in het gedeelte netwerken van dit artikel verg Roten.

## <a name="indexing-policy"></a>Indexeringsbeleid
 
**Niet-gebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

Met het Azure Cosmos DB-indexerings beleid kunt u ook opgeven welke document paden moeten worden opgenomen of uitgesloten van indexeren met behulp van index paden (IndexingPolicy. IncludedPaths en IndexingPolicy. ExcludedPaths). 

Als u alleen de paden indexeert die u nodig hebt, kunt u de schrijf prestaties verbeteren, RU-kosten voor schrijf bewerkingen verlagen en de index opslag beperken voor scenario's waarin de query patronen vooraf bekend zijn. Dit komt doordat de index kosten direct overeenkomen met het aantal unieke paden dat is geïndexeerd. De volgende code laat bijvoorbeeld zien hoe u een volledige sectie van de documenten (een substructuur) uitsluit van indexeren met behulp van het Joker teken ' * ':

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Zie [Azure Cosmos DB Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

## <a name="throughput"></a>Doorvoer
<a id="measure-rus"></a>

**Meten en afstemmen voor het gebruik van lagere RU/s**

Azure Cosmos DB biedt een uitgebreide set database bewerkingen. Deze bewerkingen omvatten relationele en hiërarchische query's met UDF-bestanden (Universal Disk Format), opgeslagen procedures en triggers, die allemaal op de documenten in een database verzameling worden uitgevoerd. 

De kosten voor elk van deze bewerkingen variëren, afhankelijk van de CPU, i/o en het geheugen die nodig zijn om de bewerking te volt ooien. In plaats van te denken over en het beheren van hardwarebronnen kunt u een aanvraag eenheid beschouwen als één maat eenheid voor de resources die nodig zijn om verschillende database bewerkingen uit te voeren en een toepassings aanvraag te onderhouden.

De door Voer is ingericht op basis van het aantal ingestelde [aanvraag eenheden](request-units.md) voor elke container. Het verbruik van de aanvraag eenheid wordt geëvalueerd als eenheden per seconde. Toepassingen die het aantal ingerichte aanvraag eenheden voor hun container overschrijden, zijn beperkt tot het aantal onder het ingerichte niveau voor de container daalt. Als voor uw toepassing een hogere door Voer is vereist, kunt u de door Voer verhogen door extra aanvraag eenheden in te richten.

De complexiteit van een query is van invloed op het aantal aanvraag eenheden dat voor een bewerking wordt verbruikt. Het aantal predikaten, de aard van de predikaten, het aantal UDF-bestanden en de grootte van de bron-gegevensset beïnvloeden de kosten van de query bewerkingen.

Als u de overhead van een bewerking (maken, bijwerken of verwijderen) wilt meten, inspecteert u de [x-MS-Request-](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) factuurkop tekst (of de equivalente `RequestCharge` eigenschap in `ResourceResponse\<T>` of `FeedResponse\<T>` in de .NET SDK) om het aantal aanvraag eenheden te meten dat door de bewerkingen wordt verbruikt:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

De aanvraag kosten die in deze koptekst worden geretourneerd, zijn een fractie van uw ingerichte door Voer (dat wil zeggen 2.000 RU/s). Als de voor gaande query bijvoorbeeld 1.000 1 KB-documenten retourneert, zijn de kosten van de bewerking 1.000. Binnen een seconde voldoet de server dus slechts twee van deze aanvragen voordat de frequentie van latere aanvragen wordt beperkt. Zie [aanvraag eenheden](request-units.md) en de [reken eenheid voor aanvragen](https://www.documentdb.com/capacityplanner)voor meer informatie.
<a id="429"></a>

**Aantal verwerkings frequenties/aanvragen afhandelen te groot**

Wanneer een client de gereserveerde door Voer voor een account probeert te overschrijden, is er geen prestatie vermindering op de server en wordt er geen gebruik van de doorvoer capaciteit meer dan het gereserveerde niveau. De preventief van de server beëindigt de aanvraag met RequestRateTooLarge (HTTP-status code 429). Het retourneert een [x-MS-retry-after-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) -header die de hoeveelheid tijd in milliseconden aangeeft dat de gebruiker moet wachten voordat de aanvraag opnieuw wordt geprobeerd.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

De Sdk's ondervangen dit antwoord impliciet, respecteert de door de server opgegeven nieuwe poging na de header en voert de aanvraag opnieuw uit. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

Als u meer dan één client cumulatief op dezelfde manier hebt uitgevoerd, is het aantal standaard pogingen dat momenteel is ingesteld op 9 intern door de client mogelijk niet voldoende. In dit geval genereert de client een CosmosException met de status code 429 naar de toepassing. 

U kunt het standaard aantal nieuwe pogingen wijzigen door de `RetryOptions` op het exemplaar in te stellen `CosmosClientOptions` . Standaard wordt de CosmosException met de status code 429 geretourneerd na een cumulatieve wacht tijd van 30 seconden als de aanvraag boven het aanvraag aantal blijft. Deze fout wordt geretourneerd, zelfs wanneer het huidige aantal nieuwe pogingen kleiner is dan het maximum aantal nieuwe pogingen, of de huidige waarde de standaard instelling is van 9 of een door de gebruiker gedefinieerde waarde.

Het gedrag voor automatische pogingen helpt de flexibiliteit en bruikbaarheid voor de meeste toepassingen te verbeteren. Het is echter mogelijk niet het beste gedrag wanneer u prestatie benchmarks uitvoert, met name wanneer u een latentie meet. De door de client waargenomen latentie krijgt een waarschuwing als het experiment de server beperking bereikt en zorgt ervoor dat de client-SDK op de achtergrond opnieuw probeert. Om latentie pieken te voor komen tijdens prestatie experimenten, meet u de kosten die worden geretourneerd door elke bewerking en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraag tarief vallen. 

Zie [aanvraag eenheden](request-units.md)voor meer informatie.

**Voor een hogere door Voer ontwerpen voor kleinere documenten**

De aanvraag kosten (dat wil zeggen, de aanvraag verwerkings kosten) van een opgegeven bewerking correleert direct naar de grootte van het document. Bewerkingen voor grote documenten kosten meer dan bewerkingen op kleine documenten.

## <a name="next-steps"></a>Volgende stappen
Zie [prestaties en schalen testen met Azure Cosmos DB](performance-testing.md)voor een voorbeeld toepassing die wordt gebruikt om Azure Cosmos DB te evalueren voor scenario's met hoge prestaties op een aantal client computers.

Zie [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal baarheid en hoge prestaties.