---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & resources'
description: Meer informatie over de SQL Node.js API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: a3e21abe2f4ed24726256689af16b48ed6721ce8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366144"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK voor SQL API: opmerkingen bij de release en resources
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET wijzigingenfeed-SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP-connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerprogramma - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Koppeling  |
|---------|---------|
|SDK downloaden  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-documentatie  |  [Naslagdocumentatie voor JavaScript SDK](/javascript/api/%40azure/cosmos/)
|SDK-installatie-instructies  |  [Installatie-instructies](https://github.com/Azure/azure-sdk-for-js)
|Bijdragen aan de SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Voorbeelden | [Node.js codevoorbeelden](sql-api-nodejs-samples.md)
| Zelfstudie Aan de slag | [Aan de slag met de JavaScript SDK](sql-api-nodejs-get-started.md)
| Zelfstudie voor web-apps | [Een Node.js webtoepassing bouwen met behulp van Azure Cosmos DB](sql-api-nodejs-application.md)
| Huidig ondersteund platform | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK-versie 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK-versie 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK-versie 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK-versie 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK versie 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK versie 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK versie 1.x.x

## <a name="release-notes"></a>Opmerkingen bij de release

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Stel de standaardwaarde ResponseContinuationTokenLimitInKB in op 1 kB. Standaard beperken we dit tot 1 kB om lange headers te voorkomen (Node.js een globale headergroottelimiet heeft). Een gebruiker kan dit veld zo instellen dat langere headers mogelijk zijn, waardoor de uitvoering van de back-end-query kan worden geoptimaliseerd.
* Schakel disableSSLVerification uit. Deze optie heeft nieuwe alternatieven die worden beschreven in [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* InitialHeaders toestaan om expliciet de header van de partitiesleutel in te stellen
* Gebruik package.json#files om te voorkomen dat overbodige bestanden worden gepubliceerd
* Fout bij sorteren van routeringskaart in oudere versie van node+v8 opgelost
* Lost de fout op wanneer de gebruiker gedeeltelijke opties voor opnieuw proberen levert

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Voorkomen dat webpack modules die worden aangeroepen met vereisen kan oplossen

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Lost een langdurige openstaande fout op waarbij ER's altijd als 0 werden gerapporteerd voor statistische query's

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 versie v3! 🎉 veel nieuwe functies, oplossingen voor fouten en enkele belangrijke wijzigingen. Primaire doelstellingen van deze release:

* Belangrijke nieuwe functies implementeren
  * DISTINCT-query's
  * LIMIT/OFFSET-query's
  * Aanvragen die door de gebruiker kunnen worden geannuleerd
* Bijwerken naar de nieuwste versie van Cosmos REST API, waarbij alle containers onbeperkt kunnen worden geschaald
* Het gebruik van Cosmos vereenvoudigen vanuit de browser
* Beter afgestemd op de nieuwe Richtlijnen voor de Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Migratiehandleiding voor belangrijke wijzigingen
##### <a name="improved-client-constructor-options"></a>Verbeterde opties voor client constructor

Constructoropties zijn vereenvoudigd:

* De naam van masterKey is gewijzigd en is verplaatst naar het hoogste niveau
* Eigenschappen die eerder onder options.auth werden gebruikt, zijn verplaatst naar het hoogste niveau

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Vereenvoudigde QueryIterator-API
In v2 waren er veel verschillende manieren om resultaten van een query te itereren of op te halen. We hebben geprobeerd de v3-API te vereenvoudigen en vergelijkbare of dubbele API's te verwijderen:

* Verwijder iterator.next() en iterator.current(). Gebruik fetchNext() om pagina's met resultaten op te halen.
* Verwijder iterator.forEach(). Gebruik in plaats daarvan async iterators.
* iterator.exevernoemd in iterator.fetchNext()
* iterator.toArray() hernoemd in iterator.fetchAll()
* Pagina's zijn nu de juiste antwoordobjecten in plaats van gewone JS-objecten
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Vaste containers zijn nu gepartities
De Cosmos-service ondersteunt nu partitiesleutels voor alle containers, inclusief de containers die eerder zijn gemaakt als vaste containers. De v3 SDK wordt bijgewerkt naar de nieuwste API-versie die deze wijziging implementeert, maar deze wordt niet gewijzigd. Als u geen partitiesleutel voor bewerkingen oplevert, wordt standaard een systeemsleutel gebruikt die werkt met al uw bestaande containers en documenten.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert verwijderd voor opgeslagen procedures
Voorheen was upsert toegestaan voor niet-gepart partitioneerde verzamelingen, maar met de update van de API-versie worden alle verzamelingen gepartitiefd, zodat we deze volledig hebben verwijderd.

##### <a name="item-reads-will-not-throw-on-404"></a>Leesitem wordt niet op 404 gooit
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Standaard schrijven in meerdere regio's
De SDK schrijft nu standaard naar meerdere regio's als uw Cosmos-configuratie dit ondersteunt. Dit was voorheen opt-in-gedrag.

##### <a name="proper-error-objects"></a>Juiste foutobjecten
Mislukte aanvragen geven nu de juiste fout of subklassen Fout. Voorheen maakten ze platte JS-objecten.

#### <a name="new-features"></a>Nieuwe functies
##### <a name="user-cancelable-requests"></a>Aanvragen die door de gebruiker kunnen worden geannuleerd
Door intern op te halen kunnen we de AbortController-API van de browser gebruiken ter ondersteuning van bewerkingen die door de gebruiker kunnen worden geannuleerd. In het geval van bewerkingen waarbij meerdere aanvragen mogelijk worden uitgevoerd (zoals partitieoverschrijdende query's), worden alle aanvragen voor de bewerking geannuleerd. Moderne browsergebruikers hebben AbortController al. Node.js moeten een polyfill-bibliotheek gebruiken

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Doorvoer instellen als onderdeel van de maakbewerking db/container
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Het genereren van header-token is opgesplitst in een nieuwe bibliotheek, @azure/cosmos-sign . Iedereen die de Cosmos REST API, kan dit gebruiken om headers te ondertekenen met dezelfde code die we in @azure/cosmos aanroepen.

##### <a name="uuid-for-generated-ids"></a>UUID voor gegenereerde ID's
v2 had aangepaste code voor het genereren van item-ID's. We zijn overgeschakeld naar de bekende en onderhouden communitybibliotheek.

##### <a name="connection-strings"></a>Verbindingsreeksen
Het is nu mogelijk om een connection string van de Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Verbeterde browserervaring
Hoewel het mogelijk was om de v2 SDK in de browser te gebruiken, was dit geen ideale ervaring. U moest meerdere ingebouwde node.js invullen en een bundelaar zoals webpack of Bundle gebruiken. De v3 SDK maakt de out-of-the-box-ervaring veel beter voor browsergebruikers.

* Interne aanvraag vervangen door ophalen (#245)
* Gebruik van Buffer verwijderen (#330)
* Gebruik van ingebouwd knooppunt verwijderen in plaats van universele pakketten/API's (#328)
* Overschakelen naar node-abort-controller (#294)

#### <a name="bug-fixes"></a>Opgeloste fouten
* Probleem opgelost met aanbiedingstests voor lezen en terug brengen (#224)
* Probleem met EnableEndpointDiscovery (#207) opgelost
* Ontbrekende RUs voor gepargineerde resultaten herstellen (#360)
* Vouw het type SQL-queryparameter uit (#346)
* TTL toevoegen aan ItemDefinition (#341)
* Metrische CP-querygegevens (#311)
* ActivityId toevoegen aan FeedResponse (#293)
* Schakel _ts van tekenreeks naar getal (#252)(#295)
* Aggregatie van aanvraagkosten (#289) opgelost
* Lege tekenreekspartitiesleutels toestaan (#277)
* Tekenreeks toevoegen aan conflictquerytype (#237)
* uniqueKeyPolicy toevoegen aan container (#234)

#### <a name="engineering-systems"></a>Technische systemen
Niet altijd de meest zichtbare wijzigingen, maar ze helpen ons team sneller betere code te verzenden.

* Rollup gebruiken voor productiebuits (#104)
* Bijwerken naar Typescript 3.5 (#327)
* Converteren naar TS-projectverwijzingen. Testmap extraheren (#270)
* NoUnusedLocals en noUnusedParameters inschakelen (#275)
* Azure Pipelines YAML voor CI-builds (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Er zijn geen codewijzigingen. Lost een probleem op waarbij een aantal extra bestanden zijn opgenomen in het pakket 2.1.4.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Regionale failover binnen het beleid voor opnieuw proberen oplossen
* De eigenschap ChangeFeed hasMoreResults is opgelost
* Updates voor dev-afhankelijkheid
* Een PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Schakel _ts van tekenreeks naar getal
* Standaardindexeringstests herstellen
* UniqueKeyPolicy terugporteren naar v2
* Foutopsporingsfixes voor demo's en demo's

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Oplossingen voor backportaanbiedingen van v3-vertakking
* Fout opgelost in handtekening van executeNext()-type
* Typfoutfixes

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Bouw herstructureren. Hiermee kunt u de SDK-versie tijdens het bouwen binnenhalen.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Nieuwe functies
* Ondersteuning voor ChangeFeed toegevoegd (#196)
* MultiPolygon-gegevenstype toegevoegd voor indexering (#191)
* Voeg de eigenschap 'sleutel' toe aan de constructor als alias voor masterKey (#202)

#### <a name="fixes"></a>Oplossingen
* Er is een fout opgelost waarbij next() een onjuiste waarde retourneert op de iterator

#### <a name="engineering-improvements"></a>Technische verbeteringen
* Integratietest toevoegen voor typescriptverbruik (#199)
* Installatie rechtstreeks vanuit GitHub inschakelen (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Hiermee voegt u de interface toe voor het agenttype van het knooppunt. TypeScript-gebruikers moeten niet langer installeren @types/node als een afhankelijkheid
* Voorkeurslocaties worden nu op de juiste wijze gehonoreerd
* Verbeteringen in bijdragen aan documentatie voor ontwikkelaars
* Verschillende typfoutfixes

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Probleem met typedefinitie opgelost dat is geïntroduceerd in 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Afhankelijkheid `big-integer` verwijderen
* Schakel over naar referentie-instructies voor het type AsyncIterable. Typescript-gebruikers moeten hun lib-instelling niet meer aanpassen.
* Typfoutfixes

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Leesmij-koppelingen herstellen

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Implementatie van interface voor opnieuw proberen herstellen

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* GA van versie 2.0.0 van de JavaScript SDK
* Ondersteuning toegevoegd voor schrijfingen in meerdere regio's.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 van versie 2.0.0 van de JavaScript SDK voor openbare preview.
* Nieuw objectmodel, met CosmosClient op het hoogste niveau en methoden gesplitst in relevante database-, container- en itemklassen. 
* Ondersteuning voor [promises](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK geconverteerd naar TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* NPM-documentatie is opgelost.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Er is ondersteuning toegevoegd voor standaard nieuwe proberen bij verbindingsproblemen.
* Ondersteuning toegevoegd voor het lezen van de verzamelingswijzigingsfeed.
* Probleem met sessieconsistentie opgelost dat af en toe 'leessessie niet beschikbaar' heeft veroorzaakt.
* Ondersteuning toegevoegd voor metrische querygegevens.
* Het maximale aantal verbindingen van de HTTP-agent is gewijzigd.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Documentatie bijgewerkt om te verwijzen Azure Cosmos DB in plaats van Azure DocumentDB.
* Ondersteuning toegevoegd voor proxyUrl-instelling in ConnectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Kleine oplossing voor bestandssystemen die gevoelig zijn voor kleine problemen.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Voegt ondersteuning toe voor sessieconsistentie.
* Voor deze SDK-versie is de nieuwste versie [van Azure Cosmos DB Emulator vereist.](https://aka.ms/cosmosdb-emulator)

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Splits proofed partitie-query's.
* Voegt ondersteuning toe voor resourcekoppeling met voor- en nas slashes (en bijbehorende tests).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    NPM-documentatie is opgelost.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Er is een fout opgelost in executeStoredProcedure waarbij betrokken documenten speciale Unicode-tekens hadden (LS, PS).
* Er is een fout opgelost bij het verwerken van documenten met Unicode-tekens in de partitiesleutel.
* Ondersteuning voor het maken van verzamelingen met de naammedia is opgelost. GitHub-probleem #114.
* Ondersteuning voor machtigingsautorisatie-token opgelost. GitHub-probleem #178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Ondersteuning toegevoegd voor een nieuw [consistentieniveau met de](consistency-levels.md) naam ConsistentPrefix.
* Ondersteuning toegevoegd voor UriFactory.
* Er is een Unicode-ondersteuningsbug opgelost. GitHub-probleem #171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Ondersteuning toegevoegd voor aggregatiequery's (COUNT, MIN, MAX, SUM en AVG).
* De optie toegevoegd voor het beheren van de mate van parallellelisme voor query's over verschillende partities.
* De optie toegevoegd voor het uitschakelen van TLS-verificatie bij het uitvoeren op Azure Cosmos DB Emulator.
* De minimale doorvoer voor gepartities is verlaagd van 10.100 RU/s naar 2500 RU/s.
* De fout in het vervolg-token voor het verzamelen van één partitie is opgelost. GitHub-probleem #107.
* De executeStoredProcedure-fout in de verwerking van 0 als één param is opgelost. GitHub-probleem #155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* De header van de gebruikersagent is opgelost om de SDK-versie op te nemen.
* Kleine opschoning van code.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* TLS-verificatie uitschakelen wanneer u de SDK gebruikt om de emulator (hostnaam=localhost) als doel te gebruiken.
* Ondersteuning toegevoegd voor het inschakelen van scriptlogboekregistratie tijdens de uitvoering van de opgeslagen procedure.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Ondersteuning toegevoegd voor parallelle query's voor kruislingse partities.
* Ondersteuning toegevoegd voor TOP/ORDER BY-query's voor gepartities.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Ondersteuning voor beleid voor opnieuw proberen toegevoegd voor beperkt aantal aanvragen. (Beperkt aanvragen ontvangen een uitzondering met een te hoge aanvraagsnelheid, foutcode 429.) Standaard worden Azure Cosmos DB keer opnieuw ingediend voor elke aanvraag wanneer foutcode 429 wordt aangetroffen, met respect voor de nieuwe pogingNa de tijd in de antwoordheader. Er kan nu een vast interval voor nieuwe poging worden ingesteld als onderdeel van de eigenschap RetryOptions op het ConnectionPolicy-object als u de nieuwe poging wilt negerenNa de tijd die is geretourneerd door de server tussen de nieuwe poging. Azure Cosmos DB wacht nu maximaal 30 seconden op elke aanvraag die wordt beperkt (ongeacht het aantal nieuwe poging) en retourneert het antwoord met foutcode 429. Deze tijd kan ook worden overschrijven in de eigenschap RetryOptions van het object ConnectionPolicy.
* Cosmos DB retourneert nu x-ms-throttle-retry-count en x-ms-throttle-retry-wait-time-ms als de antwoordheaders in elke aanvraag om het aantal nieuwe vertragingen aan te geven en de cumulatieve tijd dat de aanvraag heeft gewacht tussen de nieuwe poging.
* De klasse RetryOptions is toegevoegd, zodat de eigenschap RetryOptions wordt weergegeven in de klasse ConnectionPolicy, die kan worden gebruikt om een aantal van de standaardopties voor opnieuw proberen te overschrijven.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Ondersteuning toegevoegd voor databaseaccounts voor meerdere regio's.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Ondersteuning toegevoegd voor TTL-functie (Time To Live) voor documenten.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Gepartitiesteerde verzamelingen en](partitioning-overview.md) [door de gebruiker gedefinieerde prestatieniveaus geïmplementeerd.](performance-levels.md)

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Er is een fout opgelost in RangePartitionResolver.resolveForRead waarbij geen koppelingen worden retourneerd vanwege een slechte concat van resultaten.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* HashPartitionResolver resolveForRead(): wanneer er geen partitiesleutel werd opgegeven, werd er een uitzondering gegenereerd, in plaats van een lijst met alle geregistreerde koppelingen te retourneren.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Probleem opgelost [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - Toegewezen HTTPS-agent: vermijd het wijzigen van de globale agent voor Azure Cosmos DB doeleinden. Gebruik een toegewezen agent voor alle aanvragen van de bibliotheek.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Probleem opgelost [#81:](https://github.com/Azure/azure-documentdb-node/issues/81) streepjes in media-ID's correct verwerken.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Probleem opgelost [#95:](https://github.com/Azure/azure-documentdb-node/issues/95) waarschuwing over lekken van EventEmitter-listener.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Probleem opgelost [#92-](https://github.com/Azure/azure-documentdb-node/issues/90) wijzig de naam van de map Hash in hash voor case-gevoelige systemen.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implementeert ondersteuning voor sharding door hash-& bereikpartitie resolvers toe te voegen.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert implementeren. Nieuwe upsertXXX-methoden op documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Overgeslagen om versienummers in overeenstemming met andere SDK's te brengen.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Splits Q promises wrapper naar nieuwe opslagplaats.
* Werk bij naar het pakketbestand voor het NPM-register.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementeert routering op basis van id's.
* Probleem opgelost [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - huidige eigenschapsconflicten met methode current().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Ondersteuning toegevoegd voor georuimtelijke index.
* Valideert de id-eigenschap voor alle resources. De ID's voor resources mogen geen ?, /, #,  &#47;&#47; tekens bevatten of eindigen met een spatie.
* Nieuwe header 'voortgang van indextransformatie' toegevoegd aan ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementeert V2-indexeringsbeleid.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Probleem [#40:](https://github.com/Azure/azure-documentdb-node/issues/40) eslint- en grutconfiguraties geïmplementeerd in de core- en promise-SDK.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Probleem [#45-](https://github.com/Azure/azure-documentdb-node/issues/45) Promises-wrapper bevat geen header met fout.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* De mogelijkheid om conflicten op te vragen is geïmplementeerd door readConflicts, readConflictAsync en queryConflicts toe te voegen.
* Bijgewerkte API-documentatie.
* Probleem [#41-](https://github.com/Azure/azure-documentdb-node/issues/41) client.createDocumentAsync-fout.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Release-& datums van uittreding

Microsoft geeft ten minste **12** maanden voordat een SDK wordt gestopt een melding om de overgang naar een nieuwere/ondersteunde versie te soepeler te laten verlopen. Nieuwe functies en functionaliteiten en optimaliseringen worden alleen toegevoegd aan de huidige SDK. Het wordt daarom aangeraden altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren.

| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 juli 2019 |--- |
| [3.0.4](#3.0.4) |22 juli 2019 |--- |
| [3.0.3](#3.0.3) |17 juli 2019 |--- |
| [3.0.2](#3.0.2) |9 juli 2019 |--- |
| [3.0.0](#3.0.0) |28 juni 2019 |--- |
| [2.1.5](#2.1.5) |20 maart 2019 |--- |
| [2.1.4](#2.1.4) |15 maart 2019 |--- |
| [2.1.3](#2.1.3) |8 maart 2019 |--- |
| [2.1.2](#2.1.2) |28 januari 2019 |--- |
| [2.1.1](#2.1.1) |5 december 2018 |--- |
| [2.1.0](#2.1.0) |4 december 2018 |--- |
| [2.0.5](#2.0.5) |7 november 2018 |--- |
| [2.0.4](#2.0.4) |30 oktober 2018 |--- |
| [2.0.3](#2.0.3) |30 oktober 2018 |--- |
| [2.0.2](#2.0.2) |10 oktober 2018 |--- |
| [2.0.1](#2.0.1) |25 september 2018 |--- |
| [2.0.0](#2.0.0) |September 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 augustus 2018 |--- |
| [1.14.4](#1.14.4) |03 mei 2018 |30 augustus 2020 |
| [1.14.3](#1.14.3) |3 mei 2018 |30 augustus 2020 |
| [1.14.2](#1.14.2) |21 december 2017 |30 augustus 2020 |
| [1.14.1](#1.14.1) |10 november 2017 |30 augustus 2020 |
| [1.14.0](#1.14.0) |9 november 2017 |30 augustus 2020 |
| [1.13.0](#1.13.0) |11 oktober 2017 |30 augustus 2020 |
| [1.12.2](#1.12.2) |10 augustus 2017 |30 augustus 2020 |
| [1.12.1](#1.12.1) |10 augustus 2017 |30 augustus 2020 |
| [1.12.0](#1.12.0) |10 mei 2017 |30 augustus 2020 |
| [1.11.0](#1.11.0) |16 maart 2017 |30 augustus 2020 |
| [1.10.2](#1.10.2) |27 januari 2017 |30 augustus 2020 |
| [1.10.1](#1.10.1) |22 december 2016 |30 augustus 2020 |
| [1.10.0](#1.10.0) |03 oktober 2016 |30 augustus 2020 |
| [1.9.0](#1.9.0) |7 juli 2016 |30 augustus 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 augustus 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augustus 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 augustus 2020 |
| [1.5.6](#1.5.6) |8 maart 2016 |30 augustus 2020 |
| [1.5.5](#1.5.5) |2 februari 2016 |30 augustus 2020 |
| [1.5.4](#1.5.4) |1 februari 2016 |30 augustus 2020 |
| [1.5.2](#1.5.2) |26 januari 2016 |30 augustus 2020 |
| [1.5.2](#1.5.2) |22 januari 2016 |30 augustus 2020 |
| [1.5.1](#1.5.1) |4 januari 2016 |30 augustus 2020 |
| [1.5.0](#1.5.0) |31 december 2015 |30 augustus 2020 |
| [1.4.0](#1.4.0) |06 oktober 2015 |30 augustus 2020 |
| [1.3.0](#1.3.0) |06 oktober 2015 |30 augustus 2020 |
| [1.2.2](#1.2.2) |10 september 2015 |30 augustus 2020 |
| [1.2.1](#1.2.1) |15 augustus 2015 |30 augustus 2020 |
| [1.2.0](#1.2.0) |5 augustus 2015 |30 augustus 2020 |
| [1.1.0](#1.1.0) |9 juli 2015 |30 augustus 2020 |
| [1.0.3](#1.0.3) |4 juni 2015 |30 augustus 2020 |
| [1.0.2](#1.0.2) |23 mei 2015 |30 augustus 2020 |
| [1.0.1](#1.0.1) |15 mei 2015 |30 augustus 2020 |
| [1.0.0](#1.0.0) |8 april 2015 |30 augustus 2020 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.