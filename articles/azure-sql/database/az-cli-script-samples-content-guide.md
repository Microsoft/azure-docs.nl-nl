---
title: Azure CLI-voorbeeldscript
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure CLI-voorbeeldscript voor het maken en beheren van Azure SQL Database en beheerde exemplaren voor Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563355"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Azure CLI-voorbeelden voor Azure SQL Database en beheerde SQL-exemplaren 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

U kunt Azure SQL Database en beheerde SQL-exemplaren configureren met behulp van de <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

De volgende tabel bevat links naar Azure CLI-voorbeeldscript voor het beheren van individuele en gegroepeerde databases in Azure SQL Database. 

|Onderwerp|Beschrijving|
|---|---|
|**Databases maken in Azure SQL Database**||
| [Een individuele database maken en een firewallregel configureren](scripts/create-and-configure-database-cli.md) | Hiermee maakt u een SQL-database en configureert u een firewallregel op serverniveau. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/move-database-between-elastic-pools-cli.md) | Hiermee maakt u elastische pools, verplaatst u gegroepeerde databases en wijzigt u de rekenkracht. |
|**Databases schalen in Azure SQL Database**||
| [Een individuele database schalen](scripts/monitor-and-scale-database-cli.md) | Hiermee wordt een Azure SQL-database naar een andere rekenkracht geschaald nadat de grootte van de database is opgevraagd. |
| [Een elastische pool schalen](scripts/scale-pool-cli.md) | Hiermee wordt een elastische SQL-pool naar een andere rekenkracht geschaald. |
|**Geo-replicatie en failover configureren**||
| [Een individuele database aan een failovergroep toevoegen](scripts/add-database-to-failover-group-cli.md)| Hiermee maakt u een database en een failovergroep, voegt u de database toe aan de failovergroep en test u een failover naar de secundaire server. |
| [Een failovergroep voor een elastische pool configureren](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Hiermee maakt u een database, voegt u deze toe aan een elastische pool, voegt u de elastische pool toe aan de failovergroep en test u een failover naar de secundaire server. |
| [Actieve geo-replicatie gebruiken om een individuele database te configureren en een failover-overschakeling uit te voeren](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Hiermee wordt actieve geo-replicatie geconfigureerd voor een individuele database in Azure SQL Database en wordt overgeschakeld naar de secundaire replica. |
| [Actieve geo-replicatie gebruiken om een pooldatabase te configureren en een failover-overschakeling uit te voeren](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Hiermee wordt actieve geo-replicatie geconfigureerd voor een database in een elastische pool en wordt overgeschakeld naar de secundaire replica. |
| **Controle en bedreigingen detecteren** |
| [Controle en detectie van bedreigingen configureren](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Hiermee wordt beleid voor controle en detectie van bedreigingen geconfigureerd voor een database in Azure SQL Database. |
| **Herstellen, kopiëren, importeren en een back-up maken van een database**||
| [Back-up maken van een database](../../sql-database/scripts/sql-database-backup-database-cli.md)| Back-ups maken van een database in SQL Database naar een back-up van Azure Storage. |
| [Database herstellen](../../sql-database/scripts/sql-database-restore-database-cli.md)| Hiermee wordt een database in SQL Database teruggezet vanuit een geo-redundante back-up en wordt een verwijderde database naar de laatste back-up hersteld. |
| [Een database kopiëren naar een nieuwe server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Hiermee maakt u een kopie van een bestaande database in SQL Database in een nieuwe server. |
| [Database uit een BACPAC-bestand importeren](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Hiermee wordt een database geïmporteerd naar SQL Database vanuit een BACPAC-bestand. |
|||

Meer informatie over de [Single Database Azure CLI API](single-database-manage.md#the-azure-cli).

# <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure CLI-scriptvoorbeelden voor beheerde exemplaren voor Azure SQL.

|Onderwerp|Beschrijving|
|---|---|
| **Een door SQL beheerd exemplaar maken**||
| [Een door SQL beheerd exemplaar maken](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Hiermee maakt u een door SQL beheerd exemplaar. |
| **Transparent Data Encryption (TDE) configureren**||
| [Transparent Data Encryption beheren in een door SQL beheerd exemplaar met behulp van Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Hiermee configureert u Transparent Data Encryption (TDE) in door SQL beheerde exemplaren met behulp van Azure Key Vault met verschillende belangrijke scenario's. |
|**Een failovergroep configureren**||
| [Een failovergroep configureren voor een door SQL beheerd exemplaar](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Hiermee maakt u twee door SQL beheerde exemplaren, voegt ze toe aan een failovergroep, en test vervolgens failover van het primaire door SQL beheerde exemplaar naar het secundaire door SQL beheerde exemplaar. |
|||

Voor meer voorbeelden van door SQL beheerde exemplaren raadpleegt u [Maken](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli), [Bijwerken](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli), [Een database verplaatsen](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance) en [Werken met scripts](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Meer informatie over het [beheerde SQL-exemplaar van Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---