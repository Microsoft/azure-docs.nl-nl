---
title: Azure PowerShell-voorbeeldscripts
description: Gebruik Azure PowerShell-voorbeeldscript voor het maken en beheren van Azure SQL Database en Azure SQL Managed Instance-resources.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690964"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure PowerShell-voorbeelden voor Azure SQL Database en door Azure SQL beheerde exemplaren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met Azure SQL Database en door Azure SQL beheerde exemplaren kunt u uw databases, instanties en pools met behulp van Azure PowerShell configureren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie de AZ PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure PowerShell-voorbeeldscripts voor Azure SQL Database.

|Koppeling|Beschrijving|
|---|---|
|**Individuele databases en elastische pools maken en configureren**||
| [Een individuele database maken en een firewallregel op serverniveau configureren](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt een individuele database gemaakt en een IP-firewallregel op serverniveau geconfigureerd. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden elastische pools gemaakt, pooldatabases verplaatst en rekenkracht gewijzigd.|
|**Geo-replicatie en failover configureren**||
| [Eén database configureren en failover-overschakeling uitvoeren met actieve geo-replicatie](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt actieve geo-replicatie geconfigureerd voor een individuele database en wordt overgeschakeld naar de secundaire replica. |
| [Eén pooldatabase configureren en failover-overschakeling uitvoeren met actieve geo-replicatie](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt actieve geo-replicatie geconfigureerd voor een database in een elastische pool en wordt overgeschakeld naar de secundaire replica. |
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een individuele database](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script maakt u een database en een failovergroep, voegt u de database toe aan de failovergroep en test u een failover naar de secundaire server. |
| [Een failovergroep voor een elastische pool configureren](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script maakt u een database, voegt u deze toe aan een elastische pool, voegt u de elastische pool toe aan de failovergroep en test u een failover naar de secundaire server. |
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden metrische gegevens over prestaties van een individuele database gecontroleerd, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de metrische gegevens over prestaties wordt gemaakt. |
| [Een elastische pool schalen](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden de prestatiemetrieken gecontroleerd van een elastische pool, waarna de database naar een grotere rekenkracht wordt geschaald en er een waarschuwingsregel voor een van de prestatiemetrieken wordt gemaakt. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt beleid voor controle en detectie van bedreigingen geconfigureerd voor een database. |
| **Database herstellen, kopiëren en importeren**||
| [Database herstellen](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een database teruggezet vanuit een geo-redundante back-up en wordt een verwijderde database naar de laatste back-up hersteld. |
| [Een database kopiëren naar een nieuwe server](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een kopie van een bestaande database gemaakt in een nieuwe server. |
| [Database uit een BACPAC-bestand importeren](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Met dit PowerShell-script wordt een database vanuit een bacpac-bestand naar Azure SQL Database geïmporteerd. |
| **Gegevens tussen databases synchroniseren**||
| [Gegevens tussen databases synchroniseren](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren van gegevens tussen meerdere databases in Azure SQL Database. |
| [Gegevens synchroniseren tussen SQL Database en on-premises SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script wordt Data Sync geconfigureerd voor het synchroniseren tussen databases in Azure SQL Database en een on-premises SQL Server-database. |
| [Synchronisatieschema van SQL Data Sync bijwerken](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Met dit PowerShell-script worden items aan het synchronisatieschema van Data Sync toegevoegd of eruit verwijderd. |
|||

Meer informatie over de [Azure PowerShell API voor individuele databases](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

De volgende tabel bevat links naar Azure PowerShell-voorbeeldscripts voor Azure SQL - Beheerde exemplaren

|Koppeling|Beschrijving|
|---|---|
|**Beheerde exemplaren maken en configureren**||
| [Een beheerd exemplaar maken en beheren](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell. |
| [Een beheerd exemplaar maken met behulp van de Azure Resource Manager-sjabloon](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script laat zien hoe u een beheerd exemplaar maakt en beheert met Azure PowerShell en de Azure Resource Manager-sjabloon.|
| [Een database herstellen naar een beheerd exemplaar in een ander geografisch gebied](../managed-instance/scripts/restore-geo-backup.md) | Dit PowerShell-script neemt een back-up van een database en zet deze terug in een andere regio. Dit staat bekend als herstel na noodgevallen met geo-herstel. |
| **Transparante gegevensversleuteling configureren**||
| [Transparante gegevensversleuteling beheren in een beheerd exemplaar met behulp van uw eigen sleutel uit Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Dit PowerShell-script configureert transparante gegevensversleuteling in een Bring Your Own Key-scenario voor Azure SQL Managed Instance, met behulp van een sleutel uit Azure Key Vault.|
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een beheerd exemplaar](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Dit PowerShell-script maakt twee beheerde exemplaren, voegt ze toe aan een failovergroep, en test vervolgens failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar. |
|||

Meer informatie over [Power shell-cmdlets voor beheerde exemplaren van Azure SQL](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>Aanvullende bronnen

In de voorbeelden die op deze pagina worden weergegeven, worden de [PowerShell-cmdlets](/powershell/module/az.sql/) gebruikt voor het maken en beheren van Azure SQL-resources. Aanvullende cmdlets voor het uitvoeren van query's en het uitvoeren van veel databasetaken bevinden zich in de module [sqlserver](/powershell/module/sqlserver/). Zie [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)voor meer informatie.