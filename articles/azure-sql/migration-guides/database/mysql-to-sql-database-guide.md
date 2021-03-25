---
title: 'MySQL naar Azure SQL Database: migratie handleiding'
description: Deze hand leiding leert u uw MySQL-data bases te migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor MySQL (SSMA voor MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 14b2c1f98ae977548edb635b8a8a7a956b3f2dd7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023772"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Migratie handleiding: MySQL naar Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u hoe u uw MySQL-data base kunt migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor MySQL (SSMA voor MySQL). 

Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten

Als u uw MySQL-data base naar Azure SQL Database wilt migreren, hebt u het volgende nodig:

- U kunt controleren of uw bron omgeving wordt ondersteund. Op dit moment wordt MySQL 5,6 en 5,7 ondersteund. 
- [SQL Server Migration Assistant voor MySQL](https://www.microsoft.com/download/details.aspx?id=54257)
- Connectiviteit en voldoende machtigingen voor toegang tot zowel de bron als het doel. 


## <a name="pre-migration"></a>Premigratie 

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen.

### <a name="assess"></a>Evalueren 

Gebruik SQL Server Migration Assistant (SSMA) voor MySQL om database objecten en-gegevens te controleren en data bases te evalueren voor migratie. 

Voer de volgende stappen uit om een evaluatie te maken: 

1. Open [SQL Server Migration Assistant voor mysql](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selecteer **bestand** in het menu en kies vervolgens **Nieuw project**. 
1. Geef een naam op voor het project, een locatie om uw project op te slaan. Kies **Azure SQL database** als migratie doel. Selecteer **OK**:

   ![Nieuw project](./media/mysql-to-sql-database-guide/new-project.png)

1. Kies **verbinding maken met MySQL** en geef de verbindings gegevens op om verbinding te maken met uw MySQL-server:

   ![Verbinding maken met MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Klik met de rechter muisknop op het MySQL-schema in **MySQL Meta Data Explorer** en kies **rapport maken**. U kunt ook **rapport maken** selecteren in de navigatie balk van de bovenste regel:

   ![Rapport maken](./media/mysql-to-sql-database-guide/create-report.png)

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een overzicht te krijgen van MySQL-objecten en de inspanningen die nodig zijn om schema conversies uit te voeren. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.
 
   Bijvoorbeeld: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Conversie rapport](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit: 

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** :

   ![Type toewijzingen](./media/mysql-to-sql-database-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **MySQL-meta gegevens Verkenner**. 

### <a name="convert-schema"></a>Schema converteren 

Voer de volgende stappen uit om het schema te converteren: 

1. Beschrijving Als u dynamische of ad-hoc query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en kiest u **instructie toevoegen**. 
1. Selecteer **verbinding maken met Azure SQL database**. 
    1. Voer de verbindings gegevens in om uw data base in Azure SQL Database te koppelen.
    1. Kies uw doel SQL Database in de vervolg keuzelijst of geef een nieuwe naam op. in dat geval wordt er een Data Base op de doel server gemaakt. 
    1. Geef verificatie Details op. 
    1. Selecteer **verbinding maken**:

   ![Verbinding maken met SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Klik met de rechter muisknop op het schema en kies **schema converteren**. U kunt ook **schema converteren** selecteren in de navigatie balk van de bovenste regel nadat u de Data Base hebt gekozen:

   ![Schema converteren](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Nadat de conversie is voltooid, vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen:

   ![Geconverteerde objecten kunnen worden vergeleken met de bron](./media/mysql-to-sql-database-guide/table-comparison.png)

   Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en Bekijk de aanbevelingen:

   ![Geconverteerde query's kunnen worden vergeleken met de bron code](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Selecteer **resultaten controleren** in het deel venster uitvoer en Bekijk fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema kunt publiceren naar SQL Database.



## <a name="migrate"></a>Migrate 

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces. Migratie omvat twee stappen: het schema publiceren en de gegevens migreren. 

Als u uw schema wilt publiceren en de gegevens wilt migreren, volgt u deze stappen: 

1. Publiceer het schema: Klik met de rechter muisknop op de data base in de **meta gegevens Verkenner van Azure SQL database** en kies **synchroniseren met data base**. Met deze actie wordt het MySQL-schema gepubliceerd naar Azure SQL Database:

   ![Synchroniseren met data base](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Controleer de toewijzing tussen uw bron project en uw doel:

   ![Synchroniseren met database revisie](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. De gegevens migreren: Klik met de rechter muisknop op de data base of het object dat u wilt migreren in **MySQL Meta Data Explorer** en kies **gegevens migreren**. U kunt ook **gegevens migreren** selecteren in de bovenste navigatie balk. Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u tabellen uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit:

   ![Gegevens migreren](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Nadat de migratie is voltooid, raadpleegt u het rapport **gegevens migratie** : 

   ![Gegevens migratie rapport](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Maak verbinding met uw Azure SQL Database met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren:

    ![Valideren in SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Postmigratie 

Nadat u de **migratie** fase hebt voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit het uitvoeren van de volgende activiteiten:

1. **Validatie tests ontwikkelen**. Als u de database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

2. **Test omgeving instellen**. De test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.

3. **Validatie tests uitvoeren**. Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.

4. **Prestatie tests uitvoeren**. Voer prestatie tests uit op basis van de bron en het doel, en analyseer en vergelijk vervolgens de resultaten.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met de nauw keurigheid van gegevens en het controleren van de volledigheid, en het oplossen van prestatie problemen met de werk belasting.

Zie voor meer informatie over deze problemen en specifieke stappen om ze te beperken de [hand leiding voor validatie na de migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Migratie-assets

Voor aanvullende hulp bij het volt ooien van dit migratie scenario raadpleegt u de volgende bronnen, die zijn ontwikkeld ter ondersteuning van een echt toonaangevend migratie project.

| Titel/koppeling     | Beschrijving    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote en kleine beoordelingen aanzienlijk sneller door middel van en geautomatiseerd en uniform platform besluitvormings proces. |

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.

## <a name="next-steps"></a>Volgende stappen 

- Bekijk de [reken machine van de totale eigendoms kosten van Azure (TCO)](https://aka.ms/azure-tco) voor een schatting van de kosten besparingen die u kunt realiseren door uw workloads naar Azure te migreren.

- Zie de artikel [service en hulpprogram ma's voor gegevens migratie](https://docs.microsoft.com/azure/dms/dms-tools-matrix)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

Zie voor Video's: 
- [Overzicht van de migratie traject en de hulpprogram ma's/services die worden aanbevolen voor het uitvoeren van de evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
