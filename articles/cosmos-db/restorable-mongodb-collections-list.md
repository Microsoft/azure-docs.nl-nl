---
title: Restorable-verzamelingen weer geven in Azure Cosmos DB MongoDB API-REST API
description: De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB MongoDB-verzamelingen onder een bepaalde data base. Dit kan in het scenario waarin de container per ongeluk is verwijderd.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537540"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Restorable verzamelingen weer geven in Azure Cosmos DB-API voor MongoDB met behulp van REST API

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB-API voor MongoDB-verzamelingen onder een bepaalde data base. Dit kan in het scenario waarin de container per ongeluk is verwijderd. Voor deze API is `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` toestemming vereist

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Met optionele para meters:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI-para meters

| Name | In | Vereist | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | leertraject | True |tekenreeks| De instanceId-GUID van een restorable data base-account. |
| **location** | leertraject | True | tekenreeks| Azure Cosmos DB regio, met spaties tussen woorden en elk woord met een hoofd letter. |
| **subscriptionId** | leertraject | True | tekenreeks| De ID van het doel abonnement. |
| **API-versie** | query | True | tekenreeks | De API-versie die moet worden gebruikt voor deze bewerking. |
| **restorableMongodbDatabaseRid** | query | |tekenreeks| De resource-ID van de Azure Cosmos DB-API voor de MongoDB-data base. |

## <a name="responses"></a>Antwoorden

| Naam | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| De bewerking is voltooid. |
| Andere status codes | [DefaultErrorResponse](#defaulterrorresponse)| Fout bericht waarin wordt uitgelegd waarom de bewerking is mislukt.|

## <a name="examples"></a>Voorbeelden

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Voorbeeld aanvraag**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Voorbeeldantwoord**

Status code: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definities

|Definitie  | Beschrijving|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Een fout bericht van de service. |
| [ErrorResponse](#errorresponse) | Fout bericht. |
| [OperationType](#operationtype) | Enum om het bewerkings type van de gebeurtenis aan te geven. |
| [Resource](#resource) | De bron van een Azure Cosmos DB-API voor de verzamelings gebeurtenis MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Een Azure Cosmos DB-API voor MongoDB-verzamelings gebeurtenis |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | De eigenschappen van een Azure Cosmos DB-API voor de verzamelings gebeurtenis MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | De lijst bewerkings reactie, die de verzamelings gebeurtenissen en de bijbehorende eigenschappen bevat. |

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
| Maken |tekenreeks|gebeurtenis voor het maken van een verzameling|
| Verwijderen |tekenreeks|gebeurtenis verzameling verwijderen|
| Vervangen |tekenreeks|wijzigings gebeurtenis voor verzameling|

### <a name="resource"></a><a id="resource"></a>Resource

De resource van een Azure Cosmos DB MongoDB-verzamelings gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| _rid |tekenreeks| Een door het systeem gegenereerde eigenschap. Een unieke id. |
| eventTimestamp |tekenreeks| Het tijdstip waarop deze verzamelings gebeurtenis heeft plaatsgevonden. |
| operationType |[OperationType](#operationtype)|  Het bewerkings type van deze verzamelings gebeurtenis. |
| ownerId |tekenreeks| De naam van de MongoDB-verzameling.|
| ownerResourceId |tekenreeks| De resource-ID van de MongoDB-verzameling. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Een Azure Cosmos DB MongoDB-verzamelings gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Id |tekenreeks| De unieke resource-id van de Azure Resource Manager resource. |
| naam |tekenreeks| De naam van de Resource Manager-resource. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| De eigenschappen van een verzamelings gebeurtenis. |
| type |tekenreeks| Het type Azure-resource. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

De eigenschappen van een Azure Cosmos DB verzamelings gebeurtenis MongoDB

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| resource | [Resource](#resource)| De bron van een Azure Cosmos DB-API voor de verzamelings gebeurtenis MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

De lijst bewerkings reactie, die de verzamelings gebeurtenissen en de bijbehorende eigenschappen bevat.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| waarde |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Lijst met Azure Cosmos DB-API voor MongoDB en hun eigenschappen. |

## <a name="next-steps"></a>Volgende stappen

* [Restorable data bases weer geven](restorable-mongodb-databases-list.md)  in azure Cosmos DB-API voor MongoDb met behulp van rest API.
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.