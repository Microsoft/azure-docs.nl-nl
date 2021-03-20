---
title: Inhouds referentie configureren & beheren
description: Zoek een Naslag informatie over het configureren en beheren van Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791763"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Inhouds referentie configureren en beheren-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u een inhouds verwijzing naar verschillende hand leidingen, scripts en uitleg die u kunnen helpen bij het beheren en configureren van uw Azure SQL Database. 

## <a name="load-data"></a>Gegevens laden

- [Migreren naar SQL Database](migrate-to-database-from-sql-server.md)
- Meer informatie over het [beheren van SQL database na de migratie](manage-data-after-migrating-to-database.md).
- [Een database kopiëren](database-copy.md)
- [Een DB uit een BACPAC importeren](database-import.md)
- [Een DB naar een BACPAC exporteren](database-export.md)
- [Gegevens laden met BCP](../load-from-csv-with-bcp.md)
- [Gegevens laden met ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Functies configureren

- [Verificatie van Azure Active Directory (Azure AD) configureren](authentication-aad-configure.md)
- [Voorwaardelijke toegang configureren](conditional-access-configure.md)
- [Meervoudige Azure AD-verificatie](authentication-mfa-ssms-overview.md)
- [Multi-Factor Authentication configureren](authentication-mfa-ssms-configure.md)
- [Tijdelijk bewaarbeleid configureren](temporal-tables-retention-policy.md)
- [TDE met BYOK (Bring Your Own Key) configureren](transparent-data-encryption-byok-configure.md)
- [TDE BYOK-sleutels roteren](transparent-data-encryption-byok-key-rotation.md)
- [TDE-beveiliging verwijderen](transparent-data-encryption-byok-remove-tde-protector.md)
- [OLTP in het geheugen configureren](../in-memory-oltp-configure.md)
- [Azure Automation configureren](automation-manage.md)
- [Transactionele replicatie configureren](replication-to-sql-database.md) om de datum te repliceren tussen data bases.
- [Configureer detectie van bedreigingen](threat-detection-configure.md) zodat Azure SQL database verdachte activiteiten zoals SQL-injectie of toegang vanaf verdachte locaties kunt identificeren.
- [Configureer dynamische gegevens maskering](dynamic-data-masking-configure-portal.md) om uw gevoelige gegevens te beveiligen.
- Configureer het bewaren van [back-ups](long-term-backup-retention-configure.md) voor een Data Base om uw back-ups op Azure Blob Storage te bewaren. 
- [Configureer geo-replicatie](active-geo-replication-overview.md) om een replica van uw data base in een andere regio te blijven gebruiken.
- [Configureer beveiliging voor geo-replica's](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Uw data base bewaken en afstemmen

- [Handmatig instellen](performance-guidance.md)
- [DMV's gebruiken voor het bewaken van prestaties](monitoring-with-dmvs.md)
- [Query Store gebruiken voor het bewaken van prestaties](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Schakel automatisch afstemmen](automatic-tuning-enable.md) in om de prestaties van uw werk belasting Azure SQL database optimaliseren.
- [Schakel e-mail meldingen voor automatisch afstemmen](automatic-tuning-email-notifications-configure.md) in om informatie te krijgen over de aanbevelingen voor het afstemmen.
- [Aanbevelingen voor prestaties Toep assen](database-advisor-find-recommendations-portal.md) en uw data base optimaliseren.
- [Waarschuwingen maken](alerts-insights-configure-portal.md) voor het ontvangen van meldingen van Azure SQL database.
- [Los problemen met de verbinding](troubleshoot-common-errors-issues.md) op als u enkele verbindings problemen tussen de toepassingen en de data base ziet. U kunt ook [resource Health gebruiken om verbindings problemen op te lossen](resource-health-to-troubleshoot-connectivity.md).
- [Prestatieproblemen oplossen met Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Bestands ruimte beheren](file-space-manage.md) om het gebruik van opslag in uw data base te controleren.
- [Diagnostisch logboek van Intelligent Insights gebruiken](intelligent-insights-use-diagnostics-log.md)
- [OLTP-ruimte in het geheugen bewaken](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

- [Uitgebreide gebeurtenissen](xevent-db-diff-from-svr.md)
- [Uitgebreide gebeurtenissen opslaan in gebeurtenis bestand](xevent-code-event-file.md)
- [Uitgebreide gebeurtenissen opslaan in de ring buffer](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Een query uitvoeren op gedistribueerde gegevens

- [Query's verticaal gepartitioneerde gegevens](elastic-query-getting-started-vertical.md) over meerdere data bases.
- [Rapport over een uitgeschaalde gegevenslaag](elastic-query-horizontal-partitioning.md).
- [Query's uitvoeren voor meerdere tabellen met verschillende schema's](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Gegevenssynchronisatie

- [SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
- [Data Sync-agent](sql-data-sync-agent-overview.md)
- [Schemawijzigingen repliceren](sql-data-sync-update-sync-schema.md)
- [Bewaken met OMS](./monitor-tune-overview.md)
- [Best practices voor Data Sync](sql-data-sync-best-practices.md)
- [Problemen met Data Sync oplossen](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Taken voor Elastic Database

- [Maken en beheren](elastic-jobs-powershell-create.md) Elastic Database taken met behulp van Power shell.
- [Maken en beheren](elastic-jobs-tsql-create-manage.md) Elastic Database taken met behulp van Transact-SQL.
- [Migreren vanaf een oude elastische taak](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Database-sharding

- [Upgrade van client bibliotheek voor Elastic data base](elastic-scale-upgrade-client-library.md).
- [Shard-app maken](elastic-scale-get-started.md).
- Een [query uitvoeren op horizontale Shard-gegevens](elastic-query-getting-started.md).
- [Query's voor meerdere Shard](elastic-scale-multishard-querying.md)uitvoeren.
- [Shard-gegevens verplaatsen](elastic-scale-configure-deploy-split-and-merge.md).
- [Configureer beveiliging](elastic-scale-split-merge-security-configuration.md) in data base Shards.
- [Voeg een Shard](elastic-scale-add-a-shard.md) toe aan de huidige set data base-Shards.
- [Problemen met de Shard-kaart oplossen](elastic-database-recovery-manager.md).
- [SHARD DB migreren](elastic-convert-to-use-elastic-tools.md).
- [Tellers maken](elastic-database-perf-counters.md).
- [Gebruik Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) om Shard-gegevens op te vragen.
- [Gebruik dapper Framework](elastic-scale-working-with-dapper.md) om Shard-gegevens op te vragen.

## <a name="develop-applications"></a>Toepassingen ontwikkelen

- [Connectiviteit](connect-query-content-reference-guide.md#libraries)
- [Spark-connector gebruiken](spark-connector.md)
- [App verifiëren](application-authentication-get-client-id-keys.md)
- [Batch verwerking gebruiken voor betere prestaties](../performance-improve-use-batching.md)
- [Connectiviteitsrichtlijnen](troubleshoot-common-connectivity-issues.md)
- [DNS-aliassen](dns-alias-overview.md)
- [DNS-alias instellen Power shell](dns-alias-powershell-create.md)
- [Poorten - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C en C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Toepassingen ontwerpen

- [Ontwerpen voor herstel na noodgeval](designing-cloud-solutions-for-disaster-recovery.md)
- [Ontwerpen voor elastische pools](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Ontwerpen voor app-upgrades](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Multi tenant-toepassingen voor software als een service (SaaS) ontwerpen

- [SaaS-ontwerppatronen](saas-tenancy-app-design-patterns.md)
- [Indexeerfunctie voor SaaS-video](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Beveiliging van SaaS-app](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [hand leidingen voor Azure SQL Managed instance](../managed-instance/how-to-content-reference-guide.md)