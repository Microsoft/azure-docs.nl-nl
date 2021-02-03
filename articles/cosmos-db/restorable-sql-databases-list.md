---
title: Restorable SQL API-data bases in Azure Cosmos DB weer geven met behulp van REST API
description: De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB SQL-data bases onder het restorable-account. Dit helpt in het scenario waarin de data base per ongeluk is verwijderd om de verwijderings tijd te verkrijgen.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: d3d72cff5fcfeed17d60e2f856be4adc1a983819
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527366"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Restorable SQL API-data bases in Azure Cosmos DB weer geven met behulp van REST API

De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB SQL-data bases onder het restorable-account. Dit helpt in het scenario waarin de data base per ongeluk is verwijderd om de verwijderings tijd te verkrijgen. Voor deze API is `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` toestemming vereist

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| De bewerking is voltooid. |
| Andere status codes | [DefaultErrorResponse](#defaulterrorresponse)| Fout bericht waarin wordt uitgelegd waarom de bewerking is mislukt. |

## <a name="examples"></a>Voorbeelden

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Voorbeeldaanvraag**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Voorbeeldantwoord**

Status code: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definities

| Definitie | Beschrijving | | --- || --- | | [Data Base](#database) | Resource-object Azure Cosmos DB SQL database | | [DefaultErrorResponse](#defaulterrorresponse) | Een fout bericht van de service. | | [ErrorResponse](#errorresponse) | Fout bericht. | | [OperationType](#operationtype) | Enum om het bewerkings type van de gebeurtenis aan te geven. | | [Resource](#resource) | De resource van een Azure Cosmos DB SQL database-gebeurtenis | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Een Azure Cosmos DB SQL database-gebeurtenis | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | De eigenschappen van een Azure Cosmos DB SQL database-gebeurtenis | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | De lijst bewerkings reactie die de SQL database gebeurtenissen en de bijbehorende eigenschappen bevat. |

### <a name="database"></a><a id="database"></a>Enddatabase

Azure Cosmos DB SQL database Resource object

| **Naam**  |  **Type**  |  **Beschrijving** | | --- || --- | ---| | _colls | teken reeks | Een door het systeem gegenereerde eigenschap die het adresseer bare pad van de verzamelings bron heeft opgegeven. | | _etag | teken reeks | Een door het systeem gegenereerde eigenschap voor de resource-ETAG die vereist is voor optimistisch gelijktijdigheids beheer. | | _rid | teken reeks | Een door het systeem gegenereerde eigenschap. Een unieke id. | | _self | teken reeks | Een door het systeem gegenereerde eigenschap die het adresseer bare pad van de database resource opgeeft. | | _ts | | Een door het systeem gegenereerde eigenschap die het laatst bijgewerkte tijds tempel van de resource aanduidt. | | _users | teken reeks | Een door het systeem gegenereerde eigenschap waarmee het adresseer bare pad van de bron van de gebruiker wordt opgegeven. | | ID | teken reeks | De naam van de Azure Cosmos DB SQL database |

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
| SystemOperation |tekenreeks|de wijzigings gebeurtenis voor de data base is geactiveerd door het systeem. Deze gebeurtenis wordt niet geïnitieerd door de gebruiker|

### <a name="resource"></a><a id="resource"></a>Resource

De resource van een Azure Cosmos DB SQL database gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| _rid |tekenreeks| Een door het systeem gegenereerde eigenschap. Een unieke id. |
| database |[Database](#database)| Azure Cosmos DB SQL database Resource object |
| eventTimestamp |tekenreeks| Het tijdstip waarop deze database gebeurtenis heeft plaatsgevonden. |
| operationType |[OperationType](#operationtype)| Het bewerkings type van deze database gebeurtenis. |
| ownerId |tekenreeks| De naam van de SQL database. |
| ownerResourceId |tekenreeks| De resource-ID van de SQL database. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Een Azure Cosmos DB SQL database-gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Id |tekenreeks| De unieke resource-id van de Azure Resource Manager resource. |
| naam |tekenreeks| De naam van de Azure Resource Manager resource. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| De eigenschappen van een SQL database gebeurtenis. |
| type |tekenreeks| Het type Azure-resource. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

De eigenschappen van een Azure Cosmos DB SQL database gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| resource |[Resource](#resource)| De resource van een Azure Cosmos DB SQL database gebeurtenis |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

De lijst bewerkings reactie die de SQL database gebeurtenissen en de bijbehorende eigenschappen bevat.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| waarde |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Lijst met SQL database gebeurtenissen en hun eigenschappen. |

## <a name="next-steps"></a>Volgende stappen

* [Restorable containers weer geven](restorable-sql-containers-list.md) in azure Cosmos DB SQL-API met behulp van rest API.
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.