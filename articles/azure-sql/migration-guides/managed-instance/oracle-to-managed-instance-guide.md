---
title: 'Oracle naar Azure SQL Managed instance: migratie handleiding'
description: In deze hand leiding leert u hoe u uw Oracle-schema's naar Azure SQL Managed Instance kunt migreren met behulp van SQL Server Migration Assistant voor Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 1c2fbc90d3956ab831e6d9fac4e1e2d3540e1c6d
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025421"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Migratie handleiding: Oracle naar Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

In deze hand leiding leert u hoe u uw Oracle-schema's naar Azure SQL Managed Instance kunt migreren met behulp van SQL Server Migration Assistant voor Oracle. 

Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten

Als u uw Oracle-schema wilt migreren naar een beheerd exemplaar van SQL, hebt u het volgende nodig: 

- U kunt controleren of uw bron omgeving wordt ondersteund. 
- Voor het downloaden [van SQL Server Migration Assistant (SSMA) voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Een [Azure SQL Managed](../../managed-instance/instance-create-quickstart.md)-doel exemplaar. 
- De [benodigde machtigingen voor SSMA voor Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) en [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. Dit onderdeel van het proces bestaat uit het uitvoeren van een inventarisatie van de data bases die u moet migreren, het beoordelen van die data bases voor mogelijke migratie problemen of blok keringen en het oplossen van items die u mogelijk hebt gedetecteerd.



### <a name="assess"></a>Evalueren 

Gebruik de SQL Server Migration Assistant (SSMA) voor Oracle om database objecten en-gegevens te bekijken, data bases te beoordelen voor migratie, database objecten te migreren naar Azure SQL Managed instance en vervolgens gegevens naar de data base te migreren. 

Voer de volgende stappen uit om een evaluatie te maken: 


1. Open [SQL Server Migration Assistant voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam, een locatie op voor het opslaan van uw project en selecteer vervolgens Azure SQL Managed instance als migratie doel uit de vervolg keuzelijst. Selecteer **OK**:

   ![Nieuw project](./media/oracle-to-managed-instance-guide/new-project.png)

1. Selecteer **verbinding maken met Oracle**. Voer in het dialoog venster **verbinding maken met Oracle** de waarden in voor de verbindings Details van Oracle:

   ![Verbinding maken met Oracle](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

   Selecteer de Oracle-schema (s) die u wilt migreren: 

   ![Oracle-schema kiezen](./media/oracle-to-managed-instance-guide/select-schema.png)

1. Klik met de rechter muisknop op het Oracle-schema dat u wilt migreren in de **Oracle-meta gegevens Verkenner**, en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u de Data Base hebt geselecteerd:

   ![Rapport maken](./media/oracle-to-managed-instance-guide/create-report.png)

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een overzicht te krijgen van Oracle-objecten en de inspanningen die nodig zijn voor het uitvoeren van schema-conversies. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Beoordelings rapport](./media/oracle-to-managed-instance-guide/assessment-report.png)


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit:

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** :

   ![Type toewijzingen](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **Oracle-meta gegevens Verkenner**.

### <a name="convert-schema"></a>Schema converteren

Voer de volgende stappen uit om het schema te converteren: 

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en kies vervolgens **instructies toevoegen**.
1. Selecteer **verbinding maken met Azure SQL Managed instance**. 
    1. Voer de verbindings gegevens in om uw data base te verbinden in Azure SQL Managed instance.
    1. Kies uw doel database in de vervolg keuzelijst of geef een nieuwe naam op. in dat geval wordt er een Data Base op de doel server gemaakt. 
    1. Geef verificatie Details op. 
    1. Selecteer **verbinding maken**:

    ![Verbinding maken met beheerd SQL-exemplaar](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. Klik met de rechter muisknop op het Oracle-schema in de **Oracle-meta gegevens Verkenner** en kies vervolgens **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u het schema hebt geselecteerd:

   ![Schema converteren](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Nadat de conversie is voltooid, vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen:

   ![Tabel aanbevelingen vergelijken](./media/oracle-to-managed-instance-guide/table-comparison.png)

   Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en Bekijk de aanbevelingen: 

   ![Aanbevelingen voor de procedure vergelijken](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Selecteer **resultaten controleren** in het deel venster uitvoer en Bekijk fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema kunt publiceren naar een door SQL beheerd exemplaar.

## <a name="migrate"></a>Migrate

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces. Migratie omvat twee stappen: het schema publiceren en de gegevens migreren. 

Als u uw schema wilt publiceren en uw gegevens wilt migreren, voert u de volgende stappen uit:

1. Het schema publiceren: Klik met de rechter muisknop op het schema dat of het object dat u wilt migreren in **Oracle Meta Data Explorer**, en kies **gegevens migreren**. U kunt ook **gegevens migreren** selecteren in de bovenste navigatie balk. Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u tabellen uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit:

   ![Synchroniseren met data base](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)

   Controleer de toewijzing tussen uw bron project en uw doel:

   ![Synchroniseren met database revisie](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. De gegevens migreren: Klik met de rechter muisknop op het schema in de **Oracle-meta gegevens Verkenner** en kies **gegevens migreren**. 

   ![Gegevens migreren](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Geef verbindings Details op voor zowel Oracle als Azure SQL Managed instance.
1. Nadat de migratie is voltooid, raadpleegt u het **rapport gegevens migratie**:  

   ![Gegevens migratie rapport](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Maak verbinding met uw Azure SQL Managed instance met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren:

   ![Valideren in SSMA](./media/oracle-to-managed-instance-guide/validate-data.png)


U kunt ook SQL Server Integration Services (SSIS) gebruiken om de migratie uit te voeren. Raadpleeg voor meer informatie: 

- [Aan de slag met SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: SSIS voor Azure en hybride gegevens verplaatsing](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

    


## <a name="post-migration"></a>Postmigratie 

Nadat u de **migratie** fase hebt voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.

De [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) is een uitbrei ding voor Visual Studio code waarmee u uw Java-bron code kunt analyseren en Data Access-API-aanroepen en-query's kan detecteren, zodat u een weer gave met één deel venster krijgt van wat u moet verhelpen voor de back-end van de nieuwe data base. Zie voor meer informatie het onderwerp [onze Java-toepassing migreren vanuit Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blog. 

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit het uitvoeren van de volgende activiteiten:

1.  **Validatie tests ontwikkelen**. Als u de database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

2.  **Test omgeving instellen**. De test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.

3.  **Validatie tests uitvoeren**. Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.

4.  **Prestatie tests uitvoeren**. Voer prestatie tests uit op basis van de bron en het doel, en analyseer en vergelijk vervolgens de resultaten.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met de nauw keurigheid van gegevens en het controleren van de volledigheid, en het oplossen van prestatie problemen met de werk belasting.

> [!NOTE]
> Zie voor meer informatie over deze problemen en specifieke stappen om ze te beperken de [hand leiding voor validatie na de migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Migratie-assets 

Voor aanvullende hulp bij het volt ooien van dit migratie scenario raadpleegt u de volgende bronnen, die zijn ontwikkeld ter ondersteuning van een echt toonaangevend migratie project.

| **Titel/koppeling**                                                                                                                                          | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote en kleine beoordelingen aanzienlijk sneller door middel van en geautomatiseerd en uniform platform besluitvormings proces.                                                          |
| [Oracle Inventory script artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Deze asset bevat een PL/SQL-query die Oracle-systeem tabellen oplevert en een telling van objecten biedt per schema type, object type en status. Het biedt ook een ruwe schatting van ' onbewerkte gegevens ' in elk schema en de grootte van de tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.                                                                                                               |
| [SSMA Oracle Assessment verzameling automatiseren & consolidatie](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Deze resourceset gebruikt een CSV-bestand als vermelding (sources.csv in de project mappen) om de XML-bestanden te maken die nodig zijn voor het uitvoeren van de SSMA-evaluatie in de console modus. De source.csv wordt door de klant verschaft op basis van een inventaris van bestaande Oracle-exemplaren. De uitvoer bestanden zijn AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml en VariableValueFile.xml.|
| [SSMA voor veelvoorkomende fouten in Oracle en hoe u deze kunt oplossen](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Met Oracle kunt u een niet-scalaire voor waarde toewijzen in de component WHERE. SQL Server biedt echter geen ondersteuning voor dit type voor waarde. Als gevolg hiervan wordt met SQL Server Migration Assistant (SSMA) voor Oracle geen query's geconverteerd met een niet-scalaire voor waarde in de WHERE-component, in plaats daarvan een fout O2SS0001 te genereren. Dit technisch document bevat meer informatie over het probleem en manieren om dit op te lossen.          |
| [SQL Server migratie handleiding voor Oracle](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Dit document is gericht op de taken die zijn gekoppeld aan het migreren van een Oracle-schema naar de meest recente versie van SQL Server base. Als voor de migratie wijzigingen in functies/functionaliteit zijn vereist, moet de mogelijke gevolgen van elke wijziging in de toepassingen die gebruikmaken van de data base, zorgvuldig worden overwogen.                                                     |

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.

## <a name="next-steps"></a>Volgende stappen

- Zie de artikel [service en hulpprogram ma's voor gegevens migratie](https://docs.microsoft.com/azure/dms/dms-tools-matrix)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL Managed instance: 
  - [Een overzicht van Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Berekening van de totale eigendoms kosten van Azure (TCO)](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) s

- Zie voor video-inhoud: 
    - [Overzicht van de migratie traject en de hulpprogram ma's/services die worden aanbevolen voor het uitvoeren van de evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)