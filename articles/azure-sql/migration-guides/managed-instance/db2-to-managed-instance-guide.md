---
title: 'Db2 naar Azure SQL Managed instance: migratie handleiding'
description: Deze hand leiding leert u uw IBM Db2-data bases te migreren naar Azure SQL Managed instance met behulp van SQL Server Migration Assistant voor Db2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 15d94e17bea6a49bb5bf6fdb6c48252365676042
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228160"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>Migratie handleiding: IBM Db2 naar Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Deze hand leiding leert u uw IBM Db2-data bases te migreren naar Azure SQL Managed instance met behulp van de SQL Server Migration Assistant voor Db2. 

Raadpleeg de [migratie handleidingen van Azure data base](https://docs.microsoft.com/data-migration)voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten 

U hebt het volgende nodig om de Db2-data base te migreren naar een SQL-beheerd exemplaar:

- Om te controleren of uw [bron omgeving wordt ondersteund](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Voor het downloaden [van SQL Server Migration Assistant (SSMA) voor Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- Een doel exemplaar van een door [Azure SQL beheerd exemplaar](../../managed-instance/instance-create-quickstart.md).
- Connectiviteit en voldoende machtigingen voor toegang tot zowel de bron als het doel. 

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. 

### <a name="assess-and-convert"></a>Beoordelen en converteren

Een evaluatie maken met behulp van SQL Server Migration Assistant. 

Voer de volgende stappen uit om een evaluatie te maken:

1. Open [SSMA voor Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Selecteer **bestand**  >  **Nieuw project**. 
1. Geef een project naam en een locatie op om uw project op te slaan. Selecteer vervolgens Azure SQL Managed instance als het migratie doel in de vervolg keuzelijst en selecteer **OK**.

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Scherm opname van de project details die moeten worden opgegeven.":::


1. Bij **verbinden met Db2 voert u** waarden in voor de gegevens van de Db2-verbinding. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Scherm afbeelding met opties om verbinding te maken met uw Db2-exemplaar.":::


1. Klik met de rechter muisknop op het Db2-schema dat u wilt migreren en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u het schema hebt geselecteerd.

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Scherm afbeelding die laat zien hoe u een rapport maakt.":::

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een inventaris van de Db2-objecten te verkrijgen en de vereiste inspanning om schema conversies uit te voeren. De standaard locatie voor het rapport bevindt zich in de rapportmap in *SSMAProjects*.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Scherm afbeelding van het rapport dat u bekijkt om eventuele fouten of waarschuwingen te identificeren":::


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit: 

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** .

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Scherm opname van het selecteren van het schema en de type toewijzing.":::

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **Db2-meta gegevens Verkenner**. 

### <a name="convert-schema"></a>Schema converteren 

Voer de volgende stappen uit om het schema te converteren:

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en kies vervolgens **instructies toevoegen**. 
1. Selecteer **verbinding maken met Azure SQL Managed instance**. 
    1. Voer de verbindings gegevens in om verbinding te maken met een door Azure SQL beheerd exemplaar. 
    1. Kies uw doel database in de vervolg keuzelijst of geef een nieuwe naam op. in dat geval wordt er een Data Base op de doel server gemaakt. 
    1. Geef verificatie Details op. 
    1. Selecteer **Verbinding maken**.

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Scherm afbeelding met de gegevens die nodig zijn om verbinding te maken met SQL Server.":::


1. Klik met de rechter muisknop op het schema en kies vervolgens **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u het schema hebt geselecteerd.

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Scherm afbeelding waarin het schema wordt geselecteerd en waarmee wordt geconverteerd.":::

1. Nadat de conversie is voltooid, kunt u de structuur van het schema vergelijken en bekijken om potentiële problemen te identificeren. Los de problemen op op basis van de aanbevelingen.

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Scherm opname van de weer gave van de structuur van het schema om mogelijke problemen te identificeren en te controleren.":::

1. Selecteer in het deel venster **uitvoer** de optie **resultaten bekijken**. Bekijk fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer in het menu **bestand** de optie **project opslaan**. Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema kunt publiceren naar een door SQL beheerd exemplaar.

## <a name="migrate"></a>Migrate

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces.

Als u uw schema wilt publiceren en uw gegevens wilt migreren, voert u de volgende stappen uit:

1. Publiceer het schema. Klik **met de rechter** muisknop op de data base in **Azure SQL Managed instance Meta Data Explorer**. Selecteer vervolgens **synchroniseren met data base**.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Scherm afbeelding met de optie om te synchroniseren met de data base.":::

1. De gegevens migreren. Klik met de rechter muisknop op de data base of het object dat u wilt migreren in de **Db2-meta gegevens Verkenner** en kies **gegevens migreren**. U kunt ook **gegevens migreren** selecteren in de navigatie balk. Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u **tabellen** uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit.

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Scherm opname van het selecteren van het schema en kiezen om gegevens te migreren.":::

1. Geef verbindings Details op voor zowel het Db2-exemplaar als het SQL Managed instance. 
1. Nadat de migratie is voltooid, bekijkt u het **rapport gegevens migratie**.  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Scherm afbeelding die laat zien waar het gegevens migratie rapport moet worden gecontroleerd.":::

1. Maak verbinding met uw exemplaar van Azure SQL Managed instance met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Valideer de migratie door de gegevens en het schema te bekijken:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Scherm afbeelding die het schema in SQL Server Management Studio vergelijkt.":::

## <a name="post-migration"></a>Postmigratie 

Nadat de migratie is voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen 

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.


### <a name="perform-tests"></a>Tests uitvoeren

Testen bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **Stel de test omgeving** in: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
1. **Validatie tests uitvoeren**: Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Voer prestatie tests uit**: Voer prestatie tests uit op basis van de bron en het doel en analyseer en vergelijk vervolgens de resultaten.

## <a name="advanced-features"></a>Geavanceerde functies 

Zorg ervoor dat u profiteren van de geavanceerde Cloud functies die worden geboden door Azure SQL Managed instance, zoals [ingebouwde hoge Beschik baarheid](../../database/high-availability-sla.md), [detectie van bedreigingen](../../database/azure-defender-for-sql.md)en [het bewaken en afstemmen van uw werk belasting](../../database/monitor-tune-overview.md). 

Sommige SQL Server-functies zijn alleen beschikbaar als het [compatibiliteits niveau van de data base](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) is gewijzigd in het meest recente compatibiliteits niveau. 

## <a name="migration-assets"></a>Migratie-assets 

Raadpleeg de volgende bronnen voor meer hulp, die zijn ontwikkeld ter ondersteuning van een werkelijke migratie project betrokkenheid:

|Asset  |Description  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote voor-en hand-evaluaties versnelt door het besluitvormings proces voor een geautomatiseerd en uniform doel platform te bieden.|
|[Detectie-en evaluatie pakket voor gegevens bronnen van Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Nadat u het SQL-script op een Data Base hebt uitgevoerd, kunt u de resultaten exporteren naar een bestand op het bestands systeem. Verschillende bestands indelingen worden ondersteund, met inbegrip van *. CSV, zodat u de resultaten kunt vastleggen in externe hulpprogram ma's zoals werk bladen. Deze methode kan nuttig zijn als u eenvoudig resultaten wilt delen met teams waarop de workbench niet is geïnstalleerd.|
|[IBM Db2 LUW inventaris scripts en artefacten](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Deze asset bevat een SQL-query die voldoet aan de IBM Db2 LUW-versie 11,1-systeem tabellen en biedt een telling van objecten per schema en object type, een ruwe schatting van ' onbewerkte gegevens ' in elk schema en de grootte van tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.|
|[Db2 LUW zuivere schaal op Azure-installatie handleiding](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Deze hand leiding fungeert als uitgangs punt voor een Db2-implementatie plan. Hoewel de bedrijfs vereisten verschillen, is hetzelfde basis patroon van toepassing. Dit architectuur patroon kan ook worden gebruikt voor OLAP-toepassingen op Azure.|

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.

## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor meer informatie over services en hulpprogram Ma's van micro soft en van derden om u te helpen bij het gebruik van verschillende scenario's voor data migratie.

- Zie voor meer informatie over Azure SQL Managed instance:
   - [Een overzicht van SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Azure total cost of ownership Calculator](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor het bepalen van de kosten en grootte van workloads die naar Azure zijn gemigreerd](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)om de Access-laag van de toepassing te beoordelen.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data-laag.
