---
title: 'Oracle to SQL Server in azure Virtual Machines: migratie handleiding'
description: In deze hand leiding leert u hoe u uw Oracle-schema's naar SQL Server op Azure Virtual Machines kunt migreren met behulp van SQL Server Migration Assistant voor Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961248"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Migratie handleiding: Oracle naar SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u hoe u uw Oracle-schema's naar SQL Server op Azure Virtual Machines kunt migreren met behulp van SQL Server Migration Assistant voor Oracle. 

Zie [Data Base Migration](https://docs.microsoft.com/data-migration)(Engelstalig) voor andere migratie handleidingen. 

## <a name="prerequisites"></a>Vereisten 

Als u uw Oracle-schema wilt migreren naar SQL Server op Azure Virtual Machines, hebt u het volgende nodig:

- Een ondersteunde bron omgeving.
- [SQL Server Migration Assistant (SSMA) voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Een doel- [SQL Server-VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- De [benodigde machtigingen voor SSMA voor Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) en de [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Connectiviteit en voldoende machtigingen voor toegang tot de bron en het doel. 


## <a name="pre-migration"></a>Premigratie

U kunt de migratie naar de Cloud voorbereiden door te controleren of uw bron omgeving wordt ondersteund en dat u alle vereiste onderdelen hebt behandeld. Dit helpt om een efficiënte en geslaagde migratie te garanderen.

Dit gedeelte van het proces omvat: 
- Het uitvoeren van een inventarisatie van de data bases die u nodig hebt om te migreren.
- Deze data bases worden beoordeeld op potentiële migratie problemen of blok keringen. 
- Eventuele problemen oplossen die u hebt gedetecteerd. 

### <a name="discover"></a>Ontdekken

Gebruik [kaart Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) om bestaande gegevens bronnen te identificeren en Details over de functies die uw bedrijf gebruikt. Op die manier krijgt u een beter inzicht in de migratie en helpt u het te plannen. Dit proces omvat het scannen van het netwerk om de Oracle-instanties van uw organisatie te identificeren en de versies en functies die u gebruikt.

Ga als volgt te werk om met de kaart Toolkit een inventaris scan uit te voeren: 


1. Open [kaart Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Selecteer **Data Base maken/selecteren**:

   ![Scherm afbeelding met de optie Create/Select data base.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Selecteer **een inventarisatie database maken**. Voer een naam in voor de nieuwe inventarisatie database die u maakt, geef een korte beschrijving op en selecteer **OK**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Scherm opname van de interface voor het maken van een inventarisatie database.":::

1. Selecteer **inventaris gegevens verzamelen** om de **wizard inventaris en analyse** te openen:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Scherm opname van de koppeling inventaris gegevens verzamelen.":::


1. Selecteer in de **wizard inventaris en analyse** de optie **Oracle** en selecteer vervolgens **volgende**:

   ![Scherm opname van de pagina inventarisatie Scenario's van de wizard inventaris en analyse.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Selecteer de optie voor het zoeken naar computers die het beste past bij uw bedrijfs behoeften en-omgeving, en selecteer vervolgens **volgende**: 

   ![Scherm opname van de pagina detectie methoden van de wizard inventaris en analyse.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Voer de referenties in of maak nieuwe referenties voor de systemen die u wilt verkennen, en selecteer vervolgens **volgende**:

    ![Scherm afbeelding met de pagina referenties van alle computers van de wizard inventaris en analyse.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Stel de volg orde van de referenties in en selecteer **volgende**: 

   ![Scherm opname van de pagina met de referenties van de wizard inventaris en analyse.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Voer de referenties in voor elke computer die u wilt detecteren. U kunt unieke referenties gebruiken voor elke computer/computer, of u kunt de lijst met alle computers referenties gebruiken.  

   ![Scherm opname van de pagina computers en referenties opgeven van de wizard inventaris en analyse.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Controleer uw selecties en selecteer vervolgens **volt ooien**:

   ![Scherm opname van de pagina samen vatting van de wizard inventaris en analyse.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Nadat de scan is voltooid, bekijkt u de samen vatting van de **gegevens verzameling** . Het scannen kan enkele minuten duren, afhankelijk van het aantal data bases. Selecteer **sluiten** wanneer u klaar bent: 

   ![Scherm opname van het samen vatting van de gegevens verzameling.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Selecteer **Opties** om een rapport te genereren over de Oracle-evaluatie en database Details. Selecteer beide opties, één per keer, om het rapport te genereren.


### <a name="assess"></a>Evalueren

Nadat u de gegevens bronnen hebt geïdentificeerd, gebruikt u [SQL Server Migration Assistant voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) om de Oracle-exemplaren te evalueren die naar de virtuele machine van SQL Server worden gemigreerd. De-assistent helpt u bij het begrijpen van de hiaten tussen de bron-en doel database. U kunt database objecten en-gegevens bekijken, data bases beoordelen voor migratie, database objecten migreren naar SQL Server en vervolgens gegevens migreren naar SQL Server.

Voer de volgende stappen uit om een evaluatie te maken: 


1. Open [SQL Server Migration Assistant voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecteer in het menu **bestand** de optie **Nieuw project**. 
1. Geef een project naam en locatie op voor uw project en selecteer vervolgens een SQL Server migratie doel in de lijst. Selecteer **OK**: 

   ![Scherm opname van het dialoog venster Nieuw project.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Selecteer **verbinding maken met Oracle**. Voer waarden in voor de Oracle-verbinding in het dialoog venster **verbinding maken met Oracle** :

   ![Scherm afbeelding die het dialoog venster verbinding maken met Oracle weergeeft.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Selecteer de Oracle-schema's die u wilt migreren: 

   ![Scherm opname van de lijst met Oracle-schema's die kunnen worden gemigreerd.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. Klik in **Oracle Meta Data Explorer** met de rechter muisknop op het Oracle-schema dat u wilt migreren en selecteer vervolgens **rapport maken**. Er wordt dan een HTML-rapport gegenereerd. U kunt ook de data base selecteren en vervolgens **rapport maken** selecteren in het bovenste menu.

   ![Scherm afbeelding die laat zien hoe u een rapport maakt.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Bekijk het HTML-rapport voor conversie statistieken, fouten en waarschuwingen. Analyseer deze om conversie problemen en oplossingen te begrijpen.

    U kunt het rapport ook openen in Excel om een overzicht te krijgen van Oracle-objecten en de inspanningen die nodig zijn voor het volt ooien van schema conversies. De standaard locatie voor het rapport is de rapportmap in SSMAProjects. 

   Bijvoorbeeld: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Scherm afbeelding met een conversie rapport.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig ze op basis van vereisten, indien nodig. Voer hiervoor de volgende stappen uit: 


1. Selecteer **project instellingen** in het menu **extra** . 
1. Selecteer het tabblad **type toewijzingen** . 

   ![Scherm opname van het tabblad type toewijzingen.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in **Oracle-meta gegevens Verkenner**. 

### <a name="convert-the-schema"></a>Het schema converteren

Voer de volgende stappen uit om het schema te converteren: 

1. Beschrijving Als u dynamische of ad-hoc query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en selecteert u **instructie toevoegen**.

1. Selecteer **verbinding maken met SQL Server** in het bovenste menu. 
     1. Voer de verbindings gegevens in voor uw SQL Server op de Azure-VM. 
     1. Selecteer de doel database in de lijst of geef een nieuwe naam op. Als u een nieuwe naam opgeeft, wordt er een Data Base op de doel server gemaakt. 
     1. Geef verificatie Details op. 
     1. Selecteer **Verbinding maken**. 


   ![Scherm afbeelding die laat zien hoe u verbinding maakt met SQL Server.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Klik met de rechter muisknop op het Oracle-schema in **Oracle Meta Data Explorer** en selecteer **schema converteren**. U kunt ook **schema converteren** selecteren in het bovenste menu:

   ![Scherm afbeelding die laat zien hoe het schema moet worden geconverteerd.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Nadat de schema conversie is voltooid, controleert u de geconverteerde objecten en vergelijkt u deze met de oorspronkelijke objecten om potentiële problemen te identificeren. Gebruik de aanbevelingen om problemen op te lossen:

   ![Scherm afbeelding met een vergelijking van twee schema's.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Vergelijk de geconverteerde Transact-SQL-tekst met de originele opgeslagen procedures en Bekijk de aanbevelingen: 

   ![Scherm afbeelding waarin Transact-SQL, opgeslagen procedures en een waarschuwing worden weer gegeven.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   U kunt het project lokaal opslaan voor een herbemiddeling van het offline schema. Hiertoe selecteert u **project opslaan** in het menu **bestand** . Als u het project lokaal opslaat, kunt u de bron-en doel schema's offline evalueren en het herstel uitvoeren voordat u het schema publiceert naar SQL Server.

1. Selecteer **resultaten controleren** in het deel venster **uitvoer** en controleer vervolgens de fouten in het deel venster **fouten lijst** . 
1. Sla het project lokaal op voor een herbemiddeling van het offline schema. Selecteer **project opslaan** in het menu **bestand** . Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema publiceert naar SQL Server op Azure Virtual Machines.


## <a name="migrate"></a>Migrate

Nadat u de vereiste onderdelen hebt geïnstalleerd en de taken hebt voltooid die zijn gekoppeld aan de fase voorafgaand aan de migratie, bent u klaar om het schema en de gegevens migratie te starten. Migratie bestaat uit twee stappen: het schema publiceren en de gegevens migreren. 


Als u uw schema wilt publiceren en de gegevens wilt migreren, volgt u deze stappen: 

1. Het schema publiceren: Klik met de rechter muisknop op de data base in **SQL Server meta gegevens Verkenner** en selecteer **synchroniseren met data base**. Hiermee publiceert u het Oracle-schema naar SQL Server op Azure Virtual Machines. 

   ![Scherm opname van de opdracht synchroniseren met data base.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Controleer de toewijzing tussen uw bron project en uw doel:

   ![Scherm opname waarin de synchronisatie status wordt weer gegeven.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. De gegevens migreren: Klik met de rechter muisknop op de data base of het object dat u wilt migreren in **Oracle Meta Data Explorer** en selecteer **gegevens migreren**. U kunt ook **gegevens migreren** in het bovenste menu selecteren.

   Als u gegevens voor een hele Data Base wilt migreren, schakelt u het selectie vakje naast de naam van de data base in. Als u gegevens uit afzonderlijke tabellen wilt migreren, vouwt u de data base uit, vouwt u **tabellen** uit en schakelt u het selectie vakje naast de tabel in. Als u gegevens uit afzonderlijke tabellen wilt weglaten, schakelt u de selectie vakjes uit.

   ![Scherm opname van de opdracht gegevens migreren.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Geef verbindings Details voor Oracle en SQL Server op Azure Virtual Machines op in het dialoog venster.
1. Nadat de migratie is voltooid, bekijkt u het **rapport gegevens migratie**:

    ![Scherm afbeelding van het rapport voor gegevens migratie.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Maak verbinding met uw SQL Server op Azure Virtual Machines exemplaar met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Valideer de migratie door de gegevens en het schema te bekijken:


   ![Scherm afbeelding met een SQL Server-exemplaar in SSMA.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

In plaats van SSMA te gebruiken, kunt u SQL Server Integration Services (SSIS) gebruiken om de gegevens te migreren. Raadpleeg voor meer informatie: 
- Het artikel [SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- Het technisch document [SSIS voor Azure en hybride gegevens verplaatsing](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Postmigratie 

Nadat u de migratie fase hebt voltooid, moet u een reeks taken na migratie volt ooien om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk wordt uitgevoerd.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron eerder hebben gebruikt, het doel gaan gebruiken. Als u deze wijzigingen aanbrengt, zijn mogelijk wijzigingen in de toepassingen vereist.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) is een uitbrei ding voor Visual Studio code. U kunt hiermee uw Java-bron code analyseren en oproepen en query's voor Data Access-API'S detecteren. De Toolkit bevat een weer gave met één deel venster van wat moet worden geadresseerd ter ondersteuning van de back-end van de nieuwe data base. Zie [uw Java-toepassing migreren vanuit Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)voor meer informatie. 

### <a name="perform-tests"></a>Tests uitvoeren

Als u de database migratie wilt testen, moet u deze activiteiten volt ooien:

1. **Validatie tests ontwikkelen**. Als u de database migratie wilt testen, moet u SQL-query's gebruiken. Maak de validatie query's die moeten worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

2. **Stel een test omgeving** in. De test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.

3. **Validatie tests uitvoeren**. Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.

4. **Prestatie tests uitvoeren**. Voer prestatie tests uit op basis van de bron en het doel, en analyseer en vergelijk vervolgens de resultaten.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van de problemen met de nauw keurigheid van gegevens en het controleren van de volledigheid. Het is ook essentieel voor het oplossen van prestatie problemen met de werk belasting.

> [!Note]
> Zie voor meer informatie over deze problemen en specifieke stappen om ze te beperken de [hand leiding voor validatie na migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Migratieresources 

Voor meer informatie over het volt ooien van dit migratie scenario raadpleegt u de volgende bronnen, die zijn ontwikkeld ter ondersteuning van een echt migratie project.

| **Titel/koppeling**                                                                                                                                          | **Beschrijving**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma voorziet in aanbevolen doel platforms, Cloud gereedheid en toepassings-en database herstel niveaus voor een bepaalde werk belasting. Het biedt eenvoudige berekeningen voor berekening en rapporten van één klik waarmee u grootschalige beoordelingen kunt versnellen door een geautomatiseerd en uniform platform besluitvormings proces te bieden.                                                          |
| [Oracle Inventory script artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Deze asset bevat een PL/SQL-query die de Oracle-systeem tabellen bedoelt en een telling van objecten levert per schema type, object type en status. Het biedt ook een ruwe schatting van onbewerkte gegevens in elk schema en de grootte van de tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.                                                                                                               |
| [SSMA Oracle Assessment verzameling automatiseren & consolidatie](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Deze resourceset gebruikt een. CSV-bestand als vermelding (sources.csv in de project mappen) om de XML-bestanden te maken die u nodig hebt om een SSMA-evaluatie uit te voeren in de console modus. U geeft het source.csv bestand op door een inventarisatie te maken van bestaande Oracle-exemplaren. De uitvoer bestanden zijn AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml en VariableValueFile.xml.|
| [SSMA problemen en mogelijke oplossingen bij het migreren van Oracle-data bases](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Met Oracle kunt u een niet-scalaire voor waarde toewijzen in een component WHERE. SQL Server biedt geen ondersteuning voor dit type voor waarde. Daarom worden door SSMA voor Oracle geen query's geconverteerd die een niet-scalaire voor waarde hebben in de component WHERE. In plaats daarvan wordt er een fout gegenereerd: O2SS0001. Dit technisch document bevat informatie over het probleem en manieren om dit op te lossen.          |
| [SQL Server migratie handleiding voor Oracle](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Dit document is gericht op de taken die zijn gekoppeld aan het migreren van een Oracle-schema naar de meest recente versie van SQL Server. Als voor de migratie wijzigingen in functies/functionaliteit zijn vereist, moet u het mogelijke effect van elke wijziging in de toepassingen die gebruikmaken van de-data base zorgvuldig overwegen.                                                     |


Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Microsoft Azure-gegevens platform.


## <a name="next-steps"></a>Volgende stappen

- Als u de beschik baarheid van services wilt controleren die van toepassing zijn op SQL Server, raadpleegt u het [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Zie [Services en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij het maken van verschillende scenario's voor data base-en gegevens migratie en gespecialiseerde taken.

- Zie voor meer informatie over Azure SQL:
   - [Implementatieopties](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server op virtuele machines in Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/)


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties:
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices voor werk belastingen die zijn gemigreerd naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie voor meer informatie over licentie verlening:
   - [Bring Your Own License met de Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Krijg gratis uitgebreide ondersteuning voor SQL Server 2008 en SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Gebruik de [Preview-versie van Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)om de Access-laag van de toepassing te beoordelen.
- Zie [overzicht van database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het testen van gegevens toegang Layer A/B.


