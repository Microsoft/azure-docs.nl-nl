---
title: 'Oracle to Azure SQL Database: Migratiehandleiding'
description: In deze handleiding leert u hoe u uw Oracle-schema migreert naar Azure SQL Database met behulp van SQL Server Migration Assistant voor Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 45fbc1f85c5d7f66716fbf69deb430ce74575435
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388479"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Migratiehandleiding: Oracle to Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze handleiding leert u hoe [u](https://azure.microsoft.com/migration/migration-journey) uw Oracle-schema's migreert naar Azure SQL Database met behulp van [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant voor Oracle (SSMA voor Oracle).

Zie Azure Database [Migration Guides (Handleidingen voor Azure Database Migration) voor andere migratiehandleidingen.](https://docs.microsoft.com/data-migration)

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het migreren van uw Oracle-schema naar SQL Database:

- Controleer of uw bronomgeving wordt ondersteund.
- Download [SSMA voor Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
- Een [doel-SQL Database](../../database/single-database-create-quickstart.md) maken.
- Verkrijg de [benodigde machtigingen voor SSMA voor Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) en [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de haalbaarheid van uw [Azure-cloudmigratie te evalueren.](https://azure.microsoft.com/migration) Dit deel van het proces omvat het uitvoeren van een inventarisatie van de databases die u moet migreren, het beoordelen van deze databases op mogelijke migratieproblemen of -blokkeringen en het oplossen van items die u mogelijk hebt ontdekt.

### <a name="assess"></a>Evalueren

Met behulp van SSMA voor Oracle kunt u databaseobjecten en -gegevens controleren, databases evalueren voor migratie, databaseobjecten migreren naar SQL Database en ten slotte gegevens migreren naar de database.

Een evaluatie maken:

1. Open [SSMA voor Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Selecteer **Bestand** en selecteer vervolgens **Nieuw project**.
1. Voer een projectnaam en een locatie in om uw project op te slaan. Selecteer vervolgens **Azure SQL Database** migratiedoel in de vervolgkeuzelijst en selecteer **OK.**

   ![Schermopname van Verbinding maken met Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Selecteer **Verbinding maken met Oracle**. Voer waarden in voor Oracle-verbindingsgegevens in het dialoogvenster Verbinding maken met **Oracle.**

1. Selecteer de Oracle-schema's die u wilt migreren.

   ![Schermopname van het selecteren van het Oracle-schema.](./media/oracle-to-sql-database-guide/select-schema.png)

1. Klik **in Oracle Metadata Explorer** met de rechtermuisknop op het Oracle-schema dat u wilt migreren en selecteer vervolgens Rapport **maken** om een HTML-rapport te genereren. U kunt ook een database selecteren en vervolgens het tabblad **Rapport** maken selecteren.

   ![Schermopname van Rapport maken.](./media/oracle-to-sql-database-guide/create-report.png)

1. Bekijk het HTML-rapport om inzicht te krijgen in conversiestatistieken en eventuele fouten of waarschuwingen. U kunt het rapport ook openen in Excel om een inventaris op te halen van Oracle-objecten en de inspanning die nodig is om schemaconversies uit te voeren. De standaardlocatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Zie bijvoorbeeld `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Schermopname van een evaluatierapport.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>De gegevenstypen valideren

Valideer de standaardtoewijzingen voor gegevenstype en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit:

1. Selecteer in SSMA voor Oracle **Extra** en selecteer vervolgens **Projectinstellingen.**
1. Selecteer het **tabblad Typetoewijzing.**

   ![Schermopname van Typetoewijzing.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. U kunt de typetoewijzing voor elke tabel wijzigen door de tabel in **Oracle Metadata Explorer te selecteren.**

### <a name="convert-the-schema"></a>Het schema converteren

Het schema converteren:

1. (Optioneel) Dynamische of ad-hocquery's toevoegen aan -instructies. Klik met de rechtermuisknop op het knooppunt en selecteer vervolgens **Instructies toevoegen.**
1. Selecteer het **tabblad Verbinding Azure SQL Database** maken.
    1. Voer **SQL Database** verbindingsgegevens in om verbinding te maken met uw database.
    1. Selecteer uw doel-SQL Database-exemplaar in de vervolgkeuzelijst of voer een nieuwe naam in. In dat geval wordt er een database gemaakt op de doelserver.
    1. Voer verificatiegegevens in en selecteer **Verbinding maken.**

    ![Schermopname van Verbinding maken met Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. Klik **in Oracle Metadata Explorer** met de rechtermuisknop op het Oracle-schema en selecteer schema **converteren.** U kunt ook uw schema selecteren en vervolgens het tabblad **Schema converteren** selecteren.

   ![Schermopname van Schema converteren.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Nadat de conversie is voltooien, vergelijkt en beoordeelt u de geconverteerde objecten naar de oorspronkelijke objecten om potentiële problemen te identificeren en deze op basis van de aanbevelingen op te lossen.

   ![Schermopname van het schema Aanbevelingen beoordelen.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke opgeslagen procedures en bekijk de aanbevelingen.

   ![Schermopname met de aanbevelingen controleren.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Selecteer in het uitvoervenster Resultaten **controleren en** bekijk de fouten in het **deelvenster Foutenlijst.**
1. Sla het project lokaal op voor een offline schema-hersteloefening. Selecteer **In** het menu Bestand de optie **Project opslaan.** Deze stap biedt u de mogelijkheid om de bron- en doelschema's offline te evalueren en herstel uit te voeren voordat u het schema publiceert naar SQL Database.

## <a name="migrate"></a>Migrate

Nadat u uw databases hebt beoordeeld en eventuele verschillen hebt aangepakt, bestaat de volgende stap uit het uitvoeren van het migratieproces. Migratie omvat twee stappen: het schema publiceren en de gegevens migreren.

Uw schema publiceren en uw gegevens migreren:

1. Publiceer het schema door met de rechtermuisknop op de database te klikken vanuit het knooppunt **Databases** in **Azure SQL Database Metadata Explorer** en Synchroniseren met database te **selecteren.**

   ![Schermopname van Synchroniseren met database.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Controleer de toewijzing tussen uw bronproject en uw doel.

   ![Schermopname van Synchroniseren met de databasebeoordeling.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Migreert de gegevens door met de rechtermuisknop te klikken op de database of het object dat u wilt migreren in **Oracle Metadata Explorer** en Gegevens migreren te **selecteren.** U kunt ook het tabblad **Gegevens migreren** selecteren. Als u gegevens voor een hele database wilt migreren, selecteert u het selectievakje naast de naam van de database. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de database uit, vouwt u **Tabellen** uit en selecteert u vervolgens de selectievakjes naast de tabellen. Als u gegevens uit afzonderlijke tabellen wilt weglaten, moet u de selectievakjes uit.

   ![Schermopname van Gegevens migreren.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Voer verbindingsgegevens in voor oracle en SQL Database.
1. Nadat de migratie is voltooid, bekijkt u **het Gegevensmigratierapport.**

   ![Schermopname van het gegevensmigratierapport.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Maak verbinding met SQL Database-exemplaar met behulp [van SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)en valideer de migratie door de gegevens en het schema te controleren.

   ![Schermopname van validatie in SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

U kunt ook een SQL Server Integration Services om de migratie uit te voeren. Raadpleeg voor meer informatie:

- [Aan de slag met SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services voor Azure en hybride gegevens movement](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Postmigratie

Nadat u de migratiefase hebt voltooid, moet u een reeks taken na de migratie uitvoeren om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt. 

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens naar de doelomgeving zijn gemigreerd, moeten alle toepassingen die voorheen de bron hebben gebruikt, het doel gaan gebruiken. Voor het uitvoeren van deze taak zijn in sommige gevallen wijzigingen in de toepassingen vereist.

De [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) is een extensie voor Visual Studio Code waarmee u uw Java-broncode kunt analyseren en API-aanroepen en query's voor gegevenstoegang kunt detecteren. De toolkit biedt u een weergave met één deelvenster van wat er moet worden aangepakt om de nieuwe databaseback-end te ondersteunen. Zie de blogpost [Migrate your Java applications from Oracle (Uw Java-toepassingen migreren vanuit Oracle) voor meer](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) informatie.

### <a name="perform-tests"></a>Tests uitvoeren

De testbenadering voor databasemigratie bestaat uit de volgende activiteiten:

1. **Validatietests ontwikkelen:** als u de databasemigratie wilt testen, moet u SQL-query's gebruiken. U moet de validatiequery's maken om uit te voeren op zowel de bron- als de doeldatabase. Uw validatiequery's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **Een testomgeving instellen:** de testomgeving moet een kopie van de brondatabase en de doeldatabase bevatten. Zorg ervoor dat u de testomgeving isoleert.
1. **Validatietests uitvoeren:** voer validatietests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Prestatietests uitvoeren:** voer prestatietests uit op de bron en het doel, en analyseer en vergelijk de resultaten.

### <a name="optimize"></a>Optimaliseren

De postmigratiefase is van cruciaal belang voor het afstemmen van eventuele problemen met gegevensnauwkeurigheid, het controleren van de volledigheid en het oplossen van prestatieproblemen met de workload.

> [!NOTE]
> Zie de postmigratievalidatie- en optimalisatiehandleiding voor meer informatie over deze problemen en de stappen om deze [te verhelpen.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migratie-assets

Zie de volgende bronnen voor meer hulp bij het voltooien van dit migratiescenario. Ze zijn ontwikkeld ter ondersteuning van een echte migratieprojectbetrokkenheid.

| **Titel/koppeling**                                                                                                                                          | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model en hulpprogramma voor evaluatie van gegevensworkloads](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulpprogramma biedt voorgestelde 'best fit' doelplatforms, cloud-gereedheid en herstelniveau van toepassingen of databases voor een bepaalde workload. Het biedt eenvoudige berekening met één klik en het genereren van een rapport waarmee u grote evaluaties van onroerend goed kunt versnellen door een geautomatiseerd en uniform platformbesluitproces te bieden.                                                          |
| [Oracle Inventory Script Artifacts](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Deze asset bevat een PL/SQL-query die oracle-systeemtabellen raakt en een telling van objecten biedt op schematype, objecttype en status. Het biedt ook een ruwe schatting van onbewerkte gegevens in elk schema en de formaat van tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.                                                                                                               |
| [SSMA Oracle Assessment Collection automatiseren & consolidatie](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Deze set resources gebruikt een CSV-bestand als vermelding (sources.csv in de projectmappen) om de XML-bestanden te produceren die nodig zijn om een SSMA-evaluatie uit te voeren in de consolemodus. De source.csv wordt geleverd door de klant op basis van een inventaris van bestaande Oracle-exemplaren. De uitvoerbestanden zijn AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml en VariableValueFile.xml.|
| [SSMA for Oracle Common Errors and How to Fix Them](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Met Oracle kunt u een niet-schalende voorwaarde toewijzen in de WHERE-component. Dit type SQL Server wordt echter niet ondersteund. Als gevolg hiervan converteert SSMA voor Oracle geen query's met een niet-schalende voorwaarde in de WHERE-component. In plaats daarvan wordt de fout O2SS0001 gegenereerd. Dit technische document bevat meer informatie over het probleem en manieren om dit op te lossen.          |
| [Oracle to SQL Server Migration Handbook](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Dit document is gericht op de taken die zijn gekoppeld aan het migreren van een Oracle-schema naar de nieuwste versie van SQL Server Database. Als voor de migratie wijzigingen in functies of functionaliteit zijn vereist, moet de mogelijke impact van elke wijziging op de toepassingen die gebruikmaken van de database zorgvuldig worden overwogen.                                                     |

Het Data SQL Engineering-team heeft deze resources ontwikkeld. De kern van dit team is het deblokkeren en versnellen van complexe modernisering voor gegevensplatformmigratieprojecten naar het Azure-gegevensplatform van Microsoft.

## <a name="next-steps"></a>Volgende stappen

- Zie Services en hulpprogramma's voor gegevensmigratie voor een matrix met services en hulpprogramma's van Microsoft en derden die u kunnen helpen bij verschillende database- en gegevensmigratiescenario's en speciale [taken.](../../../dms/dms-tools-matrix.md)

- Voor meer informatie over SQL Database, zie:
  - [Een overzicht van Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [TCO-calculator (Total Cost of Ownership) van Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Zie voor meer informatie over het framework en de acceptatiecyclus voor cloudmigraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor het kosten en het formaat van workloads voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Cloudmigratie resources](https://azure.microsoft.com/migration/resources)

- Zie voor video-inhoud:
    - [Overzicht van het migratietraject en de hulpprogramma's en services die worden aanbevolen voor het uitvoeren van evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
