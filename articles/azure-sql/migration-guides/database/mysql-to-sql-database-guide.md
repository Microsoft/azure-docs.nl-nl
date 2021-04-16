---
title: 'MySQL naar Azure SQL Database: Migratiehandleiding'
description: In deze handleiding leert u hoe u uw MySQL-databases migreert naar een Azure SQL-database met behulp van SQL Server Migration Assistant for MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389448"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Migratiehandleiding: MySQL naar Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze handleiding [](https://azure.microsoft.com/migration/migration-journey) leert u hoe u uw MySQL-database migreert naar een Azure SQL-database met [behulp van SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for MySQL (SSMA for MySQL). 

Zie Azure Database [Migration Guide (Handleiding voor azure-databasemigratie) voor andere migratiehandleidingen.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het migreren van uw MySQL-database naar een SQL database, gaat u als volgt te werk:

- Controleer of uw bronomgeving wordt ondersteund. Momenteel worden MySQL 5.6 en 5.7 ondersteund. 
- Download en installeer [SQL Server Migration Assistant voor MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Zorg ervoor dat u connectiviteit hebt en voldoende machtigingen hebt om toegang te krijgen tot zowel de bron als het doel.

## <a name="pre-migration"></a>Premigratie 

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de haalbaarheid van uw [Azure-cloudmigratie te evalueren.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Evalueren 

Gebruik SQL Server Migration Assistant (SSMA) voor MySQL om databaseobjecten en -gegevens te controleren en databases te evalueren voor migratie. 

Ga als volgt te werk om een evaluatie te maken:

1. Open [SSMA voor MySQL.](https://www.microsoft.com/download/details.aspx?id=54257) 
1. Selecteer **Bestand** en selecteer vervolgens **Nieuw project**. 
1. Voer in **het deelvenster** Nieuw project een naam en locatie  in voor uw project en selecteer vervolgens in de vervolgkeuzelijst Migreren naar de **Azure SQL Database.** 
1. Selecteer **OK**.

   ![Schermopname van het deelvenster Nieuw project voor het invoeren van de naam, locatie en het doel van uw migratieproject.](./media/mysql-to-sql-database-guide/new-project.png)

1. Selecteer het **tabblad Verbinding maken met MySQL** en geef details op voor het maken van verbinding met uw MySQL-server. 

   ![Schermopname van het deelvenster 'Verbinding maken met MySQL' voor het opgeven van verbindingen met de bron.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Klik in **het deelvenster MySQL Metadata Explorer** met de rechtermuisknop op het MySQL-schema en selecteer rapport **maken.** U kunt ook het tabblad **Rapport maken** in de rechterbovenhoek selecteren.

   ![Schermopname van de koppelingen Rapport maken in SSMA for MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Bekijk het HTML-rapport om inzicht te krijgen in de conversiestatistieken, fouten en waarschuwingen. Analyseer deze om inzicht te krijgen in de conversieproblemen en oplossingen. 
   U kunt het rapport ook openen in Excel om een inventaris van MySQL-objecten op te halen en inzicht te krijgen in de inspanning die nodig is om schemaconversies uit te voeren. De standaardlocatie voor het rapport bevindt zich in de rapportmap in SSMAProjects. Bijvoorbeeld: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Schermopname van een voorbeeld van een conversierapport in SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>De gegevenstypen valideren

Valideer de standaardtoewijzingen voor gegevenstype en wijzig deze op basis van de vereisten, indien nodig. Hiervoor doet u het volgende: 

1. Selecteer **Extra** en selecteer vervolgens **Projectinstellingen.**  
1. Selecteer het **tabblad Typetoewijzingen.** 

   ![Schermopname van het deelvenster 'Typetoewijzing' in SSMA voor MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. U kunt de typetoewijzing voor elke tabel wijzigen door de tabelnaam te selecteren in het **deelvenster MySQL Metadata Explorer.** 

### <a name="convert-the-schema"></a>Het schema converteren 

Ga als volgt te werk om het schema te converteren: 

1. (Optioneel) Als u dynamische of gespecialiseerde query's wilt converteren, klikt u met de rechtermuisknop op het knooppunt en selecteert u **instructie toevoegen.** 

1. Selecteer het **tabblad Verbinding Azure SQL Database** maken en doe het volgende:

   a. Voer de details in voor het maken van verbinding met SQL database.  
   b. Selecteer uw doeldoel in de vervolgkeuzelijst SQL database. U kunt ook een nieuwe naam verstrekken. In dat geval wordt er een database gemaakt op de doelserver.  
   c. Geef verificatiegegevens op.  
   d. Selecteer **Verbinding maken**.

   ![Schermopname van het deelvenster 'Verbinding Azure SQL Database maken' in SSMA voor MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Klik met de rechtermuisknop op het schema dat u wilt gebruiken en selecteer schema **converteren.** U kunt ook rechtsboven het **tabblad Schema converteren** selecteren.

   ![Schermopname van de opdracht Schema converteren in het deelvenster MySQL Metadata Explorer.](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Nadat de conversie is voltooid, controleert en vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en deze op basis van de aanbevelingen aan te pakken. 

   ![Schermopname van een vergelijking van de geconverteerde objecten met de oorspronkelijke objecten.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en bekijk de aanbevelingen.

   ![Schermopname van een vergelijking van geconverteerde query's naar de broncode.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Selecteer in **het** deelvenster Uitvoer de optie **Resultaten controleren** en bekijk eventuele fouten in het **lijstvenster** Fouten. 
1. Sla het project lokaal op voor een offline schema-hersteloefening. Selecteer om dit te doen **Bestand**  >  **Project opslaan.** Dit biedt u de mogelijkheid om de bron- en doelschema's offline te evalueren en herstel uit te voeren voordat u het schema naar uw SQL database.

   Vergelijk de geconverteerde procedures met de oorspronkelijke procedures, zoals hier wordt weergegeven: 

   ![Schermopname van een vergelijking van de geconverteerde procedures met de oorspronkelijke procedures.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>De databases migreren 

Nadat u uw databases hebt geëvalueerd en eventuele discrepanties hebt aangepakt, kunt u het migratieproces uitvoeren. Migratie omvat twee stappen: het publiceren van het schema en het migreren van de gegevens. 

Ga als volgt te werk om het schema te publiceren en de gegevens te migreren: 

1. Publiceer het schema. Klik in **Azure SQL Database het deelvenster Metadata Explorer** met de rechtermuisknop op de database en selecteer vervolgens Synchroniseren met **database**. Met deze actie wordt het MySQL-schema naar uw SQL database.

   ![Schermopname van het deelvenster Synchroniseren met de database voor het controleren van de databasetoewijzing.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migreert de gegevens. Klik in **het deelvenster MySQL Metadata Explorer** met de rechtermuisknop op het MySQL-schema dat u wilt migreren en selecteer vervolgens Gegevens **migreren.** U kunt ook het tabblad **Gegevens migreren** in de rechterbovenhoek selecteren.

   Als u gegevens voor een hele database wilt migreren, selecteert u het selectievakje naast de databasenaam. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de database uit, vouwt u **Tabellen** uit en selecteert u vervolgens het selectievakje naast de tabel. Als u gegevens uit afzonderlijke tabellen wilt weglaten, moet u het selectievakje uit.

   ![Schermopname van de opdracht Gegevens migreren in het deelvenster MySQL Metadata Explorer.](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Nadat de migratie is voltooid, bekijkt u **het gegevensmigratierapport**.
   
   ![Schermopname van het gegevensmigratierapport.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Maak verbinding met uw SQL database met behulp [van SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren.

   ![Schermopname van SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Postmigratie 

Nadat u de migratiefase hebt voltooid, moet u een reeks taken na de migratie uitvoeren om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt. 

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens naar de doelomgeving zijn gemigreerd, moeten alle toepassingen die voorheen de bron hebben gebruikt, het doel gaan gebruiken. Hiervoor zijn in sommige gevallen wijzigingen in de toepassingen vereist.

### <a name="perform-tests"></a>Tests uitvoeren

De testbenadering voor databasemigratie bestaat uit de volgende activiteiten:

1. **Validatietests ontwikkelen:** als u de databasemigratie wilt testen, moet u SQL-query's gebruiken. U moet de validatiequery's maken om uit te voeren op zowel de bron- als de doeldatabase. Uw validatiequery's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

1. **Een testomgeving instellen:** de testomgeving moet een kopie van de brondatabase en de doeldatabase bevatten. Zorg ervoor dat u de testomgeving isoleert.

1. **Validatietests uitvoeren:** voer validatietests uit op de bron en het doel en analyseer vervolgens de resultaten.

1. **Prestatietests uitvoeren:** voer prestatietests uit op de bron en het doel, en analyseer en vergelijk de resultaten.

### <a name="optimize"></a>Optimaliseren

De postmigratiefase is van cruciaal belang voor het afstemmen van eventuele problemen met gegevensnauwkeurigheid, het controleren van de volledigheid en het oplossen van prestatieproblemen met de workload.

Zie de postmigratievalidatie- en optimalisatiehandleiding voor meer informatie over deze problemen en de stappen om deze [te verhelpen.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migratie-assets

Zie de volgende resource voor meer hulp bij het voltooien van dit migratiescenario. Het is ontwikkeld ter ondersteuning van een echte migratieprojectbetrokkenheid.

| Titel | Beschrijving |
| --- | --- |
| [Model en hulpprogramma voor evaluatie van gegevensworkloads](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Biedt voorgestelde 'best fit' doelplatforms, cloud-gereedheid en herstelniveaus voor toepassingen/databases voor opgegeven workloads. Het biedt eenvoudige berekening met één klik en het genereren van een rapport waarmee u grote evaluaties van onroerend goed kunt versnellen door een geautomatiseerd, uniform beslissingsproces voor het doelplatform te bieden. |

Het Data SQL Engineering-team heeft deze resources ontwikkeld. De kern van dit team is het deblokkeren en versnellen van complexe modernisering voor gegevensplatformmigratieprojecten naar het Azure-gegevensplatform van Microsoft.

## <a name="next-steps"></a>Volgende stappen 

- Zie de Azure total cost of ownership calculator om een schatting te maken van de kostenbesparingen die u kunt realiseren door uw workloads naar Azure [total cost of ownership migreren.](https://aka.ms/azure-tco)

- Zie Service en hulpprogramma's voor gegevensmigratie voor een matrix met services en hulpprogramma's van Microsoft en derden die u kunnen helpen bij verschillende database- en gegevensmigratiescenario's en speciale [taken.](../../../dms/dms-tools-matrix.md)

- Zie Azure Database [Migration Guide (Handleiding voor databasemigratie) voor andere migratiehandleidingen.](https://datamigration.microsoft.com/) 

- Zie Overzicht van het migratietraject en aanbevolen migratie- en evaluatiehulpprogramma's en -services voor [migratievideo's.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

- Zie [cloudmigratieoplossingen voor](https://azure.microsoft.com/migration/resources/)meer [cloudmigratieresources.](https://azure.microsoft.com/migration)

