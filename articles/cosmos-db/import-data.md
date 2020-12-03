---
title: 'Zelfstudie: Hulpprogramma voor databasemigratie voor Azure Cosmos DB'
description: "Zelfstudie: Leer hoe u de open source Azure Cosmos DB-hulpprogramma's voor gegevensmigratie gebruikt om gegevens te importeren naar Azure Cosmos DB vanuit diverse bronnen, zoals MongoDB, SQL Server, Tabelopslag, Amazon DynamoDB, CSV- en JSON-bestanden. Conversie van CSV naar JSON."
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341645"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Zelfstudie: Hulpprogramma voor gegevensmigratie gebruiken voor het migreren van uw gegevens naar Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Deze zelfstudie bevat instructies over het gebruik van het Azure Cosmos DB-hulpprogramma voor gegevensmigratie waarmee gegevens uit diverse bronnen kunnen worden geïmporteerd in Azure Cosmos-containers en -tabellen. U kunt importeren vanuit JSON-bestanden, CSV-bestanden, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB en zelfs Azure Cosmos DB SQL-API-verzamelingen. U migreert deze gegevens naar verzamelingen en tabellen voor gebruik met Azure Cosmos DB. Het hulpprogramma voor gegevensmigratie kan ook worden gebruikt wanneer u vanaf een verzameling met één partitie migreert naar een verzameling met meerdere partities voor de SQL-API.

> [!NOTE]
> Het hulpprogramma voor gegevensmigratie van Azure Cosmos DB is een open source-programma dat is ontworpen voor kleine migraties. Bekijk onze [gids voor het opnemen van gegevens](cosmosdb-migrationchoices.md) voor grotere migraties.

