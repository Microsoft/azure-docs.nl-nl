---
title: 'Toegang tot Azure SQL Database: migratie handleiding'
description: In deze hand leiding leert u hoe u uw micro soft Access-data bases kunt migreren naar een Azure-SQL database met behulp van SQL Server Migration Assistant voor toegang (SSMA voor toegang).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f9fa2426e371ab9fd99e88979cbcbbb34adb00d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643585"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Migratie handleiding: toegang tot Azure SQL Database

In deze hand leiding leert u hoe u uw micro soft Access-Data Base kunt migreren naar een Azure-SQL database met behulp van SQL Server Migration Assistant voor toegang (SSMA voor toegang).

Zie voor andere migratie [handleidingen de Azure data base Migration Guide (Engelstalig](https://docs.microsoft.com/data-migration)). 

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het migreren van uw Access-Data Base naar een SQL database, gaat u als volgt te werk:

- Controleer of uw bron omgeving wordt ondersteund. 
- Down load en Installeer [SQL Server Migration Assistant voor toegang](https://www.microsoft.com/download/details.aspx?id=54255).
- Zorg ervoor dat u verbinding hebt en voldoende machtigingen hebt voor toegang tot zowel de bron als het doel.

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen.


### <a name="assess"></a>Evalueren 

Gebruik SSMA om toegang te krijgen tot data base-objecten en-gegevens en om data bases te evalueren voor migratie. 

Ga als volgt te werk om een evaluatie te maken: 

1. Open [SSMA voor toegang](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Selecteer **bestand** en selecteer vervolgens **Nieuw project**. 
1. Geef een project naam en een locatie voor het project op en selecteer vervolgens in de vervolg keuzelijst **Azure SQL database** als migratie doel. 
1. Selecteer **OK**. 

   ![Scherm afbeelding van het deel venster Nieuw project voor het invoeren van de naam en locatie van uw migratie project.](./media/access-to-sql-database-guide/new-project.png)

1. Selecteer **data bases toevoegen** en selecteer vervolgens de data bases die u aan het nieuwe project wilt toevoegen. 

   ![Scherm afbeelding van het tabblad ' data bases toevoegen ' in SSMA voor toegang.](./media/access-to-sql-database-guide/add-databases.png)

1. Klik in het deel venster **meta gegevens Verkenner** met de rechter muisknop op een Data Base en selecteer vervolgens **rapport maken**. U kunt ook het tabblad **rapport maken** selecteren in de rechter bovenhoek.

   ![Scherm opname van de opdracht ' rapport maken ' in Access Meta Data Explorer.](./media/access-to-sql-database-guide/create-report.png)

1. Bekijk het HTML-rapport om inzicht te krijgen in de conversie statistieken en eventuele fouten of waarschuwingen. U kunt het rapport ook openen in Excel om een overzicht van de toegangs objecten te krijgen en de vereiste inspanning te begrijpen voor het uitvoeren van schema-conversies. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects. Bijvoorbeeld:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Scherm opname van een voor beeld van een database rapport evaluatie in SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>De gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig ze op basis van uw vereisten, indien nodig. Hiervoor doet u het volgende:

1. Selecteer in SSMA voor toegang de optie **extra** en selecteer vervolgens **project instellingen**. 
1. Selecteer het tabblad **type toewijzing** . 

   ![Scherm afbeelding van het deel venster type mapping in SSMA voor toegang.](./media/access-to-sql-database-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel naam te selecteren in het deel venster **meta gegevens Verkenner** .


### <a name="convert-the-schema"></a>Het schema converteren

Ga als volgt te werk om database objecten te converteren: 

1. Selecteer het tabblad **verbinding maken met Azure SQL database** en Ga als volgt te werk:

   a. Voer de details in voor het maken van verbinding met uw SQL database.  
   b. Selecteer uw doel SQL database in de vervolg keuzelijst. U kunt ook een nieuwe naam invoeren. in dat geval wordt er een Data Base op de doel server gemaakt.  
   c. Geef verificatie Details op.   
   d. Selecteer **Verbinding maken**.

   ![Scherm opname van het deel venster ' verbinding maken met Azure SQL Database ' voor het invoeren van verbindings gegevens.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Klik in het deel venster **meta gegevens Verkenner** met de rechter muisknop op de data base en selecteer vervolgens **schema converteren**. U kunt ook uw data base selecteren en vervolgens het tabblad **schema converteren** selecteren.

   ![Scherm opname van de opdracht "schema converteren" in het deel venster "meta gegevens Verkenner".](./media/access-to-sql-database-guide/convert-schema.png)

1. Nadat de conversie is voltooid, vergelijkt u de geconverteerde objecten met de oorspronkelijke objecten om potentiële problemen te identificeren en de problemen op te lossen op basis van de aanbevelingen.

   ![Scherm afbeelding met een vergelijking van de geconverteerde objecten met de bron objecten.](./media/access-to-sql-database-guide/table-comparison.png)

    Vergelijk de geconverteerde Transact-SQL-tekst met de oorspronkelijke code en Bekijk de aanbevelingen.

   ![Scherm afbeelding met een vergelijking van geconverteerde query's naar de bron code.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Beschrijving Als u een afzonderlijk object wilt converteren, klikt u met de rechter muisknop op het object en selecteert u vervolgens **schema omzetten**. Geconverteerde objecten worden vet weer gegeven in **Access Meta Data Explorer**: 

   ![Scherm opname waarin wordt getoond dat de objecten in Access Meta Data Explorer worden geconverteerd.](./media/access-to-sql-database-guide/converted-items.png)
 
1. Selecteer in het deel venster **uitvoer** het pictogram **resultaten controleren** en controleer de fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer hiervoor **bestand**  >  **opslaan Project**. Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u ze naar uw SQL database publiceert.

## <a name="migrate-the-databases"></a>De data bases migreren

Nadat u uw data bases hebt beoordeeld en eventuele verschillen hebt opgelost, kunt u het migratie proces uitvoeren. Het migreren van gegevens is een bewerking voor bulksgewijs laden waarmee rijen met gegevens worden verplaatst naar een Azure-SQL database in trans acties. Het aantal rijen dat in de SQL database in de afzonderlijke trans acties moet worden geladen, wordt geconfigureerd in de project instellingen.

Ga als volgt te werk om uw schema te publiceren en de gegevens te migreren met behulp van SSMA voor toegang: 

1. Als u dit nog niet hebt gedaan, selecteert u **verbinding maken met Azure SQL database** en geeft u de verbindings gegevens op. 

1. Publiceer het schema. Klik in Azure SQL Database het deel venster **meta gegevens Verkenner** met de rechter muisknop op de data base waarmee u wilt werken en selecteer vervolgens **synchroniseren met data base**. Met deze actie wordt het MySQL-schema gepubliceerd naar het SQL database.

1. Controleer in het deel venster **met de data base synchroniseren** de toewijzing tussen uw bron project en uw doel:

   ![Scherm afbeelding van het deel venster "synchroniseren met data base" voor het controleren van de synchronisatie met de data base.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Schakel in het deel venster **meta gegevens Verkenner** de selectie vakjes in naast de items die u wilt migreren. Als u de gehele Data Base wilt migreren, schakelt u het selectie vakje naast de data base in. 

1. De gegevens migreren. Klik met de rechter muisknop op de data base of het object dat u wilt migreren en selecteer vervolgens **gegevens migreren**. U kunt ook het tabblad **gegevens migreren** in de rechter bovenhoek selecteren.  

   Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u **tabellen** uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit.

    ![Scherm opname van de opdracht ' gegevens migreren ' in het deel venster meta gegevens Verkenner.](./media/access-to-sql-database-guide/migrate-data.png)

1. Nadat de migratie is voltooid, bekijkt u het **rapport gegevens migratie**.  

    ![Scherm afbeelding van het deel venster gegevens rapport migreren met een voorbeeld rapport voor controle.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Maak verbinding met uw Azure-SQL database met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)en valideer de migratie door de gegevens en het schema te bekijken.

   ![Scherm opname van SQL Server Management Studio Objectverkenner voor het valideren van de migratie in SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.

## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van micro soft-Services en hulpprogram ma's van derden die beschikbaar zijn om u te helpen bij het maken van verschillende scenario's voor data base-en gegevens migratie en speciale taken.

- Raadpleeg voor meer informatie over Azure SQL Database:
   - [Een overzicht van SQL Database](../../database/sql-database-paas-overview.md)
   - [Azure total cost of ownership Calculator](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het berekenen en aanpassen van werk belastingen voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)over het beoordelen van de Application Access-laag.
- Zie [overzicht van database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test op het niveau van de toegang tot data-layer A/B.
