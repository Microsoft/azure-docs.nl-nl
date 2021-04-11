---
title: 'Db2 naar SQL Server op virtuele machines van Azure: migratie handleiding'
description: In deze hand leiding leert u hoe u uw Db2-Data Base kunt migreren naar SQL Server op virtuele Azure-machines met behulp van SQL Server Migration Assistant voor Db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 003448255a82d0062e9abc3c358a47687cd5ae90
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644201"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Migratie handleiding: Db2 naar SQL Server op virtuele machines van Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

In deze migratie handleiding wordt u begeleid bij het migreren van uw gebruikers databases van Db2 naar SQL Server op Azure-Vm's met behulp van de SQL Server Migration Assistant voor Db2. 

Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 


## <a name="prerequisites"></a>Vereisten

Als u de Db2-Data Base naar SQL Server wilt migreren, hebt u het volgende nodig:

- u kunt controleren of uw [bron omgeving wordt ondersteund](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites).
- [SQL Server Migration Assistant (SSMA) voor Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Connectiviteit](../../virtual-machines/windows/ways-to-connect-to-sql.md) tussen uw bron omgeving en uw SQL Server-VM in Azure. 
- Een doel- [SQL Server op de Azure-VM](../../virtual-machines/windows/create-sql-vm-portal.md). 



## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. 


### <a name="assess"></a>Evalueren 

Gebruik SQL Server Migration Assistant (SSMA) voor DB2 om database objecten en-gegevens te controleren en data bases te evalueren voor migratie. 

Voer de volgende stappen uit om een evaluatie te maken:

1. Open [SQL Server Migration Assistant (SSMA) voor Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam, een locatie op voor het opslaan van uw project en selecteer vervolgens een SQL Server migratie doel in de vervolg keuzelijst. Selecteer **OK**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Geef project gegevens op en selecteer OK om op te slaan.":::


1. Voer waarden in voor de gegevens van de Db2-verbinding in het dialoog venster **verbinding maken met Db2** :

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Verbinding maken met uw Db2-exemplaar":::


1. Klik met de rechter muisknop op het Db2-schema dat u wilt migreren en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u het schema hebt geselecteerd:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Klik met de rechter muisknop op het schema en kies rapport maken.":::

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een inventaris van de Db2-objecten te verkrijgen en de vereiste inspanning om schema conversies uit te voeren. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Bekijk het rapport om eventuele fouten of waarschuwingen te identificeren":::


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit: 

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** :

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Het schema selecteren en vervolgens type toewijzing":::

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **Db2-meta gegevens Verkenner**. 

### <a name="convert-schema"></a>Schema converteren 

Voer de volgende stappen uit om het schema te converteren:

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en kies vervolgens **instructies toevoegen**. 
1. Selecteer **verbinding maken met SQL Server**. 
    1. Voer de verbindings gegevens in om verbinding te maken met uw SQL Server-exemplaar op uw Azure-VM. 
    1. Kies ervoor om verbinding te maken met een bestaande Data Base op de doel server of geef een nieuwe naam op voor het maken van een nieuwe Data Base op de doel server. 
    1. Geef verificatie Details op. 
    1. Selecteer **verbinding maken**:

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Verbinding maken met uw SQL Server op Azure VM":::

1. Klik met de rechter muisknop op het schema en kies vervolgens **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u het schema hebt geselecteerd:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Klik met de rechter muisknop op het schema en kies schema converteren":::

1. Nadat de conversie is voltooid, vergelijkt en controleert u de structuur van het schema om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen: 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Vergelijk en controleer de structuur van het schema om potentiële problemen te identificeren en op te lossen op basis van aanbevelingen.":::

1. Selecteer **resultaten controleren** in het deel venster uitvoer en Bekijk fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema kunt publiceren naar SQL Server op Azure VM.


## <a name="migrate"></a>Migrate

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces.

Als u uw schema wilt publiceren en uw gegevens wilt migreren, voert u de volgende stappen uit:

1. Publiceer het schema: Klik met de rechter muisknop op de data base in het knoop punt **data bases** in de **SQL Server meta gegevens Verkenner** en kies **synchroniseren met data base**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Klik met de rechter muisknop op de data base en kies synchroniseren met data base":::

1. De gegevens migreren: Klik met de rechter muisknop op de data base of het object dat u wilt migreren in de **Db2-meta gegevens Verkenner** en kies **gegevens migreren**. U kunt ook **gegevens migreren** selecteren in de bovenste navigatie balk. Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u tabellen uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u het selectie vakje uit:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Klik met de rechter muisknop op het schema en kies gegevens migreren.":::

1. Geef verbindings Details op voor de Db2-en SQL Server-exemplaren. 
1. Nadat de migratie is voltooid, raadpleegt u het **rapport gegevens migratie**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Het gegevens migratie rapport controleren":::

1.  Maak verbinding met uw SQL Server op een Azure VM-exemplaar met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en valideer de migratie door de gegevens en het schema te controleren:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Het schema in SSMS vergelijken":::

## <a name="post-migration"></a>Postmigratie 

Nadat u de migratie fase hebt voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen 

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit de volgende activiteiten:

1. **Validatie tests ontwikkelen**: als u database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **Test omgeving instellen**: de test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.
1. **Validatie tests uitvoeren**: Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Prestatie testen uitvoeren**: prestaties testen op basis van de bron en het doel, en vervolgens de resultaten analyseren en vergelijken.


## <a name="migration-assets"></a>Migratie-assets 

Raadpleeg de volgende bronnen voor meer hulp, die zijn ontwikkeld ter ondersteuning van een werkelijke migratie project betrokkenheid:

|Asset  |Description  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote voor-en hand-evaluaties versnelt door het besluitvormings proces voor een geautomatiseerd en uniform doel platform te bieden.|
|[Detectie-en evaluatie pakket voor gegevens bronnen van Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Nadat u het SQL-script op een Data Base hebt uitgevoerd, kunt u de resultaten exporteren naar een bestand op het bestands systeem. Verschillende bestands indelingen worden ondersteund, met inbegrip van *. CSV, zodat u de resultaten kunt vastleggen in externe hulpprogram ma's zoals werk bladen. Deze methode kan nuttig zijn als u eenvoudig resultaten wilt delen met teams waarop de workbench niet is geïnstalleerd.|
|[IBM Db2 LUW inventaris scripts en artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Deze asset bevat een SQL-query die voldoet aan de IBM Db2 LUW-versie 11,1-systeem tabellen en biedt een telling van objecten per schema en object type, een ruwe schatting van ' onbewerkte gegevens ' in elk schema en de grootte van tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.|
|[Db2 LUW zuivere schaal op Azure-installatie handleiding](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/db2%20PureScale%20on%20Azure.pdf)|Deze hand leiding fungeert als uitgangs punt voor een Db2-implementatie plan. Terwijl de bedrijfs vereisten verschillen, is hetzelfde basis patroon van toepassing. Dit architectuur patroon kan ook worden gebruikt voor OLAP-toepassingen op Azure.|

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Azure-gegevens platform van micro soft.

## <a name="next-steps"></a>Volgende stappen

Na de migratie raadpleegt u de [hand leiding voor validatie na de migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

Zie voor video-inhoud:
- [Overzicht van de migratie traject](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)