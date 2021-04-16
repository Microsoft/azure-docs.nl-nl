---
title: 'SAP ASE to Azure SQL Database: Migratiehandleiding'
description: In deze handleiding leert u hoe u uw SAP ASE-databases migreert naar een Azure SQL-database met behulp van SQL Server Migration Assistant voor SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0538071ffb9d244fb8b3493d6b63b27c6b56a726
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388530"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migratiehandleiding: SAP ASE naar Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze handleiding [](https://azure.microsoft.com/migration/migration-journey) leert u hoe u uw ASE-databases (SAP Adapter Server Enterprise) migreert naar een Azure SQL-database met behulp [van SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant voor SAP Adapter Server Enterprise.

Zie Azure Database [Migration Guide (Handleiding voor azure-databasemigratie) voor andere migratiehandleidingen.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Vereisten 

Voordat u begint met het migreren van uw SAP SE-database naar SQL database, gaat u als volgt te werk:

- Controleer of uw bronomgeving wordt ondersteund. 
- Download en installeer [SQL Server Migration Assistant voor SAP Adaptive Server Enterprise (voorheen SAP Sybase ASE).](https://www.microsoft.com/en-us/download/details.aspx?id=54256)
- Zorg ervoor dat u connectiviteit hebt en voldoende machtigingen hebt om toegang te krijgen tot zowel de bron als het doel.

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de haalbaarheid van uw [Azure-cloudmigratie te evalueren.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Evalueren

Met behulp [van SQL Server Migration Assistant (SSMA) voor SAP Adaptive Server Enterprise (formeel SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)kunt u databaseobjecten en -gegevens controleren, databases evalueren voor migratie, Sybase-databaseobjecten migreren naar uw SQL database en vervolgens gegevens migreren naar de SQL database. Zie voor meer informatie [SQL Server Migration Assistant voor Sybase (SybaseToSQL).](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)

Ga als volgt te werk om een evaluatie te maken: 

1. Open SSMA voor Sybase. 
1. Selecteer **Bestand** en selecteer vervolgens **Nieuw project**. 
1. Voer in **het deelvenster** Nieuw project een naam en locatie  in voor uw project en selecteer vervolgens in de vervolgkeuzelijst Migreren naar de **Azure SQL Database.** 
1. Selecteer **OK**.
1. Voer in **het deelvenster Verbinding maken met Sybase** de details van de SAP-verbinding in. 
1. Klik met de rechtermuisknop op de SAP-database die u wilt migreren en selecteer **rapport maken.** Hiermee wordt een HTML-rapport gegenereerd. U kunt ook het tabblad **Rapport maken** in de rechterbovenhoek selecteren.
1. Bekijk het HTML-rapport om inzicht te krijgen in de conversiestatistieken en eventuele fouten of waarschuwingen. U kunt het rapport ook openen in Excel om een inventaris op te halen van SAP ASE-objecten en de inspanning die nodig is om schemaconversies uit te voeren. De standaardlocatie voor het rapport bevindt zich in de rapportmap in SSMAProjects. Bijvoorbeeld:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>De typetoewijzingen valideren

Voordat u schemaconversie gaat uitvoeren, valideert u de standaardtoewijzingen voor gegevenstype of wijzigt u deze op basis van vereisten. U kunt dit doen door Tools Project Settings te selecteren, of u kunt de typetoewijzing voor elke tabel wijzigen door de tabel te selecteren  >  in de **SAP ASE Metadata Explorer.**

### <a name="convert-the-schema"></a>Het schema converteren

Ga als volgt te werk om het schema te converteren:

1. (Optioneel) Als u dynamische of gespecialiseerde query's wilt converteren, klikt u met de rechtermuisknop op het knooppunt en selecteert u **vervolgens Instructie toevoegen.** 
1. Selecteer het **tabblad Verbinding Azure SQL Database** maken en voer vervolgens de details voor uw SQL database. U kunt ervoor kiezen om verbinding te maken met een bestaande database of een nieuwe naam op te geven. In dat geval wordt er een database gemaakt op de doelserver.
1. Klik in **het deelvenster Sybase Metadata Explorer** met de rechtermuisknop op het SAP ASE-schema waar u mee werkt en selecteer schema **converteren.** 
1. Nadat het schema is geconverteerd, vergelijkt en controleert u de geconverteerde structuur naar de oorspronkelijke structuur om mogelijke problemen te identificeren. 

   Na de schemaconversie kunt u dit project lokaal opslaan voor een offline schema-hersteloefening. Selecteer om dit te doen **Bestand**  >  **Project opslaan.** Dit biedt u de mogelijkheid om de bron- en doelschema's offline te evalueren en herstel uit te voeren voordat u het schema naar uw SQL database.

1. Selecteer in **het** deelvenster Uitvoer de optie **Resultaten controleren** en bekijk eventuele fouten in het **lijstvenster** Fouten. 
1. Sla het project lokaal op voor een offline schema-hersteloefening. Selecteer om dit te doen **Bestand**  >  **Project opslaan.** Dit biedt u de mogelijkheid om de bron- en doelschema's offline te evalueren en herstel uit te voeren voordat u het schema naar uw SQL database.

## <a name="migrate-the-databases"></a>De databases migreren 

Nadat u de benodigde vereisten hebt uitgevoerd en  de taken hebt voltooid die zijn gekoppeld aan de fase vóór de migratie, bent u klaar om het schema en de gegevensmigratie uit te voeren.

Ga als volgt te werk om het schema te publiceren en de gegevens te migreren: 

1. Publiceer het schema. Klik in **Azure SQL Database het deelvenster Metadata Explorer** met de rechtermuisknop op de database en selecteer synchroniseren met **database.** Met deze actie wordt het SAP ASE-schema naar uw SQL database.

1. Migreert de gegevens. Klik in **het deelvenster SAP ASE Metadata Explorer** met de rechtermuisknop op de SAP ASE-database of het SAP ASE-object dat u wilt migreren en selecteer vervolgens Gegevens **migreren.** U kunt ook het tabblad **Gegevens migreren** in de rechterbovenhoek selecteren. 

   Als u gegevens voor een hele database wilt migreren, selecteert u het selectievakje naast de naam van de database. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de database uit, vouwt u **Tabellen** uit en selecteert u vervolgens het selectievakje naast de tabel. Als u gegevens uit afzonderlijke tabellen wilt weglaten, moet u het selectievakje uit. 
1. Nadat de migratie is voltooid, bekijkt u **het Gegevensmigratierapport.** 
1. Valideer de migratie door de gegevens en het schema te controleren. Om dit te doen, maakt u verbinding met uw SQL database met behulp [van SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="post-migration"></a>Postmigratie 

Nadat u de migratiefase hebt voltooid, moet u een reeks taken na de migratie uitvoeren om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt. 

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens naar de doelomgeving zijn gemigreerd, moeten alle toepassingen die voorheen de bron hebben gebruikt, het doel gaan gebruiken. Hiervoor moeten in sommige gevallen wijzigingen in de toepassingen worden aangebracht.

### <a name="perform-tests"></a>Tests uitvoeren

De testbenadering voor databasemigratie bestaat uit de volgende activiteiten:

1. **Validatietests ontwikkelen:** als u de databasemigratie wilt testen, moet u SQL-query's gebruiken. U moet de validatiequery's maken die u wilt uitvoeren op zowel de brondatabase als de doeldatabase. Uw validatiequery's moeten het bereik omvatten dat u hebt gedefinieerd.

1. **Een testomgeving instellen:** de testomgeving moet een kopie van de brondatabase en de doeldatabase bevatten. Zorg ervoor dat u de testomgeving isoleert.

1. **Validatietests uitvoeren:** voer validatietests uit op de bron en het doel en analyseer vervolgens de resultaten.

1. **Prestatietests uitvoeren:** voer prestatietests uit op basis van de bron en het doel, en analyseer en vergelijk de resultaten.


### <a name="optimize"></a>Optimaliseren

De postmigratiefase is van cruciaal belang voor het afstemmen van eventuele problemen met gegevensnauwkeurigheid, het controleren van de volledigheid en het oplossen van prestatieproblemen met de workload.

Zie de postmigratievalidatie- en optimalisatiehandleiding voor meer informatie over deze problemen en de stappen om deze [te verhelpen.](/sql/relational-databases/post-migration-validation-and-optimization-guide)


## <a name="next-steps"></a>Volgende stappen

- Zie Service and tools for data migration (Service en hulpprogramma's voor gegevensmigratie) voor een matrix met services en hulpprogramma's van Microsoft en derden die u kunnen helpen bij verschillende database- en gegevensmigratiescenario's en speciale [taken.](../../../dms/dms-tools-matrix.md)

- Voor meer informatie over Azure SQL Database, zie:
   - [Een overzicht van SQL Database](../../database/sql-database-paas-overview.md)
   - [Azure total cost of ownership-calculator](https://azure.microsoft.com/pricing/tco/calculator/)  

- Zie voor meer informatie over het framework en de acceptatiecyclus voor cloudmigraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor het kosten en het formaat van workloads voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Cloudmigratie resources](https://azure.microsoft.com/migration/resources)

- Zie Data Access Migration Toolkit [(preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)om de toegangslaag voor de toepassing te beoordelen.
- Zie voor meer informatie over het uitvoeren van Data Access Layer A/B-tests [Database Experimentation Assistant.](/sql/dea/database-experimentation-assistant-overview)
