---
title: Tips voor Azure Cosmos DB prestaties voor .NET SDK v2
description: Meer informatie over client configuratie opties voor het verbeteren van de prestaties van Azure Cosmos DB .NET v2 SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 57b3d5853f83fc7ee75538d7966f5e20b1a64cd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428946"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Tips voor betere prestaties van Azure Cosmos DB en .NET SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](performance-tips-async-java.md)
> * [Sync Java-SDK v2](performance-tips-java.md)

Azure Cosmos DB is een snelle en flexibele gedistribueerde data base die naadloos kan worden geschaald met gegarandeerde latentie en door voer. U hoeft geen grote architectuur wijzigingen aan te brengen of complexe code te schrijven om uw data base te schalen met Azure Cosmos DB. Omhoog en omlaag schalen is net zo eenvoudig als het maken van één API-aanroep. Zie voor meer informatie [over het inrichten van container doorvoer](how-to-provision-container-throughput.md) of [het inrichten van de doorvoer capaciteit van de data base](how-to-provision-database-throughput.md). Maar omdat Azure Cosmos DB via netwerk aanroepen wordt geopend, zijn er optimalisaties aan de client zijde die u kunt uitvoeren om piek prestaties te bereiken wanneer u de [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)gebruikt.

Als u de prestaties van uw Data Base wilt verbeteren, kunt u de volgende opties overwegen:

## <a name="upgrade-to-the-net-v3-sdk"></a>Upgrade uitvoeren naar de .NET v3 SDK

De [.net v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) wordt uitgebracht. Als u de .NET v3 SDK gebruikt, raadpleegt u de [.net v3-prestatie gids](performance-tips-dotnet-sdk-v3-sql.md) voor de volgende informatie:

- De standaard instelling is de directe TCP-modus
- Ondersteuning voor stream-API
- Ondersteuning voor aangepaste serialisatiefunctie voor het toestaan van System.Text.JSop gebruik
- Geïntegreerde batch-en bulk ondersteuning

## <a name="hosting-recommendations"></a>Aanbevelingen hosten

**Gebruik Windows 64-bits in plaats van Linux of Windows 32-bits host processing voor query-intensieve werk belastingen.**

U wordt aangeraden Windows 64-bits host te verwerken voor betere prestaties. De SQL SDK bevat een systeem eigen ServiceInterop.dll om query's lokaal te parseren en te optimaliseren. ServiceInterop.dll wordt alleen ondersteund op het Windows x64-platform. Voor Linux en andere niet-ondersteunde platforms waarbij ServiceInterop.dll niet beschikbaar is, wordt er een extra netwerk aanroep naar de gateway verzonden om de geoptimaliseerde query te krijgen. De volgende typen toepassingen gebruiken standaard 32-bits host verwerking. Voer de volgende stappen uit op basis van het type van uw toepassing om de verwerking van de host te wijzigen in 64-bits verwerking:

- Voor uitvoer bare toepassingen kunt u de verwerking van een host wijzigen door het [platform doel](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) in te stellen op **x64**  in het venster **project eigenschappen** op het tabblad **opbouwen** .

- Voor VSTest test projecten kunt u de verwerking van de host wijzigen door test   >  **instellingen** testen  >  **standaard processor architectuur als x64** te selecteren in het menu van Visual Studio **testen** .

- Voor lokaal geïmplementeerde ASP.NET-webtoepassingen kunt u de verwerking van de host wijzigen door **de 64-bits versie van IIS Express voor websites en projecten** te selecteren onder **extra**  >  **Opties**  >  **projecten en oplossingen**  >  **webprojecten**.

- Voor ASP.NET-webtoepassingen die in azure zijn geïmplementeerd, kunt u de verwerking van de host wijzigen door het **64-bits** platform te selecteren in **Toepassings instellingen** in de Azure Portal.

