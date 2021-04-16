---
title: 'Azure Synapse Analytics: Migratiehandleiding'
description: Volg deze handleiding om uw databases te migreren naar een Azure Synapse Analytics sql-pool.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565641"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Een datawarehouse migreren naar een toegewezen SQL-pool in Azure Synapse Analytics

De volgende secties bieden een overzicht van wat er bij het migreren van een bestaande datawarehouse-oplossing naar een Azure Synapse Analytics SQL-pool komt kijken.

## <a name="overview"></a>Overzicht

Voordat u met de migratie begint, moet u controleren of Azure Synapse Analytics de beste oplossing voor uw workload is. Azure Synapse Analytics is een gedistribueerd systeem dat is ontworpen om analyses uit te voeren op grote gegevens. Voor het migreren naar Azure Synapse Analytics zijn enkele ontwerpwijzigingen vereist die niet moeilijk te begrijpen zijn, maar die enige tijd in de implementatie kunnen duren. Als uw bedrijf een datawarehouse van ondernemingsklasse vereist, zijn de voordelen de moeite waard. Als u echter niet de kracht van Azure Synapse Analytics nodig hebt, is het [](https://docs.microsoft.com/sql/sql-server/) rendabeler om SQL Server of [Azure SQL Database.](https://docs.microsoft.com/azure/azure-sql/)

Overweeg het gebruik Azure Synapse Analytics wanneer u:

- Een of meer terabytes aan gegevens hebben.
- Plan het uitvoeren van analyses op aanzienlijke hoeveelheden gegevens.
- De mogelijkheid nodig om rekenkracht en opslag te schalen.
- U wilt besparen op kosten door rekenbronnen te onderbreken wanneer u ze niet nodig hebt.

In plaats Azure Synapse Analytics, kunt u andere opties overwegen voor operationele workloads (OLTP) met:

- Lees- en schrijffrequentie van hoge frequentie.
- Grote aantallen enkelvoudige selecties.
- Grote volumes met invoegingen met één rij.
- Verwerkingsbehoeften voor rij per rij.
- Incompatibele indelingen (bijvoorbeeld JSON en XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Een van de kritieke blokkeringen voor klanten is het omzetten van hun databaseobjecten wanneer ze van het ene systeem naar het andere migreren. [Azure Synapse Pad helpt u](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) bij het upgraden naar een modern datawarehouse-platform door de objectvertaling van uw bestaande datawarehouse te automatiseren. Het is een gratis, intuïtief en eenvoudig te gebruiken hulpprogramma waarmee de codevertaling wordt automatiseren om een snellere migratie naar Azure Synapse Analytics.

## <a name="prerequisites"></a>Vereisten

# <a name="migrate-from-sql-server"></a>[Migreren van SQL Server](#tab/migratefromSQLServer)

Als u uw SQL Server wilt migreren naar Azure Synapse Analytics, moet u aan de volgende vereisten hebben voldaan:

- Een datawarehouse- of analyseworkload hebben.
- Download de nieuwste versie van [Azure Synapse Pad om](https://www.microsoft.com/en-us/download/details.aspx?id=102787) de SQL Server te migreren naar Azure Synapse objecten.
- Een toegewezen [SQL-pool in een](../get-started-create-workspace.md) Azure Synapse werkruimte.

# <a name="migrate-from-netezza"></a>[Migreren vanuit Netezza](#tab/migratefromNetezza)

Als u uw Netezza-datawarehouse wilt migreren naar Azure Synapse Analytics, moet u aan de volgende vereisten hebben voldaan:

- Download de nieuwste versie van [Azure Synapse Pad om](https://www.microsoft.com/en-us/download/details.aspx?id=102787) de SQL Server te migreren naar Azure Synapse objecten.
- Een toegewezen [SQL-pool in een](../get-started-create-workspace.md) Azure Synapse werkruimte.

Zie voor meer informatie [Azure Synapse Analytics en migratie voor Netezza.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)

# <a name="migrate-from-snowflake"></a>[Migreren vanuit Snowflake](#tab/migratefromSnowflake)

Als u uw Snowflake-datawarehouse wilt migreren naar Azure Synapse Analytics, moet u aan de volgende vereisten hebben voldaan:

- Download de nieuwste versie van [Azure Synapse Pad om](https://www.microsoft.com/en-us/download/details.aspx?id=102787) Snowflake-objecten te migreren naar Azure Synapse objecten.
- Een toegewezen [SQL-pool in een](../get-started-create-workspace.md) Azure Synapse werkruimte.

# <a name="migrate-from-oracle"></a>[Migreren vanuit Oracle](#tab/migratefromOracle)

Als u uw Oracle-datawarehouse wilt Azure Synapse Analytics, moet u aan de volgende vereisten hebben voldaan:

- Een datawarehouse- of analyseworkload hebben.
- Download SQL Server Migration Assistant Oracle om Oracle-objecten te converteren naar SQL Server. Zie Oracle-databases migreren naar [SQL Server (OracleToSQL) voor meer informatie.](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)
- Download de nieuwste versie van [Azure Synapse Pad om](https://www.microsoft.com/download/details.aspx?id=102787) de SQL Server te migreren naar Azure Synapse objecten.
- Een toegewezen [SQL-pool in een](../get-started-create-workspace.md) Azure Synapse werkruimte.

Zie voor meer informatie Azure Synapse Analytics [en migratie voor een Oracle-datawarehouse.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)

---

## <a name="pre-migration"></a>Premigratie

Nadat u hebt besloten om een bestaande oplossing naar Azure Synapse Analytics migreren, moet u de migratie plannen voordat u aan de slag gaat. Een primair doel van de planning is ervoor te zorgen dat uw gegevens, tabelschema's en code compatibel zijn met Azure Synapse Analytics. Er zijn enkele compatibiliteitsverschillen tussen uw huidige systeem en Azure Synapse Analytics die u moet omdraaien. Bovendien kost het migreren van grote hoeveelheden gegevens naar Azure tijd. Een zorgvuldige planning zal het proces voor het verkrijgen van uw gegevens naar Azure versnellen.

Een ander belangrijk doel van de planning is het aanpassen van uw ontwerp om ervoor te zorgen dat uw oplossing optimaal kan profiteren van de hoge queryprestaties die Azure Synapse Analytics is ontworpen. Het ontwerpen van datawarehouses voor schaal biedt unieke ontwerppatronen, waardoor traditionele benaderingen niet altijd de beste aanpak zijn. Hoewel er na de migratie enkele ontwerpaanpassingen kunnen worden aangebracht, bespaart u later tijd door eerder in het proces wijzigingen aan te brengen.

## <a name="migrate"></a>Migrate

Als u een geslaagde migratie wilt uitvoeren, moet u uw tabelschema's, code en gegevens migreren. Zie de volgende artikelen voor gedetailleerdere richtlijnen over deze onderwerpen:

- [Tabelontwerp overwegen](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Overweeg codewijziging](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Uw gegevens migreren](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Overweeg workloadbeheer](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Meer bronnen

Het klantadviesteam heeft een aantal goede Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) gepubliceerd als blogberichten. Zie Gegevens migreren naar Azure SQL Data Warehouse [in de praktijk voor meer informatie over migratie.](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)

## <a name="migration-assets-from-real-world-engagements"></a>Migratie van assets uit echte betrokkenheiden

Zie de volgende resources voor meer hulp bij het voltooien van dit migratiescenario. Ze zijn ontwikkeld ter ondersteuning van een echte migratieprojectbetrokkenheid.

| Titel/koppeling                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Model en hulpprogramma voor evaluatie van gegevensworkloads](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulpprogramma biedt voorgestelde 'best fit' doelplatforms, cloud-gereedheid en herstelniveau voor toepassingen of databases voor een bepaalde workload. Het biedt eenvoudige berekening met één klik en het genereren van een rapport waarmee u grote evaluaties van onroerend goed kunt versnellen door een geautomatiseerd en uniform platformbesluitproces te bieden. |
| [Problemen met gegevenscoderen afhandelen tijdens het laden van gegevens naar Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Dit blogbericht biedt inzicht in enkele problemen met gegevenscoderen die u kunt tegenkomen tijdens het gebruik van PolyBase om gegevens te laden in SQL Data Warehouse. Dit artikel bevat ook enkele opties die u kunt gebruiken om dergelijke problemen op te lossen en de gegevens te laden. |
| [Tabelgrootten in Azure Synapse Analytics toegewezen SQL-pool](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Een van de belangrijkste taken die een architect moet uitvoeren, is het op halen van metrische gegevens over een nieuwe omgeving na de migratie. Voorbeelden hiervan zijn het verzamelen van laadtijden van on-premises naar de cloud en het verzamelen van PolyBase-laadtijden. Een van de belangrijkste taken is het bepalen van de opslaggrootte in SQL Data Warehouse vergeleken met het huidige platform van de klant. |
| [Hulpprogramma voor het verplaatsen van on-premises SQL Server aanmeldingen naar Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Een PowerShell-script maakt een T-SQL-opdrachtscript om aanmeldingen opnieuw te maken en databasegebruikers te selecteren van een on-premises exemplaar van SQL Server naar een Azure SQL platform as a service(PaaS)-service. Het hulpprogramma staat automatische toewijzing van Windows Server Active Directory-accounts toe aan Azure Active Directory-accounts, of het kan UPN-opzoekingen voor elke aanmelding op on-premises Windows Server Active Directory. Het hulpprogramma verplaatst eventueel ook SQL Server eigen aanmeldingen. Aangepaste server- en databaserollen worden als script uitgevoerd, samen met rollidmaatschap, databaserol en gebruikersmachtigingen. Ingesloten databases worden niet ondersteund en er wordt slechts een subset van mogelijke SQL Server-machtigingen als script gebruikt. Meer informatie is beschikbaar in het ondersteuningsdocument en het script bevat opmerkingen voor een beter begrip. |

Het Data SQL Engineering-team heeft deze resources ontwikkeld. De kern van dit team is het deblokkeren en versnellen van complexe modernisering voor gegevensplatformmigratieprojecten naar het Azure-gegevensplatform van Microsoft.

## <a name="videos"></a>Video's

Kijk hoe [Walgreens zijn](https://www.youtube.com/watch?v=86dhd8N1lH4) voorraadsysteem voor de detailhandel heeft gemigreerd met ongeveer 100 TB aan gegevens van Netezza naar Azure Synapse Analytics in recordtijd.
