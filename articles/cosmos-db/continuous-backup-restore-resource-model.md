---
title: Resource model voor de functie voor het terugzetten van Azure Cosmos DB naar een bepaald tijdstip.
description: In dit artikel wordt het resource model uitgelegd voor de functie voor het terugzetten van Azure Cosmos DB naar een bepaalde tijd. Hierin worden de para meters uitgelegd die ondersteuning bieden voor de continue back-up en bronnen die kunnen worden hersteld in Azure Cosmos DB-API voor SQL-en MongoDB-accounts.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 64033182356e66d6a69bd47c1780b7081416019e
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527396"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature"></a>Resource model voor de functie voor het terugzetten van Azure Cosmos DB naar een bepaald tijdstip
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In dit artikel wordt het resource model uitgelegd voor de functie voor het terugzetten van Azure Cosmos DB naar een bepaalde tijd. Hierin worden de para meters uitgelegd die ondersteuning bieden voor de continue back-up en bronnen die kunnen worden hersteld in Azure Cosmos DB-API voor SQL-en MongoDB-accounts.

## <a name="database-accounts-resource-model"></a>Resource model van het database account

Het resource model van het database account is bijgewerkt met enkele extra eigenschappen voor het ondersteunen van de nieuwe herstel scenario's. Deze eigenschappen zijn **BackupPolicy, CreateMode en RestoreParameters.**

### <a name="backuppolicy"></a>BackupPolicy

Een nieuwe eigenschap in het beleid voor back-up op account niveau met de naam ' type ' onder de para meter ' backuppolicy ' maakt continue back-ups en functies voor herstel naar een bepaald tijdstip mogelijk. Deze modus heet **doorlopende back-up**. In de open bare preview-versie kunt u deze modus alleen instellen wanneer u het account maakt. Nadat deze is ingeschakeld, hebben alle containers en data bases die zijn gemaakt in dit account, voortdurend back-ups en functies voor herstel op tijdstippen ingeschakeld.

> [!NOTE]
> Op dit moment is de functie voor herstel naar een bepaald tijdstip beschikbaar in de open bare preview. deze kan worden gemaakt voor Azure Cosmos DB-API voor MongoDB en SQL-accounts. Nadat u een account met een doorlopende modus hebt gemaakt, kunt u deze niet overschakelen naar een periodieke modus.

### <a name="createmode"></a>CreateMode

Deze eigenschap geeft aan hoe het account is gemaakt. De mogelijke waarden zijn ' default ' en ' Restore '. Als u een herstel bewerking wilt uitvoeren, stelt u deze waarde in op ' herstellen ' en geeft u de juiste waarden op in de `RestoreParameters` eigenschap.

### <a name="restoreparameters"></a>RestoreParameters

De `RestoreParameters` resource bevat de details van de herstel bewerking, met inbegrip van de account-id, de tijd die moet worden teruggezet en de resources die moeten worden hersteld.

|Eigenschapsnaam |Description  |
|---------|---------|
|restoreMode  | De herstel modus moet ' PointInTime ' zijn |
|restoreSource   |  De instanceId van het bron account waarvan de herstel bewerking wordt gestart.       |
|restoreTimestampInUtc  | Het tijdstip waarop het account moet worden teruggezet in de UTC-tijd. |
|databasesToRestore   | Lijst met `DatabaseRestoreSource` objecten om op te geven welke data bases en containers moeten worden hersteld. Als deze waarde leeg is, wordt het hele account hersteld.   |

**DatabaseRestoreResource** : elke resource vertegenwoordigt één data base en alle verzamelingen onder die data base.

|Eigenschapsnaam |Description  |
|---------|---------|
|databaseName | De naam van de database |
| collectionNames| De lijst met containers onder deze data base |

### <a name="sample-resource"></a>Voorbeeld resource

De volgende JSON is een voor beeld van een database account bron waarvoor continue back-up is ingeschakeld:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Restorable resources

Er is een set nieuwe resources en Api's beschikbaar om u te helpen bij het detecteren van belang rijke informatie over bronnen, die kunnen worden hersteld, locaties waar ze kunnen worden hersteld en de tijds tempels wanneer belang rijke bewerkingen op deze resources zijn uitgevoerd.

