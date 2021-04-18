---
title: 'Db2 naar SQL Server azure-VM: Migratiehandleiding'
description: In deze handleiding leert u hoe u uw IBM Db2-databases migreert naar SQL Server azure-VM met behulp van SQL Server Migration Assistant for Db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 43eff2bea6f6d95291e9ba9650ff42187e39fc70
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600162"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Migratiehandleiding: IBM Db2 to SQL Server on Azure VM
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

In deze handleiding leert u hoe u uw gebruikersdatabases migreert van IBM Db2 naar SQL Server op azure-VM's met behulp van de SQL Server Migration Assistant voor Db2. 

Zie Azure Database [Migration Guides (Handleidingen voor Azure Database Migration) voor andere migratiehandleidingen.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Vereisten

Als u uw Db2-database wilt SQL Server, hebt u het volgende nodig:

- Controleer of uw [bronomgeving wordt ondersteund.](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)
- [SQL Server Migration Assistant (SSMA) voor Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Connectiviteit](../../virtual-machines/windows/ways-to-connect-to-sql.md) tussen uw bronomgeving en uw virtuele SQL Server in Azure. 
- Een [doel-SQL Server op Azure VM](../../virtual-machines/windows/create-sql-vm-portal.md). 

## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de haalbaarheid van uw migratie te beoordelen. 

### <a name="assess"></a>Evalueren 

Gebruik SSMA voor DB2 om databaseobjecten en -gegevens te controleren en databases te evalueren voor migratie. 

Volg deze stappen om een evaluatie te maken:

1. Open [SSMA voor Db2.](https://www.microsoft.com/download/details.aspx?id=54254) 
1. Selecteer **Bestand**  >  **Nieuw project**. 
1. Geef een projectnaam en een locatie op om uw project op te slaan. Selecteer vervolgens een SQL Server migratiedoel in de vervolgkeuzelijst en selecteer **OK.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Schermopname met projectdetails die moeten worden opgegeven.":::


1. Voer **bij Verbinding maken met Db2** waarden in voor de Db2-verbindingsgegevens.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Schermopname met opties om verbinding te maken met uw Db2-exemplaar.":::


1. Klik met de rechtermuisknop op het Db2-schema dat u wilt migreren en kies **vervolgens Rapport maken.** Hiermee wordt een HTML-rapport gegenereerd. U kunt ook Rapport maken kiezen **in de** navigatiebalk nadat u het schema hebt geselecteerd.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Schermopname die laat zien hoe u een rapport maakt.":::

1. Bekijk het HTML-rapport om inzicht te krijgen in conversiestatistieken en eventuele fouten of waarschuwingen. U kunt het rapport ook openen in Excel om een inventaris op te halen van Db2-objecten en de inspanning die nodig is om schemaconversies uit te voeren. De standaardlocatie voor het rapport bevindt zich in de rapportmap in *SSMAProjects*.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Schermopname van het rapport dat u bekijkt om eventuele fouten of waarschuwingen te identificeren.":::


### <a name="validate-data-types"></a>Gegevenstypen valideren

Valideer de standaardtoewijzingen voor gegevenstype en wijzig deze op basis van de vereisten, indien nodig. Voer hiervoor de volgende stappen uit: 

1. Selecteer **Hulpprogramma's** in het menu. 
1. Selecteer **Projectinstellingen.** 
1. Selecteer het **tabblad Typetoewijzingen.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Schermopname van het selecteren van het schema en de typetoewijzing.":::

1. U kunt de typetoewijzing voor elke tabel wijzigen door de tabel in **Db2 Metadata Explorer te selecteren.** 

### <a name="convert-schema"></a>Schema converteren 

Volg deze stappen om het schema te converteren:

1. (Optioneel) Dynamische of ad-hocquery's toevoegen aan -instructies. Klik met de rechtermuisknop op het knooppunt en kies vervolgens **Instructies toevoegen.** 
1. Selecteer **Verbinding maken met SQL Server**. 
    1. Voer verbindingsgegevens in om verbinding te maken met uw exemplaar van SQL Server op uw Azure-VM. 
    1. Kies ervoor om verbinding te maken met een bestaande database op de doelserver of geef een nieuwe naam op om een nieuwe database op de doelserver te maken. 
    1. Geef verificatiegegevens op. 
    1. Selecteer **Verbinding maken**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Schermopname met de details die nodig zijn om verbinding te maken met SQL Server azure-VM.":::

1. Klik met de rechtermuisknop op het schema en kies **schema converteren.** U kunt ook Schema **converteren** kiezen in de bovenste navigatiebalk nadat u het schema hebt geselecteerd.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Schermopname van het selecteren van het schema en het converteren ervan.":::

1. Nadat de conversie is voltooien, vergelijkt en controleert u de structuur van het schema om mogelijke problemen te identificeren. De problemen oplossen op basis van de aanbevelingen. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Schermopname van het vergelijken en controleren van de structuur van het schema om mogelijke problemen te identificeren.":::

1. Selecteer in **het** deelvenster Uitvoer de optie **Resultaten bekijken.** Bekijk fouten **in het** deelvenster Foutenlijst. 
1. Sla het project lokaal op voor een offline schema-hersteloefening. Selecteer In **het** menu Bestand de optie **Project opslaan.** Dit biedt u de mogelijkheid om de bron- en doelschema's offline te evalueren en herstel uit te voeren voordat u het schema kunt publiceren naar SQL Server azure-VM.

## <a name="migrate"></a>Migrate

Nadat u uw databases hebt beoordeeld en eventuele discrepanties hebt aangepakt, bestaat de volgende stap uit het uitvoeren van het migratieproces.

Volg deze stappen om uw schema te publiceren en uw gegevens te migreren:

1. Publiceer het schema. Klik **SQL Server metagegevensverkenner** in **het knooppunt Databases** met de rechtermuisknop op de database. Selecteer vervolgens **Synchroniseren met database**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Schermopname met de optie om te synchroniseren met de database.":::

1. Migreert de gegevens. Klik met de rechtermuisknop op de database of het object dat u wilt migreren in **Db2 Metadata Explorer** en kies **Gegevens migreren.** U kunt ook Gegevens migreren **selecteren** in de navigatiebalk. Als u gegevens voor een hele database wilt migreren, selecteert u het selectievakje naast de databasenaam. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de database uit, vouwt u **Tabellen** uit en selecteert u vervolgens het selectievakje naast de tabel. Als u gegevens uit afzonderlijke tabellen wilt weglaten, moet u het selectievakje uit.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Schermopname van het selecteren van het schema en het kiezen om gegevens te migreren.":::

1. Geef verbindingsgegevens op voor de Db2- en SQL Server s. 
1. Nadat de migratie is voltooien, bekijkt u het **gegevensmigratierapport**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Schermopname die laat zien waar u het gegevensmigratierapport kunt bekijken.":::

1.  Maak verbinding met uw exemplaar van SQL Server azure-VM met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Valideer de migratie door de gegevens en het schema te controleren.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Schermopname van het vergelijken van het schema in SQL Server Management Studio.":::

## <a name="post-migration"></a>Postmigratie 

Nadat de migratie is voltooid, moet u een reeks taken na de migratie uitvoeren om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen 

Nadat de gegevens naar de doelomgeving zijn gemigreerd, moeten alle toepassingen die voorheen de bron hebben gebruikt, het doel gaan gebruiken. Hiervoor zijn in sommige gevallen wijzigingen in de toepassingen vereist.

### <a name="perform-tests"></a>Tests uitvoeren

Testen bestaat uit de volgende activiteiten:

1. **Validatietests ontwikkelen:** als u databasemigratie wilt testen, moet u SQL-query's gebruiken. U moet de validatiequery's maken die moeten worden uitgevoerd op zowel de bron- als de doeldatabase. Uw validatiequery's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.
1. **De testomgeving instellen:** de testomgeving moet een kopie van de brondatabase en de doeldatabase bevatten. Zorg ervoor dat u de testomgeving isoleert.
1. **Validatietests uitvoeren:** voer de validatietests uit op de bron en het doel en analyseer vervolgens de resultaten.
1. **Prestatietests uitvoeren:** voer prestatietests uit op de bron en het doel en analyseer en vergelijk de resultaten.

## <a name="migration-assets"></a>Migratie-assets 

Zie de volgende resources, die zijn ontwikkeld ter ondersteuning van een echte migratieprojectbetrokkenheid, voor meer hulp:

|Asset  |Description  |
|---------|---------|
|[Model en hulpprogramma voor evaluatie van gegevensworkloads](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulpprogramma biedt voorgestelde 'best fit' doelplatforms, cloud-gereedheid en herstelniveau voor toepassingen/databases voor een bepaalde workload. Het biedt eenvoudige berekening met één klik en het genereren van een rapport waarmee u grote evaluaties van onroerend goed kunt versnellen door een geautomatiseerd en uniform platformbesluitproces te bieden.|
|[Db2 zOS-gegevensactiva detectie- en evaluatiepakket](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Nadat u het SQL-script in een database hebt uitgevoerd, kunt u de resultaten exporteren naar een bestand in het bestandssysteem. Er worden verschillende bestandsindelingen ondersteund, waaronder *.csv, zodat u de resultaten kunt vastleggen in externe hulpprogramma's zoals spreadsheets. Deze methode kan handig zijn als u eenvoudig resultaten wilt delen met teams waarop workbench niet is geïnstalleerd.|
|[IBM Db2 LUW-inventarisscripts en -artefacten](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Deze asset bevat een SQL-query die ibm Db2 LUW-systeemtabellen van versie 11.1 raakt en een telling biedt van objecten per schema en objecttype, een ruwe schatting van 'onbewerkte gegevens' in elk schema en de formaat van tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.|
|[Db2 LUW pure scale on Azure - installatiehandleiding](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Deze handleiding fungeert als uitgangspunt voor een Db2-implementatieplan. Hoewel de bedrijfsvereisten verschillen, is hetzelfde basispatroon van toepassing. Dit architectuurpatroon kan ook worden gebruikt voor OLAP-toepassingen in Azure.|

Het Data SQL Engineering-team heeft deze resources ontwikkeld. De kern van dit team is het deblokkeren en versnellen van complexe modernisering voor gegevensplatformmigratieprojecten naar het Azure-gegevensplatform van Microsoft.

## <a name="next-steps"></a>Volgende stappen

Na de migratie bekijkt u de [postmigratievalidatie- en optimalisatiehandleiding.](/sql/relational-databases/post-migration-validation-and-optimization-guide) 

Zie Services en hulpprogramma's voor gegevensmigratie voor services en hulpprogramma's van Microsoft en derden die u kunnen helpen bij verschillende database- en [gegevensmigratiescenario's.](../../../dms/dms-tools-matrix.md)

Zie Overzicht van het migratietraject [voor video-inhoud.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
