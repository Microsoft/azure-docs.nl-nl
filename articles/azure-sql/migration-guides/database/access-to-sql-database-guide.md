---
title: 'Toegang tot Azure SQL Database: Migratiehandleiding'
description: In deze handleiding leert u hoe u uw Microsoft Access-databases migreert naar een Azure SQL-database met behulp van SQL Server Migration Assistant for Access (SSMA for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 137adbb045a4c449193f9029b9c72f09ddc439b1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388462"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Migratiehandleiding: Toegang tot Azure SQL Database

In deze handleiding [](https://azure.microsoft.com/migration/migration-journey) leert u hoe u uw Microsoft Access-database migreert naar een Azure SQL-database met [behulp van SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Access (SSMA for Access).

Zie Azure Database [Migration Guide (Handleiding voor databasemigratie) voor andere migratiehandleidingen.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het migreren van uw Access-database naar SQL database, gaat u als volgt te werk:

- Controleer of uw bronomgeving wordt ondersteund. 
- Download en installeer [SQL Server Migration Assistant voor Toegang.](https://www.microsoft.com/download/details.aspx?id=54255)
- Zorg ervoor dat u connectiviteit hebt en voldoende machtigingen hebt voor toegang tot zowel de bron als het doel.

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de haalbaarheid van uw [Azure-cloudmigratie te beoordelen.](https://azure.microsoft.com/migration)


### <a name="assess"></a>Evalueren 

Gebruik SSMA voor toegang om databaseobjecten en -gegevens te controleren en databases te beoordelen voor migratie. 

Ga als volgt te werk om een evaluatie te maken: 

1. Open [SSMA voor toegang.](https://www.microsoft.com/download/details.aspx?id=54255) 
1. Selecteer **Bestand** en selecteer vervolgens **Nieuw project.** 
1. Geef een projectnaam en een locatie voor uw project op en selecteer vervolgens in de vervolgkeuzelijst Azure SQL Database **als** migratiedoel. 
1. Selecteer **OK**. 

   ![Schermopname van het deelvenster Nieuw project voor het invoeren van de naam en locatie van uw migratieproject.](./media/access-to-sql-database-guide/new-project.png)

1. Selecteer **Databases toevoegen** en selecteer vervolgens de databases die aan uw nieuwe project moeten worden toegevoegd. 

   ![Schermopname van het tabblad Databases toevoegen in SSMA voor toegang.](./media/access-to-sql-database-guide/add-databases.png)

1. Klik in **het deelvenster Access Metadata Explorer** met de rechtermuisknop op een database en selecteer vervolgens Rapport **maken.** U kunt ook het tabblad **Rapport maken** in de rechterbovenhoek selecteren.

   ![Schermopname van de opdracht Rapport maken in Access Metadata Explorer.](./media/access-to-sql-database-guide/create-report.png)

1. Bekijk het HTML-rapport om inzicht te krijgen in de conversiestatistieken en eventuele fouten of waarschuwingen. U kunt het rapport ook openen in Excel om een inventaris van Access-objecten op te halen en inzicht te krijgen in de inspanning die nodig is om schemaconversies uit te voeren. De standaardlocatie voor het rapport bevindt zich in de rapportmap in SSMAProjects. Bijvoorbeeld:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Schermopname van een voorbeeld van een evaluatie van een databaserapport in SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>De gegevenstypen valideren

Valideer de standaardtoewijzingen voor gegevenstype en wijzig deze op basis van uw vereisten, indien nodig. Hiervoor doet u het volgende:

1. Selecteer in SSMA voor toegang **Extra** en selecteer vervolgens **Projectinstellingen.** 
1. Selecteer het **tabblad Typetoewijzing.** 

   ![Schermopname van het deelvenster 'Typetoewijzing' in SSMA voor toegang.](./media/access-to-sql-database-guide/type-mappings.png)

1. U kunt de typetoewijzing voor elke tabel wijzigen door de tabelnaam te selecteren in het deelvenster **Access Metadata Explorer.**


### <a name="convert-the-schema"></a>Het schema converteren

Ga als volgt te werk om databaseobjecten te converteren: 

1. Selecteer het **tabblad Verbinding Azure SQL Database** maken en doe het volgende:

   a. Voer de details in voor het maken van verbinding met SQL database.  
   b. Selecteer uw doeldoel in de vervolgkeuzelijst SQL database. U kunt ook een nieuwe naam invoeren. In dat geval wordt er een database gemaakt op de doelserver.  
   c. Geef verificatiegegevens op.   
   d. Selecteer **Verbinding maken**.

   ![Schermopname van het deelvenster 'Verbinding maken met Azure SQL Database' voor het invoeren van verbindingsgegevens.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Klik in **het deelvenster Access Metadata Explorer** met de rechtermuisknop op de database en selecteer schema **converteren.** U kunt ook uw database selecteren en vervolgens het tabblad **Schema converteren** selecteren.

   ![Schermopname van de opdracht Schema converteren in het deelvenster Access Metadata Explorer.](./media/access-to-sql-database-guide/convert-schema.png)

1. Nadat de conversie is voltooid, vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om mogelijke problemen te identificeren en de problemen op basis van de aanbevelingen op te lossen.

   ![Schermopname van een vergelijking van de geconverteerde objecten naar de bronobjecten.](./media/access-to-sql-database-guide/table-comparison.png)

    Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en bekijk de aanbevelingen.

   ![Schermopname van een vergelijking van geconverteerde query's naar de broncode.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Optioneel) Als u een afzonderlijk object wilt converteren, klikt u met de rechtermuisknop op het object en selecteert u **schema converteren.** Geconverteerde objecten worden vetgedrukt weergegeven in **Access Metadata Explorer:** 

   ![Schermopname die laat zien dat de objecten in Access Metadata Explorer worden geconverteerd.](./media/access-to-sql-database-guide/converted-items.png)
 
1. Selecteer in **het** deelvenster Uitvoer het pictogram **Resultaten** controleren en bekijk de fouten in het **deelvenster Lijst met** fouten. 
1. Sla het project lokaal op voor een offline schema-hersteloefening. Selecteer om dit te doen **Bestand**  >  **Project opslaan.** Dit biedt u de mogelijkheid om de bron- en doelschema's offline te evalueren en herstel uit te voeren voordat u ze naar uw SQL database.

## <a name="migrate-the-databases"></a>De databases migreren

Nadat u uw databases hebt geëvalueerd en eventuele discrepanties hebt aangepakt, kunt u het migratieproces uitvoeren. Het migreren van gegevens is een bewerking voor bulksgewijs laden die rijen met gegevens in Azure SQL database in transacties verplaatst. Het aantal rijen dat moet worden geladen in uw SQL database in elke transactie wordt geconfigureerd in de projectinstellingen.

Ga als volgt te werk om uw schema te publiceren en de gegevens te migreren met behulp van SSMA voor toegang: 

1. Als u dit nog niet hebt gedaan, selecteert u Verbinding maken **met Azure SQL Database** en geeft u verbindingsgegevens op. 

1. Publiceer het schema. Klik in Azure SQL Database het deelvenster **Metadata Explorer** met de rechtermuisknop op de database met wie u werkt en selecteer vervolgens Synchroniseren met **database**. Met deze actie wordt het MySQL-schema naar de SQL database.

1. Controleer in **het deelvenster Synchroniseren met de database** de toewijzing tussen uw bronproject en uw doel:

   ![Schermopname van het deelvenster Synchroniseren met de database om de synchronisatie met de database te controleren.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Schakel in **het deelvenster Access Metadata Explorer** de selectievakjes in naast de items die u wilt migreren. Schakel het selectievakje naast de database in om de hele database te migreren. 

1. Migreert de gegevens. Klik met de rechtermuisknop op de database of het object dat u wilt migreren en selecteer **vervolgens Gegevens migreren.** U kunt ook het tabblad **Gegevens migreren** in de rechterbovenhoek selecteren.  

   Als u gegevens voor een hele database wilt migreren, selecteert u het selectievakje naast de databasenaam. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de database uit, vouwt u **Tabellen** uit en selecteert u vervolgens het selectievakje naast de tabel. Als u gegevens uit afzonderlijke tabellen wilt weglaten, moet u het selectievakje uit.

    ![Schermopname van de opdracht Gegevens migreren in het deelvenster Access Metadata Explorer.](./media/access-to-sql-database-guide/migrate-data.png)

1. Nadat de migratie is voltooid, bekijkt u **het gegevensmigratierapport**.  

    ![Schermopname van het deelvenster Gegevensrapport migreren met een voorbeeldrapport voor beoordeling.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Maak verbinding met Azure SQL database met behulp [van SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)en valideer de migratie door de gegevens en het schema te controleren.

   ![Schermopname van SQL Server Management Studio Objectverkenner voor het valideren van uw migratie in SSMA.](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>Postmigratie 

Nadat u de migratiefase hebt voltooid, moet u een reeks taken na de migratie uitvoeren om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt. 

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens naar de doelomgeving zijn gemigreerd, moeten alle toepassingen die voorheen de bron hebben gebruikt, het doel gaan gebruiken. Hiervoor moeten in sommige gevallen wijzigingen in de toepassingen worden aangebracht.

### <a name="perform-tests"></a>Tests uitvoeren

De testbenadering voor databasemigratie bestaat uit de volgende activiteiten:

1. **Validatietests ontwikkelen:** als u de databasemigratie wilt testen, moet u SQL-query's gebruiken. U moet de validatiequery's maken om uit te voeren op zowel de bron- als doeldatabase. Uw validatiequery's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

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

- Zie Service and tools for data migration (Service en hulpprogramma's voor gegevensmigratie) voor een matrix met services en hulpprogramma's van Microsoft en derden die u kunnen helpen bij verschillende database- en gegevensmigratiescenario's en speciale [taken.](../../../dms/dms-tools-matrix.md)

- Raadpleeg voor meer informatie over Azure SQL Database:
   - [Een overzicht van SQL Database](../../database/sql-database-paas-overview.md)
   - [Azure total cost of ownership-calculator](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het framework en de acceptatiecyclus voor cloudmigraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor het kosten en het formaat van workloads voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Cloudmigratie resources](https://azure.microsoft.com/migration/resources)


- Zie Data Access Migration Toolkit [(preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)om de toegangslaag voor de toepassing te beoordelen.
- Zie Overview of Database Experimentation Assistant (Overzicht van gegevenstoegangslaag A/B) voor meer informatie over het uitvoeren [van Database Experimentation Assistant.](/sql/dea/database-experimentation-assistant-overview)
