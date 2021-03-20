---
title: 'PowerShell: Synchronisatieschema van SQL Data Sync bijwerken'
description: Azure PowerShell-voorbeeldscript voor het bijwerken van het synchronisatieschema voor SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792715"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Met dit Azure PowerShell-voorbeeld werkt u het synchronisatieschema bij in een bestaande synchronisatiegroep van SQL Data Sync. Wanneer u meerdere tabellen synchroniseert, helpt dit script u om het synchronisatieschema efficiënt bij te werken. In dit voorbeeld wordt het gebruik van het script **UpdateSyncSchema** gedemonstreerd, dat beschikbaar is op GitHub als [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie Az PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](../sql-data-sync-data-sql-server-sql-database.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> SQL Data Sync biedt op dit moment geen ondersteuning voor beheerde exemplaren voor Azure SQL.

## <a name="examples"></a>Voorbeelden

### <a name="add-all-tables-to-the-sync-schema"></a>Alle tabellen toevoegen aan het synchronisatieschema

In het volgende voorbeeld wordt het databaseschema vernieuwd en worden alle geldige tabellen in de hubdatabase aan het synchronisatieschema toegevoegd.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Tabellen en kolommen toevoegen en verwijderen

In het volgende voorbeeld worden `[dbo].[Table1]` en `[dbo].[Table2].[Column1]` aan het synchronisatieschema toegevoegd en wordt `[dbo].[Table3]` verwijderd.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Scriptparameters

Het script **UpdateSyncSchema** heeft de volgende parameters:

| Parameter | Opmerkingen |
|---|---|
| $subscriptionId | Het abonnement waarin de synchronisatiegroep wordt gemaakt. |
| $resourceGroupName | De resourcegroep waarin de synchronisatiegroep wordt gemaakt.|
| $serverName | De servernaam van de hubdatabase.|
| $databaseName | De naam van de hubdatabase. |
| $syncGroupName | De naam van de synchronisatiegroep. |
| $memberName | Geef de lidnaam op als u het databaseschema wilt laden uit het synchronisatielid in plaats van uit de hubdatabase. Als u het databaseschema uit de hub wilt laden, laat u deze parameter leeg. |
| $timeoutInSeconds | Time-out waarna het script het databaseschema vernieuwt. De standaardwaarde is 900 seconden. |
| $refreshDatabaseSchema | Geef aan of het script het databaseschema moet vernieuwen. Als uw databaseschema na de vorige configuratie is gewijzigd, (bijvoorbeeld als u een nieuwe tabel of kolom hebt toegevoegd) moet u het schema vernieuwen voordat u het opnieuw configureert. De standaardinstelling is onwaar. |
| $addAllTables | Als deze waarde op waar is ingesteld, worden alle geldige tabellen en kolommen aan het synchronisatieschema toegevoegd. De waarden van $TablesAndColumnsToAdd en $TablesAndColumnsToRemove worden genegeerd. |
| $tablesAndColumnsToAdd | Hier kunt u opgeven welke tabellen en kolommen aan het synchronisatieschema moeten worden toegevoegd. Elke tabel- of kolomnaam moet volledig worden gescheiden met de schemanaam. Bijvoorbeeld: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. U kunt meerdere tabel- of kolomnamen opgeven, gescheiden door een komma (,). |
| $tablesAndColumnsToRemove | Hier kunt u opgeven welke tabellen en kolommen uit het synchronisatieschema moeten worden verwijderd. Elke tabel- of kolomnaam moet volledig worden gescheiden met de schemanaam. Bijvoorbeeld: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. U kunt meerdere tabel- of kolomnamen opgeven, gescheiden door een komma (,). |

## <a name="script-explanation"></a>Uitleg van het script

Het script **UpdateSyncSchema** gebruikt de volgende opdrachten. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | Hiermee wordt informatie over een synchronisatiegroep geretourneerd. |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Werkt een synchronisatiegroep bij. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Hiermee wordt informatie over een sync-lid geretourneerd. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Hiermee wordt informatie over een synchronisatieschema geretourneerd. |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Werkt een synchronisatieschema bij. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../powershell-script-content-guide.md).

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

- Overzicht: [Gegevens synchroniseren tussen Azure SQL Database en SQL Server met SQL Data Sync in Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Data Sync instellen
    - Gebruik de zelfstudie voor de Azure-portal[: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server](../sql-data-sync-sql-server-configure.md)
    - PowerShell gebruiken
        -  [PowerShell gebruiken voor het synchroniseren van gegevens tussen meerdere databases in Azure SQL Database](sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken voor het synchroniseren van gegevens tussen Azure SQL-database en SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent: [Data Sync Agent voor SQL Data Sync in Azure](../sql-data-sync-agent-overview.md)
- Best practices: [Best practices voor SQL Data Sync in Azure](../sql-data-sync-best-practices.md)
- Bewaken: [SQL Data Sync bewaken met Azure Monitor-logboeken](../monitor-tune-overview.md)
- Problemen oplossen: [Problemen met SQL Data Sync in Azure oplossen](../sql-data-sync-troubleshoot.md)
- Het synchronisatieschema bijwerken
    - Transact-SQL gebruiken: [De replicatie van schemawijzigingen in SQL Data Sync in Azure automatiseren](../sql-data-sync-update-sync-schema.md)

Meer informatie over SQL Database vindt u in:

- [Overzicht van SQL Database?](../sql-database-paas-overview.md)
- [Database Lifecycle Management (DLM)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))