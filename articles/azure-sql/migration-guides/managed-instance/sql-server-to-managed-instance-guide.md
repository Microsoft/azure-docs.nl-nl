---
title: 'SQL Server naar SQL Managed instance: migratie handleiding'
description: In deze hand leiding leert u uw SQL Server-data bases migreren naar Azure SQL Managed instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 6dcbf4a570fb5cdb58c914ea5e4b1164ed6a76ca
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564487"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Migratie handleiding: SQL Server naar een door SQL beheerd exemplaar
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Deze hand leiding helpt u bij het migreren van uw SQL Server-exemplaar naar Azure SQL Managed instance. 

U kunt SQL Server die on-premises of op worden uitgevoerd, migreren: 

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Data Base service (AWS RDS) 
- Compute Engine (Google Cloud Platform-GCP)  
- Cloud-SQL voor SQL Server (Google Cloud Platform – GCP) 

Zie het [migratie overzicht](sql-server-to-managed-instance-overview.md)voor meer informatie over migratie. Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere scenario's.

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Migratie proces stroom":::

## <a name="prerequisites"></a>Vereisten 

Als u uw SQL Server naar Azure SQL Managed instance wilt migreren, moet u de volgende vereisten door lopen: 

- Kies een [migratie methode](sql-server-to-managed-instance-overview.md#compare-migration-options) en de bijbehorende hulpprogram ma's die nodig zijn voor de gekozen methode
- Installeer [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) op een computer die verbinding kan maken met uw bron SQL Server


## <a name="pre-migration"></a>Premigratie

Nadat u hebt gecontroleerd of uw bron omgeving wordt ondersteund, begint u met de fase voorafgaand aan de migratie. Ontdek alle bestaande gegevens bronnen, beoordeel de haal baarheid van de migratie en Identificeer eventuele belemmeringen voor het blok keren van de migratie.  

### <a name="discover"></a>Ontdekken

Scan in de fase ontdekken het netwerk om alle SQL Server exemplaren en functies te identificeren die door uw organisatie worden gebruikt. 

Gebruik [Azure migrate](../../../migrate/migrate-services-overview.md) om de geschiktheid voor migratie van on-premises servers te beoordelen, op prestaties gebaseerde grootte te bepalen en kosten ramingen op te geven voor het uitvoeren van deze in Azure. 

U kunt ook de [micro soft Assessment and Planning Toolkit (de ' kaart Toolkit ')](https://www.microsoft.com/download/details.aspx?id=7826) gebruiken om uw huidige IT-infra structuur te beoordelen. De Toolkit biedt een krachtig hulp programma voor inventarisatie, evaluatie en rapportage om het migratie planningsproces te vereenvoudigen. 

Zie [Services en hulpprogram ma's die beschikbaar zijn voor scenario's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor meer informatie over de hulpprogram ma's die beschikbaar zijn voor gebruik voor de Discover-fase. 

### <a name="assess"></a>Evalueren 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Nadat gegevens bronnen zijn gedetecteerd, evalueert u alle on-premises SQL Server exemplaren die kunnen worden gemigreerd naar Azure SQL Managed instance om migratie blok keringen of compatibiliteits problemen te identificeren. 

U kunt de Data Migration Assistant (versie 4,1 en hoger) gebruiken om te bepalen welke data bases er worden opgehaald: 

- [Aanbevelingen voor Azure-doel](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Aanbevelingen voor Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Voer de volgende stappen uit om uw omgeving te beoordelen met de evaluatie van de database migratie: 

1. Open de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selecteer **bestand** en kies vervolgens **nieuwe beoordeling**. 
1. Geef een project naam op, selecteer SQL Server als bron server type en selecteer vervolgens Azure SQL Managed instance als doel server type. 
1. Selecteer de type (n) van de evaluatie rapporten die u wilt genereren. Bijvoorbeeld database compatibiliteit en functie pariteit. Op basis van het type evaluatie kunnen de vereiste machtigingen op de bron SQL Server verschillen.  DMA markeert de machtigingen die vereist zijn voor de gekozen Advisor voordat de evaluatie wordt uitgevoerd.
    - De categorie **functie pariteit** biedt een uitgebreide set aanbevelingen, alternatieven die beschikbaar zijn in Azure en verkleint de stappen om u te helpen bij het plannen van uw migratie project. (sysadmin-machtigingen vereist)
    - De categorie **compatibiliteits problemen** bevat gedeeltelijk ondersteunde of niet-ondersteunde functie compatibiliteits problemen die mogelijk de migratie blok keren, evenals aanbevelingen voor het oplossen van deze ( `CONNECT SQL` , `VIEW SERVER STATE` en de `VIEW ANY DEFINITION` vereiste machtigingen).
1. Geef de gegevens van de bron verbinding voor uw SQL Server op en maak verbinding met de bron database.
1. Selecteer **evaluatie starten**. 
1. Wanneer het proces is voltooid, selecteert en bekijkt u de evaluatie rapporten voor migratie blok keren en pariteits problemen met functies. Het beoordelings rapport kan ook worden geëxporteerd naar een bestand dat kan worden gedeeld met andere teams of mede werkers in uw organisatie. 
1. Bepaal het compatibiliteits niveau van de data base dat na de migratie wordt geminimaliseerd.  
1. Bepaal het beste Azure SQL Managed instance SKU voor uw on-premises werk belasting. 

Zie [een SQL Server migratie-evaluatie uitvoeren met Data Migration Assistant](/sql/dma/dma-assesssqlonprem)voor meer informatie.

Als het beheerde exemplaar van SQL geen geschikt doel is voor uw werk belasting, is het mogelijk dat SQL Server op virtuele machines van Azure een levensvatbaar alternatief doel is voor uw bedrijf. 

#### <a name="scaled-assessments-and-analysis"></a>Geschaalde evaluaties en analyse

Data Migration Assistant biedt ondersteuning voor het uitvoeren van geschaalde evaluaties en consolidatie van de evaluatie rapporten voor analyse. Als u meerdere servers en data bases hebt die op schaal moeten worden beoordeeld en geanalyseerd om een breder overzicht van de gegevens te krijgen, klikt u op de volgende koppelingen voor meer informatie.

- [Geschaalde evaluaties uitvoeren met Power shell](/sql/dma/dma-consolidatereports)
- [Analyse rapporten analyseren met behulp van Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Het uitvoeren van analyses op schaal voor meerdere data bases kan ook worden geautomatiseerd met behulp [van het opdracht regel hulpprogramma van DMA](/sql/dma/dma-commandline) , waarmee de resultaten ook kunnen worden geüpload naar [Azure migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) voor verdere analyse en doel gereedheid.

### <a name="create-a-performance-baseline"></a>Een basis lijn voor prestaties maken

Als u de prestaties van uw werk belasting wilt vergelijken met de oorspronkelijke werk belasting die wordt uitgevoerd op SQL Server, maakt u een basis lijn voor prestaties die u kunt gebruiken om te vergelijken. Bekijk de prestaties van de [basis lijn](sql-server-to-managed-instance-performance-baseline.md) voor meer informatie. 

### <a name="create-sql-managed-instance"></a>Door SQL beheerd exemplaar maken 

Maak op basis van de informatie in de fase detecteren en beoordelen een passend, met een doel gericht SQL beheerd exemplaar. U kunt dit doen met behulp van de sjabloon [Azure Portal](../../managed-instance/instance-create-quickstart.md), [Power shell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)of een [Azure Resource Manager (arm)](../../managed-instance/create-template-quickstart.md). 


## <a name="migrate"></a>Migrate

Nadat u taken hebt voltooid die zijn gekoppeld aan de fase voorafgaand aan de migratie, bent u klaar om het schema en de gegevens migratie uit te voeren. 

Migreer uw gegevens met de gekozen [migratie methode](sql-server-to-managed-instance-overview.md#compare-migration-options). 

In deze hand leiding worden de twee populairste opties-Azure Database Migration Service (DMS) en systeem eigen back-up en herstel beschreven. 

### <a name="database-migration-service"></a>Database Migration Service

Volg de onderstaande stappen om migraties uit te voeren met behulp van DMS:

1. Registreer de resource provider **micro soft. DataMigration** in uw abonnement als u dit voor de eerste keer uitvoert.
1. Maak een Azure Database Migration Service-exemplaar op een gewenste locatie van uw keuze (bij voor keur in dezelfde regio als uw doel exemplaar van Azure SQL Managed instance) en selecteer een bestaand virtueel netwerk of maak een nieuw abonnement om uw DMS-exemplaar te hosten.
1. Nadat u uw DMS-exemplaar hebt gemaakt, maakt u een nieuw migratie project en geeft u het type bron server op als **SQL Server** en het type doel server als **Azure SQL database beheerd exemplaar**. Kies het type activiteit in de Blade voor het maken van een project-online-of offline gegevens migratie. 
1.  Geef de bron SQL Server Details op de pagina gegevens van de **migratie bron** en de details van het doel exemplaar van Azure SQL Managed instance op de pagina doel gegevens van de **migratie** . Selecteer **Next**.
1. Kies de data base die u wilt migreren. 
1. Geef configuratie-instellingen op om de **SMB-netwerk share** op te geven die de back-upbestanden van uw Data Base bevat. Gebruik Windows-gebruikers referenties met DMS die toegang hebben tot de netwerk share. Geef de **gegevens** van uw Azure Storage-account op. 
1. Controleer het migratie overzicht en kies **migratie uitvoeren**. U kunt vervolgens de migratie activiteit controleren en de voortgang van de database migratie controleren.
1. Nadat de data base is hersteld, kiest u **Cutover starten**. Tijdens het migratie proces wordt de back-up van het einde van het logboek gekopieerd zodra u deze beschikbaar maakt in de SMB-netwerk share en deze terugzet op het doel. 
1. Stop alle binnenkomend verkeer naar de bron database en werk de connection string bij naar de nieuwe data base van Azure SQL Managed instance. 

Zie voor een gedetailleerde stapsgewijze zelf studie van deze migratie optie [SQL Server migreren naar een Azure SQL Managed instance online met behulp van DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Back-ups en herstellen 

Een van de belangrijkste mogelijkheden van een Azure SQL Managed instance om snelle en eenvoudige database migratie mogelijk te maken is de systeem eigen terugzet bewerking van back-upbestanden van data bases () die zijn `.bak` opgeslagen op op [Azure Storage](https://azure.microsoft.com/services/storage/). Backup en Restore is een asynchrone bewerking op basis van de grootte van uw data base. 

In het volgende diagram ziet u een overzicht van het proces:

![Diagram toont SQL Server met een pijl met de naam BACKUP/upload naar URL stromen naar Azure Storage en een tweede pijl met het label herstellen van URL die van Azure Storage naar een beheerd exemplaar van SQL wordt genoemd.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> De tijd voor het maken van de back-up, het uploaden ervan naar Azure Storage en het uitvoeren van een systeem eigen terugzet bewerking naar een Azure SQL Managed instance is gebaseerd op de grootte van de data base. Factor een voldoende downtime om de werking voor grote data bases te kunnen bieden. 


Voer de volgende stappen uit om te migreren met back-up en herstel: 

1. Maak een back-up van uw Data Base naar Azure Blob-opslag. Gebruik bijvoorbeeld [back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Gebruik het [hulp programma Microsoft Azure](https://go.microsoft.com/fwlink/?LinkID=324399) om data bases te ondersteunen die ouder zijn dan SQL Server 2012 SP1 Cu2. 
1. Maak verbinding met uw met Azure SQL beheerd exemplaar met behulp van SQL Server Management Studio. 
1. Een referentie maken met behulp van een Shared Access Signature voor toegang tot uw Azure Blob Storage-account met uw database back-ups. Bijvoorbeeld:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Herstel de back-up vanuit de Azure Storage-BLOB-container. Bijvoorbeeld: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Nadat de herstel bewerking is voltooid, bekijkt u de data base in **objectverkenner** in SQL Server Management Studio. 

Zie [een Data Base herstellen naar Azure SQL Managed instance met SSMS](../../managed-instance/restore-sample-database-quickstart.md)voor meer informatie over deze migratie optie.

> [!NOTE]
> Een terugzetbewerking voor de database is asynchroon en kan opnieuw worden uitgevoerd. Mogelijk wordt er een fout weergegeven in SQL Server Management Studio als de verbinding wordt verbroken of als er een time-out optreedt. Op de achtergrond wordt blijvend geprobeerd om de database terug te zetten. U kunt de voortgang hiervan volgen in de weergaven [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) en [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).



## <a name="data-sync-and-cutover"></a>Gegevens synchronisatie en cutover

Bij het gebruik van migratie opties die voortdurend wijzigingen repliceren/synchroniseren van bron naar het doel, kunnen de bron gegevens en het schema van het doel veranderen. Zorg er tijdens de gegevens synchronisatie voor dat alle wijzigingen in de bron tijdens het migratie proces worden vastgelegd en toegepast op het doel. 

Nadat u hebt gecontroleerd of de gegevens op de bron en het doel hetzelfde zijn, kunt u cutover van de bron naar de doel omgeving. Het is belang rijk om het cutover-proces met Business/Application teams te plannen om ervoor te zorgen dat een minimale onderbreking tijdens cutover geen invloed heeft op de bedrijfs continuïteit. 

> [!IMPORTANT]
> Zie voor meer informatie over de specifieke stappen die zijn gekoppeld aan het uitvoeren van een cutover als onderdeel van de migraties met behulp van DMS, [migratie Cutover uitvoeren](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


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

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies die beschikbaar worden gesteld door SQL Managed instance, zoals [ingebouwde hoge Beschik baarheid](../../database/high-availability-sla.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md)en [het bewaken en afstemmen van uw werk belasting](../../database/monitor-tune-overview.md). 

Met [Azure SQL-analyse](../../../azure-monitor/insights/azure-sql.md) kunt u een groot aantal beheerde exemplaren op een gecentraliseerde manier bewaken.

Sommige SQL Server-functies zijn alleen beschikbaar als het [compatibiliteits niveau van de data base](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) is gewijzigd in het meest recente compatibiliteits niveau (150). 


## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL Managed instance:
   - [Service lagen in Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Verschillen tussen SQL Server en Azure SQL Managed instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.