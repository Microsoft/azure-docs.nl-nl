---
title: Restorable database accounts op locatie weer geven met behulp van Azure Cosmos DB REST API
description: Een lijst met alle herstor bare Azure Cosmos DB database accounts die beschikbaar zijn onder het abonnement en in een regio.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 2a3fbc1bb00c57c20436c19602c135f1917c6a60
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527667"
---
# <a name="list-restorable-database-accounts-by-location-using-azure-cosmos-db-rest-api"></a>Restorable database accounts op locatie weer geven met behulp van Azure Cosmos DB REST API

Een lijst met alle herstor bare Azure Cosmos DB database accounts die beschikbaar zijn onder het abonnement en in een regio. Voor deze aanroep is `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` toestemming vereist.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts?api-version=2020-06-01-preview

```

## <a name="uri-parameters"></a>URI-para meters

| Name | In | Vereist | Type | Description |
| --- | --- | --- | --- | --- |
| **location** | leertraject | True | tekenreeks| Azure Cosmos DB regio, met spaties tussen woorden en elk woord met een hoofd letter. |
| **subscriptionId** | leertraject | True | tekenreeks| De ID van het doel abonnement. |
| **API-versie** | query | True | tekenreeks | De API-versie die moet worden gebruikt voor deze bewerking. |

## <a name="responses"></a>Antwoorden

| Naam | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| De bewerking is voltooid. |
| Andere status codes | [DefaultErrorResponse](#defaulterrorresponse)| Fout bericht waarin wordt uitgelegd waarom de bewerking is mislukt. |

## <a name="examples"></a>Voorbeelden

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Voorbeeld aanvraag**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Voorbeeldantwoord**

Status code: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definities

|Definitie  | Beschrijving|
| --- | --- |
| [ApiType](#apitype) | Enum om het API-type van het herstorable database account aan te geven. |
| [DefaultErrorResponse](#defaulterrorresponse) | Een fout bericht van de service. |
| [ErrorResponse](#errorresponse) | Fout bericht. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Een Azure Cosmos DB restorable data base-account. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | De lijst bewerkings reactie, die de herstorable database accounts en de bijbehorende eigenschappen bevat. |
| [RestorableLocationResource](#restorablelocationresource) | Eigenschappen van de regionale restorable-account. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enum om het API-type van het herstorable database account aan te geven.

| **Naam** | **Type** |
| --- | --- |
| Cassandra |tekenreeks|
| Gremlin |tekenreeks|
| GremlinV2 |tekenreeks|
| MongoDB |tekenreeks|
| SQL |tekenreeks|
| Tabel |tekenreeks|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Een Azure Cosmos DB restorable data base-account.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Id |tekenreeks| De unieke resource-id van de Azure Resource Manager resource. |
| location |tekenreeks| De locatie van de resource groep waartoe de resource behoort. |
| naam |tekenreeks| De naam van de Resource Manager-resource. |
| Eigenschappen. AccountName |tekenreeks| De naam van het account van de globale data base. |
| Eigenschappen. apiType |[ApiType](#apitype)| Het API-type van het database account dat kan worden ontstorbaar. |
| Eigenschappen. creationTime |tekenreeks| De aanmaak tijd van het restorable database account (ISO-8601-indeling). |
| Eigenschappen. deletionTime |tekenreeks| Het tijdstip waarop het restorable database account is verwijderd (ISO-8601-indeling). |
| Eigenschappen. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Lijst met regio's waarvan het database account kan worden hersteld. |
| type |tekenreeks| Het type Azure-resource. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

De lijst bewerkings reactie, die de herstorable database accounts en de bijbehorende eigenschappen bevat.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| waarde |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Lijst met herstorable database accounts en de bijbehorende eigenschappen. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

Eigenschappen van de regionale restorable-account.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| creationTime |tekenreeks| De aanmaak tijd van het regionale restorable database account (ISO-8601-indeling). |
| deletionTime |tekenreeks| Het tijdstip waarop het regionale restorable database account is verwijderd (ISO-8601-indeling). |
| locatie |tekenreeks| De locatie van het regionale restorable-account. |
| regionalDatabaseAccountInstanceId |tekenreeks| De exemplaar-ID van de regionale restorable-account. |

## <a name="next-steps"></a>Volgende stappen

* [Restorable verzamelingen weer geven](restorable-database-accounts-list-by-location.md) in azure Cosmos DB-API voor MongoDb met behulp van rest API.
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.