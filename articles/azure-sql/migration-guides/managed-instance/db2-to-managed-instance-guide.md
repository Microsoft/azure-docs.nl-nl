---
title: 'DB2 naar SQL Managed instance: migratie handleiding'
description: Volg deze hand leiding voor het migreren van uw DB2-data bases naar Azure SQL Managed instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 3902661553bbb390e6e999e0f67cd043eee8df09
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359180"
---
# <a name="migration-guide-db2-to-sql-managed-instance"></a>Migratie handleiding: DB2 naar SQL-beheerd exemplaar
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

In deze hand leiding leert u hoe u uw DB2-data bases naar Azure SQL Managed Instance kunt migreren met behulp van de SQL Server Migration Assistant voor DB2. 

Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere scenario's.

## <a name="prerequisites"></a>Vereisten 

U hebt het volgende nodig om de DB2-Data Base te migreren naar een SQL-beheerd exemplaar:

- u kunt controleren of uw bron omgeving wordt ondersteund.
- voor het downloaden [van SQL Server Migration Assistant (SSMA) voor DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- een [Azure SQL Managed](../../database/single-database-create-quickstart.md)-doel exemplaar.


## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. 

### <a name="assess-and-convert"></a>Beoordelen en converteren

Een evaluatie maken met behulp van SQL Server Migration Assistant (SSMA). 

Voer de volgende stappen uit om een evaluatie te maken:

1. Open SQL Server Migration Assistant (SSMA) voor DB2. 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam, een locatie op voor het opslaan van uw project en selecteer vervolgens Azure SQL Managed instance als migratie doel uit de vervolg keuzelijst. Selecteer **OK**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Geef project gegevens op en selecteer OK om op te slaan.":::


1. Voer in het dialoog venster **verbinding maken met DB2** waarden in voor de gegevens van de DB2-verbinding. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Verbinding maken met uw DB2-exemplaar":::


1. Klik met de rechter muisknop op het DB2-schema dat u wilt migreren en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u het schema hebt geselecteerd. 

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Klik met de rechter muisknop op het schema en kies rapport maken.":::

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een inventaris van de DB2-objecten te verkrijgen en de vereiste inspanning om schema conversies uit te voeren. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Bekijk het rapport om eventuele fouten of waarschuwingen te identificeren":::


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit: 

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** . 

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Het schema selecteren en vervolgens type toewijzing":::

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **DB2-meta gegevens Verkenner**. 

### <a name="schema-conversion"></a>Schema conversie 

Voer de volgende stappen uit om het schema te converteren:

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en kies vervolgens **instructies toevoegen**. 
1. Selecteer **verbinding maken met Azure SQL database**. 
    1. Voer de verbindings gegevens in om verbinding te maken met uw Azure SQL Managed instance.  
    1. Kies uw doel database in de vervolg keuzelijst. 
    1. Selecteer **Verbinding maken**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Vul de details in om verbinding te maken met SQL Server":::


1. Klik met de rechter muisknop op het schema en kies vervolgens **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u het schema hebt geselecteerd. 

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Klik met de rechter muisknop op het schema en kies schema converteren":::

1. Nadat de conversie is voltooid, vergelijkt en controleert u de structuur van het schema om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Vergelijk en controleer de structuur van het schema om potentiële problemen te identificeren en op te lossen op basis van aanbevelingen.":::

1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . 


## <a name="migrate"></a>Migreren

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces.

Als u uw schema wilt publiceren en uw gegevens wilt migreren, voert u de volgende stappen uit:

1. Het schema publiceren: Klik met de rechter muisknop op de data base in het knoop punt **data bases** in de **meta gegevens Verkenner van het Azure SQL Managed instance** en kies **synchroniseren met data base**.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Klik met de rechter muisknop op de data base en kies synchroniseren met data base":::

1. De gegevens migreren: Klik met de rechter muisknop op het schema in de **DB2-meta gegevens Verkenner** en kies **gegevens migreren**. 

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Klik met de rechter muisknop op het schema en kies gegevens migreren.":::

1. Geef verbindings Details op voor zowel het DB2-exemplaar als het SQL Managed instance. 
1. Het **gegevens migratie rapport** weer geven. 

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Het gegevens migratie rapport controleren":::

1. Maak verbinding met een SQL-beheerd exemplaar met behulp van SQL Server Management Studio en valideer de migratie door de gegevens en het schema te bekijken. 

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Het schema in SSMS vergelijken":::

## <a name="post-migration"></a>Postmigratie 

Nadat u de migratie fase hebt voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen 

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.


### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **Test omgeving instellen**: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
1. **Validatie tests uitvoeren**: Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Prestatie testen uitvoeren**: prestaties testen op basis van de bron en het doel, en vervolgens de resultaten analyseren en vergelijken.

   > [!NOTE]
   > Voor hulp bij het ontwikkelen en uitvoeren van validatie tests na de migratie, moet u rekening houden met de oplossing voor gegevens kwaliteit die beschikbaar is via de partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 


## <a name="leverage-advanced-features"></a>Geavanceerde functies gebruiken 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies die worden geboden door Azure SQL Managed instance, zoals [ingebouwde hoge Beschik baarheid](../../database/high-availability-sla.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md)en [het bewaken en afstemmen van uw werk belasting](../../database/monitor-tune-overview.md). 


Sommige SQL Server-functies zijn alleen beschikbaar als het [compatibiliteits niveau van de data base](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) is gewijzigd in het meest recente compatibiliteits niveau (150). 

## <a name="migration-assets"></a>Migratie-assets 

Raadpleeg de volgende bronnen voor meer hulp, die zijn ontwikkeld ter ondersteuning van een werkelijke migratie project betrokkenheid:

|Asset  |Description  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote voor-en hand-evaluaties versnelt door het besluitvormings proces voor een geautomatiseerd en uniform doel platform te bieden.|
|[Detectie-en evaluatie pakket voor gegevens bronnen van DB2 zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Nadat u het SQL-script op een Data Base hebt uitgevoerd, kunt u de resultaten exporteren naar een bestand op het bestands systeem. Verschillende bestands indelingen worden ondersteund, met inbegrip van *. CSV, zodat u de resultaten kunt vastleggen in externe hulpprogram ma's zoals werk bladen. Deze methode kan nuttig zijn als u eenvoudig resultaten wilt delen met teams waarop de workbench niet is geïnstalleerd.|
|[IBM DB2 LUW inventaris scripts en artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Deze asset bevat een SQL-query die voldoet aan de IBM DB2 LUW-versie 11,1-systeem tabellen en biedt een telling van objecten per schema en object type, een ruwe schatting van ' onbewerkte gegevens ' in elk schema en de grootte van tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.|
|[DB2 LUW zuivere schaal op Azure-installatie handleiding](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Deze hand leiding fungeert als uitgangs punt voor een DB2-implementatie plan. Terwijl de bedrijfs vereisten verschillen, is hetzelfde basis patroon van toepassing. Dit architectuur patroon kan ook worden gebruikt voor OLAP-toepassingen op Azure.|

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.


## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL Managed instance:
   - [Een overzicht van SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.