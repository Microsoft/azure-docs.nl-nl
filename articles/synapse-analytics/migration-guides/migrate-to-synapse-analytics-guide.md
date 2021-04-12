---
title: 'Azure Synapse Analytics: migratie handleiding'
description: Volg deze hand leiding om uw data bases te migreren naar een exclusieve SQL-groep voor Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8304064e62ea3996e2ee6be6e12885cb853c9375
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278776"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Een Data Warehouse migreren naar een toegewezen SQL-groep in azure Synapse Analytics

In de volgende secties vindt u een overzicht van wat er is betrokken bij het migreren van een bestaande Data Warehouse-oplossing naar een exclusieve SQL-groep voor Azure Synapse Analytics.

## <a name="overview"></a>Overzicht

Voordat u met de migratie begint, moet u controleren of Azure Synapse Analytics de beste oplossing is voor uw werk belasting. Azure Synapse Analytics is een gedistribueerd systeem dat is ontworpen voor het uitvoeren van analyses op grote gegevens. Voor de migratie naar Azure Synapse Analytics zijn enkele ontwerp wijzigingen vereist die niet moeilijk te begrijpen zijn, maar dit kan enige tijd duren om te implementeren. Als uw bedrijf een Data Warehouse op bedrijfs niveau vereist, zijn de voor delen de moeite waard. Als u de kracht van Azure Synapse Analytics echter niet nodig hebt, is het rendabeler om [SQL Server](https://docs.microsoft.com/sql/sql-server/) of [Azure SQL database](https://docs.microsoft.com/azure/azure-sql/)te gebruiken.

U kunt met behulp van Azure Synapse Analytics het volgende doen:

- Een of meer terabytes aan gegevens hebben.
- Plan om analyses uit te voeren op substantiële hoeveel heden gegevens.
- U hebt de mogelijkheid om reken-en opslag capaciteit te schalen.
- U wilt besparen op kosten door reken resources te onderbreken wanneer u deze niet nodig hebt.

In plaats van Azure Synapse Analytics kunt u andere opties voor operationele (OLTP) werk belastingen gebruiken die het volgende hebben:

- Hoge frequentie Lees-en schrijf bewerkingen.
- Grote aantallen Singleton wordt geselecteerd.
- Grote hoeveel heden van één rij worden ingevoegd.
- Verwerkings behoeften voor rijen per rij.
- Incompatibele indelingen (bijvoorbeeld JSON en XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Een van de belang rijke blok keringen van klanten is om hun database objecten te vertalen wanneer deze van het ene naar het andere systeem worden gemigreerd. Met [Azure Synapse traject](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) kunt u een upgrade uitvoeren naar een modern Data Warehouse-platform door de object vertalingen van uw bestaande Data Warehouse te automatiseren. Het is een gratis, intuïtief hulp programma dat de code omzetting automatiseert om een snellere migratie naar Azure Synapse Analytics mogelijk te maken.

## <a name="prerequisites"></a>Vereisten

# <a name="migrate-from-sql-server"></a>[Migreren van SQL Server](#tab/migratefromSQLServer)

Als u uw SQL Server-Data Warehouse wilt migreren naar Azure Synapse Analytics, controleert u of u aan de volgende vereisten voldoet:

- Een Data Warehouse of een analytische werk belasting hebben.
- Down load de nieuwste versie van [Azure Synapse-routes](https://www.microsoft.com/en-us/download/details.aspx?id=102787) om SQL Server-objecten te migreren naar Azure Synapse-objecten.
- Een [toegewezen SQL-groep](../get-started-create-workspace.md) hebben in een Azure Synapse-werk ruimte.

# <a name="migrate-from-netezza"></a>[Migreren vanaf Netezza](#tab/migratefromNetezza)

Als u uw Netezza-Data Warehouse wilt migreren naar Azure Synapse Analytics, controleert u of u aan de volgende vereisten voldoet:

- Down load de nieuwste versie van [Azure Synapse-routes](https://www.microsoft.com/en-us/download/details.aspx?id=102787) om SQL Server-objecten te migreren naar Azure Synapse-objecten.
- Een [toegewezen SQL-groep](../get-started-create-workspace.md) hebben in een Azure Synapse-werk ruimte.

Zie [oplossingen voor Azure Synapse Analytics en migratie voor Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)voor meer informatie.

# <a name="migrate-from-snowflake"></a>[Migreren vanaf sneeuw](#tab/migratefromSnowflake)

Als u uw sneeuw Data Warehouse wilt migreren naar Azure Synapse Analytics, controleert u of u aan de volgende vereisten voldoet:

- Down load de nieuwste versie van [Azure Synapse-paden](https://www.microsoft.com/en-us/download/details.aspx?id=102787) om sneeuw-objecten te migreren naar Azure Synapse-objecten.
- Een [toegewezen SQL-groep](../get-started-create-workspace.md) hebben in een Azure Synapse-werk ruimte.

# <a name="migrate-from-oracle"></a>[Migreren vanuit Oracle](#tab/migratefromOracle)

Als u uw Oracle-Data Warehouse wilt migreren naar Azure Synapse Analytics, controleert u of u aan de volgende vereisten voldoet:

- Een Data Warehouse of een analytische werk belasting hebben.
- Down load SQL Server Migration Assistant voor Oracle om Oracle-objecten te converteren naar SQL Server. Zie [Oracle-data bases migreren naar SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)voor meer informatie.
- Down load de nieuwste versie van [Azure Synapse-routes](https://www.microsoft.com/download/details.aspx?id=102787) om SQL Server-objecten te migreren naar Azure Synapse-objecten.
- Een [toegewezen SQL-groep](../get-started-create-workspace.md) hebben in een Azure Synapse-werk ruimte.

Zie [oplossingen voor Azure Synapse Analytics en migratie voor een Oracle-Data Warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)voor meer informatie.

---

## <a name="pre-migration"></a>Premigratie

Nadat u besluit een bestaande oplossing naar Azure Synapse Analytics te migreren, moet u uw migratie plannen voordat u aan de slag gaat. Een primair doel van het plannen is om ervoor te zorgen dat uw gegevens, tabel schema's en code compatibel zijn met Azure Synapse Analytics. Er zijn een aantal compatibiliteits verschillen tussen uw huidige systeem en Azure Synapse Analytics die u moet omzeilen. Bovendien kost het migreren van grote hoeveel heden gegevens naar Azure tijd. Met een zorgvuldige planning wordt het proces van het ophalen van uw gegevens naar Azure sneller uitgevoerd.

Een ander belang rijk doel van het plannen is om uw ontwerp aan te passen om ervoor te zorgen dat uw oplossing optimaal profiteert van de hoge query prestaties die Azure Synapse Analytics is ontworpen om te bieden. Met het ontwerpen van data warehouses voor schalen worden unieke ontwerp patronen geïntroduceerd. traditionele benaderingen zijn dus niet altijd het beste. Hoewel er na de migratie enkele ontwerp aanpassingen kunnen worden aangebracht, bespaart u later tijd bij wijzigingen.

## <a name="migrate"></a>Migrate

Als u een geslaagde migratie wilt uitvoeren, moet u de tabel schema's, code en gegevens migreren. Raadpleeg de volgende artikelen voor meer informatie over deze onderwerpen:

- [Tabel ontwerp overwegen](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Code wijziging overwegen](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Uw gegevens migreren](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [Werkbelasting beheer overwegen](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>Meer bronnen

Het team van klanten advies heeft enkele fantastische richt lijnen voor Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) gepubliceerd als blog berichten. Zie voor meer informatie over migratie [gegevens migreren naar Azure SQL data warehouse in de praktijk](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Migratie-assets van werkelijke afspraken

Raadpleeg de volgende bronnen voor meer hulp bij het volt ooien van dit migratie scenario. Ze zijn ontwikkeld ter ondersteuning van een praktijk gerichte migratie project.

| Titel/koppeling                              | Beschrijving                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheid en toepassings-of database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren, waarmee u grote en ongeëvenaarde evaluaties versnelt door een geautomatiseerd en uniform platform besluitvormings proces te bieden. |
| [Problemen met gegevens versleuteling verwerken tijdens het laden van gegevens in azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Dit blog bericht geeft inzicht in een aantal problemen met gegevens codering die kunnen optreden tijdens het gebruik van poly Base om gegevens te laden naar SQL Data Warehouse. Dit artikel bevat ook enkele opties die u kunt gebruiken om dergelijke problemen op te lossen en de gegevens met succes te laden. |
| [Tabel grootten in azure Synapse Analytics toegewezen SQL-groep ophalen](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Een van de belangrijkste taken die een architect moet uitvoeren, is het verkrijgen van metrische gegevens over een nieuwe omgeving na migratie. Voor beelden zijn onder andere het verzamelen van laad tijden van on-premises naar de Cloud en het verzamelen van polybase laad tijden. Een van de belangrijkste taken is het bepalen van de opslag grootte in SQL Data Warehouse vergeleken met het huidige platform van de klant. |
| [Hulp programma voor het verplaatsen van on-premises SQL Server aanmeldingen bij Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Met een Power shell-script maakt u een T-SQL-opdracht script voor het opnieuw maken van aanmeldingen en selecteert u database gebruikers van een on-premises exemplaar van SQL Server naar een Azure SQL platform as a Service-Service (PaaS). Met het hulp programma kunt u de automatische toewijzing van Windows Server-Active Directory accounts aan Azure Active Directory accounts, of kan de UPN-zoek acties voor elke aanmelding worden uitgevoerd op basis van on-premises Windows Server-Active Directory. Het hulp programma verplaatst SQL Server systeem eigen aanmeldingen ook. Aangepaste server-en database rollen worden gescripteerd, samen met het rollidmaatschap, de databaserol en de gebruikers machtigingen. Inge sloten data bases worden niet ondersteund en alleen een subset van mogelijke SQL Server machtigingen wordt gescripteerd. Meer informatie is beschikbaar in het document voor ondersteuning en het script bevat opmerkingen waarmee u de inzichten kunt vergemakkelijken. |

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.

## <a name="videos"></a>Video's

Bekijk hoe [Walgreens zijn Retail Inventory System](https://www.youtube.com/watch?v=86dhd8N1lH4) met ongeveer 100 TB aan gegevens van Netezza naar Azure Synapse Analytics in record tijd heeft gemigreerd.
