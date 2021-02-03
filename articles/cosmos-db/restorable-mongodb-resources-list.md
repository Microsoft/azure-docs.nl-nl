---
title: Restorable resources weer geven in Azure Cosmos DB-API voor MongoDB met behulp van REST API
description: Een lijst retour neren van de combi natie van data base en verzameling die op het account bestaan op basis van de opgegeven tijds tempel en locatie. Dit helpt in scenario's om te valideren welke resources bestaan op bepaalde tijds tempel en locatie.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dc90bfb6325831276b3c6171b73aebfa2877cf68
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527688"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Restorable resources weer geven in Azure Cosmos DB-API voor MongoDB met behulp van REST API

Een lijst retour neren van de combi natie van data base en verzameling die op het account bestaan op basis van de opgegeven tijds tempel en locatie. Dit helpt in scenario's om te valideren welke resources bestaan op bepaalde tijds tempel en locatie. Deze API vereist `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` machtiging.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Met optionele para meters:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI-para meters

| Name | In | Vereist | Type | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | leertraject | True |tekenreeks| De instanceId-GUID van een restorable data base-account. |
| **location** | leertraject | True | tekenreeks| Azure Cosmos DB regio, met spaties tussen woorden en elk woord met een hoofd letter. |
| **subscriptionId** | leertraject | True | tekenreeks| De ID van het doel abonnement. |
| **API-versie** | query | True | tekenreeks | De API-versie die moet worden gebruikt voor deze bewerking. |
| **restoreLocation** | query | |tekenreeks| De locatie waar de restorable bronnen zich bevinden. |
| **restoreTimestampInUtc** | query | |tekenreeks| De tijds tempel van het moment waarop de te ontstorende bronnen bestonden. |

## <a name="responses"></a>Antwoorden

| Naam | Type | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| De bewerking is voltooid. |
| Andere status codes | [DefaultErrorResponse](#defaulterrorresponse)| Fout bericht waarin wordt uitgelegd waarom de bewerking is mislukt. |


## <a name="examples"></a>Voorbeelden

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Voorbeeld aanvraag**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Voorbeeldantwoord**

Status code: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definities

| Definitie | Beschrijving | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Specifieke data bases die moeten worden hersteld. | | [DefaultErrorResponse](#defaulterrorresponse) | Een fout bericht van de service. | | [ErrorResponse](#errorresponse) | Fout bericht. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | De lijst bewerkings reactie die de herstorable SQL-resources bevat. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Specifieke data bases die moeten worden hersteld.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| collectionNames |teken reeks []| De namen van de verzamelingen die beschikbaar zijn voor herstel. |
| databaseName |tekenreeks| De naam van de data base die beschikbaar is voor herstel. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

De lijst bewerkings reactie die de herstorable Azure Cosmos DB-API voor MongoDB-resources bevat.

| **Naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| waarde |[DatabaseRestoreResource](#databaserestoreresource)[]| Lijst met restorable Azure Cosmos DB-API voor MongoDB-resources, waaronder de namen van de data base en verzameling. |

## <a name="next-steps"></a>Volgende stappen

* [Restorable data bases weer geven](restorable-mongodb-databases-list.md)  in azure Cosmos DB-API voor MongoDb met behulp van rest API.
* [Resource model](continuous-backup-restore-resource-model.md) van de modus voor continue back-ups.