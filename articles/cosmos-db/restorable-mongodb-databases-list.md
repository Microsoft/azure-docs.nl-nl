---
title: Restorable data bases weer geven in Azure Cosmos DB-API voor MongoDB met behulp van REST API
description: De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB MongoDB-data bases onder het restorable-account. Dit helpt in het scenario waarin de data base per ongeluk is verwijderd om de verwijderings tijd te verkrijgen.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527693"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Restorable data bases weer geven in Azure Cosmos DB-API voor MongoDB met behulp van REST API

De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB MongoDB-data bases onder het restorable-account. Dit helpt in het scenario waarin de data base per ongeluk is verwijderd om de verwijderings tijd te verkrijgen. Voor deze API is `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` toestemming vereist

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-para meters

| Name | In | Vereist | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | leertraject | True |tekenreeks| De instanceId-GUID van een restorable data base-account. |
| **location** | leertraject | True | tekenreeks| Azure Cosmos DB regio, met spaties tussen woorden en elk woord met een hoofd letter. |
| **subscriptionId** | leertraject | True | tekenreeks| De ID van het doel abonnement. |
| **API-versie** | query | True | tekenreeks | De API-versie die moet worden gebruikt voor deze bewerking. |

## <a name="responses"></a>Antwoorden

| Naam | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| De bewerking is voltooid. |
| Andere status codes | [DefaultErrorResponse](#defaulterrorresponse)| Fout bericht waarin wordt uitgelegd waarom de bewerking is mislukt.|

## <a name="examples"></a>Voorbeelden

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Voorbeeld aanvraag**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Voorbeeldantwoord**

Status code: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definities

| Definitie | Beschrijving | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Een fout bericht van de service. | | [ErrorResponse](#errorresponse) | Fout bericht. | | [OperationType](#operationtype) | Enum om het bewerkings type van de gebeurtenis aan te geven. | | [Resource](#resource) | De resource van een Azure Cosmos DB-API voor MongoDB-database gebeurtenis | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Een Azure Cosmos DB-API voor MongoDB-database gebeurtenis | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | De eigenschappen van een Azure Cosmos DB-API voor de MongoDB-database gebeurtenis | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | De lijst bewerkings respons die de Azure Cosmos DB-API bevat voor MongoDB-database gebeurtenissen en hun eigenschappen. |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Een fout bericht van de service.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| fout | [ErrorResponse](#errorresponse)| Fout bericht. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Fout bericht.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| code |tekenreeks| Foutcode. |
| message |tekenreeks| Fout bericht dat aangeeft waarom de bewerking is mislukt. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Enum om het bewerkings type van de gebeurtenis aan te geven.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Maken |tekenreeks|gebeurtenis voor het maken van een Data Base|
| Verwijderen |tekenreeks|database verwijderings gebeurtenis|
| Vervangen |tekenreeks|data base-wijzigings gebeurtenis|

### <a name="resource"></a><a id="resource"></a>Resource

De resource van een Azure Cosmos DB-API voor MongoDB-database gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| _rid |tekenreeks| Een door het systeem gegenereerde eigenschap. Een unieke id. |
| eventTimestamp |tekenreeks| Het tijdstip waarop deze database gebeurtenis heeft plaatsgevonden. |
| operationType |[OperationType](#operationtype)| Het bewerkings type van deze database gebeurtenis.  |
| ownerId |tekenreeks| De naam van de Azure Cosmos DB-API voor de MongoDB-data base.|
| ownerResourceId |tekenreeks| De resource-ID Azure Cosmos DB-API voor de MongoDB-data base. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Een Azure Cosmos DB-API voor MongoDB-database gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Id |tekenreeks| De unieke resource-id van de Azure Resource Manager resource. |
| naam |tekenreeks| De naam van de Resource Manager-resource. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| De eigenschappen van een Azure Cosmos DB-API voor de MongoDB-database gebeurtenis. |
| type |tekenreeks| Het type Azure-resource. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

De eigenschappen van een Azure Cosmos DB-API voor de MongoDB-database gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| resource |[Resource](#resource)| De resource van een Azure Cosmos DB-API voor MongoDB-database gebeurtenis |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

De lijst bewerkings reactie die de database gebeurtenissen en de bijbehorende eigenschappen bevat.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| waarde |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Lijst met database gebeurtenissen en hun eigenschappen. |

## <a name="next-steps"></a>Volgende stappen

* [Restorable resources weer geven](restorable-mongodb-resources-list.md)  in azure Cosmos DB-API voor MongoDb met behulp van rest API.
* [Restorable verzamelingen weer geven](restorable-mongodb-collections-list.md)  in azure Cosmos DB-API voor MongoDb met behulp van rest API.
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.