> [!NOTE] 
> Nieuwe Visual Studio-projecten worden standaard ingesteld op **elke CPU**. We raden u aan om uw project in te stellen op **x64** zodat het niet wordt overgeschakeld naar **x86**. Een project ingesteld op **een wille keurige CPU** kan eenvoudig overschakelen naar **x86** als er een alleen-x86-afhankelijkheid is toegevoegd.<br/>
> ServiceInterop.dll moet zich in de map bevindt waarin de SDK-DLL wordt uitgevoerd. Dit is alleen een probleem als u hand matig Dll's kopieert of aangepaste build/Deployment-systemen hebt.
    
**Garbage Collection aan de server zijde inschakelen (GC)**

Het verminderen van de frequentie van garbagecollection kan in sommige gevallen helpen. Stel in .NET [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) in op `true` .

**De werk belasting van uw client uitschalen**

Als u test met hoge doorvoer niveaus (meer dan 50.000 RU/s), kan de client toepassing het knel punt worden als gevolg van het uitvallen van CPU-of netwerk gebruik. Als u dit punt bereikt, kunt u het Azure Cosmos DB-account nog verder pushen door uw client toepassingen op meerdere servers te schalen.

> [!NOTE] 
> Hoog CPU-gebruik kan leiden tot grotere latentie en time-outuitzonderingen voor aanvragen.

## <a name="networking"></a><a id="networking"></a> Inbel

**Verbindings beleid: modus directe verbinding gebruiken**

De .NET v2 SDK standaard verbindings modus is Gateway. U kunt de verbindings modus configureren tijdens de constructie van het `DocumentClient` exemplaar met behulp van de `ConnectionPolicy` para meter. Als u de directe modus gebruikt, moet u deze ook instellen met `Protocol` behulp van de `ConnectionPolicy` para meter. Zie het artikel [connectiviteits modi](sql-sdk-connection-modes.md) voor meer informatie over de verschillende connectiviteits opties.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Tijdelijke poortuitputting**

Als u een hoog verbindings volume of een hoog poort gebruik voor uw instanties ziet, controleert u eerst of uw client exemplaren Singleton zijn. Met andere woorden, de client exemplaren moeten uniek zijn voor de levens duur van de toepassing.

Bij het uitvoeren van het TCP-protocol optimaliseert de client voor latentie door gebruik te maken van de langdurige verbindingen, in tegens telling tot het HTTPS-protocol, waardoor de verbindingen na 2 minuten van inactiviteit worden beëindigd.

In scenario's waarin u over sparse-toegang beschikt en als u een hoger aantal verbindingen krijgt in vergelijking met de toegang tot de gateway modus, kunt u het volgende doen:

* Configureer de eigenschap [Connection Policy. PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) in `PrivatePortPool` (effectief met Framework versie>= 4.6.1 en .net core versie >= 2,0): met deze eigenschap kan de SDK een kleine groep tijdelijke poorten gebruiken voor verschillende Azure Cosmos DB bestemmings eindpunten.
* Configureer de eigenschap [Connection Policy. IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) moet groter zijn dan of gelijk zijn aan 10 minuten. De aanbevolen waarden liggen tussen 20 minuten en 24 uur.

**Open async aanroepen om opstart latentie bij eerste aanvraag te voor komen**

De eerste aanvraag heeft standaard een hogere latentie omdat de routerings tabel van het adres moet worden opgehaald. Wanneer u [SDK v2](sql-api-sdk-dotnet.md)gebruikt, roept u `OpenAsync()` eenmaal aan tijdens de initialisatie om te voor komen dat deze opstart latentie bij de eerste aanvraag. De aanroep ziet er als volgt uit: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` genereert aanvragen voor het verkrijgen van de routerings tabel van het adres voor alle containers in het account. Voor accounts met veel containers, maar waarvan de toepassing toegang heeft tot een subset hiervan, `OpenAsync` zou een onnodige hoeveelheid verkeer genereren, waardoor de initialisatie traag wordt. Het gebruik hiervan `OpenAsync` is mogelijk niet nuttig in dit scenario, omdat het opstarten van toepassingen wordt vertraagd.

**Voor prestaties, termijnen-clients in dezelfde Azure-regio**

Als dat mogelijk is, plaatst u toepassingen die Azure Cosmos DB aanroepen in dezelfde regio als de Azure Cosmos DB-Data Base. Hier volgt een vergelijking van benadering: aanroepen naar Azure Cosmos DB binnen dezelfde regio worden binnen 1 MS tot 2 MS voltooid, maar de latentie tussen de West-en Oost kust van de VS is meer dan 50 MS. Deze latentie kan variëren van aanvraag tot aanvraag, afhankelijk van de route die door de aanvraag wordt genomen, terwijl deze van de client wordt door gegeven aan de grens van het Azure-Data Center. U kunt de laagst mogelijke latentie verkrijgen door ervoor te zorgen dat de aanroepende toepassing zich in dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eind punt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschik bare regio's.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Het Azure Cosmos DB verbindings beleid" border="false":::

**Het aantal threads/taken verhogen**
<a id="increase-threads"></a>

Omdat aanroepen naar Azure Cosmos DB via het netwerk worden gemaakt, moet u mogelijk de mate van parallelle uitvoering van uw aanvragen variëren, zodat de client toepassing mini maal tijd nodig heeft om te wachten tussen aanvragen. Als u bijvoorbeeld de [parallelle bibliotheek](/dotnet/standard/parallel-programming/task-parallel-library-tpl)van .net-taak gebruikt, maakt u op volg orde van honderden taken die worden gelezen van of schrijven naar Azure Cosmos db.

**Versneld netwerken inschakelen**
 
Om latentie en CPU-jitter te verminderen, raden we u aan om versneld netwerken op virtuele client machines in te scha kelen. Zie [een virtuele Windows-machine maken met versneld netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) of [een virtuele Linux-machine maken met versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-gebruik

**De meest recente SDK installeren**

De Azure Cosmos DB Sdk's worden voortdurend verbeterd om de beste prestaties te leveren. Raadpleeg de [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) -pagina's om de meest recente SDK te bepalen en verbeteringen te bekijken.

**Een singleton Azure Cosmos DB-client gebruiken voor de levens duur van uw toepassing**

Elk `DocumentClient` exemplaar is thread-safe en voert efficiënt verbindings beheer en adres caching uit wanneer deze in de directe modus worden uitgevoerd. Om efficiënt verbindings beheer en betere prestaties van de SDK-client mogelijk te maken, wordt u aangeraden één exemplaar te gebruiken per `AppDomain` voor de levens duur van de toepassing.

**System.Net MaxConnections per host verg Roten met de gateway modus**

Azure Cosmos DB aanvragen worden gedaan via HTTPS/REST wanneer u de gateway modus gebruikt. Deze worden onderworpen aan de standaard verbindings limiet per hostnaam of IP-adres. Mogelijk moet u `MaxConnections` een hogere waarde instellen (100 tot 1.000), zodat de client bibliotheek meerdere gelijktijdige verbindingen met Azure Cosmos db kan gebruiken. In .NET SDK 1.8.0 en hoger is de standaard waarde voor [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 50. Als u de waarde wilt wijzigen, kunt u [Documents. client. Connection Policy. MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) op een hogere waarde instellen.

**Parallelle query's voor gepartitioneerde verzamelingen afstemmen**

SQL .NET SDK 1.9.0 en hoger ondersteunen parallelle query's, waarmee u parallel een gepartitioneerde verzameling kunt uitvoeren. Zie [code voorbeelden](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs) met betrekking tot het werken met de sdk's voor meer informatie. Parallelle query's zijn ontworpen om betere latentie en door Voer van query's te bieden dan hun seriële tegen hanger. Parallelle query's bieden twee para meters die u aan uw vereisten kunt aanpassen: 
- `MaxDegreeOfParallelism` Hiermee bepaalt u het maximum aantal partities waarmee gelijktijdig query's kunnen worden uitgevoerd. 
- `MaxBufferedItemCount` Hiermee bepaalt u het aantal vooraf opgehaalde resultaten.

***Afstemmings graad van parallelle uitvoering***

Parallelle query werkt door meerdere partities parallel te doorzoeken. Maar gegevens van een afzonderlijke partitie worden serieel opgehaald ten opzichte van de query. De instelling `MaxDegreeOfParallelism` in [SDK v2](sql-api-sdk-dotnet.md) tot het aantal partities is de beste kans om de meest uitvoerende query te bereiken, op voor waarde dat alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u de mate van parallelle uitvoering instellen op een hoog getal. Het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de mate van parallelle uitvoering.

Parallelle query's produceren het meest voor deel als de gegevens gelijkmatig worden verdeeld over alle partities met betrekking tot de query. Als de gepartitioneerde verzameling is gepartitioneerd zodat alle of de meeste gegevens die door een query zijn geretourneerd, in een paar partities worden geconcentreerd (één partitie is het ergste geval), kunnen deze partities de prestaties van de query opsporen.

***MaxBufferedItemCount afstemmen***
    
Parallelle query is ontworpen om de resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Deze vooraf ophalen helpt de algehele latentie van een query te verbeteren. De `MaxBufferedItemCount` para meter beperkt het aantal vooraf opgehaalde resultaten. Stel `MaxBufferedItemCount` in op het verwachte aantal geretourneerde resultaten (of een hoger getal), zodat de query het maximale voor deel van het vooraf ophalen kan ontvangen.

Het vooraf ophalen werkt op dezelfde manier, ongeacht de mate van parallellisme, en er is één buffer voor de gegevens van alle partities.  

**Uitstel implementeren met RetryAfter-intervallen**

Tijdens de prestatie tests moet u de belasting verg Roten tot een klein aantal aanvragen wordt beperkt. Als aanvragen worden beperkt, moet de client toepassing worden uitgeschakeld op een vertraging voor het door de server opgegeven interval voor nieuwe pogingen. Door de uitstel te respecteren, zorgt u ervoor dat u een minimale hoeveelheid tijd kunt wachten tussen nieuwe pogingen. 

Ondersteuning voor het beleid voor opnieuw proberen is opgenomen in deze Sdk's:
- Versie 1.8.0 en hoger van de [.NET SDK voor SQL](sql-api-sdk-dotnet.md) en de [Java SDK voor SQL](sql-api-sdk-java.md)
- Versie 1.9.0 en hoger van de [Node.js SDK voor SQL](sql-api-sdk-node.md) en de [python-SDK voor SQL](sql-api-sdk-python.md)
- Alle ondersteunde versies van de [.net core](sql-api-sdk-dotnet-core.md) sdk's 

Zie [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter)voor meer informatie.
    
In versie 1,19 en hoger van de .NET SDK is er een mechanisme voor het vastleggen van aanvullende diagnostische gegevens en latentie problemen bij problemen, zoals wordt weer gegeven in het volgende voor beeld. U kunt de diagnostische teken reeks registreren voor aanvragen met een hogere lees latentie. De vastgelegde diagnostische teken reeks helpt u inzicht te krijgen in het aantal keren dat u 429 fouten hebt ontvangen voor een bepaalde aanvraag.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Document-Uri's in cache opslaan voor minder lees latentie**

Cache waar mogelijk document-Uri's voor de beste Lees prestaties. U moet de logica definiëren om de resource-ID in de cache op te slaan wanneer u een resource maakt. Zoek acties op basis van resource-Id's zijn sneller dan op naam gebaseerde zoek acties, waardoor de prestaties worden verbeterd in de cache.

**De pagina grootte voor query's/feeds voor betere prestaties afstemmen**

Wanneer u een bulk-Lees bewerking uitvoert van documenten met behulp van de functie voor lees bewerkingen (bijvoorbeeld `ReadDocumentFeedAsync` ) of wanneer u een SQL-query uitgeeft, worden de resultaten op een gesegmenteerde manier geretourneerd als de resultatenset te groot is. Standaard worden resultaten geretourneerd in delen van 100 items of 1 MB, waarbij de limiet eerst wordt bereikt.

Om het aantal netwerk round trips te verminderen dat vereist is om alle toepasselijke resultaten op te halen, kunt u de pagina grootte verg Roten door [x-MS-maximum-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) te gebruiken om 1.000-headers aan te vragen. Als u slechts enkele resultaten wilt weer geven, bijvoorbeeld als uw gebruikers interface of toepassings-API slechts 10 resultaten per keer retourneert, kunt u ook de pagina grootte verkleinen tot 10 om de door Voer voor lees bewerkingen en query's te verminderen.

> [!NOTE] 
> De `maxItemCount` eigenschap mag niet alleen worden gebruikt voor paginering. Het belangrijkste gebruik is het verbeteren van de prestaties van query's door het maximum aantal items te verminderen dat op één pagina wordt geretourneerd.  

U kunt ook de pagina grootte instellen met behulp van de beschik bare Azure Cosmos DB Sdk's. Met de eigenschap [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount) in `FeedOptions` kunt u het maximum aantal items instellen dat in de opsommings bewerking moet worden geretourneerd. Wanneer `maxItemCount` is ingesteld op-1, detecteert de SDK automatisch de optimale waarde, afhankelijk van de grootte van het document. Bijvoorbeeld:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Wanneer een query wordt uitgevoerd, worden de resulterende gegevens verzonden binnen een TCP-pakket. Als u te laag een waarde opgeeft voor `maxItemCount` , is het aantal trips dat nodig is voor het verzenden van de gegevens in het TCP-pakket hoog. Dit is van invloed op de prestaties. Als u niet zeker weet welke waarde voor de eigenschap moet worden ingesteld, kunt u `maxItemCount` deze het beste instellen op-1 en de SDK de standaard waarde laten kiezen.

**Het aantal threads/taken verhogen**

Zie [het aantal threads/taken](#increase-threads) in het gedeelte netwerken van dit artikel verg Roten.

## <a name="indexing-policy"></a>Indexeringsbeleid
 
**Niet-gebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

Met het Azure Cosmos DB indexerings beleid kunt u ook opgeven welke document paden u wilt opnemen in of uitsluiten van indexeren met behulp van index paden (IndexingPolicy. IncludedPaths en IndexingPolicy. ExcludedPaths). Indexerings paden kunnen de schrijf prestaties verbeteren en de index opslag beperken voor scenario's waarin de query patronen vooraf bekend zijn. Dit komt doordat de index kosten direct overeenkomen met het aantal unieke paden dat is geïndexeerd. Deze code laat bijvoorbeeld zien hoe u een volledige sectie van de documenten (een substructuur) uitsluit van indexeren met behulp van het Joker teken ' * ':

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Zie [Azure Cosmos DB Indexing policies](index-policy.md)(Engelstalig) voor meer informatie.

## <a name="throughput"></a><a id="measure-rus"></a> Vracht

**Meten en afstemmen voor lagere aanvraag eenheden/tweede gebruik**

Azure Cosmos DB biedt een uitgebreide set database bewerkingen. Deze bewerkingen omvatten relationele en hiërarchische query's met Udf's, opgeslagen procedures en triggers, die allemaal op de documenten in een database verzameling worden uitgevoerd. De kosten voor elk van deze bewerkingen variëren, afhankelijk van de CPU, IO en het geheugen die nodig zijn om de bewerking te volt ooien. In plaats van te denken over en het beheren van hardwarebronnen, kunt u een aanvraag eenheid (RU) beschouwen als een enkele maat eenheid voor de resources die nodig zijn om verschillende database bewerkingen uit te voeren en een toepassings aanvraag te onderhouden.

De door Voer is ingericht op basis van het aantal ingestelde [aanvraag eenheden](request-units.md) voor elke container. Het verbruik van de aanvraag eenheid wordt geëvalueerd als een rente per seconde. Toepassingen die het aantal ingerichte aanvraag eenheden voor hun container overschrijden, zijn beperkt tot het aantal onder het ingerichte niveau voor de container daalt. Als voor uw toepassing een hogere door Voer is vereist, kunt u de door Voer verhogen door extra aanvraag eenheden in te richten.

De complexiteit van een query is van invloed op het aantal aanvraageenheden dat voor een bewerking wordt verbruikt. Het aantal predikaten, de aard van de predikaten, het aantal Udf's en de grootte van de bron gegevensset beïnvloeden alle de kosten van de query bewerkingen.

Als u de overhead van een bewerking (maken, bijwerken of verwijderen) wilt meten, inspecteert u de [x-MS-Request-](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) factuurkop tekst (of de equivalente `RequestCharge` eigenschap in `ResourceResponse\<T>` of `FeedResponse\<T>` in de .NET SDK) om het aantal aanvraag eenheden te meten dat door de bewerkingen wordt verbruikt:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

De aanvraag kosten die in deze header worden geretourneerd, zijn een fractie van uw ingerichte door Voer (dat wil zeggen 2.000 RUs/seconde). Als de voor gaande query bijvoorbeeld 1.000 1 KB-documenten retourneert, zijn de kosten van de bewerking 1.000. Binnen één seconde worden er dus slechts twee aanvragen door de server gehonoreerd voordat het aantal latere aanvragen wordt beperkt. Zie [aanvraag eenheden](request-units.md) en de [reken eenheid voor aanvragen](https://www.documentdb.com/capacityplanner)voor meer informatie.
<a id="429"></a>

**Aantal verwerkings frequenties/aanvragen afhandelen te groot**

Wanneer een client de gereserveerde door Voer voor een account probeert te overschrijden, is er geen prestatie vermindering op de server en wordt er geen gebruik van de doorvoer capaciteit meer dan het gereserveerde niveau. De server preventief de aanvraag met RequestRateTooLarge te beëindigen (HTTP-status code 429). Het retourneert een [x-MS-retry-after-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) -header die de hoeveelheid tijd, in milliseconden, aangeeft dat de gebruiker moet wachten voordat de aanvraag opnieuw wordt geprobeerd.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

De Sdk's ondervangen dit antwoord impliciet, respecteert de door de server opgegeven nieuwe poging na de header en voert de aanvraag opnieuw uit. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

Als u meer dan één client cumulatief op dezelfde manier hebt uitgevoerd, is het standaard aantal nieuwe pogingen dat momenteel is ingesteld op 9 intern door de client mogelijk niet voldoende. In dit geval genereert de client een DocumentClientException met de status code 429 naar de toepassing. 

U kunt het standaard aantal nieuwe pogingen wijzigen door de `RetryOptions` op het exemplaar in te stellen `ConnectionPolicy` . Standaard wordt de DocumentClientException met de status code 429 geretourneerd na een cumulatieve wacht tijd van 30 seconden als de aanvraag boven het aanvraag aantal blijft. Deze fout wordt weer gegeven zelfs wanneer het huidige aantal nieuwe pogingen kleiner is dan het maximum aantal nieuwe pogingen, of de huidige waarde de standaard instelling is van 9 of een door de gebruiker gedefinieerde waarde.

Het gedrag voor automatische pogingen helpt de flexibiliteit en bruikbaarheid voor de meeste toepassingen te verbeteren. Het is echter mogelijk niet het beste gedrag wanneer u prestatie benchmarks uitvoert, met name wanneer u een latentie meet. De door de client waargenomen latentie krijgt een waarschuwing als het experiment de server beperking bereikt en zorgt ervoor dat de client-SDK op de achtergrond opnieuw probeert. Om latentie pieken te voor komen tijdens prestatie experimenten, meet u de kosten die worden geretourneerd door elke bewerking en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraag tarief vallen. Zie [aanvraag eenheden](request-units.md)voor meer informatie.

**Voor een hogere door Voer ontwerpen voor kleinere documenten**

De aanvraag kosten (dat wil zeggen, de aanvraag verwerkings kosten) van een bepaalde bewerking correleert direct naar de grootte van het document. Bewerkingen voor grote documenten kosten meer dan bewerkingen op kleine documenten.

## <a name="next-steps"></a>Volgende stappen

Zie [prestaties en schalen testen met Azure Cosmos DB](performance-testing.md)voor een voorbeeld toepassing die wordt gebruikt om Azure Cosmos DB te evalueren voor scenario's met hoge prestaties op een aantal client computers.

Zie [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal baarheid en hoge prestaties.