* **[SQL-API](./introduction.md)** : u kunt een van de bronopties in het hulpprogramma voor gegevensmigratie gebruiken om op kleine schaal gegevens te importeren. [Meer informatie over migratieopties voor het importeren van gegevens op grote schaal](cosmosdb-migrationchoices.md).
* **[Table-API](table-introduction.md)** : u kunt het hulpprogramma voor gegevensmigratie of [AzCopy](table-import.md#migrate-data-by-using-azcopy) gebruiken om gegevens te importeren. Zie [Gegevens importeren voor gebruik met de Azure Cosmos DB Table-API](table-import.md) voor meer informatie.
* **[API van Azure Cosmos DB voor MongoDB](mongodb-introduction.md)** : het hulpprogramma voor gegevensmigratie biedt geen ondersteuning voor de API van Azure Cosmos DB voor MongoDB, noch als bron noch als doel. Raadpleeg [MongoDB-gegevens migreren naar een Cosmos-database met de API van Cosmos DB voor MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) voor instructies voor het migreren van de gegevens naar of uit verzamelingen in Azure Cosmos DB. U kunt het hulpprogramma voor gegevensmigratie nog wel gebruiken om gegevens vanaf MongoDB te exporteren naar Azure Cosmos DB SQL-API-verzamelingen voor gebruik met de SQL-API.
* **[Cassandra-API](graph-introduction.md)** : het hulpprogramma voor gegevensmigratie is geen ondersteund importprogramma voor Cassandra-API-accounts. [Meer informatie over migratieopties voor het importeren van gegevens in Cassandra-API](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[Gremlin-API](graph-introduction.md)** : het hulpprogramma voor gegevensmigratie is momenteel geen ondersteund importprogramma voor Gremlin API-accounts. [Meer informatie over migratieopties voor het importeren van gegevens in Gremlin-API](cosmosdb-migrationchoices.md#other-apis) 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Het hulpprogramma voor gegevensmigratie installeren
> * Gegevens importeren vanuit verschillende gegevensbronnen
> * Exporteren vanaf Azure Cosmos DB naar JSON

## <a name="prerequisites"></a><a id="Prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel volgt, moet u de volgende stappen uitvoeren:

* **Installeer** [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) of hoger.

* **Doorvoer vergroten:** de duur van de gegevensmigratie is afhankelijk van de hoeveelheid doorvoer die u voor een afzonderlijke verzameling of een reeks verzamelingen instelt. Verhoog de doorvoer voor grotere gegevensmigraties. Nadat u de migratie hebt voltooid, verlaagt u de doorvoer om kosten te besparen. Zie [prestatieniveaus](performance-levels.md) en [prijscategorieën](https://azure.microsoft.com/pricing/details/cosmos-db/) in Azure Cosmos DB voor meer informatie over het verhogen van de doorvoer in de Azure-portal.

* **Azure Cosmos DB-resources maken:** voordat u gegevens gaat migreren, maakt u vooraf alle verzamelingen vanuit de Azure-portal. Als u wilt migreren naar een Azure Cosmos DB-account dat doorvoer op databaseniveau heeft, geeft u een partitiesleutel op wanneer u de Azure Cosmos-containers maakt.

> [!IMPORTANT]
> Als u er zeker van wilt zijn dat het hulpprogramma voor gegevensmigratie gebruikmaakt van TLS 1.2 (Transport Layer Security) bij het verbinding maken met uw Azure Cosmos-accounts, gebruikt u .NET Framework-versie 4.7 of volgt u de instructies in [dit artikel](/dotnet/framework/network-programming/tls).

## <a name="overview"></a><a id="Overviewl"></a>Overzicht

Het hulpprogramma voor gegevensmigratie is een open source-oplossing waarmee gegevens worden geïmporteerd naar Azure Cosmos DB vanuit diverse bronnen, zoals:

* JSON-bestanden
* MongoDB
* SQL Server
* CSV-bestanden
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos-containers

Het hulpprogramma voor importeren bevat een grafische gebruikersinterface (dtui.exe), maar kan ook worden aangestuurd vanaf de opdrachtregel (dt.exe). Er is een optie om de bijbehorende opdracht uit te voeren na het instellen van een import via de gebruikersinterface. U kunt brongegevens in tabelvorm, zoals SQL Server- of CSV-bestanden, transformeren om hiërarchische relaties (subdocumenten) te maken tijdens het importeren. Lees door voor meer informatie over bronopties, voorbeeldopdrachten om te importeren vanuit elke bron, doelopties en het weergeven van importresultaten.

> [!NOTE]
> Gebruik het hulpprogramma voor migratie van Azure Cosmos DB alleen voor kleine migraties. Bekijk onze [gids voor het opnemen van gegevens](cosmosdb-migrationchoices.md) voor grote migraties.

## <a name="installation"></a><a id="Install"></a>Installatie

De broncode van het hulpprogramma voor migratie is beschikbaar op GitHub in [deze opslagplaats](https://github.com/azure/azure-documentdb-datamigrationtool). U kunt de oplossing lokaal downloaden en compileren, of u kunt [een vooraf gecompileerd binair bestand downloaden](https://aka.ms/csdmtool). Vervolgens voert u een van de volgende opdrachten uit:

* **Dtui.exe**: grafische-interfaceversie van het hulpprogramma
* **Dt.exe**: opdrachtregelversie van het hulpprogramma

## <a name="select-data-source"></a>Gegevensbron selecteren

Nadat u het hulpprogramma hebt geïnstalleerd, is het tijd om uw gegevens te importeren. Welk type gegevens wilt u importeren?

* [JSON-bestanden](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-exportbestanden](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-bestanden](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos-containers](#SQLSource)
* [HBase](#HBaseSource)
* [Azure DB Cosmos-bulkimport](#SQLBulkTarget)
* [Azure DB Cosmos sequentiële recordimport](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>JSON-bestanden importeren

Met de importfunctie voor JSON-bronbestanden kunt u een of meer uit een enkel document bestaande JSON-bestanden importeren of JSON-bestanden die elk een matrix met JSON-documenten hebben. Wanneer u mappen toevoegt die JSON-bestanden hebben om te importeren, kunt u in submappen recursief zoeken naar bestanden.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Schermopname van opties voor JSON-bronbestanden - hulpprogramma's voor databasemigratie":::

De verbindingsreeks heeft de volgende indeling:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* De `<CosmosDB Endpoint>` is de URI van het eindpunt. U kunt deze waarde ophalen uit de Microsoft Azure-portal. Navigeer naar uw Azure Cosmos-account. Open het deelvenster **overzicht** en kopieer de waarde van de **URI**.
* De `<AccountKey>` is het "wachtwoord" of de **PRIMAIRE SLEUTEL**. U kunt deze waarde ophalen uit de Microsoft Azure-portal. Navigeer naar uw Azure Cosmos-account. Open de deelvensters **verbindingsreeksen** of **sleutels** en kopieer de waarde "wachtwoord" of **PRIMAIRE SLEUTEL**.
* De `<CosmosDB Database>` is de naam van de CosmosDB-database.

Voorbeeld: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de Cosmos DB-account die in het verbindingsreeksveld is opgegeven, kan worden geopend.

Hierna volgen enkele opdrachtregelvoorbeelden om JSON-bestanden te importeren:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importeren vanaf MongoDB

> [!IMPORTANT]
> Volg deze [instructies](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) als u importeert naar een Cosmos DB-account dat is geconfigureerd met de API van Azure Cosmos DB voor MongoDB.

Met de importoptie voor MongoDB-bronnen kunt u importeren vanuit een enkele MongoDB-verzameling, eventueel documenten filteren met een query, en de documentstructuur wijzigen met een projectie.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Schermopname van MongoDB-bronopties":::

De verbindingsreeks heeft de standaard MongoDB-indeling:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de MongoDB-instantie die is opgegeven in het verbindingsreeksveld, kan worden geopend.

Voer de naam van de verzameling in van waaruit gegevens worden geïmporteerd. U kunt eventueel een bestand voor een query opgeven of leveren, zoals `{pop: {$gt:5000}}`, of een projectie, zoals `{loc:0}`, om de gegevens die u wilt importeren, te filteren en te vormen.

Hierna volgen enkele opdrachtregelvoorbeelden om te importeren vanaf MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>MongoDB-exportbestanden

> [!IMPORTANT]
> Volg deze [instructies](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) als u importeert naar een Azure Cosmos DB-account met ondersteuning voor MongoDB.

Met de importoptie voor MongoDB-export van JSON-bronbestanden kunt u een of meer JSON-bestanden importeren die zijn geproduceerd vanaf het hulpprogramma mongoexport.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Schermopname van opties voor MongoDB-exportbron":::

Wanneer u mappen toevoegt die MongoDB-export JSON-bestanden hebben om te importeren, kunt u recursief zoeken naar bestanden in submappen.

Hierna volgt een opdrachtregelvoorbeeld om te importeren vanaf MongoDB-export JSON-bestanden:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importeren vanaf SQL Server

Met de importoptie voor SQL-bronnen kunt u importeren vanaf een afzonderlijke SQL Server-database en optioneel de records die moeten worden geïmporteerd, filteren met behulp van een query. Verder kunt u de documentstructuur wijzigen door een genest scheidingsteken op te geven (hierover later meer informatie).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Schermopname van opties voor SQL-bronnen - hulpprogramma's voor databasemigratie":::

De indeling van de verbindingsreeks is de standaardindeling van een SQL-verbindingsreeks.

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de SQL Server-instantie die is opgegeven in het verbindingsreeksveld kan worden geopend.

De eigenschap van het type genest scheidingsteken wordt gebruikt om hiërarchische relaties (subdocumenten) te maken tijdens het importeren. Kijk eens naar de volgende SQL-query:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Deze retourneert de volgende (gedeeltelijke) waarden:

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Schermafbeelding van SQL-queryresultaten":::

Let op de aliassen zoals Address.AddressType en Address.Location.StateProvinceName. Door een scheidingsteken voor nesten '.' op te geven, maakt het importprogramma Address- en Address.Location-subdocumenten tijdens het importeren. Hier volgt een voorbeeld van een resulterend document in Azure Cosmos DB:

*{ "id": "956", "Naam": "Finer Verkoop ed Service", "Adres": { "AddressType": "Hoofdkantoor", "AddressLine1": "Verbindingsweg 122", "Locatie": { "Stad": "Rotterdam", "StateProvinceName": "Zuid-Holland" }, "PostalCode": "3092 AX", "CountryRegionName": "Nederland" } }*

Hierna volgen enkele opdrachtregelvoorbeelden om te importeren vanaf SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>CSV-bestanden importeren en CSV converteren naar JSON

Met de importoptie voor CSV-bestandsbronnen kunt u een of meer CSV-bestanden importeren. Wanneer u mappen toevoegt die CSV-bestanden hebben om te importeren, kunt u in submappen recursief zoeken naar bestanden.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Schermopname van CSV-bronopties - CSV naar JSON":::

Vergelijkbaar met de SQL-bron kan de eigenschap van het type geneste scheidingsteken worden gebruikt om hiërarchische relaties (subdocumenten) te maken tijdens het importeren. Kijk eens naar de volgende CSV-koprij en -gegevensrijen:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Schermopname van CSV-voorbeeldrecords - CSV naar JSON":::

Let op de aliassen zoals DomainInfo.Domain_Name en RedirectInfo.Redirecting. Door een scheidingsteken voor nesten '.' op te geven, maakt het importprogramma DomainInfo- en RedirectInfo-subdocumenten tijdens het importeren. Hier volgt een voorbeeld van een resulterend document in Azure Cosmos DB:

*{ "DomainInfo": { "Domeinnaam": "ACUS.GOV", "Adres_domeinnaam": "https:\//www.ACUS.GOV" }, "Federaal Agentschap": "Administratieve Conferentie van de Verenigde Staten", "RedirectInfo": { "Omleiden": "0", "Doel_omleiding": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Met het importprogramma wordt een poging gedaan informatie over het type af te leiden voor waarden zonder aanhalingstekens in CSV-bestanden (waarden tussen aanhalingstekens worden altijd behandeld als tekenreeksen).  Typen worden geïdentificeerd in de volgende volgorde: getal, datum/tijd, Booleaanse waarde.  

Er zijn nog twee dingen op te merken over CSV-import:

1. Standaard worden waarden zonder aanhalingstekens altijd afgekapt voor tabs en spaties; waarden tussen aanhalingstekens blijven behouden zoals ze zijn. Dit gedrag kan worden overschreven met het selectievakje Waarden tussen aanhalingstekens afkappen of de opdrachtregeloptie /s.TrimQuoted.
2. Standaard wordt een null zonder aanhalingstekens beschouwd als een null-waarde. Dit gedrag kan worden overschreven (dat wil zeggen een null zonder aanhalingstekens behandelen als een 'null'-tekenreeks) met het selectievakje NULL als tekenreeks behandelen of de opdrachtregeloptie /s.NoUnquotedNulls.

Hier volgt een voorbeeld van een opdrachtregel voor CSV-import:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importeren uit Azure Table Storage

Met de bronimportfunctie voor Azure Table Storage kunt u importeren vanaf een afzonderlijke Azure Table Storage-tabel. U kunt eventueel de tabelentiteiten filteren die moeten worden geïmporteerd.

U kunt gegevens uitvoeren die uit Azure Table Storage zijn geïmporteerd naar Azure Cosmos DB-tabellen, en entiteiten voor gebruik met de Table-API. Geïmporteerde gegevens kunnen ook worden uitgevoerd naar verzamelingen en documenten voor gebruik met de SQL-API. Table-API is echter alleen beschikbaar als doel in het opdrachtregelprogramma. U kunt niet exporteren naar Table-API met behulp van de gebruikersinterface van het hulpprogramma voor gegevensmigratie. Zie [Gegevens importeren voor gebruik met de Azure Cosmos DB Table-API](table-import.md) voor meer informatie.

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Schermopname van bronopties voor Azure Table Storage":::

De indeling van de verbindingsreeks voor Azure Table Storage is:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de Azure Table Storage-instantie die is opgegeven in het verbindingsreeksveld kan worden geopend.

Voer de naam in van de Azure-tabel van waaruit u wilt importeren. U kunt eventueel een [filter](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings) opgeven.

De importoptie voor Azure Table Storage-bron heeft de volgende aanvullende opties:

1. Interne velden opnemen
   1. Alle: alle interne velden opnemen (PartitionKey, RowKey en Timestamp)
   2. Geen: alle interne velden uitsluiten
   3. RowKey: alleen het veld RowKey opnemen
2. Kolommen selecteren
   1. Azure Table Storage-filters bieden geen ondersteuning voor projecties. Als u alleen specifieke Azure Table-entiteitseigenschappen wilt importeren, kunt u deze toevoegen aan de lijst Kolommen selecteren. Alle andere eigenschappen van entiteiten worden genegeerd.

Hierna volgt een opdrachtregelvoorbeeld om te importeren vanaf Azure Table Storage:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importeren vanaf Amazon DynamoDB

Met de importoptie voor Amazon DynamoDB-bronnen kunt u importeren vanuit een enkele Amazon DynamoDB-tabel. U kunt eventueel de entiteiten filteren die moeten worden geïmporteerd. Er zijn verschillende sjablonen om het instellen van een import zo eenvoudig mogelijk te maken.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Schermopname met bronopties van Amazon DynamoDB - hulpprogramma's voor databasemigratie.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Schermopname met bronopties van Amazon DynamoDB met sjabloon - hulpprogramma's voor databasemigratie.":::

De indeling van de Amazon DynamoDB-verbindingsreeks is:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de Amazon DynamoDB-instantie die is opgegeven in het verbindingsreeksveld, kan worden geopend.

Hierna volgt een opdrachtregelvoorbeeld om te importeren vanaf Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importeren vanaf Azure Blob-opslag

Met de bronimportfunctie voor JSON-bestanden, MongoDB-exportbestanden en CSV-bestanden kunt u een of meer bestanden importeren uit Azure Blob-opslag. Nadat u een URL van de Blob-container en de Accountsleutel hebt opgegeven, geeft u een reguliere expressie op om de te selecteren bestanden te importeren.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Schermopname van bronopties voor Blob-bestanden":::

Hier volgt een opdrachtregelvoorbeeld om JSON-bestanden te importeren vanuit Azure Blob-opslag:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importeren vanuit een SQL-API-verzameling

Met de importoptie voor Azure Cosmos DB-bronnen kunt u gegevens importeren vanuit een of meer Azure Cosmos-containers en optioneel documenten filteren met behulp van een query.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Schermopname van opties voor Azure Cosmos DB-bronnen":::

De indeling van de Azure Cosmos DB-verbindingsreeks is:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

U kunt de Azure Cosmos DB-verbindingsreeks ophalen op de pagina Sleutels in de Azure-portal, zoals beschreven in [Een Azure Cosmos DB-account beheren](./how-to-manage-database-account.md). De naam van de database moet zijn toegevoegd aan de verbindingsreeks in de volgende indeling:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de Azure Cosmos DB-instantie die is opgegeven in het verbindingsreeksveld, kan worden geopend.

Als u wilt importeren vanuit één Azure Cosmos-container, voert u de naam in van de verzameling waaruit gegevens moeten worden geïmporteerd. Als u uit meer dan één Azure Cosmos-container wilt importeren, geeft u een reguliere expressie op die overeenkomt met een of meer verzamelingsnamen (bijvoorbeeld collection01 | collection02 | collection03). U kunt eventueel een query of een bestand opgeven om de gegevens die u wilt importeren, zowel te filteren als te vormen.

> [!NOTE]
> Aangezien het verzamelingenveld reguliere expressies accepteert, moeten die tekens overeenkomstig een escape-teken krijgen als u importeert uit een enkele verzameling waarvan de naam tekens van de reguliere expressie heeft.

De importoptie voor Azure Cosmos DB-bronnen heeft de volgende geavanceerde opties:

1. Interne velden opnemen: hiermee bepaalt u of Azure Cosmos DB-documentsysteemeigenschappen wel of niet moeten worden opgenomen in de export (bijvoorbeeld _rid, _ts).
2. Aantal nieuwe pogingen bij fouten: hiermee bepaalt u het aantal malen dat de verbinding met Azure Cosmos DB opnieuw moet worden geprobeerd in geval van een tijdelijke fout (bijvoorbeeld onderbreking van netwerkconnectiviteit).
3. Interval tussen nieuwe pogingen: hiermee bepaalt u hoe lang moet worden gewacht voordat de verbinding met Azure Cosmos DB opnieuw moet worden geprobeerd in geval van een tijdelijke fouten (bijvoorbeeld onderbreking van netwerkconnectiviteit).
4. Verbindingsmodus: hiermee bepaalt u de verbindingsmodus die moet worden gebruikt met Azure Cosmos DB. De beschikbare opties zijn DirectTcp, DirectHttps en Gateway. De rechtstreekse verbindingsmodi zijn sneller, terwijl de gatewaymodus minder problemen oplevert voor de firewall aangezien deze alleen poort 443 gebruikt.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Schermopname van geavanceerde opties voor Azure Cosmos DB-bronnen":::

> [!TIP]
> Het importprogramma gebruikt standaard verbindingsmodus DirectTcp. Als u problemen ondervindt met de firewall, schakelt u over op de verbindingsmodus Gateway, omdat hiervoor alleen poort 443 is vereist.

Hierna volgen enkele opdrachtregelvoorbeelden om te importeren vanaf Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> Het Azure Cosmos DB-gegevensimportprogramma ondersteunt ook het importeren van gegevens vanuit de [Azure Cosmos DB Emulator](local-emulator.md). Bij het importeren van een lokale emulator stelt u het eindpunt in op `https://localhost:<port>`.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importeren vanuit HBase

Met de importoptie voor HBase-bronnen kunt u gegevens importeren vanuit een HBase-tabel en optioneel de gegevens filteren. Er zijn verschillende sjablonen om het instellen van een import zo eenvoudig mogelijk te maken.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Schermopname met bronopties van HBase.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Schermopname met bronopties van HBase-bron met het contextmenu Filter uitgevouwen.":::

De indeling va de HBase Stargate-verbindingsreeks is:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de HBase-instantie die is opgegeven in het verbindingsreeksveld, kan worden geopend.

Hierna volgt een opdrachtregelvoorbeeld om te importeren vanuit HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importeren naar de SQL-API (bulkimport)

Met de Azure Cosmos DB-bulkimportfunctie kunt u importeren vanuit alle beschikbare bronopties MET een opgeslagen Azure Cosmos DB-procedure voor efficiëntie. Het hulpprogramma biedt ondersteuning voor het importeren van een Azure Cosmos-container met één partitie. Het biedt ook ondersteuning voor shard-imports, waarbij gegevens worden gepartitioneerd in meer dan één Azure Cosmos-container met één partitie. Zie [Partitioneren en schalen in Azure Cosmos DB](partitioning-overview.md) voor meer informatie over gegevenspartitionering. Het hulpprogramma maakt de opgeslagen procedure, voert deze uit en verwijdert deze uit de doelverzameling(en).  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Schermopname van Azure Cosmos DB-bulkopties":::

De indeling van de Azure Cosmos DB-verbindingsreeks is:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

De verbindingsreeks van het Azure Cosmos DB-account kan worden opgehaald uit de pagina Sleutels van de Azure Portal, zoals beschreven in [Een account voor Azure Cosmos DB beheren](./how-to-manage-database-account.md). De naam van de database moet echter worden toegevoegd aan de verbindingsreeks in de volgende indeling:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de Azure Cosmos DB-instantie die is opgegeven in het verbindingsreeksveld, kan worden geopend.

Als u wilt importeren naar één verzameling, voert u de naam in van de verzameling waaruit gegevens moeten worden geïmporteerd en klikt u op de knop Toevoegen. Als u meer dan één verzameling wilt importeren, voert u ofwel de verzamelingsnaam afzonderlijk in of gebruikt u de volgende syntaxis om meer dan één verzameling op te geven: *collection_prefix*[begin index - eind index]. Houd rekening met de volgende richtlijnen wanneer u meer dan één verzameling opgeeft met de hiervoor genoemde syntaxis:

1. Alleen bereiknaampatronen met gehele getallen worden ondersteund. Wanneer u bijvoorbeeld collection[0-3] opgeeft, worden de volgende verzamelingen gemaakt: collection0, collection1, collection2, collection3.
2. U kunt een verkorte syntaxis gebruiken: met collection[3] maakt u dezelfde set verzamelingen die in stap 1 worden genoemd.
3. Er kan meer dan een vervanging worden opgegeven. Met collection[0-1] [0-9] worden bijvoorbeeld 20 verzamelingsnamen met voorloopnullen (collection01,... 02... 03) gegenereerd.

Wanneer de naam (namen) van de verzameling is (zijn) opgegeven, kiest u de gewenste doorvoer van de verzameling(en) (400 RU’s naar 10.000 RU’s). Kies een hogere doorvoer voor de beste importprestaties. Zie [Prestatieniveaus in Azure Cosmos DB](performance-levels.md) voor meer informatie over prestatieniveaus.

> [!NOTE]
> De instelling van de prestatiedoorvoer is alleen van toepassing op het maken van de verzameling. Als de opgegeven verzameling al bestaat, wordt de bijbehorende doorvoer niet gewijzigd.

Als u importeert naar meer dan één verzameling, ondersteunt het importprogramma hash-sharding. In dit scenario geeft u de documenteigenschap op die u wilt gebruiken als de partitiesleutel. (Als het veld Partitiesleutel leeg blijft, wordt sharding van de documenten willekeurig uitgevoerd in alle doelverzamelingen.)

U kunt eventueel opgeven welk veld in de importbron moet worden gebruikt als Azure Cosmos DB-document-id-eigenschap tijdens het importeren. Als documenten deze eigenschap niet hebben, wordt met het importprogramma een GUID gegenereerd als de id-eigenschapswaarde.

Er zijn tijdens het importeren een aantal geavanceerde opties beschikbaar. Ten eerste kunt u uw eigen opgeslagen procedure voor import opgeven, hoewel het hulpprogramma een standaard opgeslagen procedure voor bulkimport (BulkInsert.js) bevat:

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Schermopname van opties voor Azure Cosmos DB-bulkinvoeging van opgeslagen procedures":::

Verder kunt u, wanneer u datumtypen importeert (bijvoorbeeld van SQL Server of MongoDB), kiezen tussen de drie opties voor importeren:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Schermopname van Azure Cosmos DB-importopties voor datum/tijd":::

* Tekenreeks: behouden als een tekenreekswaarde
* Epoch: behouden als een Epoch-getalswaarde
* Beide: zowel tekenreekswaarden als Epoch-getalswaarden behouden. Met deze optie maakt u een subdocument, bijvoorbeeld: "date_joined": { "Waarde": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

De optie Azure Cosmos DB-bulkimportfunctie heeft de volgende geavanceerde opties:

1. Batchgrootte: het hulpprogramma wordt standaard ingesteld op een batchgrootte van 50.  Als de documenten die moeten worden geïmporteerd groot zijn, kunt u overwegen de batchgrootte te verlagen. Als de documenten die moeten worden geïmporteerd daarentegen klein zijn, kunt u overwegen de batchgrootte te verhogen.
2. Maximale scriptgrootte (bytes): het hulpprogramma staat standaard ingesteld op een maximale scriptgrootte van 512 KB.
3. Automatische id-generatie uitschakelen: als elk document dat moet worden geïmporteerd, een id-veld bevat, kunt u de prestatie verbeteren door deze optie te selecteren. Documenten zonder een uniek id-veld worden niet geïmporteerd.
4. Bestaande documenten bijwerken: het hulpprogramma staat standaard ingesteld op het niet vervangen van bestaande documenten met id-conflicten. Als u deze optie selecteert, worden bestaande documenten met overeenkomende id overschreven. Deze functie is handig voor geplande gegevensmigraties waarmee bestaande documenten worden bijgewerkt.
5. Aantal nieuwe pogingen bij fouten: geeft op hoe vaak de verbinding met Azure Cosmos DB opnieuw moet worden geprobeerd tijdens tijdelijke fouten (bijvoorbeeld onderbreking van netwerkconnectiviteit).
6. Interval tussen nieuwe pogingen: hiermee bepaalt u hoe lang moet worden gewacht voordat de verbinding met Azure Cosmos DB opnieuw moet worden geprobeerd in geval van een tijdelijke fouten (bijvoorbeeld onderbreking van netwerkconnectiviteit).
7. Verbindingsmodus: hiermee bepaalt u de verbindingsmodus die moet worden gebruikt met Azure Cosmos DB. De beschikbare opties zijn DirectTcp, DirectHttps en Gateway. De rechtstreekse verbindingsmodi zijn sneller, terwijl de gatewaymodus minder problemen oplevert voor de firewall aangezien deze alleen poort 443 gebruikt.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Schermopname van geavanceerde opties voor Azure Cosmos DB-bulkimport":::

> [!TIP]
> Het importprogramma gebruikt standaard verbindingsmodus DirectTcp. Als u problemen ondervindt met de firewall, schakelt u over op de verbindingsmodus Gateway, omdat hiervoor alleen poort 443 is vereist.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importeren naar de SQL-API (sequentiële recordimport)

Met de Azure Cosmos DB-importfunctie voor sequentiële records kunt u importeren vanuit een beschikbare bronoptie op een record-voor-recordbasis. U kunt deze optie selecteren als u importeert naar een bestaande verzameling die het quotum van opgeslagen procedures heeft bereikt. Het hulpprogramma biedt ondersteuning voor het importeren naar een enkele Azure Cosmos-container (zowel met één als meerdere partities). Het biedt ook ondersteuning voor shard-imports, waarbij gegevens worden gepartitioneerd in meer dan één Azure Cosmos-container, met één partitie of met meerdere partities. Zie [Partitioneren en schalen in Azure Cosmos DB](partitioning-overview.md) voor meer informatie over gegevenspartitionering.

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Schermopname van Azure DB Cosmos-importopties voor sequentiële records":::

De indeling van de Azure Cosmos DB-verbindingsreeks is:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

U kunt de verbindingsreeks voor het Azure Cosmos DB-account ophalen op de pagina Sleutels in de Azure-portal, zoals beschreven in [Een Azure Cosmos DB-account beheren](./how-to-manage-database-account.md). De naam van de database moet zijn toegevoegd aan de verbindingsreeks in de volgende indeling:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Gebruik de opdracht Verifiëren om te controleren of de Azure Cosmos DB-instantie die is opgegeven in het verbindingsreeksveld, kan worden geopend.

Als u wilt importeren naar één verzameling, voert u de naam in van de verzameling waarin gegevens moeten worden geïmporteerd, en klikt u op de knop Toevoegen. Als u naar meer dan één verzameling wilt importeren, voert u elke verzamelingsnaam afzonderlijk in. U kunt ook de volgende syntaxis gebruiken om meer dan één verzameling op te geven: *collection_prefix*[begin index - eind index]. Houd rekening met de volgende richtlijnen wanneer u meer dan één verzameling opgeeft met de hiervoor genoemde syntaxis:

1. Alleen bereiknaampatronen met gehele getallen worden ondersteund. Wanneer u bijvoorbeeld collection[0-3] opgeeft, worden de volgende verzamelingen gemaakt: collection0, collection1, collection2, collection3.
2. U kunt een verkorte syntaxis gebruiken: met collection[3] maakt u dezelfde set verzamelingen die in stap 1 worden genoemd.
3. Er kan meer dan een vervanging worden opgegeven. Met collection[0-1] [0-9] worden bijvoorbeeld 20 verzamelingsnamen met voorloopnullen (collection01,... 02... 03) gemaakt.

Wanneer de naam (namen) van de verzameling is (zijn) opgegeven, kiest u de gewenste doorvoer van de verzameling(en) (400 RU’s naar 250.000 RU’s). Kies een hogere doorvoer voor de beste importprestaties. Zie [Prestatieniveaus in Azure Cosmos DB](performance-levels.md) voor meer informatie over prestatieniveaus. Voor elke import naar verzamelingen met doorvoer > 10.000 RU’s is een partitiesleutel vereist. Als u ervoor kiest om meer dan 250.000 RU’s te hebben, moet u een aanvraag indienen in de portal om uw account te laten vergroten.

> [!NOTE]
> De doorvoerinstelling is alleen van toepassing op het maken van de verzameling of database. Als de opgegeven verzameling al bestaat, wordt de bijbehorende doorvoer niet gewijzigd.

Als u importeert naar meer dan één verzameling, ondersteunt het importprogramma hash-sharding. In dit scenario geeft u de documenteigenschap op die u wilt gebruiken als de partitiesleutel. (Als het veld Partitiesleutel leeg blijft, wordt sharding van de documenten willekeurig uitgevoerd in alle doelverzamelingen.)

U kunt eventueel opgeven welk veld in de importbron moet worden gebruikt als Azure Cosmos DB-document-id-eigenschap tijdens het importeren. (Als documenten deze eigenschap niet hebben, wordt met het importprogramma een GUID gegenereerd als de id-eigenschapswaarde.)

Er zijn tijdens het importeren een aantal geavanceerde opties beschikbaar. Ten eerste kunt u, wanneer u datumtypen importeert (bijvoorbeeld van SQL Server of MongoDB), kiezen tussen de drie opties voor importeren:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Schermopname van Azure Cosmos DB-importopties voor datum/tijd":::

* Tekenreeks: behouden als een tekenreekswaarde
* Epoch: behouden als een Epoch-getalswaarde
* Beide: zowel tekenreekswaarden als Epoch-getalswaarden behouden. Met deze optie maakt u een subdocument, bijvoorbeeld: "date_joined": { "Waarde": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

De Azure Cosmos DB-bulkimportoptie voor sequentiële records heeft de volgende geavanceerde opties:

1. Aantal parallelle aanvragen: het hulpprogramma staat standaard ingesteld op twee parallelle aanvragen. Als de documenten die moeten worden geïmporteerd klein zijn, kunt u overwegen het aantal parallelle aanvragen te verhogen. Als dit nummer te veel wordt verhoogd, wordt de import mogelijk beperkt.
2. Automatische id-generatie uitschakelen: als elk document dat moet worden geïmporteerd, een id-veld bevat, kunt u de prestatie verbeteren door deze optie te selecteren. Documenten zonder een uniek id-veld worden niet geïmporteerd.
3. Bestaande documenten bijwerken: het hulpprogramma staat standaard ingesteld op het niet vervangen van bestaande documenten met id-conflicten. Als u deze optie selecteert, worden bestaande documenten met overeenkomende id overschreven. Deze functie is handig voor geplande gegevensmigraties waarmee bestaande documenten worden bijgewerkt.
4. Aantal nieuwe pogingen bij fouten: geeft op hoe vaak de verbinding met Azure Cosmos DB opnieuw moet worden geprobeerd tijdens tijdelijke fouten (bijvoorbeeld onderbreking van netwerkconnectiviteit).
5. Interval tussen nieuwe pogingen: hiermee bepaalt u hoe lang moet worden gewacht voordat de verbinding met Azure Cosmos DB opnieuw moet worden geprobeerd tijdens tijdelijke fouten (bijvoorbeeld onderbreking van netwerkconnectiviteit).
6. Verbindingsmodus: hiermee bepaalt u de verbindingsmodus die moet worden gebruikt met Azure Cosmos DB. De beschikbare opties zijn DirectTcp, DirectHttps en Gateway. De rechtstreekse verbindingsmodi zijn sneller, terwijl de gatewaymodus minder problemen oplevert voor de firewall aangezien deze alleen poort 443 gebruikt.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Schermopname van geavanceerde importopties voor sequentiële Azure DB Cosmos-records":::

> [!TIP]
> Het importprogramma gebruikt standaard verbindingsmodus DirectTcp. Als u problemen ondervindt met de firewall, schakelt u over op de verbindingsmodus Gateway, omdat hiervoor alleen poort 443 is vereist.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Een indexeringsbeleid opgeven

Wanneer u het migratieprogramma Azure Cosmos DB SQL-API-verzamelingen laat maken tijdens het importeren, kunt u het indexeringsbeleid van de verzamelingen opgeven. In de sectie met geavanceerde opties van Azure Cosmos DB-bulkimport en sequentiële Azure Cosmos DB-records, gaat u naar de sectie Indexeringsbeleid.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Schermopname met geavanceerde opties van het indexeringsbeleid van Azure Cosmos DB.":::

Wanneer u de geavanceerde optie Indexeringsbeleid gebruikt, kunt u een indexeringsbeleidbestand selecteren, handmatig een indexeringsbeleid invoeren of een keuze maken uit een reeks standaardsjablonen (door met de rechtermuisknop te klikken in het tekstvak voor indexeringsbeleid).

De beleidssjablonen die het hulpprogramma biedt, zijn:

* Standaard. Dit beleid werkt het beste als u gelijkheidsquery’s uitvoert voor tekenreeksen. Het werkt ook als u de query’s ORDER BY, bereik en gelijkheid uitvoer voor getallen. Dit beleid heeft een lagere opslagoverhead voor indexering dan Bereik.
* Bereik. Dit beleid wordt aanbevolen wanneer u de query’s ORDER BY, bereik en gelijkheid gebruikt voor zowel getallen als tekenreeksen. Dit beleid heeft een hogere opslagoverhead voor indexering dan Standaard of Hash.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Schermopname met geavanceerde opties van het indexeringsbeleid van Azure Cosmos DB waarin doelinformatie wordt toegelicht.":::

> [!NOTE]
> Als u geen indexeringsbeleid opgeeft, wordt het standaardbeleid toegepast. Zie [Azure Cosmos DB-indexeringsbeleid](index-policy.md) voor meer informatie over indexeringsbeleid.

## <a name="export-to-json-file"></a>Exporteren naar JSON-bestand

Met de Azure Cosmos DB JSON-exportfunctie kunt u een van de beschikbare bronopties exporteren naar een JSON-bestand dat een matrix met JSON-documenten heeft. Het hulpprogramma verwerkt de export voor u. U kunt er ook voor kiezen om de resulterende migratieopdracht weer te geven en de opdracht zelf uit voeren. Het resulterende JSON-bestand kan lokaal of in Azure Blob-opslag worden opgeslagen.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Schermopname van Azure Cosmos DB JSON-optie voor het exporteren van lokale bestanden":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Schermopname van de exportoptie voor Azure Cosmos DB JSON Azure Blob Storage":::

U kunt eventueel de resulterende JSON opschonen. Met deze actie wordt het resulterende document vergroot terwijl de inhoud beter leesbaar wordt.

* JSON-standaardexport

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Opgeschoonde JSON-export

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Hier volgt een opdrachtregelvoorbeeld om het JSON-bestand te exporteren naar Azure Blob-opslag:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Geavanceerde configuratie

In het scherm Geavanceerde configuratie geeft u de locatie op van het logboekbestand waarnaar eventuele fouten moeten worden geschreven. De volgende regels zijn van toepassing op deze pagina:

1. Als er geen bestandsnaam is opgegeven, worden alle fouten geretourneerd op de pagina Resultaten.
2. Als een bestandsnaam wordt opgegeven zonder een map, wordt het bestand gemaakt (of overschreven) in de huidige map in de omgeving.
3. Als u een bestaand bestand selecteert, wordt het bestand overschreven. Er bestaat geen toevoegoptie.
4. Kies vervolgens of u alle, kritieke, of geen foutberichten wilt vastleggen in het logboek. Ten slotte bepaalt u hoe vaak het overdrachtsbericht op het scherm moet worden bijgewerkt met de voortgang ervan.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="Schermopname van het scherm Geavanceerde configuratie":::

## <a name="confirm-import-settings-and-view-command-line"></a>De importinstellingen bevestigen en opdrachtregel weergeven

1. Nadat u de broninformatie, doelinformatie en geavanceerde configuratie hebt opgegeven, controleert u het migratieoverzicht, en bekijkt of kopieert u de resulterende migratieopdracht, als u wilt. (Het kopiëren van de opdracht is handig voor het automatiseren van importbewerkingen.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="Schermopname van het scherm Samenvatting.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Schermopname van het scherm Samenvatting met Command Line Preview.":::

2. Wanneer u tevreden bent met de bron en doelopties, klikt u op **Importeren**. De verstreken tijd, het aantal overgedragen bestanden en foutgegevens (als u geen bestandsnaam hebt opgegeven in de geavanceerde configuratie) worden bijgewerkt wanneer de import in bewerking is. Als dit klaar is, kunt u de resultaten exporteren (bijvoorbeeld om eventuele importfouten op te lossen).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Schermopname met de JSON-exportoptie van Azure Cosmos DB.":::

3. U kunt ook een nieuwe import starten door alle waarden opnieuw in te stellen of door de bestaande instellingen te behouden. (U kunt er bijvoorbeeld voor kiezen om de verbindingsreeksgegevens, de bron en het doel, en meer te behouden.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Schermopname met de JSON-exportoptie van Azure Cosmos DB met het dialoogvenster waarin de nieuwe import bevestigd wordt.":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Het hulpprogramma voor gegevensmigratie geïnstalleerd
> * Gegevens geïmporteerd uit verschillende gegevensbronnen
> * Geëxporteerd vanaf Azure Cosmos DB naar JSON

U kunt nu verder gaan met de volgende zelfstudie om te leren hoe u query's uitvoert op gegevens in Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Hoe kan ik query’s uitvoeren op gegevens?](../cosmos-db/tutorial-query-sql-api.md)
