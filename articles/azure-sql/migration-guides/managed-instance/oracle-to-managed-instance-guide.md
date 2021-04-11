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
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553903"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Migratie handleiding: Oracle naar Azure SQL Managed instance

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

In deze hand leiding leert u hoe u uw Oracle-schema's naar Azure SQL Managed Instance kunt migreren met behulp van SQL Server Migration Assistant voor Oracle (SSMA for Oracle).

Raadpleeg de [migratie handleidingen van Azure data base](https://docs.microsoft.com/data-migration)voor andere migratie handleidingen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het migreren van uw Oracle-schema naar een SQL Managed instance:

- Controleer of uw bron omgeving wordt ondersteund.
- Down load [SSMA voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Een door [SQL beheerd exemplaar](../../managed-instance/instance-create-quickstart.md) doel hebben.
- Verkrijg de [benodigde machtigingen voor SSMA voor Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) en [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. Dit onderdeel van het proces bestaat uit het uitvoeren van een inventarisatie van de data bases die u moet migreren, het beoordelen van die data bases voor mogelijke migratie problemen of blok keringen en het oplossen van items die u mogelijk hebt gedetecteerd.

### <a name="assess"></a>Evalueren

Met SSMA voor Oracle kunt u database objecten en-gegevens bekijken, data bases voor migratie beoordelen, database objecten migreren naar een beheerd exemplaar van SQL en vervolgens gegevens migreren naar de data base.

Een evaluatie maken:

1. Open [SSMA voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
1. Selecteer **bestand** en selecteer vervolgens **Nieuw project**.
1. Voer een project naam en een locatie in om uw project op te slaan. Selecteer vervolgens **Azure SQL Managed instance** als het migratie doel in de vervolg keuzelijst en selecteer **OK**.

   ![Scherm opname van het nieuwe project.](./media/oracle-to-managed-instance-guide/new-project.png)

1. Selecteer **verbinding maken met Oracle**. Voer waarden in voor Oracle-verbindings Details in het dialoog venster **verbinding maken met Oracle** .

   ![Scherm opname die laat zien hoe u verbinding maakt met Oracle.](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. Selecteer de Oracle-schema's die u wilt migreren.

   ![Scherm opname van het selecteren van het Oracle-schema.](./media/oracle-to-managed-instance-guide/select-schema.png)

1. Klik in **Oracle Meta Data Explorer** met de rechter muisknop op het Oracle-schema dat u wilt migreren en selecteer vervolgens **rapport maken** om een HTML-rapport te genereren. U kunt ook een Data Base selecteren en vervolgens het tabblad **rapport maken** selecteren.

   ![Scherm opname van het rapport maken.](./media/oracle-to-managed-instance-guide/create-report.png)

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een overzicht te krijgen van Oracle-objecten en de inspanningen die nodig zijn voor het uitvoeren van schema-conversies. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Zie bijvoorbeeld `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Scherm afbeelding met een beoordelings rapport.](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>De gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit:

1. Selecteer in SSMA voor Oracle de optie **extra** en selecteer vervolgens **project instellingen**.
1. Selecteer het tabblad **type toewijzing** .

   ![Scherm opname van het type toewijzing.](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in **Oracle-meta gegevens Verkenner**.

### <a name="convert-the-schema"></a>Het schema converteren

Het schema converteren:

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en selecteer vervolgens **instructies toevoegen**.
1. Selecteer het tabblad **verbinding maken met Azure SQL Managed instance** .
    1. Voer de verbindings gegevens in om uw data base te verbinden in **SQL database Managed instance**.
    1. Selecteer uw doel database in de vervolg keuzelijst of voer een nieuwe naam in. in dat geval wordt er een Data Base op de doel server gemaakt.
    1. Voer verificatie gegevens in en selecteer **verbinding maken**.

    ![Scherm afbeelding die laat zien hoe u verbinding maakt met Azure SQL Managed instance.](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. Klik in **Oracle Meta Data Explorer** met de rechter muisknop op het Oracle-schema en selecteer vervolgens **schema omzetten**. U kunt ook uw schema selecteren en vervolgens het tabblad **schema converteren** selecteren.

   ![Scherm opname van schema.](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Nadat de conversie is voltooid, vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen.

   ![Scherm afbeelding waarin de tabel aanbevelingen worden weer gegeven.](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en Bekijk de aanbevelingen.

   ![Scherm opname van de procedure waarin aanbevelingen worden weer gegeven.](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Selecteer in het deel venster uitvoer de optie **resultaten controleren** en controleer de fouten in het deel venster **foutenlijst** .
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer in het menu **bestand** de optie **project opslaan**. Deze stap biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema naar SQL Managed instance publiceert.

## <a name="migrate"></a>Migrate

Nadat u klaar bent met het beoordelen van uw data bases en eventuele discrepanties hebt opgelost, is de volgende stap het migratie proces uit te voeren. Migratie bestaat uit twee stappen: het schema publiceren en de gegevens migreren.

Uw schema publiceren en uw gegevens migreren:
1. Publiceer het schema door met de rechter muisknop op de data base te klikken in het knoop punt **data bases** in **Azure SQL Managed instance Meta Data Explorer** en selecteer **synchroniseren met data base**.

   ![Scherm afbeelding die de synchronisatie met de data base weergeeft.](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. Controleer de toewijzing tussen uw bron project en uw doel.

   ![Scherm opname van de weer gave van de database revisie.](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migreer de gegevens door met de rechter muisknop te klikken op het schema of het object dat u wilt migreren in **Oracle Meta Data Explorer** en selecteer **gegevens migreren**. U kunt ook het tabblad **gegevens migreren** selecteren. Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u **tabellen** uit en selecteert u vervolgens de selectie vakjes naast de tabellen. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u de selectie vakjes uit.

   ![Scherm opname van de migratie gegevens.](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Voer de verbindings gegevens in voor zowel Oracle als SQL Managed instance.
1. Nadat de migratie is voltooid, bekijkt u het **rapport gegevens migratie**.

   ![Scherm opname van het gegevens migratie rapport.](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Maak verbinding met uw exemplaar van SQL Managed instance met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)en valideer de migratie door de gegevens en het schema te controleren.

   ![Scherm opname van de validatie in SSMA voor Oracle.](./media/oracle-to-managed-instance-guide/validate-data.png)

U kunt ook SQL Server Integration Services gebruiken om de migratie uit te voeren. Raadpleeg voor meer informatie:

- [Aan de slag met SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services voor Azure en hybride gegevens verplaatsing](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Postmigratie

Nadat u de *migratie* fase hebt voltooid, moet u een reeks taken na migratie volt ooien om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. Bij het uitvoeren van deze stap moeten in sommige gevallen wijzigingen in de toepassingen worden aangebracht.

De [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) is een uitbrei ding voor Visual Studio code waarmee u uw Java-bron code kunt analyseren en oproepen en query's voor Data Access-api's detecteert. De Toolkit bevat een weer gave met één deel venster van wat moet worden geadresseerd voor de ondersteuning van de nieuwe data base-back-end. Zie voor meer informatie het blog bericht over het [migreren van onze Java-toepassing vanuit Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) .

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u de database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
2. **Een test omgeving instellen**: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
3. **Validatie tests uitvoeren**: voer validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
4. **Voer prestatie tests uit**: Voer prestatie tests uit op basis van de bron en het doel en analyseer en vergelijk vervolgens de resultaten.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met gegevens nauwkeurigheid, het controleren van de volledigheid en het oplossen van prestatie problemen met de werk belasting.

> [!NOTE]
> Zie voor meer informatie over deze problemen en de stappen om deze te beperken de [hand leiding voor validatie na migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Migratie-assets

Raadpleeg de volgende bronnen voor meer hulp bij het volt ooien van dit migratie scenario. Ze zijn ontwikkeld ter ondersteuning van een praktijk gerichte migratie project.

| **Titel/koppeling**                                                                                                                                          | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheid en toepassings-of database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren, waarmee u grote en ongeëvenaarde evaluaties versnelt door een geautomatiseerd en uniform platform besluitvormings proces te bieden.                                                          |
| [Oracle Inventory script artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Deze asset bevat een PL/SQL-query die Oracle-systeem tabellen oplevert en een telling van objecten biedt per schema type, object type en status. Het biedt ook een ruwe schatting van onbewerkte gegevens in elk schema en de grootte van de tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.                                                                                                               |
| [SSMA Oracle Assessment verzameling automatiseren & consolidatie](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Deze resourceset gebruikt een CSV-bestand als vermelding (sources.csv in de project mappen) om de XML-bestanden te maken die nodig zijn voor het uitvoeren van een SSMA-evaluatie in de console modus. De source.csv wordt door de klant verschaft op basis van een inventaris van bestaande Oracle-exemplaren. De uitvoer bestanden zijn AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml en VariableValueFile.xml.|
| [SSMA voor veelvoorkomende fouten in Oracle en hoe u deze kunt oplossen](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Met Oracle kunt u een nonscalar-voor waarde toewijzen in de component WHERE. SQL Server biedt echter geen ondersteuning voor dit type voor waarde. Als gevolg hiervan worden door SSMA voor Oracle geen query's geconverteerd met een nonscalar-voor waarde in de component WHERE. In plaats daarvan wordt de fout O2SS0001 gegenereerd. Dit technisch document bevat meer informatie over het probleem en manieren om dit op te lossen.          |
| [SQL Server migratie handleiding voor Oracle](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Dit document is gericht op de taken die zijn gekoppeld aan het migreren van een Oracle-schema naar de nieuwste versie van SQL Server Data Base. Als voor de migratie wijzigingen in functies of functionaliteit zijn vereist, moet de mogelijke gevolgen van elke wijziging in de toepassingen die gebruikmaken van de data base zorgvuldig worden beschouwd.                                                     |

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.

## <a name="next-steps"></a>Volgende stappen

- Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van micro soft-Services en hulpprogram ma's van derden die beschikbaar zijn om u te helpen bij het maken van verschillende scenario's voor data base-en gegevens migratie en speciale taken.

- Zie voor meer informatie over SQL Managed instance:
  - [Een overzicht van Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Berekening van de totale eigendoms kosten van Azure (TCO)](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het berekenen en aanpassen van werk belastingen voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Zie voor video-inhoud:
    - [Overzicht van de migratie traject en de hulpprogram ma's en services die worden aanbevolen voor het uitvoeren van de evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
