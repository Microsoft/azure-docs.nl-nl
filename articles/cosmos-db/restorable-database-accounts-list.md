---
title: Restorable database accounts weer geven met behulp van Azure Cosmos DB REST API
description: Een lijst met alle herstor bare Azure Cosmos DB database accounts die beschikbaar zijn in een abonnement.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 71aafc756e5291e148c3b162f8946544b6e3c2d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537574"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Restorable database accounts weer geven met behulp van Azure Cosmos DB REST API

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Een lijst met alle herstor bare Azure Cosmos DB database accounts die beschikbaar zijn in het abonnement. Voor deze aanroep is `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` toestemming vereist.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-para meters

| Name | In | Vereist | Type | Description |
| --- | --- | --- | --- | --- |
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
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
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
      "location": "East US",
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
| Eigenschappen. AccountName |tekenreeks| De naam van het globale database account |
| Eigenschappen. apiType |[ApiType](#apitype)| Het API-type van het database account dat kan worden ontstorbaar. |
| Eigenschappen. creationTime |tekenreeks| De aanmaak tijd van het restorable database account (ISO-8601-indeling). |
| Eigenschappen. deletionTime |tekenreeks| Het tijdstip waarop het restorable database account is verwijderd (ISO-8601-indeling). |
| Eigenschappen. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Lijst met regio's waarvan het database account kan worden hersteld. |
| type |tekenreeks| Het type Azure-resource. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

De lijst bewerkings respons die de herstorable database accounts en de bijbehorende eigenschappen bevat.

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

* [Restorable data base accounts: lijst per locatie.](restorable-database-accounts-list-by-location.md)
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.