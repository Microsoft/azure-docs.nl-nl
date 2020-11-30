---
title: DB2 om te SQL Server op virtuele machines van Azure (migratie handleiding)
description: Volg deze hand leiding voor het migreren van uw DB2-Server naar SQL Server op Azure-Vm's.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 5312ee08bad9d0b84e13c53fe4b1de59fad896f1
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326043"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Migratie handleiding: DB2 naar SQL Server op virtuele machines van Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

In deze migratie handleiding wordt u begeleid bij het migreren van uw gebruikers databases van DB2 naar SQL Server op Azure-Vm's met behulp van de SQL Server Migration Assistant voor DB2. 

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 


## <a name="prerequisites"></a>Vereisten

Als u de DB2-Data Base naar SQL Server wilt migreren, hebt u het volgende nodig:

- u kunt controleren of uw bron omgeving wordt ondersteund.
- [SQL Server Migration Assistant (SSMA) voor DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Connectiviteit](../../virtual-machines/windows/ways-to-connect-to-sql.md) tussen uw bron omgeving en uw SQL Server-VM in Azure. 



## <a name="pre-migration"></a>Premigratie

Nadat u aan de vereisten hebt voldaan, bent u klaar om de topologie van uw omgeving te ontdekken en de uitvoer baarheid van uw migratie te beoordelen. 

### <a name="assess"></a>Evalueren 

Een evaluatie maken met behulp van SQL Server Migration Assistant (SSMA). 

Voer de volgende stappen uit om een evaluatie te maken:

1. Open SQL Server Migration Assistant (SSMA) voor DB2. 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam, een locatie op voor het opslaan van uw project en selecteer vervolgens een SQL Server migratie doel in de vervolg keuzelijst. Selecteer **OK**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Geef project gegevens op en selecteer OK om op te slaan.":::


1. Voer in het dialoog venster **verbinding maken met DB2** waarden in voor de gegevens van de DB2-verbinding. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-db2.png" alt-text="Verbinding maken met uw DB2-exemplaar":::


1. Klik met de rechter muisknop op het DB2-schema dat u wilt migreren en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u het schema hebt geselecteerd. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Klik met de rechter muisknop op het schema en kies rapport maken.":::

1. Bekijk het HTML-rapport om de conversie statistieken en eventuele fouten of waarschuwingen te begrijpen. U kunt het rapport ook openen in Excel om een inventaris van de DB2-objecten te verkrijgen en de vereiste inspanning om schema conversies uit te voeren. De standaard locatie voor het rapport bevindt zich in de rapportmap in SSMAProjects.

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Bekijk het rapport om eventuele fouten of waarschuwingen te identificeren":::


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit: 

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** . 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Het schema selecteren en vervolgens type toewijzing":::

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **DB2-meta gegevens Verkenner**. 

### <a name="convert-schema"></a>Schema converteren 

Voer de volgende stappen uit om het schema te converteren:

1. Beschrijving Dynamische of ad-hoc query's toevoegen aan-instructies. Klik met de rechter muisknop op het knoop punt en kies vervolgens **instructies toevoegen**. 
1. Selecteer **verbinding maken met SQL Server**. 
    1. Voer de verbindings gegevens in om verbinding te maken met uw SQL Server-exemplaar op uw Azure-VM. 
    1. Kies ervoor om verbinding te maken met een bestaande Data Base op de doel server of geef een nieuwe naam op voor het maken van een nieuwe Data Base op de doel server. 
    1. Selecteer **Verbinden**. 

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Verbinding maken met uw SQL Server op Azure VM":::


1. Klik met de rechter muisknop op het schema en kies vervolgens **schema converteren**. U kunt ook **schema converteren** selecteren in de bovenste navigatie balk nadat u het schema hebt geselecteerd. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Klik met de rechter muisknop op het schema en kies schema converteren":::

1. Nadat de conversie is voltooid, vergelijkt en controleert u de structuur van het schema om potentiële problemen te identificeren en te verhelpen op basis van de aanbevelingen. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Vergelijk en controleer de structuur van het schema om potentiële problemen te identificeren en op te lossen op basis van aanbevelingen.":::

1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . 


## <a name="migrate"></a>Migreren

Nadat u klaar bent met het beoordelen van uw data bases en eventuele verschillen hebt opgelost, is de volgende stap het uitvoeren van het migratie proces.

Als u uw schema wilt publiceren en uw gegevens wilt migreren, voert u de volgende stappen uit:

1. Publiceer het schema: Klik met de rechter muisknop op de data base in het knoop punt **data bases** in de **SQL Server meta gegevens Verkenner** en kies **synchroniseren met data base**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Klik met de rechter muisknop op de data base en kies synchroniseren met data base":::

1. De gegevens migreren: Klik met de rechter muisknop op het schema in de **DB2-meta gegevens Verkenner** en kies **gegevens migreren**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Klik met de rechter muisknop op het schema en kies gegevens migreren.":::

1. Geef verbindings Details op voor de DB2-en SQL Server-exemplaren. 
1. Het **gegevens migratie rapport** weer geven. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Het gegevens migratie rapport controleren":::

1. Maak verbinding met uw SQL Server-exemplaar met behulp van SQL Server Management Studio en valideer de migratie door de gegevens en het schema te controleren. 

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

   > [!NOTE]
   > Voor hulp bij het ontwikkelen en uitvoeren van validatie tests na de migratie, moet u rekening houden met de oplossing voor gegevens kwaliteit die beschikbaar is via de partner [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 

## <a name="migration-assets"></a>Migratie-assets 

Raadpleeg de volgende bronnen voor meer hulp, die zijn ontwikkeld ter ondersteuning van een werkelijke migratie project betrokkenheid:

|Asset  |Beschrijving  |
|---------|---------|
|[Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote voor-en hand-evaluaties versnelt door het besluitvormings proces voor een geautomatiseerd en uniform doel platform te bieden.|
|[Detectie-en evaluatie pakket voor gegevens bronnen van DB2 zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Nadat u het SQL-script op een Data Base hebt uitgevoerd, kunt u de resultaten exporteren naar een bestand op het bestands systeem. Verschillende bestands indelingen worden ondersteund, met inbegrip van *. CSV, zodat u de resultaten kunt vastleggen in externe hulpprogram ma's zoals werk bladen. Deze methode kan nuttig zijn als u eenvoudig resultaten wilt delen met teams waarop de workbench niet is geïnstalleerd.|
|[IBM DB2 LUW inventaris scripts en artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Deze asset bevat een SQL-query die voldoet aan de IBM DB2 LUW-versie 11,1-systeem tabellen en biedt een telling van objecten per schema en object type, een ruwe schatting van ' onbewerkte gegevens ' in elk schema en de grootte van tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.|
|[DB2 LUW zuivere schaal op Azure-installatie handleiding](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Deze hand leiding fungeert als uitgangs punt voor een DB2-implementatie plan. Terwijl de bedrijfs vereisten verschillen, is hetzelfde basis patroon van toepassing. Dit architectuur patroon kan ook worden gebruikt voor OLAP-toepassingen op Azure.|

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.

## <a name="next-steps"></a>Volgende stappen

Na de migratie raadpleegt u de [hand leiding voor validatie na de migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

Zie [Data Base Migration](https://datamigration.microsoft.com/)(Engelstalig) voor andere migratie handleidingen. 

Zie voor video-inhoud:
- [De database migratie handleiding gebruiken](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/)
- [Overzicht van de migratie traject](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)