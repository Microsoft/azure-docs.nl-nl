---
title: Zoeken in Azure Cosmos DB gegevens
titleSuffix: Azure Cognitive Search
description: Gegevens importeren uit Azure Cosmos DB naar een Doorzoek bare index in azure Cognitive Search. Indexeer functies automatiseren gegevens opname voor geselecteerde gegevens bronnen, zoals Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 563edae0292062e1ed7f216c69aeeb84ef0fa7a8
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119472"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Cosmos DB-gegevens indexeren met behulp van een indexeerfunctie in Azure Cognitive Search 

> [!IMPORTANT] 
> SQL API is algemeen beschikbaar.
> MongoDB-API, Gremlin-API en Cassandra-API-ondersteuning zijn momenteel beschikbaar als open bare preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. U kunt toegang tot de voor beelden aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. 
> [Rest API Preview-versies](search-api-preview.md) bieden deze functies. Er is momenteel beperkte ondersteuning voor portals en geen ondersteuning voor de .NET-SDK.

> [!WARNING]
> Alleen Cosmos DB verzamelingen waarvoor een [indexerings beleid](../cosmos-db/index-policy.md) is ingesteld op [consistent](../cosmos-db/index-policy.md#indexing-mode) , worden ondersteund door Azure Cognitive Search. Het indexeren van verzamelingen met een Lazy-indexerings beleid wordt niet aanbevolen en kan leiden tot ontbrekende gegevens. Verzamelingen waarvoor indexeren is uitgeschakeld, worden niet ondersteund.

In dit artikel wordt beschreven hoe u een Azure Cosmos DB [Indexeer functie](search-indexer-overview.md) configureert om inhoud te extra heren en deze te doorzoeken in azure Cognitive Search. Met deze werk stroom wordt een Azure Cognitive Search-index gemaakt en wordt deze geladen met bestaande tekst die is geëxtraheerd uit Azure Cosmos DB. 

Omdat de terminologie verwarrend kan zijn, is het een goed idee dat [Azure Cosmos DB indexering](../cosmos-db/index-overview.md) en [Azure Cognitive Search indexeren](search-what-is-an-index.md) verschillende bewerkingen zijn, uniek zijn voor elke service. Voordat u begint met het indexeren van Azure Cognitive Search, moet uw Azure Cosmos DB-data base al bestaan en gegevens bevatten.

Met de Cosmos DB Indexeer functie in azure Cognitive Search kunt u [Azure Cosmos DB-items](../cosmos-db/account-databases-containers-items.md#azure-cosmos-items) die toegankelijk zijn via verschillende protocollen, verkennen. 

+ Voor [SQL API](../cosmos-db/sql-query-getting-started.md), die algemeen beschikbaar is, kunt u de [Portal](#cosmos-indexer-portal), [rest API](/rest/api/searchservice/indexer-operations)of [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) gebruiken om de gegevens bron en Indexeer functie te maken.

+ Voor de [MongoDb-API (preview)](../cosmos-db/mongodb-introduction.md)kunt u de [portal](#cosmos-indexer-portal) of de [rest API versie 2020-06-30-preview](search-api-preview.md) gebruiken om de gegevens bron en Indexeer functie te maken.

+ Voor [Cassandra-API (preview)](../cosmos-db/cassandra-introduction.md) en [Gremlin-API (preview)](../cosmos-db/graph-introduction.md)kunt u alleen de [rest API versie 2020-06-30-preview](search-api-preview.md) gebruiken om de gegevens bron en Indexeer functie te maken.


> [!Note]
> U kunt een stem op de gebruikers stem voor de [Table-API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) casten als u deze wilt zien in azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Gebruik de portal

> [!Note]
> De portal biedt momenteel ondersteuning voor de SQL-API en de MongoDB-API (preview).

De eenvoudigste methode voor het indexeren van Azure Cosmos DB items is het gebruik van een wizard in de [Azure Portal](https://portal.azure.com/). Door gegevens te bemonsteren en meta gegevens te lezen in de container, kunt u de wizard [**gegevens importeren**](search-import-data-portal.md) in azure Cognitive Search een standaard index maken, bron velden toewijzen aan doel index velden en de index in één bewerking laden. Afhankelijk van de grootte en complexiteit van de bron gegevens, kunt u in slechts enkele minuten een werk index voor het doorzoeken van de volledige tekst hebben.

U kunt het beste dezelfde regio of locatie gebruiken voor zowel Azure Cognitive Search als Azure Cosmos DB voor lagere latentie en om bandbreedte kosten te voor komen.

### <a name="1---prepare-source-data"></a>1: Bron gegevens voorbereiden

U moet beschikken over een Cosmos DB account, een Azure Cosmos DB-Data Base die is toegewezen aan de SQL-API, MongoDB API (preview) of Gremlin-API (preview) en inhoud in de-data base.

Zorg ervoor dat uw Cosmos DB-data base gegevens bevat. Met de [wizard gegevens importeren](search-import-data-portal.md) worden meta gegevens gelezen en gegevens bemonstering uitgevoerd om een index schema af te leiden, maar worden ook gegevens uit Cosmos DB geladen. Als de gegevens ontbreken, wordt de wizard gestopt met deze fout ' fout bij het detecteren van het index schema van de gegevens Bron: kan geen prototype-index samen stellen omdat gegevens bron ' emptycollection ' geen gegevens heeft geretourneerd '.

### <a name="2---start-import-data-wizard"></a>2-wizard gegevens importeren starten

U kunt [de wizard starten](search-import-data-portal.md) vanaf de opdracht balk op de pagina Azure Cognitive Search service, of als u verbinding maakt met Cosmos DB SQL-API, kunt u klikken op **Azure toevoegen Cognitive Search** in het gedeelte **instellingen** van het navigatie deel venster van uw Cosmos DB account.

   ![De opdracht gegevens importeren in de portal](./media/search-import-data-portal/import-data-cmd2.png "De wizard Gegevens importeren starten")

### <a name="3---set-the-data-source"></a>3: de gegevens bron instellen

Op de **gegevens bron** pagina moet de bron **Cosmos DB** zijn, met de volgende specificaties:

+ **Naam** is de naam van het gegevens bron object. Nadat u deze hebt gemaakt, kunt u deze voor andere werk belastingen kiezen.

+ **Cosmos DB-account** moet een van de volgende indelingen hebben:
    1. De primaire of secundaire connection string van Cosmos DB met de volgende indeling: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` .
        + Voor de **MongoDb-verzamelingen** van versie 3,2 en versie 3,6 gebruikt u de volgende indeling voor het Cosmos DB-account in de Azure portal: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
        + Voor **Gremlin-grafieken en Cassandra-tabellen** meldt u zich aan voor de preview-versie van de [Indexeer functie](https://aka.ms/azure-cognitive-search/indexer-preview) om toegang te krijgen tot de preview-versie en informatie over het format teren van de referenties.
    1.  Een beheerde identiteit connection string met de volgende indeling die geen account sleutel bevat: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` . Als u deze connection string notatie wilt gebruiken, volgt u de instructies voor het [instellen van een indexer-verbinding met een Cosmos DB-Data Base met behulp van een beheerde identiteit](search-howto-managed-identities-cosmos-db.md).

+ **Data Base** is een bestaande data base van het account. 

+ **Verzameling** is een container met documenten. Er moeten documenten zijn om de import bewerking te kunnen volt ooien. 

+ De **query** kan leeg zijn als u alle documenten wilt, anders kunt u een query invoeren waarmee een document deel verzameling wordt geselecteerd. De **query** is alleen beschikbaar voor de SQL-API.

   ![Definitie van gegevens bron Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definitie van gegevens bron Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-de pagina verrijkte inhoud in de wizard overs Laan

Het toevoegen van cognitieve vaardig heden (of verrijking) is geen import vereiste. Tenzij u een specifieke behoefte hebt om [AI-verrijking toe te voegen](cognitive-search-concept-intro.md) aan uw indexerings pijplijn, moet u deze stap overs Laan.

Als u de stap wilt overs Laan, klikt u op de blauwe knoppen onder aan de pagina voor ' volgende ' en ' overs Laan '.

### <a name="5---set-index-attributes"></a>5-index kenmerken instellen

Op de pagina **index** ziet u een lijst met velden met een gegevens type en een reeks selectie vakjes voor het instellen van index kenmerken. Met de wizard kunt u een lijst met velden genereren op basis van meta gegevens en door de bron gegevens te bemonsteren. 

U kunt kenmerken bulksgewijs selecteren door te klikken op het selectie vakje boven aan een kenmerk kolom. Kies **ophalen** en **doorzoekbaar** voor elk veld dat moet worden geretourneerd naar een client-app en waarop de volledige Zoek verwerking van de tekst van toepassing is. U zult merken dat gehele getallen geen volledige tekst of fuzzy Doorzoek bare waarden zijn (getallen worden geëvalueerd Verbatim en zijn vaak nuttig in filters).

Lees de beschrijving van [index kenmerken](/rest/api/searchservice/create-index#bkmk_indexAttrib) en [taal analyse](/rest/api/searchservice/language-support) functies voor meer informatie. 

Neem even de tijd om uw selecties te controleren. Nadat u de wizard hebt uitgevoerd, worden er fysieke gegevens structuren gemaakt en kunt u deze velden niet bewerken zonder alle objecten te verwijderen en opnieuw te maken.

   ![Index definitie Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Index definitie Cosmos DB")

### <a name="6---create-indexer"></a>6-Indexeer functie maken

Volledig opgegeven, maakt de wizard drie afzonderlijke objecten in uw zoek service. Een gegevens bron object en Index object worden opgeslagen als benoemde resources in uw Azure Cognitive Search-service. Met de laatste stap maakt u een Indexeer functie-object. Als u de Indexeer functie een naam geeft, kan deze bestaan als een zelfstandige resource, die u onafhankelijk van het index-en gegevens bron object kunt plannen en beheren, gemaakt in dezelfde wizard reeks.

Als u niet bekend bent met Indexeer functies, is een *Indexeer functie* een resource in azure Cognitive Search die een externe gegevens bron verkent voor Doorzoek bare inhoud. De uitvoer van de wizard **gegevens importeren** is een Indexeer functie waarmee uw Cosmos DB gegevens bron wordt verkend, Doorzoek bare inhoud wordt geëxtraheerd en geïmporteerd in een index op Azure Cognitive Search.

De volgende scherm afbeelding toont de standaard configuratie van de Indexeer functie. U kunt overschakelen naar een **keer** als u de Indexeer functie één keer wilt uitvoeren. Klik op **verzenden** om de wizard uit te voeren en alle objecten te maken. Indexering begint onmiddellijk.

   ![Definitie van Cosmos DB Indexeer functie](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definitie van Cosmos DB Indexeer functie")

U kunt het importeren van gegevens in de portal pagina's bewaken. Voortgangs meldingen geven de indexerings status aan en hoeveel documenten er worden geüpload. 

Wanneer het indexeren is voltooid, kunt u [Search Explorer](search-explorer.md) gebruiken om een query uit te voeren op de index.

> [!NOTE]
> Als de verwachte gegevens niet worden weer gegeven, moet u mogelijk meer kenmerken instellen voor meer velden. Verwijder de index en Indexeer functie die u zojuist hebt gemaakt, en stap de wizard opnieuw uit en wijzig uw selecties voor index kenmerken in stap 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST-API's gebruiken

U kunt de REST API gebruiken om Azure Cosmos DB gegevens te indexeren, het volgen van een werk stroom die uit drie delen is gemeen schappelijk voor alle Indexeer functies in azure Cognitive Search: een gegevens bron maken, een index maken, een Indexeer functie maken. Gegevens extractie van Cosmos DB vindt plaats wanneer u de aanvraag voor het maken van een index verzendt. Nadat deze aanvraag is voltooid, hebt u een query die kan worden geïndexeerd. 

> [!NOTE]
> Voor het indexeren van gegevens uit Cosmos DB Gremlin API of Cosmos DB Cassandra-API moet u eerst toegang tot de gefeedte previews aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. Zodra de aanvraag is verwerkt, ontvangt u instructies voor het gebruik van de [rest API versie 2020-06-30-preview](search-api-preview.md) om de gegevens bron te maken.

Eerder in dit artikel wordt vermeld dat [Azure Cosmos DB](../cosmos-db/index-overview.md) indexering en indexering van [Azure Cognitive Search](search-what-is-an-index.md) een afzonderlijke bewerking zijn. Voor het indexeren van Cosmos DB worden standaard alle documenten automatisch geïndexeerd, behalve met de Cassandra-API. Als u automatisch indexeren uitschakelt, kan alleen toegang worden verkregen tot documenten via hun eigen koppelingen of door query's met behulp van de document-ID. Voor het indexeren van Azure-Cognitive Search moet Cosmos DB automatisch indexeren zijn ingeschakeld in de verzameling die wordt geïndexeerd door Azure Cognitive Search. Wanneer u zich aanmeldt voor de Cosmos DB Cassandra-API Indexeer functie preview, krijgt u instructies voor het instellen van Cosmos DB indexeren.

> [!WARNING]
> Azure Cosmos DB is de volgende generatie DocumentDB. Eerder met API-versie **2017-11-11** kunt u de `documentdb` syntaxis gebruiken. Dit betekent dat u uw gegevens bron type kunt opgeven als `cosmosdb` of `documentdb` . Met ingang van API-versie **2019-05-06** worden de Azure Cognitive Search-api's en-Portal alleen ondersteund `cosmosdb` als in dit artikel wordt geadviseerd. Dit betekent dat het gegevens bron type moet zijn `cosmosdb` Als u verbinding wilt maken met een Cosmos DB eind punt.

### <a name="1---assemble-inputs-for-the-request"></a>1-invoer voor de aanvraag samen stellen

Voor elke aanvraag moet u de service naam en de beheerders sleutel voor Azure Cognitive Search (in de kop POST) en de naam en sleutel van het opslag account voor de Blob-opslag opgeven. U kunt [postman](search-get-started-rest.md) of [Visual Studio code](search-get-started-vs-code.md) gebruiken om HTTP-aanvragen te verzenden naar Azure Cognitive Search.

Kopieer de volgende vier waarden naar Klad blok, zodat u ze in een aanvraag kunt plakken:

+ Naam van Azure Cognitive Search-service
+ Azure Cognitive Search-beheer sleutel
+ Cosmos DB connection string

U kunt deze waarden vinden in de portal:

1. Kopieer de URL van de zoek service op de pagina overzicht van de portal pagina's voor Azure Cognitive Search.

2. Klik in het navigatie deel venster links op **sleutels** en kopieer vervolgens de primaire of secundaire sleutel (ze zijn equivalent).

3. Ga naar de portal pagina's voor uw Cosmos-opslag account. Klik in het navigatie deel venster links onder **instellingen** op **sleutels**. Deze pagina bevat een URI, twee sets verbindings reeksen en twee sets sleutels. Kopieer een van de verbindings reeksen naar Klad blok.

### <a name="2---create-a-data-source"></a>2: een gegevens bron maken

Een **gegevens bron** geeft de gegevens aan die moeten worden geïndexeerd, referenties en beleid voor het identificeren van wijzigingen in de gegevens (zoals gewijzigde of verwijderde documenten in uw verzameling). De gegevens bron wordt gedefinieerd als een onafhankelijke resource, zodat deze kan worden gebruikt door meerdere Indexeer functies.

Als u een gegevens bron wilt maken, moet u een POST-aanvraag formuleren:

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }
```

De hoofd tekst van de aanvraag bevat de definitie van de gegevens bron, die de volgende velden moet bevatten:

| Veld   | Beschrijving |
|---------|-------------|
| **name** | Vereist. Kies een wille keurige naam voor uw gegevens bron object. |
|**type**| Vereist. Moet zijn `cosmosdb` . |
|**aanmeldingsgegevens** | Vereist. U moet de indeling Cosmos DB connection string of een beheerde identiteit connection string notatie volgen.<br/><br/>Voor **SQL-verzamelingen** kunnen verbindings reeksen volgen op een van de volgende indelingen: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>Een beheerde identiteit connection string met de volgende indeling die geen account sleutel bevat: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;` . Als u deze connection string notatie wilt gebruiken, volgt u de instructies voor het [instellen van een indexer-verbinding met een Cosmos DB-Data Base met behulp van een beheerde identiteit](search-howto-managed-identities-cosmos-db.md).<br/><br/>Voor de **MongoDb-verzamelingen** van versie 3,2 en versie 3,6 worden een van de volgende indelingen gebruikt voor de Connection String: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>Een beheerde identiteit connection string met de volgende indeling die geen account sleutel bevat: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;` . Als u deze connection string notatie wilt gebruiken, volgt u de instructies voor het [instellen van een indexer-verbinding met een Cosmos DB-Data Base met behulp van een beheerde identiteit](search-howto-managed-identities-cosmos-db.md).<br/><br/>Voor **Gremlin-grafieken en Cassandra-tabellen** meldt u zich aan voor de preview-versie van de [Indexeer functie](https://aka.ms/azure-cognitive-search/indexer-preview) om toegang te krijgen tot de preview-versie en informatie over het format teren van de referenties.<br/><br/>Vermijd poort nummers in de eind punt-URL. Als u het poort nummer toevoegt, kan Azure Cognitive Search uw Azure Cosmos DB-Data Base niet indexeren.|
| **verpakking** | Bevat de volgende elementen: <br/>**naam**: vereist. Geef de ID op van de database verzameling die moet worden geïndexeerd.<br/>**query**: optioneel. U kunt een query opgeven voor het afvlakken van een wille keurig JSON-document in een plat schema dat door Azure Cognitive Search kan worden geïndexeerd.<br/>Query's worden niet ondersteund voor de MongoDB-API, de Gremlin-API en de Cassandra-API. |
| **dataChangeDetectionPolicy** | Aanbevelingen. Zie de sectie [gewijzigde documenten indexeren](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Optioneel. Zie het gedeelte [verwijderde documenten indexeren](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Query's gebruiken om geïndexeerde gegevens te vorm geven
U kunt een SQL-query opgeven voor het samen voegen van geneste eigenschappen of matrices, project JSON-eigenschappen en filteren van de gegevens die moeten worden geïndexeerd. 

> [!WARNING]
> Aangepaste query's worden niet ondersteund voor **MongoDb-API**, **Gremlin-API** en **Cassandra-API**: `container.query` para meter moet worden ingesteld op null of worden wegge laten. Als u een aangepaste query wilt gebruiken, laat het ons dan weten over de stem van de [gebruiker](https://feedback.azure.com/forums/263029-azure-search).

Voorbeeld document:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

Filter query:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

Afvlakking van query:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Projectie query:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Query voor het afvlakken van matrices:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3-een doel index voor zoeken maken 

[Maak een doel-Azure-Cognitive search index](/rest/api/searchservice/create-index) als u er nog geen hebt. In het volgende voor beeld wordt een index gemaakt met het veld ID en beschrijving:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

Zorg ervoor dat het schema van uw doel index compatibel is met het schema van de bron-JSON-documenten of de uitvoer van uw aangepaste query projectie.

> [!NOTE]
> Voor gepartitioneerde verzamelingen is de standaard document sleutel Azure Cosmos DB `_rid` eigenschap, die door Azure Cognitive Search automatisch wordt hernoemd, `rid` omdat veld namen niet kunnen beginnen met een onderstrepings teken. Azure Cosmos DB- `_rid` waarden bevatten ook tekens die ongeldig zijn in Azure Cognitive Search sleutels. Daarom `_rid` zijn de waarden base64-gecodeerd.
> 
> Voor MongoDB-verzamelingen wordt de naam van de eigenschap automatisch door Azure Cognitive Search gewijzigd `_id` in `id` .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Toewijzing tussen JSON-gegevens typen en Azure Cognitive Search-gegevens typen
| JSON-gegevens type | Compatibele doel veld typen voor index |
| --- | --- |
| Booleaanse waarde |EDM. Boolean, EDM. String |
| Getallen die eruit zien als gehele getallen |EDM. Int32, EDM. Int64, EDM. String |
| Getallen die eruitzien als zwevende punten |Edm.Double, Edm.String |
| Tekenreeks |Edm.String |
| Matrices van primitieve typen, bijvoorbeeld ["a", "b", "c"] |Collection(EDM.String) |
| Teken reeksen die eruitzien als datums |Edm.DateTimeOffset, Edm.String |
| Geojson-objecten, bijvoorbeeld {"type": "punt", "coördinaten": [Long, lat]} |Edm.GeographyPoint |
| Andere JSON-objecten |N.v.t. |

### <a name="4---configure-and-run-the-indexer"></a>4: de Indexeer functie configureren en uitvoeren

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie nu maken:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

## <a name="use-net"></a>.NET gebruiken

De algemeen beschik bare .NET SDK heeft volledige pariteit met de algemeen beschik bare REST API. We raden u aan de sectie voor gaande REST API te bekijken voor meer informatie over concepten, werk stromen en vereisten. U kunt vervolgens de volgende .NET API-referentie documentatie raadplegen voor het implementeren van een JSON-indexer in beheerde code.

+ [azure.search.documents. Indexes. Models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. Indexes. Models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents. Indexes. Models. searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents. Indexes. Models. SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Gewijzigde documenten indexeren

Het doel van het detectie beleid voor gegevens wijzigingen is het efficiënt identificeren van gewijzigde gegevens items. Momenteel wordt het enige ondersteunde beleid [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) gebruikt voor het gebruik van de `_ts` eigenschap (Time Stamp) van Azure Cosmos DB, die als volgt wordt opgegeven:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

Het gebruik van dit beleid wordt sterk aanbevolen om te zorgen voor een goede prestaties van de Indexeer functie. 

Als u een aangepaste query gebruikt, zorg er dan voor dat de `_ts` eigenschap wordt geprojecteerd door de query.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Incrementele voortgang en aangepaste query's

Incrementele voortgang tijdens het indexeren zorgt ervoor dat als de uitvoering van de Indexeer functie wordt onderbroken door tijdelijke fouten of limiet voor uitvoerings tijd, de Indexeer functie kan ophalen waar de volgende keer wordt uitgevoerd, in plaats van de volledige verzameling helemaal opnieuw te indexeren. Dit is vooral belang rijk bij het indexeren van grote verzamelingen. 

Als u incrementele voortgang wilt inschakelen wanneer u een aangepaste query gebruikt, moet u ervoor zorgen dat de resultaten door de query worden gesorteerd op basis van de `_ts` kolom. Zo kunt u periodiek controleren of Azure Cognitive Search gebruikt om incrementele voortgang te bieden bij de aanwezigheid van fouten.   

In sommige gevallen, zelfs als uw query een `ORDER BY [collection alias]._ts` component bevat, kan Azure Cognitive Search mogelijk niet afleiden dat de query wordt gesorteerd door de `_ts` . U kunt Azure Cognitive Search vertellen dat de resultaten worden geordend met behulp van de `assumeOrderByHighWaterMarkColumn` configuratie-eigenschap. Als u deze Hint wilt opgeven, maakt of bijwerkt u de Indexeer functie als volgt: 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Verwijderde documenten indexeren

Wanneer rijen uit de verzameling worden verwijderd, wilt u deze rijen meestal ook verwijderen uit de zoek index. Het doel van het detectie beleid voor het verwijderen van gegevens is het efficiënt identificeren van verwijderde gegevens items. Momenteel is het enige ondersteunde beleid het `Soft Delete` beleid (verwijderen is gemarkeerd met een vlag van een sorteer bewerking), dat als volgt wordt opgegeven:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Als u een aangepaste query gebruikt, zorg er dan voor dat de eigenschap waarnaar wordt verwezen, `softDeleteColumnName` wordt geprojecteerd door de query.

In het volgende voor beeld wordt een gegevens bron gemaakt met een voorlopig verwijderings beleid:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>Volgende stappen

Gefeliciteerd U hebt geleerd hoe u Azure Cosmos DB met Azure Cognitive Search integreert met behulp van een Indexeer functie.

* Zie de [pagina Azure Cosmos DB Service](https://azure.microsoft.com/services/cosmos-db/)voor meer informatie over Azure Cosmos db.
* Zie de [pagina zoek service](https://azure.microsoft.com/services/search/)voor meer informatie over Azure Cognitive Search.