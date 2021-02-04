---
title: Restorable SQL API-containers in Azure Cosmos DB weer geven met behulp van REST API
description: De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB SQL-containers onder een bepaalde data base. Dit kan in het scenario waarin de container per ongeluk is verwijderd.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527712"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Restorable SQL API-containers in Azure Cosmos DB weer geven met behulp van REST API

De gebeurtenis feed weer geven van alle mutaties die zijn uitgevoerd op alle Azure Cosmos DB SQL-containers onder een bepaalde data base. Dit kan in het scenario waarin de container per ongeluk is verwijderd. Voor deze API is `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` toestemming vereist

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Met optionele para meters:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI-para meters

| Name | In | Vereist | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | leertraject | True |tekenreeks| De instanceId-GUID van een restorable data base-account. |
| **location** | leertraject | True | tekenreeks| Azure Cosmos DB regio, met spaties tussen woorden en elk woord met een hoofd letter. |
| **subscriptionId** | leertraject | True | tekenreeks| De ID van het doel abonnement. |
| **API-versie** | query | True | tekenreeks | De API-versie die moet worden gebruikt voor deze bewerking. |
| **restorableSqlDatabaseRid** | query | |tekenreeks| De resource-ID van de SQL database. |

## <a name="responses"></a>Antwoorden

| Naam | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| De bewerking is voltooid. |
| Andere status codes | [DefaultErrorResponse](#defaulterrorresponse)| Fout bericht waarin wordt uitgelegd waarom de bewerking is mislukt. |

## <a name="examples"></a>Voorbeelden

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Voorbeeld aanvraag**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Voorbeeldantwoord**

Status code: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definities

| Definitie | Beschrijving | | --- || --- | | [Container](#container) | Azure Cosmos DB SQL-container Resource-object | | [DefaultErrorResponse](#defaulterrorresponse) | Een fout bericht van de service. | | [ErrorResponse](#errorresponse) | Fout bericht. | | [OperationType](#operationtype) | Enum om het bewerkings type van de gebeurtenis aan te geven. | | [Resource](#resource) | De resource van een Azure Cosmos DB SQL-container gebeurtenis | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Een Azure Cosmos DB SQL-container gebeurtenis | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | De eigenschappen van een Azure Cosmos DB SQL-container gebeurtenis | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | De lijst bewerkings reactie die de SQL-container gebeurtenissen en de bijbehorende eigenschappen bevat. |

### <a name="container"></a><a id="container"></a>Container

Azure Cosmos DB SQL-container Resource object

| **Naam**  |  **Type**  |  **Beschrijving** | | --- || --- | ---| | _etag | teken reeks | Een door het systeem gegenereerde eigenschap voor de resource-ETAG die vereist is voor optimistisch gelijktijdigheids beheer. | | _rid | teken reeks | Een door het systeem gegenereerde eigenschap. Een unieke id. | | _self | teken reeks | Een door het systeem gegenereerde eigenschap die het adresseer bare pad van de container resource opgeeft. | | _ts | | Een door het systeem gegenereerde eigenschap die het laatst bijgewerkte tijds tempel van de resource aanduidt. | | ID | teken reeks | De naam van de Azure Cosmos DB SQL-container |

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
| Maken |tekenreeks|gebeurtenis container maken|
| Verwijderen |tekenreeks|gebeurtenis container verwijderen|
| Vervangen |tekenreeks|container wijzigings gebeurtenis|
| SystemOperation |tekenreeks|de gebeurtenis voor het wijzigen van de container is geactiveerd door het systeem. Deze gebeurtenis wordt niet geïnitieerd door de gebruiker|

### <a name="resource"></a><a id="resource"></a>Resource

De resource van een Azure Cosmos DB SQL-container gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| _rid |tekenreeks| Een door het systeem gegenereerde eigenschap. Een unieke id. |
| container |[Container](#container)| Azure Cosmos DB SQL-container Resource object |
| eventTimestamp |tekenreeks| Het tijdstip waarop deze container gebeurtenis zich voordeed. |
| operationType |[OperationType](#operationtype)| Het bewerkings type van deze container gebeurtenis. |
| ownerId |tekenreeks| De naam van de SQL-container. |
| ownerResourceId |tekenreeks| De resource-ID van de SQL-container. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Een Azure Cosmos DB SQL-container gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | ---
| Id |tekenreeks| De unieke resource-id van de Azure Resource Manager resource. |
| naam |tekenreeks| De naam van de Azure Resource Manager resource. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| De eigenschappen van een SQL-container gebeurtenis. |
| type |tekenreeks| Het type Azure-resource. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

De eigenschappen van een Azure Cosmos DB SQL-container gebeurtenis

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| resource |[Resource](#resource)| De resource van een Azure Cosmos DB SQL-container gebeurtenis |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

De lijst bewerkings reactie die de SQL-container gebeurtenissen en de bijbehorende eigenschappen bevat.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| waarde |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Lijst met SQL-container gebeurtenissen en de bijbehorende eigenschappen. |

## <a name="next-steps"></a>Volgende stappen

* [Restorable data bases weer geven](restorable-mongodb-databases-list.md)  in azure Cosmos DB-API voor MongoDb met behulp van rest API.
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.