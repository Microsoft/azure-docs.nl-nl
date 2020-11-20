---
title: Matrix voor Azure Database Migration Service-hulpprogram ma's
description: Meer informatie over de services en hulpprogram ma's die beschikbaar zijn voor het migreren van data bases en het ondersteunen van verschillende fasen van het migratie proces.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: reference
ms.date: 03/03/2020
ms.openlocfilehash: 1a8ae7fd03a0182256970b9eb9eb18b7c77894ff
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962974"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Services en hulpprogramma's voor gegevensmigratiescenario's

Dit artikel bevat een matrix van de services en hulpprogram ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie en speciale taken.

In de volgende tabellen worden de services en hulpprogram ma's geïdentificeerd die u kunt gebruiken om de gegevens migratie te plannen en om de verschillende fasen te volt ooien.

> [!NOTE]
> In de volgende tabellen vertegenwoordigen items die zijn gemarkeerd met een asterisk (*) hulpprogram ma's van derden.

## <a name="business-justification-phase"></a>Zakelijke rechtvaardigings fase

| Bron | Doel | Via<br/>Inventaris | Doel en SKU<br/>aanbeveling | TCO/ROI en<br/>Business Case |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL Database | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Azure SQL DB MI | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure SQL-VM | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure Synapse Analytics | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize](https://www.cloudamize.com/) |  | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Azure SQL DB, MI, VM |  | [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, MI, VM | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | Azure Synapse Analytics | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB voor PostgreSQL-<br/>Enkele server | [KAART Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize](https://www.cloudamize.com/) | [Cloudamize](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure DB voor MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure DB voor MySQL |  |  | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server |  |  | [TCO reken machine](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP IQ | Azure SQL DB, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Fase vóór migratie

| Bron | Doel | App-gegevens toegang<br/>Laag evaluatie | Database<br/>Beoordeling | Prestaties<br/>Beoordeling |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL Database | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL-VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| RDS SQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure DB voor PostgreSQL-<br/>Enkele server |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize](https://www.cloudamize.com/) | [Cloudamize](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure DB voor MySQL |  |  |  |
| RDS MySQL | Azure DB voor MySQL |  |  |  |
| PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server |  |  |  |
| RDS PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server |  |  |  |
| DB2 | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Access | Azure SQL DB, MI, VM |  | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)  /  [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP IQ | Azure SQL DB, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Migratie fase

| Bron | Doel | Schema | Gegevens<br/>Breken | Gegevens<br/>Aanschaffen |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL Database | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL DB MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL-VM | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure Synapse Analytics |  |  |  |
| RDS SQL | Azure SQL DB, MI, VM | [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure Synapse Analytics | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure DB voor PostgreSQL-<br/>Enkele server | [Ispirer*](https://www.ispirer.com/solutions) | [Ispirer*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Imanis-gegevens *](https://www.imanisdata.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Imanis-gegevens *](https://www.imanisdata.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize](https://www.cloudamize.com/)<br/>[Imanis-gegevens *](https://www.imanisdata.com/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis-gegevens *](https://www.imanisdata.com/) | [Imanis-gegevens *](https://www.imanisdata.com/) | [Imanis-gegevens *](https://www.imanisdata.com/) |
| MySQL | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure DB voor MySQL | [MySQL-Dump *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure DB voor MySQL | [MySQL-Dump *](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server | [PAG/dump *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server | [PAG/dump *](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Access | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [Attunity](https://www.attunity.com/products/replicate/)<br/>[Realtimeplatform](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP IQ | Azure SQL DB, MI, VM | [Ispirer*](https://www.ispirer.com/solutions) | [Ispirer*](https://www.ispirer.com/solutions) | |
| | | | | |

## <a name="post-migration-phase"></a>Fase na de migratie

| Bron | Doel | Optimaliseren |
| --- | --- | --- |
| SQL Server | Azure SQL Database | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL DB MI | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure SQL-VM | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics |  |
| RDS SQL | Azure SQL DB, MI, VM |  |
| Oracle | Azure SQL DB, MI, VM |  |
| Oracle | Azure Synapse Analytics |  |
| Oracle | Azure DB voor PostgreSQL-<br/>Enkele server |  |
| MongoDB | Cosmos DB | [Cloudamize](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, MI, VM |  |
| MySQL | Azure DB voor MySQL |  |
| RDS MySQL | Azure DB voor MySQL |  |
| PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server |  |
| RDS PostgreSQL | Azure DB voor PostgreSQL-<br/>Enkele server |  |
| DB2 | Azure SQL DB, MI, VM |  |
| Access | Azure SQL DB, MI, VM |  |
| Sybase-SAP-ASE | Azure SQL DB, MI, VM |  |
| Sybase-SAP IQ | Azure SQL DB, MI, VM |  |
| | | |

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [Wat is de Azure database Migration service](dms-overview.md)voor een overzicht van de Azure database Migration service.