> [!NOTE]
> Alle API-bestanden die worden gebruikt voor het inventariseren van deze resources, hebben de volgende machtigingen nodig:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Restorable data base account

Deze resource bevat een exemplaar van een database account dat kan worden hersteld. Het database account kan een verwijderd of een Live-account zijn. Het bevat informatie waarmee u het bron database account kunt vinden dat u wilt herstellen.

|Eigenschapsnaam |Beschrijving  |
|---------|---------|
| Id | De unieke id van de resource. |
| accountName | De naam van het algemene database account. |
| creationTime | De tijd in UTC waarop het account is gemaakt.  |
| deletionTime | De tijd in UTC waarop het account is verwijderd.  Deze waarde is leeg als het account Live is. |
| apiType | Het API-type van het Azure Cosmos DB-account. |
| restorableLocations | De lijst met locaties waar het account bestond. |
| restorableLocations: locatie | De regio naam van het regionale account. |
| restorableLocations: regionalDatabaseAccountInstanceI | De GUID van het regionale account. |
| restorableLocations: creationTime | De tijd in UTC waarop het regionale account is gemaakt.|
| restorableLocations: deletionTime | De tijd in UTC waarop het regionale account is verwijderd. Deze waarde is leeg als het regionale account actief is.|

Voor een lijst met alle herstorable accounts raadpleegt u [restorable data base accounts-list](restorable-database-accounts-list.md) of [restorable data base accounts-list by locatie](restorable-database-accounts-list-by-location.md) articles.

### <a name="restorable-sql-database"></a>Restorable SQL database

Elke resource bevat informatie over een mutatie gebeurtenis, zoals het maken en verwijderen van de SQL Database. Deze informatie kan u helpen bij scenario's waarin de data base per ongeluk is verwijderd en als u wilt weten wanneer deze gebeurtenis heeft plaatsgevonden.

|Eigenschapsnaam |Description  |
|---------|---------|
| eventTimestamp | De tijd in UTC waarop de data base wordt gemaakt of verwijderd. |
| ownerId | De naam van de SQL database. |
| ownerResourceId | De resource-ID van de SQL database|
| operationType | Het bewerkings type van deze database gebeurtenis. Dit zijn de mogelijke waarden:<br/><ul><li>Maken: gebeurtenis data base maken</li><li>Verwijderen: gebeurtenis data base verwijderen</li><li>Vervangen: wijzigings gebeurtenis van data base</li><li>SystemOperation: de gebeurtenis voor het wijzigen van de data base is geactiveerd door het systeem. Deze gebeurtenis wordt niet geïnitieerd door de gebruiker</li></ul> |
| database |De eigenschappen van de SQL database op het moment van de gebeurtenis|

Zie [restorable SQL data bases-List](restorable-sql-databases-list.md) article (Engelstalig) voor een lijst met alle database mutaties.

### <a name="restorable-sql-container"></a>Restorable SQL-container

Elke resource bevat informatie over een mutatie gebeurtenis, zoals het maken en verwijderen van gegevens die zijn opgetreden in de SQL-container. Deze informatie kan u helpen bij scenario's waarin de container is gewijzigd of verwijderd, en als u wilt weten wanneer deze gebeurtenis zich voordeed.

|Eigenschapsnaam |Description  |
|---------|---------|
| eventTimestamp    | De tijd in UTC waarop deze container gebeurtenis zich voordeed.|
| ownerId| De naam van de SQL-container.|
| ownerResourceId   | De resource-ID van de SQL-container.|
| operationType | Het bewerkings type van deze container gebeurtenis. Dit zijn de mogelijke waarden: <br/><ul><li>Maken: gebeurtenis container maken</li><li>Verwijderen: container verwijderings gebeurtenis</li><li>Vervangen: container wijzigings gebeurtenis</li><li>SystemOperation: de gebeurtenis voor het wijzigen van de container is geactiveerd door het systeem. Deze gebeurtenis wordt niet geïnitieerd door de gebruiker</li></ul> |
| container | De eigenschappen van de SQL-container op het moment van de gebeurtenis.|

Zie voor een lijst met alle container mutaties onder dezelfde Data Base [restorable SQL-containers-lijst](restorable-sql-containers-list.md) artikel.

