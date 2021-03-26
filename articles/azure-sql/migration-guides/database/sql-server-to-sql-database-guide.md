---
title: 'SQL Server Azure SQL Database: migratie handleiding'
description: Volg deze hand leiding voor het migreren van uw SQL Server-data bases naar Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: e2de694a153276dcace1070d35af44dec1056e03
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564922"
---
# <a name="migration-guide-sql-server-to-azure-sql-database"></a>Migratie handleiding: SQL Server naar Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Deze hand leiding helpt u bij het migreren van uw SQL Server-exemplaar naar Azure SQL Database. 

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Data Base service (AWS RDS) 
- Compute Engine (Google Cloud Platform-GCP)  
- Cloud-SQL voor SQL Server (Google Cloud Platform – GCP) 

Zie het [migratie overzicht](sql-server-to-sql-database-overview.md)voor meer informatie over migratie. Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Migratie proces stroom":::

## <a name="prerequisites"></a>Vereisten 

Als u uw SQL Server naar Azure SQL Database wilt migreren, controleert u of u de volgende vereisten hebt: 

- Een gekozen [migratie methode](sql-server-to-sql-database-overview.md#compare-migration-options) en overeenkomende hulpprogram ma's.
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) geïnstalleerd op een computer die verbinding kan maken met uw bron SQL Server.
- Een doel [Azure SQL database](../../database/single-database-create-quickstart.md). 
- Connectiviteit en de juiste machtigingen voor toegang tot zowel de bron als het doel. 



## <a name="pre-migration"></a>Premigratie

Nadat u hebt gecontroleerd of uw bron omgeving wordt ondersteund, begint u met de fase voorafgaand aan de migratie. Ontdek alle bestaande gegevens bronnen, beoordeel de haal baarheid van de migratie en Identificeer eventuele belemmeringen voor het blok keren van de migratie. 

### <a name="discover"></a>Ontdekken

Scan in de fase ontdekken het netwerk om alle SQL Server exemplaren en functies te identificeren die door uw organisatie worden gebruikt. 

Gebruik [Azure migrate](../../../migrate/migrate-services-overview.md) om de geschiktheid voor migratie van on-premises servers te beoordelen, op prestaties gebaseerde grootte te bepalen en kosten ramingen op te geven voor het uitvoeren van deze in Azure. 

