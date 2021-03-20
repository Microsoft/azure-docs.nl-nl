---
title: 'Toegang tot Azure SQL Database: migratie handleiding'
description: In deze hand leiding leert u hoe u uw micro soft Access-data bases kunt migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor toegang (SSMA for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e323b1c15d78da4e8c1a82ae8848df7f59b0dd87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657229"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Migratie handleiding: toegang tot Azure SQL Database

Deze migratie handleiding leert u uw micro soft Access-data bases te migreren naar Azure SQL Database met behulp van de SQL Server Migration Assistant voor toegang.

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten

Als u uw Access-Data Base naar Azure SQL Database wilt migreren, hebt u het volgende nodig:

- u kunt controleren of uw bron omgeving wordt ondersteund. 
- [SQL Server Migration Assistant voor toegang](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen.


### <a name="assess"></a>Evalueren 

Een evaluatie maken met behulp [van SQL Server Migration Assistant voor toegang](https://www.microsoft.com/download/details.aspx?id=54255). 

Voer de volgende stappen uit om een evaluatie te maken: 

1. Open SQL Server Migration Assistant voor toegang. 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. Geef een naam op voor uw migratie project. 

   ![Nieuw project kiezen](./media/access-to-sql-database-guide/new-project.png)

1. Selecteer **data bases toevoegen** en kies data bases die aan het nieuwe project moeten worden toegevoegd. 

   ![Data bases toevoegen kiezen](./media/access-to-sql-database-guide/add-databases.png)

1. Klik in **Access Meta Data Explorer** met de rechter muisknop op de data base en kies **rapport maken**. 

   ![Klik met de rechter muisknop op de data base en kies rapport maken.](./media/access-to-sql-database-guide/create-report.png)

1. Bekijk de voorbeeld evaluatie. Bijvoorbeeld: 

   ![De evaluatie van het voorbeeld rapport bekijken](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit:

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** . 

   ![Type toewijzingen](./media/access-to-sql-database-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **Access-meta gegevens Verkenner**.


### <a name="convert-schema"></a>Schema converteren

Voer de volgende stappen uit om database objecten te converteren: 

1. Selecteer **verbinding maken met Azure SQL database** en geef de verbindings gegevens op.

   ![Verbinding maken met Azure SQL Database](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Klik met de rechter muisknop op de data base in **Access-meta gegevens Verkenner** en kies **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u de Data Base hebt geselecteerd.

   ![Klik met de rechter muisknop op de data base en kies schema converteren](./media/access-to-sql-database-guide/convert-schema.png)

   Vergelijkt geconverteerde query's naar oorspronkelijke query's: 

   ![Geconverteerde query's kunnen worden vergeleken met de bron code](./media/access-to-sql-database-guide/query-comparison.png)

   Vergelijkt geconverteerde objecten met de oorspronkelijke objecten: 

   ![Geconverteerde objecten kunnen worden vergeleken met de bron](./media/access-to-sql-database-guide/table-comparison.png)

1. Beschrijving Als u een afzonderlijk object wilt converteren, klikt u met de rechter muisknop op het object en kiest u **schema converteren**. Geconverteerde objecten worden vet weer gegeven in de **Access Meta Data Explorer**: 

   ![Vetgedrukte objecten in meta gegevens Verkenner zijn geconverteerd](./media/access-to-sql-database-guide/converted-items.png)
 
1. Selecteer **resultaten controleren** in het deel venster uitvoer en Bekijk fouten in het deel venster **fouten lijst** . 


## <a name="migrate"></a>Migrate

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces. Het migreren van gegevens is een bewerking voor bulksgewijs laden waarmee rijen met gegevens worden verplaatst naar Azure SQL Database in trans acties. Het aantal rijen dat moet worden geladen in Azure SQL Database in elke trans actie, wordt geconfigureerd in de project instellingen.

Voer de volgende stappen uit om gegevens te migreren met behulp van SSMA voor toegang: 

1. Als u dat nog niet hebt gedaan, selecteert u **verbinding maken met Azure SQL database** en geeft u de verbindings gegevens op. 
1. Klik met de rechter muisknop op de data base in de **meta gegevens Verkenner van Azure SQL database** en kies **synchroniseren met data base**. Met deze actie wordt het MySQL-schema gepubliceerd naar Azure SQL Database.

   ![Synchroniseren met data base](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Controleer de toewijzing tussen uw bron project en uw doel:

   ![De synchronisatie met de data base controleren](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. **Access Meta Data Explorer** gebruiken om selectie vakjes in te checken naast de items die u wilt migreren. Als u de gehele Data Base wilt migreren, schakelt u het selectie vakje naast de data base in. 
1. Klik met de rechter muisknop op de data base of het object dat u wilt migreren en kies **gegevens migreren**. 
   Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u tabellen uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit.

    ![Gegevens migreren](./media/access-to-sql-database-guide/migrate-data.png)

    De gemigreerde gegevens controleren: 

    ![Gegevens controle migreren](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Maak verbinding met uw Azure SQL Database met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren.

   ![Valideren in SSMA](./media/access-to-sql-database-guide/validate-data.png)



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

| **Titel/koppeling**                                                                                                                                          | **Beschrijving**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote en kleine beoordelingen aanzienlijk sneller door middel van en geautomatiseerd en uniform platform besluitvormings proces. |


Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.

## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Raadpleeg voor meer informatie over Azure SQL Database:
   - [Een overzicht van SQL Database](../../database/sql-database-paas-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.

