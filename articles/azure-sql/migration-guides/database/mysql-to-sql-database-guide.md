---
title: 'MySQL naar Azure SQL Database: migratie handleiding'
description: In deze hand leiding leert u hoe u uw MySQL-data bases kunt migreren naar een Azure-SQL database met behulp van SQL Server Migration Assistant voor MySQL (SSMA voor MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 1f1692aaa74f56c404a8fae7aa91e94baecbb7e1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626483"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Migratie handleiding: MySQL naar Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u hoe u uw MySQL-data base kunt migreren naar een Azure-SQL database met behulp van SQL Server Migration Assistant voor MySQL (SSMA voor MySQL). 

Zie voor andere migratie [handleidingen de Azure data base Migration Guide (Engelstalig](https://docs.microsoft.com/data-migration)). 

## <a name="prerequisites"></a>Vereisten

Ga als volgt te werk voordat u begint met het migreren van uw MySQL-data base naar een SQL database:

- Controleer of uw bron omgeving wordt ondersteund. Op dit moment worden MySQL 5,6 en 5,7 ondersteund. 
- Down load en Installeer [SQL Server Migration Assistant voor mysql](https://www.microsoft.com/download/details.aspx?id=54257).
- Zorg ervoor dat u verbinding hebt en voldoende machtigingen hebt voor toegang tot zowel de bron als het doel.

## <a name="pre-migration"></a>Premigratie 

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen.

### <a name="assess"></a>Evalueren 

Gebruik SQL Server Migration Assistant (SSMA) voor MySQL om database objecten en-gegevens te controleren en data bases te evalueren voor migratie. 

Ga als volgt te werk om een evaluatie te maken:

1. Open [SSMA voor mysql](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selecteer **bestand** en selecteer vervolgens **Nieuw project**. 
1. Voer in het deel venster **Nieuw project** een naam en locatie in voor uw project en selecteer vervolgens in de vervolg keuzelijst **migreren naar** de optie **Azure SQL database**. 
1. Selecteer **OK**.

   ![Scherm afbeelding van het deel venster Nieuw project voor het invoeren van de naam, locatie en doel van uw migratie project.](./media/mysql-to-sql-database-guide/new-project.png)

1. Selecteer het tabblad **verbinding maken met MySQL** en geef de Details voor het verbinden van uw MySQL-server op. 

   ![Scherm opname van het deel venster ' verbinding maken met MySQL ' voor het opgeven van verbindingen met de bron.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Klik in het deel venster **MySQL-meta gegevens Verkenner** met de rechter muisknop op het MySQL-schema en selecteer vervolgens **rapport maken**. U kunt ook het tabblad **rapport maken** selecteren in de rechter bovenhoek.

   ![Scherm opname van de koppelingen ' rapport maken ' in SSMA voor MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Bekijk het HTML-rapport om inzicht te krijgen in de conversie statistieken, fouten en waarschuwingen. Analyseer het om inzicht te krijgen in de conversie problemen en oplossingen. 
   U kunt het rapport ook openen in Excel om een inventaris van MySQL-objecten te ontvangen en inzicht te krijgen in de inspanningen die nodig zijn voor het uitvoeren van schema-conversies. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects. Bijvoorbeeld: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Scherm afbeelding van een voor beeld van een conversie rapport in SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>De gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig ze op basis van vereisten, indien nodig. Hiervoor doet u het volgende: 

1. Selecteer **extra** en selecteer vervolgens **project instellingen**.  
1. Selecteer het tabblad **type toewijzingen** . 

   ![Scherm afbeelding van het deel venster type mapping in SSMA voor MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de naam van de tabel te selecteren in het deel venster **MySQL Meta Data Explorer** . 

### <a name="convert-the-schema"></a>Het schema converteren 

Ga als volgt te werk om het schema te converteren: 

1. Beschrijving Als u dynamische of gespecialiseerde query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en selecteert u vervolgens **instructie toevoegen**. 

1. Selecteer het tabblad **verbinding maken met Azure SQL database** en Ga als volgt te werk:

   a. Voer de details in voor het maken van verbinding met uw SQL database.  
   b. Selecteer uw doel SQL database in de vervolg keuzelijst. U kunt ook een nieuwe naam opgeven. in dat geval wordt er een Data Base op de doel server gemaakt.  
   c. Geef verificatie Details op.  
   d. Selecteer **Verbinding maken**.

   ![Scherm opname van het deel venster ' verbinden met Azure SQL Database ' in SSMA voor MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Klik met de rechter muisknop op het schema waarmee u wilt werken en selecteer vervolgens **schema omzetten**. U kunt ook het tabblad **schema converteren** selecteren in de rechter bovenhoek.

   ![Scherm opname van de opdracht "schema converteren" in het deel venster "MySQL meta gegevens Verkenner".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Nadat de conversie is voltooid, controleert en vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen. 

   ![Scherm afbeelding met een vergelijking van de geconverteerde objecten met de oorspronkelijke objecten.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en Bekijk de aanbevelingen.

   ![Scherm afbeelding met een vergelijking van geconverteerde query's naar de bron code.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Selecteer in het deel venster **uitvoer** de optie **resultaten bekijken** en Bekijk eventuele fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer hiervoor **bestand**  >  **opslaan Project**. Dit geeft u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema naar uw SQL database publiceert.

   Vergelijk de geconverteerde procedures met de oorspronkelijke procedures, zoals hier wordt weer gegeven: 

   ![Scherm afbeelding met een vergelijking van de geconverteerde procedures met de oorspronkelijke procedures.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>De data bases migreren 

Nadat u uw data bases hebt beoordeeld en eventuele verschillen hebt opgelost, kunt u het migratie proces uitvoeren. Migratie bestaat uit twee stappen: het schema publiceren en de gegevens migreren. 

Ga als volgt te werk om het schema te publiceren en de gegevens te migreren: 

1. Publiceer het schema. Klik in Azure SQL Database het deel venster **meta gegevens Verkenner** met de rechter muisknop op de data base en selecteer vervolgens **synchroniseren met data base**. Met deze actie wordt het MySQL-schema gepubliceerd naar uw SQL database.

   ![Scherm afbeelding van het deel venster "synchroniseren met data base" voor het controleren van database toewijzing.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. De gegevens migreren. Klik in het deel venster **MySQL Meta Data Explorer** met de rechter muisknop op het MySQL-schema dat u wilt migreren en selecteer vervolgens **gegevens migreren**. U kunt ook het tabblad **gegevens migreren** in de rechter bovenhoek selecteren.

   Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u **tabellen** uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit.

   ![Scherm afbeelding van de opdracht "gegevens migreren" in het deel venster "MySQL meta gegevens Verkenner".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Nadat de migratie is voltooid, bekijkt u het **rapport gegevens migratie**.
   
   ![Scherm afbeelding van het rapport voor gegevens migratie.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Maak verbinding met uw SQL database met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren.

   ![Scherm opname van SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Postmigratie 

Nadat u de *migratie* fase hebt voltooid, moet u een reeks taken na migratie volt ooien om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u de database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

1. **Een test omgeving instellen**: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.

1. **Validatie tests uitvoeren**: voer validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.

1. **Voer prestatie tests uit**: Voer prestatie tests uit op basis van de bron en het doel en analyseer en vergelijk vervolgens de resultaten.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met gegevens nauwkeurigheid, het controleren van de volledigheid en het oplossen van prestatie problemen met de werk belasting.

Zie voor meer informatie over deze problemen en de stappen om deze te beperken de [hand leiding voor validatie na migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Migratie-assets

Zie de volgende resource voor meer informatie over het volt ooien van dit migratie scenario. Het is ontwikkeld ter ondersteuning van een werkelijke migratie project betrokkenheid.

| Titel | Beschrijving |
| --- | --- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Biedt voorgestelde ' Best passend ' doel platforms, Cloud gereedheids en toepassings-en database herstel niveaus voor opgegeven werk belastingen. U kunt met één klik berekeningen en rapporten genereren waarmee u grote voor-en hand-evaluaties versnelt door een geautomatiseerd, uniform platform besluitvormings proces te bieden. |

Het data IT-team van SQL IT heeft deze resource ontwikkeld. Het basis Handvest van het team is het deblokkeren en versnellen van complexe moderniserings voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.

## <a name="next-steps"></a>Volgende stappen 

- Zie de [Azure Total Cost of Ownership Calculator](https://aka.ms/azure-tco)voor meer informatie over de kosten besparingen die u kunt realiseren door uw workloads naar Azure te migreren.

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van micro soft-Services en hulpprogram ma's van derden die beschikbaar zijn om u te helpen bij het maken van verschillende scenario's voor data base-en gegevens migratie en speciale taken.

- Zie voor andere migratie [handleidingen de Azure data base Migration Guide (Engelstalig](https://datamigration.microsoft.com/)). 

- Zie [overzicht van de migratie traject en aanbevolen migratie-en evaluatie hulpprogramma's en-services](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)voor migratie Video's.
