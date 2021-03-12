---
title: 'Oracle naar Azure Synapse Analytics: migratie handleiding'
description: In de volgende secties vindt u een overzicht van wat er is betrokken bij het migreren van een bestaande Oracle data base-oplossing naar Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 6b5412b24ce6da3476e0c80f31fb07e3647fe5a2
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225740"
---
# <a name="migration-guide-migrate-oracle-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migratie handleiding: Oracle Data Warehouse migreren naar een toegewezen SQL-groep in azure Synapse Analytics
In de volgende secties vindt u een overzicht van wat er is betrokken bij het migreren van een bestaande Oracle Data Warehouse-oplossing naar Azure Synapse Analytics.

## <a name="overview"></a>Overzicht
Voordat u migreert, moet u controleren of Azure Synapse Analytics de beste oplossing voor uw werk belasting is. Azure Synapse Analytics is een gedistribueerd systeem dat is ontworpen voor het uitvoeren van analyses op grote gegevens. Voor de migratie naar Azure Synapse Analytics zijn enkele ontwerp wijzigingen vereist die niet moeilijk te begrijpen zijn, maar dit kan enige tijd duren om te implementeren. Als uw bedrijf een Data Warehouse op bedrijfs niveau vereist, zijn de voor delen de moeite waard. Als u de kracht van Azure Synapse Analytics echter niet nodig hebt, is het rendabeler om [SQL Server](https://docs.microsoft.com/sql/sql-server/) of [Azure SQL database](https://docs.microsoft.com/azure/azure-sql/)te gebruiken.

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
- Incompatibele indelingen (JSON, XML).

## <a name="prerequisites"></a>Vereisten
Als u uw Oracle-Data Warehouse wilt migreren naar Azure Synapse Analytics, controleert u of u de volgende vereisten hebt: 

- Een Data Warehouse of een analytische werk belasting 
- SSMA voor Oracle om Oracle-objecten te converteren naar SQL Server. Zie [Oracle-data bases migreren naar SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) voor meer informatie. 
- Nieuwste versie van het hulp programma [Azure Synapse-paden](https://www.microsoft.com/en-us/download/details.aspx?id=102787) voor het migreren van SQL Server-objecten naar Azure Synapse-objecten.
- Een [exclusieve SQL-groep](../get-started-create-workspace.md) in de Azure Synapse-werk ruimte.  


## <a name="pre-migration"></a>Premigratie
Nadat u de beslissing hebt genomen voor het migreren van een bestaande oplossing naar Azure Synapse Analytics, is het belang rijk dat u de migratie plant voordat u aan de slag gaat. Een primair doel van het plannen is om ervoor te zorgen dat uw gegevens, tabel schema's en code compatibel zijn met Azure Synapse Analytics. Er zijn een aantal compatibiliteits verschillen tussen uw huidige systeem en SQL Data Warehouse die u moet omzeilen. Bovendien kost het migreren van grote hoeveel heden gegevens naar Azure tijd. Met een zorgvuldige planning wordt het proces van het ophalen van uw gegevens naar Azure sneller uitgevoerd. Een ander belang rijk doel van het plannen is om uw ontwerp aan te passen om ervoor te zorgen dat uw oplossing optimaal profiteert van de hoge query prestaties die Azure Synapse Analytics is ontworpen om te bieden. Met het ontwerpen van data warehouses voor schalen worden unieke ontwerp patronen geïntroduceerd. traditionele benaderingen zijn dus niet altijd het beste. Hoewel er na de migratie enkele ontwerp aanpassingen kunnen worden aangebracht, bespaart u later tijd bij wijzigingen.

## <a name="azure-synapse-pathway"></a>Azure Synapse-routes
Een van de belang rijke blok keringen van klanten vertaalt de SQL-code bij het migreren van het ene systeem naar het andere. Met [Azure Synapse traject](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) kunt u een upgrade uitvoeren naar een modern Data Warehouse-platform door de code omzetting van uw bestaande Data Warehouse te automatiseren. Het is een gratis, intuïtief en gemakkelijk te gebruiken hulp programma waarmee de code omzetting wordt geautomatiseerd, waardoor de migratie naar Azure Synapse Analytics sneller kan worden gemigreerd.

## <a name="migrate"></a>Migrate
Als u een geslaagde migratie wilt uitvoeren, moet u de tabel schema's, code en gegevens migreren. Voor meer informatie over deze onderwerpen raadpleegt u:
- In het artikel worden [uw schema's gemigreerd](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- In het artikel wordt [uw code gemigreerd](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- In het artikel worden [uw gegevens gemigreerd](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>Aanvullende bronnen 
- Het kat (klant advies team) heeft enkele fantastische richt lijnen voor Azure Synapse Analytics (voorheen SQL Data Warehouse) gepubliceerd als blog berichten. Bekijk het artikel en [Migreer de gegevens naar Azure SQL data warehouse in de praktijk](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)om meer informatie over de migratie te krijgen.
- Bekijk het technische document [om uw data base-migratie naar Azure te selecteren](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) voor aanvullende informatie en aanbevelingen.
- Zie de artikel [service en hulpprogram ma's voor gegevens migratie](https://docs.microsoft.com/azure/dms/dms-tools-matrix)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

## <a name="migration-assets-from-real-world-engagements"></a>Migratie-assets van werkelijke afspraken
Voor aanvullende hulp bij het volt ooien van dit migratie scenario raadpleegt u de volgende bronnen, die zijn ontwikkeld ter ondersteuning van een echt toonaangevend migratie project.

| Titel/koppeling                              | Beschrijving                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote en kleine beoordelingen aanzienlijk sneller door middel van en geautomatiseerd en uniform platform besluitvormings proces. |
| [Problemen met gegevens versleuteling verwerken tijdens het laden van gegevens in azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Deze blog is bedoeld om inzicht te krijgen in een aantal gegevens coderings problemen die kunnen optreden bij het gebruik van poly Base om gegevens te laden naar SQL Data Warehouse. Dit artikel bevat ook enkele opties die u kunt gebruiken om dergelijke problemen op te lossen en de gegevens met succes te laden. |
| [Tabel grootten in azure Synapse Analytics SQL-groep ophalen](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Een van de belangrijkste taken die een architect moet uitvoeren, is het verkrijgen van metrische gegevens over een nieuwe omgeving na migratie: het verzamelen van laad tijden van on-premises naar de Cloud, het verzamelen van polybase load tijden, enzovoort. Van deze taken is een van de belangrijkste voor het bepalen van de opslag grootte in SQL Data Warehouse vergeleken met het huidige platform van de klant. |
| [Hulp programma voor het verplaatsen van on-premises SQL Server aanmeldingen bij Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Een Power shell-script waarmee een T-SQL-opdracht script wordt gemaakt om aanmeldingen opnieuw te maken en database gebruikers te selecteren van een on-premises SQL Server een Azure SQL PaaS-service. Met het hulp programma kunnen Windows AD-accounts automatisch worden toegewezen aan Azure AD-accounts of kan de UPN-zoek acties voor elke aanmelding worden uitgevoerd op basis van de on-premises Windows Active Directory. Met het hulp programma kunt u ook SQL Server systeem eigen aanmeldingen verplaatsen. Aangepaste server-en database rollen worden gescripteerd, evenals rollen lidmaatschap en databaserol en gebruikers machtigingen. Inge sloten data bases worden nog niet ondersteund en alleen een subset van mogelijke SQL Server machtigingen wordt gescripteerd. de machtigingen verlenen met toekenning worden dus niet ondersteund (complexe machtigings structuren). Meer informatie is beschikbaar in het ondersteunings document en het script bevat opmerkingen om inzicht te krijgen. |

> [!NOTE]
> Deze bovenstaande bronnen zijn ontwikkeld als onderdeel van het programma voor het maken van gegevens migratie (DM aan de slag), dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van DM meer is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie deelneemt aan het programma DM aan de slag, neemt u contact op met uw account team en vraagt u om een aanstelling in te dienen.

## <a name="videos"></a>Video's
- Zie de video- [hand leiding voor database migratie](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/)voor een overzicht van de Azure data base Migration Guide en de informatie die het bevat.
- Zie het video- [overzicht van de migratie traject en de hulpprogram ma's/services die worden aanbevolen voor het uitvoeren van de evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)voor een door loop van de fasen van het migratie proces en Details over de specifieke hulpprogram ma's en services die worden aanbevolen voor het uitvoeren van een evaluatie en migratie.