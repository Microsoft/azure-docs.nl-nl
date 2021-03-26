---
title: Problemen met het verbinden van bron databases
titleSuffix: Azure Database Migration Service
description: Meer informatie over het oplossen van bekende problemen/fouten die zijn gekoppeld aan het verbinden van Azure Database Migration Service met bron databases.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: cffa8d9a0647ff5fe970801d5da98e23be0b2aaf
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567322"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>DMS-fouten oplossen bij het verbinden van de brondatabases

In het volgende artikel vindt u gedetailleerde informatie over het oplossen van mogelijke problemen die zich kunnen voordoen bij het verbinden van de Azure Database Migration Service (DMS) met de bron database. Elke sectie hieronder is gekoppeld aan een specifiek type bron database, waarin de fout wordt weer gegeven die u kunt tegen komen, samen met details en koppelingen naar informatie over het oplossen van problemen met de verbinding.

## <a name="sql-server"></a>SQL Server

Mogelijke problemen met het maken van verbinding met een bron SQL Server Data Base en hoe u deze kunt aanpakken, vindt u in de volgende tabel.

| Fout         | Details van oorzaak en probleem oplossing |
| ------------- | ------------- |
| SQL-verbinding is mislukt. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk. Controleer of de naam van het exemplaar juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan.<br> | Deze fout treedt op als de bron server niet kan worden gevonden door de service. Om het probleem op te lossen, raadpleegt u het artikel fout bij het verbinden met de [bron SQL Server bij het gebruik van een dynamische poort of een benoemd exemplaar](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Fout 53** -SQL-verbinding is mislukt. (Ook voor fout codes 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Deze fout treedt op als de service geen verbinding kan maken met de bron server. Om het probleem op te lossen, raadpleegt u de volgende bronnen en probeert u het opnieuw. <br><br>  [Interactieve gebruikers handleiding voor het oplossen van het verbindings probleem](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Vereisten voor het migreren van SQL Server naar Azure SQL Database](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [Vereisten voor het migreren van SQL Server naar een beheerd exemplaar van Azure SQL](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **Fout 18456** -aanmelden is mislukt.<br> | Deze fout treedt op als de service geen verbinding kan maken met de bron database met behulp van de gegeven T-SQL-referenties. Controleer de ingevoerde referenties om het probleem op te lossen. U kunt ook verwijzen naar [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) of naar de documenten voor het oplossen van problemen die worden vermeld in de opmerking onder deze tabel, en probeer het opnieuw. |
| Ongeldige waarde {0} van AccountName. Verwachte indeling voor AccountName is DomainName\UserName<br> | Deze fout treedt op als de gebruiker Windows-verificatie selecteert, maar de gebruikers naam heeft een ongeldige indeling. Om het probleem op te lossen, geeft u de gebruikers naam op in de juiste indeling voor Windows-verificatie of selecteert u **SQL-verificatie**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Mogelijke problemen met het maken van verbinding met een bron-AWS RDS MySQL-data base en hoe u deze kunt aanpakken, vindt u in de volgende tabel.

| Fout         | Details van oorzaak en probleem oplossing |
| ------------- | ------------- |
| **Fout [2003]**[HY000]-verbinding is mislukt. FOUT [HY000] [MySQL] [ODBC x. x (w) stuur programma] kan geen verbinding maken met de MySQL-server op {server} (10060) | Deze fout treedt op als het MySQL ODBC-stuur programma geen verbinding kan maken met de bron server. Om het probleem op te lossen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw.<br> |
| **Fout [2005]**[HY000]-verbinding is mislukt. FOUT [HY000] [MySQL] [ODBC x. x (w) stuur programma] onbekende MySQL-server-host {server} | Deze fout treedt op als de bron-host niet kan worden gevonden in RDS. Het probleem kan zijn veroorzaakt doordat de vermelde bron niet bestaat of omdat er een probleem is met de RDS-infra structuur. Om het probleem op te lossen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw.<br> |
| **Fout [1045]**[HY000]-verbinding is mislukt. FOUT [HY000] [MySQL] [ODBC x. x (w) stuur programma] toegang geweigerd voor gebruiker ' {User} ' @ ' {server} ' (met wacht woord: Ja) | Deze fout treedt op als het MySQL ODBC-stuur programma geen verbinding kan maken met de bron server vanwege ongeldige referenties. Controleer de referenties die u hebt ingevoerd. Als het probleem blijft bestaan, controleert u of de bron computer de juiste referenties heeft. Mogelijk moet u het wacht woord opnieuw instellen in de-console. Als het probleem zich blijft voordoen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw.<br> |
| **Fout [9002]**[HY000]-verbinding is mislukt. FOUT [HY000] [MySQL] [ODBC x. x (w) stuur programma] de connection string zijn mogelijk niet goed. Ga naar de portal voor verwijzingen.| Deze fout treedt op als de verbinding is mislukt vanwege een probleem met de connection string. Controleer of de ingevoerde connection string geldig is. Om het probleem op te lossen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw.<br> |
| **Fout in binaire logboek registratie. De variabele binlog_format heeft de waarde {Value}. Wijzig de waarde in Row.** | Deze fout treedt op als er een fout is opgetreden in de binaire logboek registratie. de variabele binlog_format heeft een onjuiste waarde. U kunt het probleem oplossen door de binlog_format in de parameter groep te wijzigen in ROW en vervolgens het exemplaar opnieuw te starten. Zie voor meer informatie de logboek bestanden voor [binaire logboek registratie en variabelen](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) of de AWS-documentatie voor de [RDS MySQL-data base](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Raadpleeg de volgende bronnen voor meer informatie over het oplossen van problemen met het maken van verbinding met een RDS MySQL-data base van een bron AWS:
> * [Problemen met Amazon RDS-connectiviteit oplossen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hoe kan ik problemen oplossen met het maken van verbinding met mijn Amazon RDS data base-exemplaar?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Mogelijke problemen met het maken van verbinding met een bron AWS RDS PostgreSQL-data base en hoe u deze kunt aanpakken, vindt u in de volgende tabel.

| Fout         | Details van oorzaak en probleem oplossing |
| ------------- | ------------- |
| **Fout [101]**[08001]-verbinding is mislukt. FOUT [08001] time-out verstreken. | Deze fout treedt op als het post gres-stuur programma geen verbinding kan maken met de bron server. Om het probleem op te lossen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw. |
| **Fout: de para meter wal_level heeft de waarde {Value}. Wijzig de waarde in logisch om replicatie toe te staan.** | Deze fout treedt op als de para meter wal_level een onjuiste waarde heeft. U kunt het probleem oplossen door de rds.logical_replication in de parameter groep te wijzigen in 1 en vervolgens het exemplaar opnieuw op te starten. Zie voor meer informatie de [vereisten voor het migreren naar Azure postgresql met behulp van DMS](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) of [POSTGRESQL op Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Raadpleeg de volgende bronnen voor meer informatie over het oplossen van problemen met het maken van verbinding met een bron-AWS RDS PostgreSQL-Data Base:
> * [Problemen met Amazon RDS-connectiviteit oplossen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hoe kan ik problemen oplossen met het maken van verbinding met mijn Amazon RDS data base-exemplaar?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Mogelijke problemen met het maken van verbinding met een bron AWS RDS SQL Server-Data Base en hoe u deze kunt adresseren, vindt u in de volgende tabel.

| Fout         | Details van oorzaak en probleem oplossing |
| ------------- | ------------- |
| **Fout 53** -SQL-verbinding is mislukt. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server is niet gevonden of is niet toegankelijk. Controleer of de naam van het exemplaar juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan. (provider: named pipes-provider, fout: 40-kan geen verbinding openen met SQL Server | Deze fout treedt op als de service geen verbinding kan maken met de bron server. Om het probleem op te lossen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw. |
| **Fout 18456** -aanmelden is mislukt. De aanmelding is mislukt voor de gebruiker {User} | Deze fout treedt op als de service geen verbinding kan maken met de bron database met de T-SQL-referenties die worden gegeven. Controleer de ingevoerde referenties om het probleem op te lossen. U kunt ook verwijzen naar [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) of naar de documenten voor het oplossen van problemen die worden vermeld in de opmerking onder deze tabel, en probeer het opnieuw. |
| **Fout 87** : de verbindings reeks is ongeldig. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk. Controleer of de naam van het exemplaar juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan. (provider: SQL-netwerk interfaces, fout: 25-verbindings reeks is niet geldig) | Deze fout treedt op als de service geen verbinding kan maken met de bron server vanwege een ongeldige connection string. Om het probleem op te lossen, controleert u de beschik bare connection string. Als het probleem zich blijft voordoen, raadpleegt u de documenten voor probleem oplossing in de opmerking onder deze tabel en probeert u het opnieuw. |
| **Fout: Server certificaat wordt niet vertrouwd.** Er is een verbinding met de server tot stand gebracht, maar vervolgens is er een fout opgetreden tijdens het aanmeldings proces. (provider: SSL-provider, fout: 0-de certificaat keten is uitgegeven door een niet-vertrouwde instantie.) | Deze fout treedt op als het gebruikte certificaat niet wordt vertrouwd. Om het probleem op te lossen, moet u een certificaat zoeken dat kan worden vertrouwd en het vervolgens inschakelen op de-server. U kunt ook de optie certificaat vertrouwen selecteren tijdens het verbinden. Voer deze actie alleen uit als u vertrouwd bent met het gebruikte certificaat en u deze vertrouwt. <br> TLS-verbindingen die zijn versleuteld met een zelfondertekend certificaat, bieden geen sterke beveiliging: ze zijn gevoelig voor man-in-the-middle-aanvallen. Vertrouw niet op TLS met zelfondertekende certificaten in een productie omgeving of op servers die zijn verbonden met internet. <br> Zie voor meer informatie [SSL gebruiken met een Microsoft SQL Server DB-exemplaar](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) of [zelf studie: migratie van RDS-SQL Server naar Azure met behulp van DMS](./index.yml). |
| **Fout 300** -de gebruiker heeft niet de vereiste machtigingen. De machtiging voor het weer geven van de SERVER status is geweigerd voor het object {Server}, de data base {data base} | Deze fout treedt op als de gebruiker niet gemachtigd is om de migratie uit te voeren. Om het probleem op te lossen, raadpleegt u [Server machtigingen verlenen-Transact-SQL](/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) of [zelf studie: RDS SQL Server migreren naar Azure met behulp van DMS](./index.yml) voor meer informatie. |

> [!NOTE]
> Raadpleeg de volgende bronnen voor meer informatie over het oplossen van problemen met het maken van verbinding met een bron-AWS RDS SQL Server:
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (Verbindingsproblemen met SQL Server oplossen)
> * [Hoe kan ik problemen oplossen met het maken van verbinding met mijn Amazon RDS data base-exemplaar?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Bekende problemen

* [Bekende problemen/migratie beperkingen met online migraties naar Azure SQL Database](./index.yml)
* [Bekende problemen/migratie beperkingen met online migraties naar Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Bekende problemen/migratie beperkingen met online migraties naar Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Volgende stappen

* Bekijk het artikel [Azure database Migration Service Power shell](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Bekijk het artikel [How to Configure Server para meters in azure database for MySQL met behulp van de Azure Portal](../mysql/howto-server-parameters.md).
* Bekijk het artikel [overzicht van de vereisten voor het gebruik van Azure database Migration service](./pre-reqs.md).
* Raadpleeg de [Veelgestelde vragen over het gebruik van Azure database Migration service](./faq.md).
