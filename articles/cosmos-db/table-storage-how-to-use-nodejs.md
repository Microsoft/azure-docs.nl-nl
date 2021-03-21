---
title: Azure Table Storage of de Azure Cosmos DB Table-API van Node.js
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage of de Azure Cosmos DB Table-API van Node.js.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079696"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Azure Table Storage of de Azure Cosmos DB Table-API van Node.js gebruiken
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Dit artikel wordt uitgelegd hoe u tabellen maakt, uw gegevens opslaat en CRUD-bewerkingen op de gegevens uitvoert. Kies de Azure Table service of de Azure Cosmos DB Table-API. De voorbeelden zijn geschreven in Node.js.

## <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Een Azure-opslagaccount maken**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Een Azure Cosmos DB Table-API-account maken**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Configureer uw toepassing voor toegang tot Azure Storage of de Azure Cosmos DB Table-API

U hebt voor het gebruik van Azure Storage of Azure Cosmos DB de Azure Storage SDK voor Node.js nodig. Dit bevat een set met handige bibliotheken die met de Storage REST-services communiceren.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Node Package Manager (NPM) gebruiken om het pakket te installeren

1. Gebruik een opdrachtregelinterface zoals **PowerShell** (Windows), **Terminal** (Mac) of **Bash** (Unix) en ga naar de map waar u uw toepassing hebt gemaakt.
2. Typ **npm install azure-storage** in het opdrachtvenster. De uitvoer van de opdracht is vergelijkbaar met het volgende voorbeeld.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. U kunt de **ls**-opdracht handmatig uitvoeren om te controleren of een **node_modules**-map is gemaakt. In deze map vindt u het **azure-storage**-pakket. Dit pakket bevat de bibliotheken die u nodig hebt om toegang te krijgen tot opslag.

### <a name="import-the-package"></a>Het pakket importeren

Voeg de volgende code toe aan het begin van het **server.js**-bestand in uw toepassing:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Uw verbindingsreeks toevoegen

U kunt verbinding maken met het Azure Storage-account of het Azure Cosmos DB Table-API-account. Haal de verbindingsreeks op voor het soort account dat u gebruikt.

### <a name="add-an-azure-storage-connection"></a>Een Azure Storage-verbinding toevoegen

De Azure Storage-module leest de omgevingsvariabelen AZURE_STORAGE_ACCOUNT en AZURE_STORAGE_ACCESS_KEY, of AZURE_STORAGE_CONNECTION_STRING voor de informatie die nodig is om verbinding te maken met uw Azure Storage-account. Als deze omgevingsvariabelen niet zijn ingesteld, moet u de accountgegevens opgeven bij het aanroepen van `TableService`. Zo wordt met de volgende code een `TableService`-object gemaakt:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Een Azure Cosmos DB-verbinding toevoegen

Als u een Azure Cosmos DB-verbinding wilt toevoegen, maakt u een `TableService`-object en geeft u uw accountnaam, primaire sleutel en eindpunt op. U kunt deze waarden kopiëren uit **Instellingen** > **Verbindingsreeks** in Azure Portal voor uw Cosmos DB-account. Bijvoorbeeld:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Een tabel maken

Met de volgende code maakt u een `TableService`-object en gebruikt u dit om een nieuwe tabel te maken.

```javascript
var tableSvc = azure.createTableService();
```

Met de aanroep van `createTableIfNotExists` wordt een nieuwe tabel met de opgegeven naam gemaakt als de tabel nog niet bestaat. In het volgende voorbeeld wordt een nieuwe tabel genaamd 'mytable' gemaakt als deze nog niet bestaat:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

`result.created` is `true` als een nieuwe tabel wordt gemaakt, en `false` als de tabel al bestaat. De `response` bevat informatie over de aanvraag.

### <a name="apply-filters"></a>Filters toepassen

U optioneel filters toepassen op de bewerkingen die worden uitgevoerd met behulp van `TableService`. Filterbewerkingen kunnen logboekregistratie, automatische nieuwe pogingen, enzovoort bevatten. Filters zijn objecten die een methode implementeren met de handtekening:

```javascript
function handle (requestOptions, next)
```

Na uitvoering van de voorbewerkingen voor de aanvraagopties, moet de methode **next** aanroepen en een retouraanroep doorgeven met de volgende handtekening:

```javascript
function (returnObject, finalCallback, next)
```

In deze retouraanroep en na verwerking van `returnObject` (de reactie van de aanvraag op de server), moet de retouraanroep `next` aanroepen als deze bestaat om door te gaan met het verwerken van andere filters of gewoon `finalCallback` aanroepen om te stoppen met service-aanroepen.

Bij de Azure-SDK voor Node.js zijn twee filters opgenomen waarmee logica voor opnieuw proberen wordt geïmplementeerd: `ExponentialRetryPolicyFilter` en `LinearRetryPolicyFilter`. Met de volgende code wordt een `TableService`-object gemaakt dat gebruikmaakt van `ExponentialRetryPolicyFilter`:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Wanneer u een entiteit wilt toevoegen, moet u eerst een object maken dat uw entiteitseigenschappen definieert. Alle entiteiten moeten een **PartitionKey** en **RowKey** bevatten, die unieke id's voor de entiteit zijn.

* **PartitionKey** bepaalt de partitie waarop de entiteit is opgeslagen.
* **RowKey** identificeert de entiteit in de partitie.

**PartitionKey** en **RowKey** moet beide tekenreekswaarden zijn. Zie [Het gegevensmodel van de tabelservice](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) voor meer informatie.

Hier volgt een voorbeeld van het definiëren van een entiteit. **dueDate** is gedefinieerd als een type van `Edm.DateTime`. Het specificeren van het type is optioneel en als dit niet wordt opgegeven, wordt het type afgeleid.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Er is ook een veld `Timestamp` voor elke record. Dit wordt door Azure ingesteld wanneer een entiteit wordt ingevoegd of bijgewerkt.

U kunt ook `entityGenerator` gebruiken om entiteiten te maken. In het volgende voorbeeld wordt de dezelfde taakentiteit gemaakt met behulp van `entityGenerator`.

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Wanneer u een entiteit wilt toevoegen aan de tabel, geeft u het entiteitsobject door aan de `insertEntity`-methode.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Als de bewerking is gelukt, bevat `result` de [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) van het ingevoegde record en `response` informatie over de bewerking.

Voorbeeld van een reactie:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Met`insertEntity` wordt niet standaard de ingevoegde entiteit geretourneerd als onderdeel van de `response`-informatie. Als u van plan bent andere bewerkingen uit te voeren op deze entiteit of de informatie in de cache wilt plaatsen, kan het handig zijn om de entiteit te retourneren als onderdeel van de `result`. U kunt dit doen door `echoContent` als volgt in te schakelen:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Een entiteit bijwerken

Er zijn meerdere methoden beschikbaar voor het bijwerken van een bestaande entiteit:

* `replaceEntity`: hiermee wordt een bestaande entiteit bijgewerkt door de entiteit te vervangen.
* `mergeEntity`: hiermee wordt een bestaande entiteit bijgewerkt door nieuwe eigenschapswaarden in de bestaande entiteit samen te voegen.
* `insertOrReplaceEntity`: hiermee wordt een bestaande entiteit bijgewerkt door de entiteit te vervangen. Als er geen entiteit bestaat, wordt er een nieuwe ingevoegd.
* `insertOrMergeEntity`: hiermee wordt een bestaande entiteit bijgewerkt door nieuwe eigenschapswaarden samen te voegen met bestaande. Als er geen entiteit bestaat, wordt er een nieuwe ingevoegd.

Het volgende voorbeeld laat zien hoe u een entiteit bijwerkt met `replaceEntity`:

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Standaard wordt bij het bijwerken van een entiteit niet gecontroleerd of de bij te werken gegevens eerder zijn gewijzigd door een ander proces. Gelijktijdige updates verwerken:
>
> 1. Haal de ETag op van het object dat wordt bijgewerkt. Deze wordt geretourneerd als onderdeel van de `response` voor elke entiteit-gerelateerde bewerking en kan worden opgehaald via `response['.metadata'].etag`.
> 2. Bij het uitvoeren van een updatebewerking op een entiteit, voegt u de eerder opgehaalde ETag-informatie toe aan de nieuwe entiteit. Bijvoorbeeld:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Voer de updatebewerking uit. Als de entiteit is gewijzigd sinds u de ETag-waarde hebt opgehaald, bijvoorbeeld in een ander exemplaar van uw toepassing, wordt een `error` geretourneerd met de mededeling dat niet is voldaan aan de updatevoorwaarde die is opgegeven in de aanvraag.
>
>

