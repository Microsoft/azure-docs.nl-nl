---
title: 'SAP ASE naar Azure SQL Database: migratie handleiding'
description: In deze hand leiding leert u hoe u uw SAP ASE-data bases naar een Azure-SQL database kunt migreren met behulp van SQL Server Migration Assistant voor SAP adapter server Enter prise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f4648c216a0b6d06309c0166aba501d4f3f02a10
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027513"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migratie handleiding: SAP ASE naar Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u [hoe](https://azure.microsoft.com/migration/migration-journey) u uw ASE-data bases (SAP adapter server Enter prise) kunt migreren naar een Azure-SQL database met behulp van [SQL Server-migratie](https://azure.microsoft.com/migration/migration-journey) -assistent voor SAP adapter server Enter prise.

Zie voor andere migratie [handleidingen de Azure data base Migration Guide (Engelstalig](https://docs.microsoft.com/data-migration)). 

## <a name="prerequisites"></a>Vereisten 

Ga als volgt te werk voordat u begint met het migreren van uw SAP SE-Data Base naar uw SQL database:

- Controleer of uw bron omgeving wordt ondersteund. 
- Down load en Installeer [SQL Server Migration Assistant voor SAP Adaptive Server Enter prise (voorheen SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Zorg ervoor dat u verbinding hebt en voldoende machtigingen hebt voor toegang tot zowel de bron als het doel.

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de haal baarheid van uw [Azure-Cloud migratie](https://azure.microsoft.com/migration)te beoordelen.

### <a name="assess"></a>Evalueren

Met behulp van [SQL Server Migration Assistant (SSMA) voor SAP Adaptive Server Enter prise (formeel SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)kunt u database objecten en-gegevens controleren, data bases voor migraties evalueren, Sybase-database objecten migreren naar uw SQL database en vervolgens gegevens migreren naar de SQL database. Zie [SQL Server Migration Assistant voor Sybase (SybaseToSQL) voor](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)meer informatie.

Ga als volgt te werk om een evaluatie te maken: 

1. Open SSMA voor Sybase. 
1. Selecteer **bestand** en selecteer vervolgens **Nieuw project**. 
1. Voer in het deel venster **Nieuw project** een naam en locatie in voor uw project en selecteer vervolgens in de vervolg keuzelijst **migreren naar** de optie **Azure SQL database**. 
1. Selecteer **OK**.
1. Voer in het deel venster **verbinding maken met Sybase** de SAP-verbindings gegevens in. 
1. Klik met de rechter muisknop op de SAP-data base die u wilt migreren en selecteer vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook het tabblad **rapport maken** selecteren in de rechter bovenhoek.
1. Bekijk het HTML-rapport om inzicht te krijgen in de conversie statistieken en eventuele fouten of waarschuwingen. U kunt het rapport ook openen in Excel om een inventarisatie te krijgen van SAP ASE-objecten en de inspanning die vereist is voor het uitvoeren van schema-conversies. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects. Bijvoorbeeld:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>De type toewijzingen valideren

Voordat u schema conversie uitvoert, valideert u de standaard toewijzingen voor gegevens typen of wijzigt u deze op basis van vereisten. U kunt dit doen door **extra**  >  **project instellingen** te selecteren, of u kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **ASE-meta gegevens Verkenner van SAP**.

### <a name="convert-the-schema"></a>Het schema converteren

Ga als volgt te werk om het schema te converteren:

1. Beschrijving Als u dynamische of gespecialiseerde query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en selecteert u vervolgens **instructie toevoegen**. 
1. Selecteer de **Azure SQL database tabblad verbinding maken** en voer de gegevens voor uw SQL database in. U kunt ervoor kiezen om verbinding te maken met een bestaande data base of een nieuwe naam op te geven. in dat geval wordt er een Data Base op de doel server gemaakt.
1. Klik in het deel venster **Sybase Meta Data Explorer** met de rechter muisknop op het SAP ASE-schema waarmee u werkt en selecteer vervolgens **schema omzetten**. 
1. Nadat het schema is geconverteerd, vergelijkt en controleert u de geconverteerde structuur naar de oorspronkelijke structuur om potentiële problemen te identificeren. 

   Na de schema conversie kunt u dit project lokaal opslaan voor een herbemiddeling van het offline schema. Selecteer hiervoor **bestand**  >  **opslaan Project**. Dit geeft u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema naar uw SQL database publiceert.

1. Selecteer in het deel venster **uitvoer** de optie **resultaten bekijken** en Bekijk eventuele fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer hiervoor **bestand**  >  **opslaan Project**. Dit geeft u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema naar uw SQL database publiceert.

## <a name="migrate-the-databases"></a>De data bases migreren 

Nadat u de vereiste onderdelen hebt geïnstalleerd en de taken hebt voltooid die zijn gekoppeld aan de fase *voorafgaand* aan de migratie, bent u klaar om het schema en de gegevens migratie uit te voeren.

Ga als volgt te werk om het schema te publiceren en de gegevens te migreren: 

1. Publiceer het schema. Klik in Azure SQL Database het deel venster **meta gegevens Verkenner** met de rechter muisknop op de data base en selecteer vervolgens **synchroniseren met data base**. Met deze actie wordt het SAP ASE-schema gepubliceerd naar uw SQL database.

1. De gegevens migreren. Klik in het deel venster **ASE meta gegevens Verkenner** met de rechter muisknop op de SAP ASE-data base of het object dat u wilt migreren en selecteer vervolgens **gegevens migreren**. U kunt ook het tabblad **gegevens migreren** in de rechter bovenhoek selecteren. 

   Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u **tabellen** uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit. 
1. Nadat de migratie is voltooid, bekijkt u het **rapport gegevens migratie**. 
1. Valideer de migratie door de gegevens en het schema te bekijken. Als u dit wilt doen, maakt u verbinding met uw SQL database met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Volgende stappen

- Zie [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van micro soft-Services en hulpprogram ma's van derden die beschikbaar zijn om u te helpen bij het maken van verschillende scenario's voor data base-en gegevens migratie en speciale taken.

- Voor meer informatie over Azure SQL Database raadpleegt u:
   - [Een overzicht van SQL Database](../../database/sql-database-paas-overview.md)
   - [Azure total cost of ownership Calculator](https://azure.microsoft.com/pricing/tco/calculator/)  

- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het berekenen en aanpassen van werk belastingen voor migratie naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Cloudmigratie resources](https://azure.microsoft.com/migration/resources)

- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)over het beoordelen van de Application Access-laag.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.
