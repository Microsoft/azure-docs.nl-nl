---
title: De Azure Storage-tabelservice of Azure Cosmos DB Table-API gebruiken in PHP
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage of de Azure Cosmos DB Table-API van PHP.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: 9d059c899e4a64d4d2c1b880b2a1d0f89258f33b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079628"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>De Azure Storage-tabelservice of de Azure Cosmos DB Table-API gebruiken in PHP
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Dit artikel laat zien hoe u tabellen maakt, uw gegevens opslaat en CRUD-bewerkingen op de gegevens uitvoert. Kies de Azure Table-service of de Azure Cosmos DB Table-API. De voorbeelden zijn geschreven in PHP en maken gebruik van de [clientbibliotheek voor PHP voor Azure Storage-tabellen][download]. De volgende scenario's worden behandeld: **een tabel maken en verwijderen** en **entiteiten in een tabel invoegen, verwijderen en hierop query's uitvoeren**. Zie het gedeelte [Volgende stappen](#next-steps) voor meer informatie over de Azure-tabelservice.

## <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Een Azure-opslagaccount maken**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Een Azure Cosmos DB Table-API-account maken**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Een PHP-toepassing maken

De enige vereiste voor het maken van een PHP-toepassing voor de toegang tot de Azure Storage-tabelservice of Azure Cosmos DB Table-API is dat in de code wordt verwezen naar klassen in de azure-storage-table-SDK voor PHP. U kunt elk ontwikkelprogramma gebruiken om uw toepassing te maken, waaronder Kladblok.

In deze handleiding gebruikt u de Azure Storage-tabelservice of Azure Cosmos DB-functies die lokaal kunnen worden aangeroepen vanuit een PHP-toepassing of in de code die wordt uitgevoerd in een Azure-webrol, -werkrol of -website.

## <a name="get-the-client-library"></a>De clientbibliotheek ophalen

1. Maak een bestand met de naam composer.json in de hoofdmap van uw project en voeg de volgende code toe:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Download [composer.phar](https://getcomposer.org/composer.phar) naar de hoofdmap. 
3. Open een opdrachtprompt en voer de volgende opdracht uit in de hoofdmap van uw project:
   ```
   php composer.phar install
   ```
   U kunt ook naar de [clientbibliotheek voor PHP voor Azure Storage-tabellen](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) op GitHub gaan om de broncode te klonen.

## <a name="add-required-references"></a>Vereiste verwijzingen toevoegen

Als u de Azure Storage-tabelservice of Azure Cosmos DB-API's wilt gebruiken, moet u het volgende doen:

* Maak een verwijzing naar het autoloaderbestand met de instructie [require_once][require_once] en
* een verwijzing naar alle klassen die u gebruikt.

In het volgende voorbeeld wordt getoond hoe u het autoloaderbestand opneemt en een verwijzing maakt naar de klasse **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

In de onderstaande voorbeelden wordt de instructie `require_once` altijd weergegeven, maar er wordt alleen verwezen naar de klassen die nodig zijn om het voorbeeld uit te voeren.

## <a name="add-your-connection-string"></a>Uw verbindingsreeks toevoegen

U kunt verbinding maken met het Azure Storage-account of het Azure Cosmos DB Table-API-account. Haal de verbindingsreeks op voor het soort account dat u gebruikt.

### <a name="add-a-storage-table-service-connection"></a>Een verbinding voor de Azure Storage-tabelservice toevoegen

U moet eerst over een geldige verbindingsreeks beschikken voordat u een instantie kunt maken van een client voor de Azure Storage-tabelservice. De indeling voor de verbindingsreeks voor de Azure Storage-tabelservice is:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>Een verbinding voor de opslagemulator toevoegen

U kunt als volgt toegang krijgen tot de opslagemulator:

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Een Azure Cosmos DB-verbinding toevoegen

U moet eerst over een geldige verbindingsreeks beschikken voordat u een instantie kunt maken van een Azure Cosmos DB Table-client. De indeling voor de Azure Cosmos DB-verbindingsreeks is:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

Als u een client voor de Azure Storage-tabelservice of een Azure Cosmos DB-client wilt maken, moet u de klasse **TableRestProxy** gebruiken. U kunt:

* De verbindingsreeks rechtstreeks aan deze klasse doorgeven of
* De **CloudConfigurationManager (CCM)** gebruiken om meerdere externe bronnen te controleren op de verbindingsreeks:
  * Deze wordt standaard geleverd met ondersteuning voor één externe bron: omgevingsvariabelen.
  * U kunt nieuwe bronnen toevoegen door de klasse `ConnectionStringSource` uit te breiden.

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Een tabel maken

Met het object **TableRestProxy** kunt u een tabel maken via de methode **createTable**. Bij het maken van een tabel kunt u de time-out voor de tabelservice instellen. (Zie [Time-outs instellen voor tabelservicebewerkingen][table-service-timeouts] voor meer informatie over de time-out voor de tabelservice.)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Zie [Het gegevensmodel van de tabelservice][table-data-model] voor meer informatie over beperkingen voor tabelnamen.

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Als u een entiteit wilt toevoegen aan een tabel, maakt u een nieuw object **Entity** en geeft u dit door aan **TableRestProxy->insertEntity**. Wanneer u een entiteit maakt, moet u een `PartitionKey` en `RowKey` opgeven. Dit zijn de unieke id's voor een entiteit en zijn waarden waarop sneller query's kunnen worden uitgevoerd dan op andere entiteitseigenschappen. Het systeem maakt gebruik van `PartitionKey` om de entiteiten van de tabel automatisch over veel Storage-knooppunten te verdelen. Entiteiten met dezelfde `PartitionKey` worden op hetzelfde knooppunt opgeslagen. (Bewerkingen in meerdere entiteiten die zijn opgeslagen op hetzelfde knooppunt kunnen beter worden uitgevoerd dan bewerkingen in entiteiten die zijn opgeslagen op verschillende knooppunten.) De `RowKey` is de unieke id van een entiteit binnen een partitie.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Zie [Het gegevensmodel van de tabelservice][table-data-model] voor meer informatie over tabeleigenschappen en -typen.

U kunt met de klasse **TableRestProxy** op twee manieren entiteiten invoegen: **insertOrMergeEntity** en **insertOrReplaceEntity**. Als u deze methoden wilt gebruiken, maakt u een nieuwe **entiteit** en geeft u deze als parameter door aan een van de methoden. Met beide methoden wordt de entiteit ingevoegd, als deze nog niet bestaat. Als de entiteit al bestaat, worden met **insertOrMergeEntity** de eigenschapswaarden bijgewerkt als de eigenschappen al bestaan en worden nieuwe eigenschappen toegevoegd als deze nog niet bestaan. Met **insertOrReplaceEntity** wordt de bestaande entiteit volledig vervangen. In het volgende voorbeeld wordt getoond hoe u **insertOrMergeEntity** gebruikt. Als de entiteit met `PartitionKey` 'tasksSeattle' en `RowKey` '1' nog niet bestaat, wordt deze ingevoegd. Als deze echter eerder is ingevoegd (zoals weergegeven in het bovenstaande voorbeeld), wordt de eigenschap `DueDate` bijgewerkt en wordt de eigenschap `Status` toegevoegd. De eigenschappen `Description` en `Location` worden ook bijgewerkt, maar met waarden waardoor ze feitelijk ongewijzigd blijven. Als deze laatste twee eigenschappen niet zouden zijn toegevoegd, zoals in het voorbeeld, maar zouden voorkomen in de doelentiteit, zouden de bestaande waarden voor de eigenschappen ongewijzigd blijven.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Eén entiteit ophalen

Met de methode **TableRestProxy->getEntity** kunt u één entiteit ophalen door de `PartitionKey` en `RowKey` van de entiteit op te vragen. In het volgende voorbeeld worden de partitiesleutel `tasksSeattle` en de rijsleutel `1` doorgegeven aan de methode **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Entiteitsquery's worden samengesteld met filters (zie [Query's uitvoeren op tabellen en entiteiten][filters] voor meer informatie). Als u alle entiteiten in de partitie wilt ophalen, gebruikt u het filter 'PartitionKey eq *partition_name*'. In het volgende voorbeeld wordt getoond hoe u alle entiteiten in de partitie `tasksSeattle` ophaalt door een filter door te geven aan de methode **queryEntities**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Een subset van entiteiten in een partitie ophalen