Als u `replaceEntity` en `mergeEntity` gebruikt en de bij te werken entiteit niet bestaat, mislukt de updatebewerking. Gebruik daarom `insertOrReplaceEntity` of `insertOrMergeEntity` als u een entiteit wilt opslaan ongeacht of deze al bestaat.

De `result` voor geslaagde updatebewerkingen bevat de **Etag** van de bijgewerkte entiteit.

## <a name="work-with-groups-of-entities"></a>Werken met groepen entiteiten

Soms is het zinvol om meerdere bewerkingen samen in een batch te verzenden om te zorgen dat ze atomisch worden verwerkt door de server. Gebruik hiervoor de `TableBatch`-klasse om een batch te maken en gebruik vervolgens de `executeBatch`-methode van `TableService` om de batchbewerkingen uit te voeren.

 In het volgende voorbeeld ziet u hoe u twee entiteiten in een batch kunt verzenden.

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Voor geslaagde batchbewerkingen bevat `result` informatie voor elke bewerking in de batch.

### <a name="work-with-batched-operations"></a>Batchbewerkingen gebruiken

U kunt bewerkingen die zijn toegevoegd aan een batch, inspecteren door de `operations`-eigenschap te bekijken. U kunt ook de volgende methoden gebruiken om te werken met bewerkingen:

* **clear** wist u alle bewerkingen uit een batch.
* **getOperations** haalt bewerkingen uit de batch.
* **hasOperations** retourneert 'true' als de batch bewerkingen bevat.
* **removeOperations** verwijdert een bewerking.
* **size** retourneert het aantal bewerkingen in de batch.

## <a name="retrieve-an-entity-by-key"></a>Een entiteit ophalen op basis van sleutel

Wanneer u een specifieke entiteit wilt retourneren op basis van de **PartitionKey** en **RowKey**, gebruikt u de **retrieveEntity**-methode.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Nadat u deze bewerking is voltooid, bevat `result` de entiteit.

## <a name="query-a-set-of-entities"></a>Een query uitvoeren voor een aantal entiteiten

Wanneer u een query wilt uitvoeren op een tabel, gebruikt u het **TableQuery**-object om een query-expressie op te bouwen met de volgende componenten:

* **select**: de velden die moeten worden geretourneerd door de query.
* **where**: de waar-component.

  * **and**: een `and` where-conditie.
  * **or**: een `or` where-conditie.
* **top**: het aantal op te halen items.

In het volgende voorbeeld wordt een query gemaakt waarmee de bovenste vijf items met een PartitionKey 'hometasks' worden geretourneerd.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Omdat **select** niet wordt gebruikt, worden alle velden geretourneerd. Als u de query wilt uitvoeren op een tabel, gebruikt u **queryEntities**. In het volgende voorbeeld wordt deze query gebruikt om entiteiten te retourneren uit 'mytable'.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Als dit lukt, bevat `result.entries` een matrix van entiteiten die overeenkomen met de query. Als de query kan niet alle entiteiten kan retourneren, is `result.continuationToken` niet-*null* en kan worden gebruikt als de derde parameter van **queryEntities** om meer resultaten op te halen. Gebruik voor de eerste query *null* voor de derde parameter.

### <a name="query-a-subset-of-entity-properties"></a>Een query uitvoeren op een subset van entiteitseigenschappen

Met een query naar een tabel kunnen slechts enkele velden van een entiteit worden opgehaald.
Dit verbruikt minder bandbreedte en kan de queryprestaties verbeteren, vooral bij grote entiteiten. Gebruik de **select**-component en geef de namen door van de velden die moeten worden geretourneerd. De volgende query retourneert bijvoorbeeld alleen de velden **description** en **dueDate**.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen

