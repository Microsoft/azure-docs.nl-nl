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
ms.openlocfilehash: d59a4fc2cbf52d95e6b5a5100b5ffe06c5dbed7e
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564924"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Migratie handleiding: MySQL naar Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u hoe u uw MySQL-data base kunt migreren naar Azure SQL Database met behulp van SQL Server Migration Assistant voor MySQL (SSMA voor MySQL). 

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten

Als u uw MySQL-data base naar Azure SQL Database wilt migreren, hebt u het volgende nodig:

- u kunt controleren of uw bron omgeving wordt ondersteund. Op dit moment wordt MySQL 5,6 en 5,7 ondersteund. 
- [SQL Server Migration Assistant voor MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>Premigratie 

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen.

### <a name="assess"></a>Evalueren 

Met behulp van [SQL Server Migration Assistant voor mysql](https://www.microsoft.com/download/confirmation.aspx?id=54257)kunt u database objecten en-gegevens bekijken en data bases voor migratie beoordelen.

Voer de volgende stappen uit om een evaluatie te maken.

1. Open SQL Server Migration Assistant voor MySQL. 
1. Selecteer **bestand** in het menu en kies vervolgens **Nieuw project**. Geef een naam op voor het project, een locatie om uw project op te slaan.
1. Kies **Azure SQL database** als migratie doel. 
1. Kies **verbinding maken met MySQL** en geef de verbindings gegevens op om verbinding te maken met uw MySQL-server. 
1. Klik met de rechter muisknop op het MySQL-schema in **MySQL Meta Data Explorer** en kies **rapport maken**. U kunt ook **rapport maken** selecteren in de bovenste navigatie balk. 
1. Bekijk het HTML-rapport voor conversie statistieken, evenals fouten en waarschuwingen. Analyseer het om conversie problemen en oplossingen te begrijpen. 

   Dit rapport kan ook worden geopend vanuit de map SSMA projects, zoals geselecteerd in het eerste scherm. In het bovenstaande voor beeld gaat u naar het report.xml-bestand van:
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   en open het in Excel om een overzicht te krijgen van MySQL-objecten en de inspanningen die nodig zijn voor het uitvoeren van schema-conversies.

### <a name="validate-data-types"></a>Gegevens typen valideren

Voordat u schema conversie uitvoert, worden de standaard toewijzingen voor gegevens typen gevalideerd of gewijzigd op basis van vereisten. U kunt dit doen door te navigeren naar het menu Hulpprogram Ma's en project instellingen te kiezen of door de tabel te selecteren in de ' MySQL-meta gegevens Verkenner '.

### <a name="convert-schema"></a>Schema converteren 

Voer de volgende stappen uit om het schema te converteren: 

1. Beschrijving Als u dynamische of ad-hoc query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en kiest u **instructie toevoegen**. 
1. Kies **verbinding maken met Azure SQL database** in de bovenste navigatie balk en geef de verbindings gegevens op. U kunt ervoor kiezen om verbinding te maken met een bestaande data base of een nieuwe naam op te geven. in dat geval wordt er een Data Base op de doel server gemaakt.
1. Klik met de rechter muisknop op het schema en kies **schema converteren**. 
1. Nadat het schema is geconverteerd, vergelijkt u de geconverteerde code met de oorspronkelijke code om potentiële problemen te identificeren. 



## <a name="migrate"></a>Migrate 

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces. Migratie omvat twee stappen: het schema publiceren en de gegevens migreren. 

Als u het schema wilt publiceren en de gegevens wilt migreren, voert u de volgende stappen uit: 

1. Klik met de rechter muisknop op de data base in de **meta gegevens Verkenner van Azure SQL database** en kies **synchroniseren met data base**. Met deze actie wordt het MySQL-schema gepubliceerd naar Azure SQL Database.
1. Klik met de rechter muisknop op het MySQL-schema in de **MySQL-meta gegevens Verkenner** en kies **gegevens migreren**. U kunt ook **migreren van gegevens** uit de navigatie op de bovenste regel selecteren. 
1. Nadat de migratie is voltooid, raadpleegt u het rapport **gegevens migratie** : 
1.  Valideer de migratie door de gegevens en het schema op Azure SQL Database te controleren met behulp van SQL Server Management Studio (SSMS).


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