### <a name="restorable-sql-resources"></a>Onstorbare SQL-resources

Elke resource vertegenwoordigt één data base en alle containers onder die data base.

|Eigenschapsnaam |Description  |
|---------|---------|
| databaseName  | De naam van de SQL database.
| collectionNames   | De lijst met SQL-containers onder deze data base.|

Zie [restorable SQL resources-list](restorable-sql-resources-list.md) article (Engelstalig) voor een lijst met SQL database en container combinatie die voor het account bestaan op de opgegeven tijds tempel en locatie.

### <a name="restorable-mongodb-database"></a>Restorable MongoDB-data base

Elke resource bevat informatie over een mutatie gebeurtenis, zoals het maken en verwijderen van gegevens die zijn opgetreden in de MongoDB-data base. Deze informatie kan u helpen bij het scenario waarin de data base per ongeluk is verwijderd en de gebruiker moet nagaan wanneer die gebeurtenis heeft plaatsgevonden.

|Eigenschapsnaam |Description  |
|---------|---------|
|eventTimestamp| De tijd in UTC waarop deze database gebeurtenis plaatsvond.|
| ownerId| De naam van de MongoDB-data base. |
| ownerResourceId   | De resource-ID van de MongoDB-data base. |
| operationType |   Het bewerkings type van deze database gebeurtenis. Dit zijn de mogelijke waarden:<br/><ul><li> Maken: gebeurtenis data base maken</li><li> Verwijderen: gebeurtenis data base verwijderen</li><li> Vervangen: wijzigings gebeurtenis van data base</li><li> SystemOperation: de gebeurtenis voor het wijzigen van de data base is geactiveerd door het systeem. Deze gebeurtenis wordt niet geïnitieerd door de gebruiker </li></ul> |

Zie [restorable MongoDb data bases-List](restorable-mongodb-databases-list.md) article (Engelstalig) voor een lijst met alle database mutaties.

### <a name="restorable-mongodb-collection"></a>Restorable MongoDB-verzameling

Elke resource bevat informatie over een mutatie gebeurtenis, zoals het maken en verwijderen van gegevens die zijn opgetreden in de MongoDB-verzameling. Deze informatie kan u helpen bij scenario's waarin de verzameling is gewijzigd of verwijderd en de gebruiker moet nagaan wanneer die gebeurtenis heeft plaatsgevonden.

|Eigenschapsnaam |Description  |
|---------|---------|
| eventTimestamp |De tijd in UTC waarop deze verzamelings gebeurtenis plaatsvond. |
| ownerId| De naam van de MongoDB-verzameling. |
| ownerResourceId   | De resource-ID van de MongoDB-verzameling. |
| operationType |Het bewerkings type van deze verzamelings gebeurtenis. Dit zijn de mogelijke waarden:<br/><ul><li>Maken: gebeurtenis verzameling maken</li><li>Verwijderen: gebeurtenis verzameling verwijderen</li><li>Vervangen: gebeurtenis verzameling wijzigen</li><li>SystemOperation: de gebeurtenis voor het wijzigen van de verzameling die door het systeem is geactiveerd. Deze gebeurtenis wordt niet geïnitieerd door de gebruiker</li></ul> |

Zie [restorable MongoDb Collections-List](restorable-mongodb-collections-list.md) article (Engelstalig) voor een lijst met alle container mutaties onder dezelfde data base.

### <a name="restorable-mongodb-resources"></a>Restorable MongoDB-resources

Elke resource vertegenwoordigt één data base en alle verzamelingen onder die data base.

|Eigenschapsnaam |Description  |
|---------|---------|
| databaseName  |De naam van de MongoDB-data base. |
| collectionNames | De lijst met MongoDB-verzamelingen onder deze data base. |

Voor een lijst met alle combi Naties van MongoDB-data base en-verzameling die voor het account bestaan op de opgegeven tijds tempel en locatie, raadpleegt u [restorable MongoDb resources-lijst](restorable-mongodb-resources-list.md) artikel.

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Machtigingen beheren](continuous-backup-restore-permissions.md) die vereist zijn voor het herstellen van gegevens met doorlopende back-upmodus.