U kunt een entiteit verwijderen met behulp van zijn partities en rijsleutels. In dit voorbeeld bevat het **task1**-object de **RowKey**- en **PartitionKey**-waarden van de te verwijderen entiteit. Het object wordt doorgegeven aan de **deleteEntity**-methode.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Overweeg het gebruik van ETags bij het verwijderen van items om zeker te weten dat het item niet is gewijzigd door een ander proces. Zie [Een entiteit bijwerken](#update-an-entity) voor informatie over het gebruik van ETags.
>
>

## <a name="delete-a-table"></a>Een tabel verwijderen

Met de volgende code wordt een tabel uit een opslagaccount verwijderd.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Als u niet zeker weet of de tabel bestaat, gebruikt u **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Vervolgtokens gebruiken

Wanneer u query's uitvoert op tabellen en veel resultaten krijgt, let dan op de vervolgtokens. Mogelijk zijn er zonder dat u het weet grote hoeveelheden gegevens beschikbaar voor uw query als u in uw query geen rekening hebt gehouden met de aanwezigheid van vervolgtokens.

Het **results**-object geretourneerd tijdens het uitvoeren van een query op entiteitensets, stelt een `continuationToken`-eigenschap in wanneer een dergelijk token aanwezig is. Vervolgens kunt u deze bij het uitvoeren van een query gebruiken om door te gaan in de partitie- en tabelentiteiten.

Wanneer u een query uitvoert, kunt u een `continuationToken`-parameter opgeven tussen de queryobjectinstantie en de retouraanroepfunctie:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Als u het `continuationToken`-object inspecteert, vindt u eigenschappen zoals `nextPartitionKey`, `nextRowKey` en `targetLocation`, die kunnen worden gebruikt om door alle resultaten te lopen.

U kunt ook `top` ook in combinatie met `continuationToken` om de paginagrootte in te stellen.

## <a name="work-with-shared-access-signatures"></a>Werken met handtekeningen voor gedeelde toegang

Shared Access Signatures (SAS) zijn een veilige manier om op detailniveau toegang te bieden tot tabellen zonder dat u de naam of sleutels van uw opslagaccount hoeft te geven. SAS wordt vaak gebruikt voor beperkte toegang tot uw gegevens, bijvoorbeeld om een mobiele app toe te staan om een query uit te voeren op records.

Een vertrouwde toepassing zoals een cloud-gebaseerde service genereert een SAS met de **generateSharedAccessSignature** van de **TableService**, en biedt dit aan een niet-vertrouwde of semi-vertrouwde toepassing aan, zoals een mobiele app. De SAS wordt gegenereerd op basis van beleid, waarin de begin- en einddatum wordt vermeld voor de geldigheid van de SAS, evenals het toegangsniveau verleend aan de SAS-houder.

In het volgende voorbeeld wordt een nieuwe beleidsregel gegenereerd voor gedeelde toegang, die zorgt dat de SAS-houder een query ('r') kan uitvoeren op de tabel. Deze toegang verloopt 100 minuten na het tijdstip waarop de toegang is gemaakt.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Houd er rekening mee dat u ook de informatie over de host moet opgeven, aangezien dit is vereist wanneer de SAS-houder toegang probeert te krijgen tot de tabel.

De clienttoepassing maakt dan gebruik van de SAS met **TableServiceWithSAS** om bewerkingen op de tabel uit te voeren. In het volgende voorbeeld wordt verbinding gemaakt met de tabel en een query uitgevoerd. Zie het artikel [Beperkte toegang verlenen tot Azure Storage-resources via SAS (Shared Access Signatures)](../storage/common/storage-sas-overview.md) voor de indeling van tableSAS.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Omdat de SAS alleen querytoegang biedt, wordt er een fout geretourneerd als u entiteiten wilt invoegen, bijwerken of verwijderen.

### <a name="access-control-lists"></a>Toegangsbeheerlijsten

U kunt ook een ACL (Access Control List) gebruiken om het toegangsbeleid in te stellen voor een SAS. Dit is handig als u meerdere clients toegang wilt geven tot de tabel, maar voor elke client een ander toegangsbeleid wilt instellen.

Een ACL wordt geïmplementeerd met behulp van een matrix van toegangsbeleidregels, met een id die is gekoppeld aan elk beleid. In het volgende voorbeeld worden twee beleidsregels gedefinieerd, één voor 'gebruiker1' en één voor 'gebruiker2':

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

In het volgende voorbeeld wordt de huidige ACL voor de **hometasks**-tabel opgehaald en vervolgens het nieuwe beleid toegevoegd met behulp van **setTableAcl**. Deze aanpak biedt u de volgende mogelijkheid:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Nadat de ACL is ingesteld, kunt u een SAS maken op basis van de id van een beleid. In het volgende voorbeeld wordt een nieuwe SAS voor 'gebruiker2' gemaakt:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende informatie bronnen voor meer informatie:

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
* [Azure Storage SDK voor Node.js](https://github.com/Azure/azure-storage-node)-opslag op GitHub.
* [Azure voor Node.js-ontwikkelaars](/azure/developer/javascript/)
* [Een Node.js-web-app maken in Azure](../app-service/quickstart-nodejs.md)
* [Een Node.js-toepassing bouwen en implementeren in een Azure Cloud Service](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (met Windows PowerShell)