Het patroon uit het vorige voorbeeld kan ook worden gebruikt voor het ophalen van een subset van entiteiten in een partitie. De subset van entiteiten die u ophaalt, wordt bepaald door het filter dat u gebruikt (zie [Query's uitvoeren op tabellen en entiteiten][filters] voor meer informatie). In het volgende voorbeeld wordt getoond hoe u een filter gebruikt voor het ophalen van alle entiteiten met een specifieke `Location` en een `DueDate` die vroeger is dan een opgegeven datum.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Een subset van entiteitseigenschappen ophalen

U kunt met een query een subset van entiteitseigenschappen ophalen. Deze methode, *projectie* genoemd, verbruikt minder bandbreedte en kan de queryprestaties verbeteren, vooral bij grote entiteiten. Als u een eigenschap wilt opgeven die moet worden opgehaald, geeft u de naam van de eigenschap door aan de methode **Query->addSelectField**. U kunt deze methode meerdere keren aanroepen om meer eigenschappen toe te voegen. Nadat u **TableRestProxy->queryEntities** hebt uitgevoerd, hebben de geretourneerde entiteiten alleen de geselecteerde eigenschappen. (Als u een subset van tabelentiteiten wilt retourneren, gebruikt u een filter zoals in de bovenstaande query's wordt getoond.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Een entiteit bijwerken

U kunt een bestaande entiteit bijwerken met de methoden **Entity->setProperty** en **Entity->addProperty** voor de entiteit en vervolgens **TableRestProxy->updateEntity** aanroepen. In het volgende voorbeeld wordt een entiteit opgehaald, één eigenschap gewijzigd, een andere eigenschap verwijderd en wordt een nieuwe eigenschap toegevoegd. U kunt een eigenschap verwijderen door de waarde ervan in te stellen op **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen

Als u een entiteit wilt verwijderen, geeft u de tabelnaam en de `PartitionKey` en `RowKey` van de entiteit door aan de methode **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

U kunt voor gelijktijdigheidscontroles de Etag zo instellen dat een entiteit wordt verwijderd met de methode **DeleteEntityOptions->setEtag** en door het object **DeleteEntityOptions** als een vierde parameter door te geven aan **deleteEntity**.

## <a name="batch-table-operations"></a>Batchbewerkingen voor tabellen

Met de methode **TableRestProxy->batch** kunt u meerdere bewerkingen uitvoeren in één aanvraag. In dit patroon worden bewerkingen toegevoegd aan het object **BatchRequest** en wordt vervolgens het object **BatchRequest** doorgegeven aan de methode **TableRestProxy->batch**. Als u een bewerking wilt toevoegen aan een **BatchRequest**-object, kunt u een van de volgende methoden meerdere keren aanroepen:

* **addInsertEntity** (hiermee wordt een insertEntity-bewerking toegevoegd)
* **addUpdateEntity** (hiermee wordt een updateEntity-bewerking toegevoegd)
* **addMergeEntity** (hiermee wordt een mergeEntity-bewerking toegevoegd)
* **addInsertOrReplaceEntity** (hiermee wordt een insertOrReplaceEntity-bewerking toegevoegd)
* **addInsertOrMergeEntity** (hiermee wordt een insertOrMergeEntity-bewerking toegevoegd)
* **addDeleteEntity** (hiermee wordt een deleteEntity-bewerking toegevoegd)

In het volgende voorbeeld wordt getoond hoe u de bewerkingen **insertEntity** en **deleteEntity** uitvoert in één aanvraag. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Zie [Entiteitsgroepstransacties uitvoeren][entity-group-transactions] voor meer informatie over batchbewerkingen voor tabellen.

## <a name="delete-a-table"></a>Een tabel verwijderen

En als u ten slotte een tabel wilt verwijderen, geeft u de tabelnaam door aan de methode **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basisprincipes van de Azure-tabelservice en Azure Cosmos DB, volgt u deze koppelingen voor meer informatie.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.

* [PHP-ontwikkelaarscentrum](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions
