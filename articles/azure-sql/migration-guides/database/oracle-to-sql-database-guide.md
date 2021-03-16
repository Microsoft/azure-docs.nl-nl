---
title: 'Oracle to SQL Database: migratie handleiding'
description: In deze hand leiding leert u hoe u uw Oracle-schema kunt migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor Oracle (SSMA for Oracle).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 11a3d386eae9b77a5f53b7e8d2dbcf012edd9386
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564925"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Migratie handleiding: Oracle naar Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u hoe u uw Oracle-schema's kunt migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor Oracle.

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten

Als u uw Oracle-schema wilt migreren naar SQL Database hebt u het volgende nodig: 

- U kunt controleren of uw bron omgeving wordt ondersteund. 
- Voor het downloaden [van SQL Server Migration Assistant (SSMA) voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Een doel [Azure SQL database](../../database/single-database-create-quickstart.md). 
- De [benodigde machtigingen voor SSMA voor Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) en [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. Dit onderdeel van het proces bestaat uit het uitvoeren van een inventarisatie van de data bases die u moet migreren, het beoordelen van die data bases voor mogelijke migratie problemen of blok keringen en het oplossen van items die u mogelijk hebt gedetecteerd.



### <a name="assess"></a>Evalueren 


Gebruik de SQL Server Migration Assistant (SSMA) voor Oracle om database objecten en-gegevens te bekijken, data bases te beoordelen voor migratie, database objecten te migreren naar Azure SQL Database en tenslotte gegevens naar de data base te migreren. 


Voer de volgende stappen uit om een evaluatie te maken: 


1. Open [SQL Server Migration Assistant voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam op, een locatie om uw project op te slaan en selecteer vervolgens Azure SQL Database als migratie doel in de vervolg keuzelijst. Selecteer **OK**.
1. Voer in het dialoog venster **verbinding maken met Oracle** de waarden in voor de details van de Oracle-verbinding.
1. Klik met de rechter muisknop op het Oracle-schema dat u wilt migreren in de **Oracle-meta gegevens Verkenner**, en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u de Data Base hebt geselecteerd.
1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een overzicht te krijgen van Oracle-objecten en de inspanningen die nodig zijn voor het uitvoeren van schema-conversies. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`



### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit:

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** . 
1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **Oracle-meta gegevens Verkenner**.

### <a name="convert-schema"></a>Schema converteren

Voer de volgende stappen uit om het schema te converteren: 

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en kies vervolgens **instructies toevoegen**.
1. Selecteer **verbinding maken met Azure SQL database**. 
    1. Voer de verbindings gegevens in om uw data base in Azure SQL Database te koppelen.
    1. Kies uw doel SQL Database in de vervolg keuzelijst.
    1. Selecteer **Verbinding maken**.

1. Klik met de rechter muisknop op het schema en kies vervolgens **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u het schema hebt geselecteerd.
1. Nadat de conversie is voltooid, vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen.
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** .

## <a name="migrate"></a>Migrate

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces. Migratie omvat twee stappen: het schema publiceren en de gegevens migreren. 

Als u uw schema wilt publiceren en uw gegevens wilt migreren, voert u de volgende stappen uit:

1. Publiceer het schema: Klik met de rechter muisknop op de data base in het knoop punt **data bases** in de **Azure SQL database meta gegevens Verkenner** en kies **synchroniseren met data base**.
1. De gegevens migreren: Klik met de rechter muisknop op het schema in de **Oracle-meta gegevens Verkenner** en kies **gegevens migreren**. 
1. Geef verbindings Details op voor zowel Oracle als Azure SQL Database.
1. Het **gegevens migratie rapport** weer geven.
1. Maak verbinding met uw Azure SQL Database met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren.


U kunt ook SQL Server Integration Services (SSIS) gebruiken om de migratie uit te voeren. Raadpleeg voor meer informatie: 

- [SQL Server Migration Assistant: gegevens van niet-micro soft-gegevens platformen evalueren en migreren naar SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
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

- Voor meer informatie over Azure SQL Database raadpleegt u: 
  - [Een overzicht van Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Berekening van de totale eigendoms kosten van Azure (TCO)](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie voor video-inhoud: 
    - [Overzicht van de migratie traject en de hulpprogram ma's/services die worden aanbevolen voor het uitvoeren van de evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)


