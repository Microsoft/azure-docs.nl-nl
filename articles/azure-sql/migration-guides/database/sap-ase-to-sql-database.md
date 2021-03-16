---
title: 'SAP ASE naar Azure SQL Database: migratie handleiding'
description: Deze hand leiding leert u uw SAP ASE-data bases te migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor SAP adapter server Enter prise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564921"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migratie handleiding: SAP ASE naar Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Deze hand leiding leert u uw SAP ASE-data bases te migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor SAP adapter server Enter prise.

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten 

Als u uw SAP SE-Data Base naar Azure SQL Database wilt migreren, hebt u het volgende nodig:

- u kunt controleren of uw bron omgeving wordt ondersteund. 
- [SQL Server Migration Assistant voor SAP Adaptive Server Enter prise (voorheen SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen.

### <a name="assess"></a>Evalueren

Gebruik [SQL Server Migration Assistant (SSMA) voor SAP Adaptive Server Enter prise (formeel SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) om database objecten en-gegevens te bekijken, data bases te beoordelen voor migratie, Sybase data base-objecten te migreren naar Azure SQL database en vervolgens gegevens te migreren naar Azure SQL database. Zie [SQL Server Migration Assistant voor Sybase (SybaseToSQL) voor](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)meer informatie.

Voer de volgende stappen uit om een evaluatie te maken: 

1. Open **SSMA voor Sybase**. 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam op, een locatie om uw project op te slaan en selecteer vervolgens Azure SQL Database als migratie doel in de vervolg keuzelijst. Selecteer **OK**.
1. Voer in het dialoog venster **verbinding maken met Sybase** de waarden voor SAP-verbindings gegevens in. 
1. Klik met de rechter muisknop op de SAP-data base die u wilt migreren en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd.
1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een inventaris van de DB2-objecten te verkrijgen en de vereiste inspanning om schema conversies uit te voeren. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Type toewijzingen valideren

Voordat u schema conversie uitvoert, worden de standaard toewijzingen voor gegevens typen gevalideerd of gewijzigd op basis van vereisten. U kunt dit doen door te navigeren naar het menu **extra** en **project instellingen** te kiezen of door de tabel te selecteren in de **SAP ASE-meta gegevens Verkenner**.


### <a name="convert-schema"></a>Schema converteren

Voer de volgende stappen uit om het schema te converteren:

1. Beschrijving Als u dynamische of ad-hoc query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en kiest u **instructie toevoegen**. 
1. Selecteer **verbinding maken met Azure SQL database** in de bovenste navigatie balk en geef Azure SQL database Details op. U kunt ervoor kiezen om verbinding te maken met een bestaande data base of een nieuwe naam op te geven. in dat geval wordt er een Data Base op de doel server gemaakt.
1. Klik met de rechter muisknop op het SAP ASE-schema in **Sybase meta gegevens Verkenner** en kies **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk. 
1. Vergelijk en controleer de structuur van het schema om potentiële problemen te identificeren. 

   Na schema conversie kunt u dit project lokaal opslaan voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema kunt publiceren naar Azure SQL Database.

Zie [schema converteren](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql) voor meer informatie.


## <a name="migrate"></a>Migrate 

Nadat u de vereiste onderdelen hebt geïnstalleerd en de taken hebt voltooid die zijn gekoppeld aan de fase **voorafgaand** aan de migratie, bent u klaar om het schema en de gegevens migratie uit te voeren.

Als u het schema wilt publiceren en de gegevens wilt migreren, voert u de volgende stappen uit: 

1. Klik met de rechter muisknop op de data base in **Azure SQL database meta gegevens Verkenner** en kies **synchroniseren met data base**.  Met deze actie wordt het SAP ASE-schema gepubliceerd naar het Azure SQL Database-exemplaar.
1. Klik met de rechter muisknop op het SAP ASE-schema in **SAP ASE Meta Data Explorer** en kies **gegevens migreren**.  U kunt ook **gegevens migreren** selecteren in de bovenste navigatie balk.  
1. Nadat de migratie is voltooid, raadpleegt u het **rapport gegevens migratie**: 
1. Valideer de migratie door de gegevens en het schema te bekijken op het Azure SQL Database-exemplaar met behulp van Azure SQL Database Management Studio (SSMS).


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

> [!NOTE]
> Zie voor meer informatie over deze problemen en specifieke stappen om ze te beperken de [hand leiding voor validatie na de migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).


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