U kunt ook de [micro soft Assessment and Planning Toolkit (de ' kaart Toolkit ')](https://www.microsoft.com/download/details.aspx?id=7826) gebruiken om uw huidige IT-infra structuur te beoordelen. De Toolkit biedt een krachtig hulp programma voor inventarisatie, evaluatie en rapportage om het migratie planningsproces te vereenvoudigen. 

Zie [Services en hulpprogram ma's die beschikbaar zijn voor scenario's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor meer informatie over de hulpprogram ma's die beschikbaar zijn voor gebruik voor de Discover-fase. 

### <a name="assess"></a>Evalueren 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Nadat gegevens bronnen zijn gedetecteerd, evalueert u alle on-premises SQL Server Data Base (s) die kunnen worden gemigreerd naar Azure SQL Database om migratie blokken of compatibiliteits problemen te identificeren. 

U kunt de Data Migration Assistant (versie 4,1 en hoger) gebruiken om te bepalen welke data bases er worden opgehaald: 

- [Aanbevelingen voor Azure-doel](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Aanbevelingen voor Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Voer de volgende stappen uit om uw omgeving te beoordelen met de evaluatie van de database migratie: 

1. Open de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selecteer **bestand** en kies vervolgens **nieuwe beoordeling**. 
1. Geef een project naam op, selecteer SQL Server als bron server type en selecteer Azure SQL Database als doel server type. 
1. Selecteer de type (n) van de evaluatie rapporten die u wilt genereren. Bijvoorbeeld database compatibiliteit en functie pariteit. Op basis van het type evaluatie kunnen de vereiste machtigingen op de bron SQL Server verschillen.  DMA markeert de machtigingen die vereist zijn voor de gekozen Advisor voordat de evaluatie wordt uitgevoerd.
    - De categorie **functie pariteit** biedt een uitgebreide set aanbevelingen, alternatieven die beschikbaar zijn in Azure en verkleint de stappen om u te helpen bij het plannen van uw migratie project. (sysadmin-machtigingen vereist)
    - De categorie **compatibiliteits problemen** bevat gedeeltelijk ondersteunde of niet-ondersteunde functie compatibiliteits problemen die mogelijk de migratie blok keren, evenals aanbevelingen voor het oplossen van deze ( `CONNECT SQL` , `VIEW SERVER STATE` en de `VIEW ANY DEFINITION` vereiste machtigingen).
1. Geef de gegevens van de bron verbinding voor uw SQL Server op en maak verbinding met de bron database.
1. Selecteer **evaluatie starten**. 
1. Nadat het proces is voltooid, selecteert u de evaluatie rapporten voor migratie blok keren en pariteits problemen met functies door deze te controleren. Het beoordelings rapport kan ook worden geëxporteerd naar een bestand dat kan worden gedeeld met andere teams of mede werkers in uw organisatie. 
1. Bepaal het compatibiliteits niveau van de data base dat na de migratie wordt geminimaliseerd.  
1. Bepaal de beste Azure SQL Database SKU voor uw on-premises werk belasting. 

Zie [een SQL Server migratie-evaluatie uitvoeren met Data Migration Assistant](/sql/dma/dma-assesssqlonprem)voor meer informatie.

Als de evaluatie meerdere blok keringen detecteert om te bevestigen dat de data base niet gereed is voor een Azure SQL Database migratie, kunt u het volgende overwegen:

- Door [Azure SQL beheerd exemplaar](../managed-instance/sql-server-to-managed-instance-overview.md) als er meerdere afhankelijkheden met exemplaar bereik zijn
- [SQL Server op Azure virtual machines](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) als zowel SQL database als SQL Managed instance niet geschikt zijn voor geschikte doelen. 



#### <a name="scaled-assessments-and-analysis"></a>Geschaalde evaluaties en analyse
Data Migration Assistant biedt ondersteuning voor het uitvoeren van geschaalde evaluaties en consolidatie van de evaluatie rapporten voor analyse. 

Als u meerdere servers en data bases hebt die op schaal moeten worden beoordeeld en geanalyseerd om een breder overzicht van de gegevens te krijgen, raadpleegt u de volgende koppelingen voor meer informatie:

- [Geschaalde evaluaties uitvoeren met Power shell](/sql/dma/dma-consolidatereports)
- [Analyse rapporten analyseren met behulp van Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Het uitvoeren van analyses op schaal voor meerdere data bases, met name grote bestanden, kan ook worden geautomatiseerd met behulp van het [hulp programma voor de DMA-opdracht regel](/sql/dma/dma-commandline) en geüpload naar [Azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) voor verdere analyse en doel gereedheid.

## <a name="migrate"></a>Migrate

Nadat u taken hebt voltooid die zijn gekoppeld aan de fase voorafgaand aan de migratie, bent u klaar om het schema en de gegevens migratie uit te voeren. 

Migreer uw gegevens met de gekozen [migratie methode](sql-server-to-sql-database-overview.md#compare-migration-options). 

In deze hand leiding worden de twee populairste opties-Data Migration Assistant en Azure Database Migration Service beschreven. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

Voer de volgende stappen uit om een Data Base te migreren van SQL Server naar Azure SQL Database met behulp van DMA: 

1. Down load en installeer de Migration Assistant van de [Data Base](https://www.microsoft.com/download/details.aspx?id=53595).
1. Maak een nieuw project en selecteer **migratie** als het project type.
1. Stel het type bron server in op **SQL Server** en geef het type doel server op **Azure SQL database**, selecteer het migratie bereik als **schema en gegevens** en selecteer **maken**.
1. Geef in het migratie project de gegevens van de bron server op, zoals de server naam, de referenties om verbinding te maken met de server en de bron database die moet worden gemigreerd.
1. Geef in de details van de doel server de naam op van de Azure SQL Database-Server, de referenties om verbinding te maken met de server en de doel database waarnaar moet worden gemigreerd.
1. Selecteer de schema objecten en implementeer deze voor de doel Azure SQL Database.
1. Selecteer tot slot **gegevens migratie starten** en controleer de voortgang van de migratie.

Zie voor een gedetailleerde zelf studie [migratie van on-premises SQL Server of SQL Server op Azure-vm's om te Azure SQL database met behulp van de Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Schaal uw Data Base naar een hogere servicelaag en de berekenings grootte tijdens het import proces om de snelheid van het importeren te maximaliseren door meer resources te leveren. U kunt vervolgens omlaag schalen nadat het importeren is voltooid.</br>
> - Het compatibiliteits niveau van de geïmporteerde data base is gebaseerd op het compatibiliteits niveau van de bron database. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Als u data bases wilt migreren van SQL Server naar Azure SQL Database met behulp van DMS, volgt u de onderstaande stappen:

1. Als u dat nog niet hebt gedaan, registreert u de resource provider **micro soft. DataMigration** in uw abonnement. 
1. Maak een Azure Database Migration Service-exemplaar op een gewenste locatie van uw keuze (bij voor keur in dezelfde regio als uw doel Azure SQL Database). Selecteer een bestaand virtueel netwerk of maak een nieuwe om uw DMS-exemplaar te hosten.
1. Nadat uw DMS-exemplaar is gemaakt, maakt u een nieuw migratie project en geeft u het type bron server op als **SQL Server** en het type doel server als **Azure SQL database**. Kies **offline gegevens migratie** als het type activiteit in de Blade migratie project maken.
1. Geef de bron SQL Server Details op de pagina met details van de **migratie bron** en de doel Azure SQL database Details op de pagina doel gegevens van de **migratie** .
1. Wijs de bron-en doel database voor migratie toe en selecteer vervolgens de tabellen die u wilt migreren.
1. Bekijk het migratie overzicht en selecteer **migratie uitvoeren**. U kunt vervolgens de migratie activiteit controleren en de voortgang van de database migratie controleren.

Zie [SQL Server migreren naar een Azure SQL database met behulp van DMS](../../../dms/tutorial-sql-server-to-azure-sql.md)voor een gedetailleerde zelf studie. 

## <a name="data-sync-and-cutover"></a>Gegevens synchronisatie en cutover

Bij het gebruik van migratie opties die voortdurend wijzigingen repliceren/synchroniseren van bron naar het doel, kunnen de bron gegevens en het schema van het doel veranderen. Zorg er tijdens de gegevens synchronisatie voor dat alle wijzigingen in de bron tijdens het migratie proces worden vastgelegd en toegepast op het doel. 

Nadat u hebt gecontroleerd of de gegevens in de bron en het doel gelijk zijn, kunt u cutover van de bron naar de doel omgeving. Het is belang rijk om het cutover-proces met Business/Application teams te plannen om ervoor te zorgen dat een minimale onderbreking tijdens cutover geen invloed heeft op de bedrijfs continuïteit. 

> [!IMPORTANT]
> Zie voor meer informatie over de specifieke stappen die zijn gekoppeld aan het uitvoeren van een cutover als onderdeel van de migraties met behulp van DMS, [migratie Cutover uitvoeren](../../../dms/tutorial-sql-server-to-azure-sql.md).

## <a name="migration-recommendations"></a>Aanbevelingen voor migratie

Als u de migratie naar Azure SQL Database wilt versnellen, moet u rekening houden met de volgende aanbevelingen:

|  | Bron conflicten | Aanbeveling |
|--|--|--|
| **Bron (doorgaans on-premises)** |Primair knel punt tijdens de migratie in bron is gegevens-I/O en latentie van gegevens bestanden die zorgvuldig moeten worden gecontroleerd.  |Op basis van de latentie van DATA IO en gegevens bestanden en afhankelijk van of het een virtuele machine of fysieke server is, moet u de opslag beheerder en de opties verkennen om het knel punt te verhelpen. |
|**Doel (Azure SQL Database)**|De grootste beperkende factor is de frequentie van het genereren en de latentie van het logboek bestand. Met Azure SQL Database kunt u een frequentie van Maxi maal 96 MB/s-logboek generatie ophalen. | Als u de migratie wilt versnellen, schaalt u de doel-SQL-Data Base naar Bedrijfskritiek GEN5 8 vCore om de maximale logboek generatie snelheid van 96 MB/s te verkrijgen en kunt u ook lage latentie voor het logboek bestand bereiken. De [grootschalige](../../database/service-tier-hyperscale.md) -servicelaag levert een logboek frequentie van 100 MB per seconde, ongeacht het gekozen service niveau |
|**Netwerk** |De benodigde netwerk bandbreedte is gelijk aan het maximale aantal opname snelheden van het logboek 96 MB/s (768 MB/s) |Afhankelijk van de netwerk verbinding van uw on-premises Data Center naar Azure, controleert u uw netwerk bandbreedte (doorgaans [Azure ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)) om te voldoen aan de maximale opname frequentie van het logboek. |
|**Virtuele machine die wordt gebruikt voor Data Migration Assistant (DMA)** |CPU is het belangrijkste knel punt voor de virtuele machine met DMA |Overwegingen bij het versnellen van de gegevens migratie met behulp van </br>-Virtuele machines van Azure compute </br>-Gebruik ten minste F8s_v2 (8 VCore) VM voor het uitvoeren van DMA </br>-Zorg ervoor dat de virtuele machine wordt uitgevoerd in dezelfde Azure-regio als het doel |
|**Azure Database Migration Service (DMS)** |De berekenings capaciteit van bronnen en database objecten berekenen voor DMS |Gebruik Premium 4 vCore. DMS zorgt automatisch voor database objecten zoals refererende sleutels, triggers, beperkingen en niet-geclusterde indexen en vereist geen hand matige tussen komst.  |


## <a name="post-migration"></a>Postmigratie

Nadat u de migratie fase hebt voltooid, gaat u door een reeks taken na migratie om ervoor te zorgen dat alles soepel en efficiënt werkt. 

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met de nauw keurigheid van gegevens en het controleren van de volledigheid, en het oplossen van prestatie problemen met de werk belasting. 

### <a name="remediate-applications"></a>Toepassingen herstellen 

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. Dit kan in sommige gevallen wijzigingen in de toepassingen vereisen.

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **Test omgeving instellen**: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
1. **Validatie tests uitvoeren**: Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Prestatie testen uitvoeren**: prestaties testen op basis van de bron en het doel, en vervolgens de resultaten analyseren en vergelijken.


## <a name="leverage-advanced-features"></a>Geavanceerde functies gebruiken 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies die worden geboden door SQL Database, zoals [ingebouwde hoge Beschik baarheid](../../database/high-availability-sla.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md)en [het bewaken en afstemmen van uw werk belasting](../../database/monitor-tune-overview.md). 

Sommige SQL Server-functies zijn alleen beschikbaar als het [compatibiliteits niveau van de data base](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) is gewijzigd in het meest recente compatibiliteits niveau (150). 

Zie [Azure SQL database na migratie beheren](../../database/manage-data-after-migrating-to-database.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Voor meer informatie over SQL Database raadpleegt u:
    - [Een overzicht van Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.
