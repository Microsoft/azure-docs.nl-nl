---
title: 'REST API: synchroniseren tussen meerdere data bases'
description: Gebruik een REST API voorbeeld script om te synchroniseren tussen meerdere data bases.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565881"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>REST API gebruiken om gegevens te synchroniseren tussen meerdere data bases 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

In dit REST API voor beeld wordt SQL Data Sync geconfigureerd voor het synchroniseren van gegevens tussen meerdere data bases.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync in Azure ](../sql-data-sync-data-sql-server-sql-database.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> SQL Data Sync biedt op dit moment geen ondersteuning voor beheerde exemplaren voor Azure SQL.

## <a name="create-sync-group"></a>Synchronisatiegroep maken

Gebruik de sjabloon [maken of bijwerken](/rest/api/sql/syncgroups/createorupdate) om een synchronisatie groep te maken.
 
Wanneer u een synchronisatie groep maakt, moet u het synchronisatie schema (table\column) niet door geven en niet door geven in masterSyncMemberName, omdat de synchronisatie groep op dit moment nog geen table\column-informatie heeft.

Voorbeeld aanvraag voor het maken van een synchronisatie groep: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Voorbeeld antwoord voor het maken van een synchronisatie groep:

Status code: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Status code: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Sync-lid maken

Gebruik de sjabloon [maken of bijwerken](/rest/api/sql/syncmembers/createorupdate) om een synchronisatie lid te maken.

Voorbeeld aanvraag voor het maken van een Sync-lid:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Voorbeeld antwoord voor het maken van een Sync-lid:

Status code: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Status code: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Schema vernieuwen

Wanneer de synchronisatie groep is gemaakt, vernieuwt u het schema met behulp van de volgende sjablonen.

Gebruik de sjabloon voor het vernieuwen van een [hub](/rest/api/sql/syncgroups/refreshhubschema)  om het schema voor de hub-data base te vernieuwen. 

Voorbeeld aanvraag voor het vernieuwen van een hub-database schema: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Voorbeeld antwoord voor het vernieuwen van een hub-database schema: 

Status code: 200

Status code: 202

Gebruik de [List hub schemas](/rest/api/sql/syncgroups/listhubschemas) -sjabloon om het hub-database schema weer te geven. 

Gebruik de [schema sjabloon lid vernieuwen](/rest/api/sql/syncmembers/refreshmemberschema) om het schema van het member-data base te vernieuwen. 

Gebruik de [lijst met leden schema's](/rest/api/sql/syncmembers/listmemberschemas) om het data base-schema van de lijst weer te geven. 

Ga pas verder met de volgende stap wanneer het schema is vernieuwd. 

## <a name="update-sync-group"></a>Synchronisatie groep bijwerken 

Gebruik de sjabloon [maken of bijwerken](/rest/api/sql/syncgroups/createorupdate) om uw synchronisatie groep bij te werken.

Werk de synchronisatie groep bij door het synchronisatie schema op te geven. Neem uw schema en masterSyncMemberName op. Dit is de naam die het schema bevat dat u wilt gebruiken. 

Voorbeeld aanvraag voor het bijwerken van de synchronisatie groep: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Voorbeeld antwoord voor het bijwerken van de synchronisatie groep: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Synchronisatie lid bijwerken

Gebruik de sjabloon [maken of bijwerken](/rest/api/sql/syncmembers/createorupdate) om het Sync-lid bij te werken.

Voorbeeld aanvraag voor het bijwerken van een Sync-lid: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Voorbeeld antwoord voor het bijwerken van een Sync-lid: 

Status code: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Status code: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Synchronisatie activeren

Gebruik de [trigger Sync](/rest/api/sql/syncgroups/triggersync) -sjabloon om een synchronisatie bewerking te activeren.

Voorbeeld aanvraag voor het activeren van de synchronisatie bewerking: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Voorbeeld reactie voor het activeren van de synchronisatie bewerking: 

Status code: 200

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../powershell-script-content-guide.md).

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

- Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync in Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Data Sync instellen
    - Gebruik de zelfstudie voor de Azure-portal[: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server](../sql-data-sync-sql-server-configure.md)
    - PowerShell gebruiken: [PowerShell gebruiken om gegevens te synchroniseren tussen een database in Azure SQL Database en SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent: [Data Sync Agent voor SQL Data Sync in Azure](../sql-data-sync-agent-overview.md)
- Best practices: [Best practices voor SQL Data Sync in Azure](../sql-data-sync-best-practices.md)
- Bewaken: [SQL Data Sync bewaken met Azure Monitor-logboeken](../monitor-tune-overview.md)
- Problemen oplossen: [Problemen met SQL Data Sync in Azure oplossen](../sql-data-sync-troubleshoot.md)
- Het synchronisatieschema bijwerken
    - Transact-SQL gebruiken: [De replicatie van schemawijzigingen in SQL Data Sync in Azure automatiseren](../sql-data-sync-update-sync-schema.md)
    - PowerShell gebruiken: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](update-sync-schema-in-sync-group.md)

Meer informatie over SQL Database vindt u in:

- [Overzicht van SQL Database?](../sql-database-paas-overview.md)
- [Database Lifecycle Management (DLM)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))