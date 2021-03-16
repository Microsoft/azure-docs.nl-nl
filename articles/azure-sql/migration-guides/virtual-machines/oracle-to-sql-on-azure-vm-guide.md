---
title: 'Oracle to SQL Server op Azure VM: migratie handleiding'
description: In deze hand leiding leert u hoe u uw Oracle-schema's kunt migreren naar SQL Server op Azure-Vm's met behulp van SQL Server Migration Assistant voor Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f9b6dea216e05bb645daf5fdd041cec692821af8
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564896"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Migratie handleiding: Oracle naar SQL Server op Azure VM
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In deze hand leiding leert u hoe u uw Oracle-schema's kunt migreren naar SQL Server op Azure VM met behulp van SQL Server Migration Assistant voor Oracle. 

Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere scenario's.

## <a name="prerequisites"></a>Vereisten 

Als u uw Oracle-schema wilt migreren naar SQL Server op Azure VM, hebt u het volgende nodig:

- U kunt controleren of uw bron omgeving wordt ondersteund.
- Voor het downloaden [van SQL Server Migration Assistant (SSMA) voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Een doel- [SQL Server-VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- De [benodigde machtigingen voor SSMA voor Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) en [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).

## <a name="pre-migration"></a>Premigratie

Controleer tijdens de voor bereiding op de migratie naar de Cloud of uw bron omgeving wordt ondersteund en dat u alle vereiste onderdelen hebt behandeld. Dit helpt om een efficiënte en geslaagde migratie te garanderen.

Dit onderdeel van het proces bestaat uit het uitvoeren van een inventarisatie van de data bases die u moet migreren, het beoordelen van die data bases voor mogelijke migratie problemen of blok keringen en het oplossen van items die u mogelijk hebt gedetecteerd. 

### <a name="discover"></a>Ontdekken

Gebruik de [kaart Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) om bestaande gegevens bronnen te identificeren en Details over de functies die worden gebruikt door uw bedrijf om een beter inzicht te krijgen in en te plannen voor de migratie. Dit proces omvat het scannen van het netwerk om alle Oracle-exemplaren van uw organisatie samen te stellen met de versie en de functies die in gebruik zijn.

Voer de volgende stappen uit om de kaart-Toolkit te gebruiken voor het uitvoeren van een inventaris scan: 

1. Open de [kaart Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Selecteer **Data Base maken/selecteren**.
1. Selecteer **een inventarisatie database maken**, voer een naam in voor de nieuwe inventarisatie database die u wilt maken, geef een korte beschrijving op en selecteer **OK**. 
1. Selecteer **inventaris gegevens verzamelen** om de **wizard inventaris en analyse** te openen. 
1. Kies in de **wizard inventarisatie en analyse** de optie **Oracle** en selecteer vervolgens **volgende**. 
1. Kies de computer zoek optie die het beste past bij uw bedrijfs behoeften en-omgeving, en selecteer vervolgens **volgende**: 
1. Voer de referenties in of maak nieuwe referenties voor de systemen die u wilt verkennen, en selecteer vervolgens **volgende**.
1. Stel de volg orde van de referenties in en selecteer **volgende**. 
1. Geef de referenties op voor elke computer die u wilt detecteren. U kunt unieke referenties gebruiken voor elke computer/computer, of u kunt ervoor kiezen om de lijst **alle computer referenties** te gebruiken.  
1. Controleer uw selectie samenvatting en selecteer vervolgens **volt ooien**.
1. Nadat de scan is voltooid, bekijkt u het overzichts rapport van de **gegevens verzameling** . Het scannen duurt enkele minuten en is afhankelijk van het aantal data bases. Selecteer **sluiten** wanneer u klaar bent. 
1. Selecteer **Opties** om een rapport te genereren over de Oracle-evaluatie en database Details. Selecteer beide opties (één voor één) om het rapport te genereren.


### <a name="assess"></a>Evalueren

Nadat u de gegevens bronnen hebt geïdentificeerd, gebruikt u de [SQL Server Migration Assistant (SSMA) voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) om de Oracle-instanties die worden gemigreerd naar de SQL Server virtuele machine te beoordelen, zodat u inzicht krijgt in de tussen ruimten tussen de twee. Met behulp van de Migration Assistant kunt u database objecten en-gegevens bekijken, data bases beoordelen voor migratie, database objecten migreren naar SQL Server en vervolgens gegevens migreren naar SQL Server.

Voer de volgende stappen uit om een evaluatie te maken: 

1. Open de  [SQL Server Migration Assistant (SSMA) voor Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecteer **bestand** en kies vervolgens **Nieuw project**. 
1. Geef een project naam, een locatie op voor het opslaan van uw project en selecteer vervolgens een SQL Server migratie doel in de vervolg keuzelijst. Selecteer **OK**. 
1. Voer in het dialoog venster **verbinding maken met Oracle** de waarden in voor de details van de Oracle-verbinding.
1. Klik met de rechter muisknop op het Oracle-schema dat u wilt migreren in de **Oracle-meta gegevens Verkenner**, en kies vervolgens **rapport maken**. Hiermee wordt een HTML-rapport gegenereerd. U kunt ook **rapport maken** kiezen op de navigatie balk nadat u de Data Base hebt geselecteerd.

1. Selecteer in **Oracle-meta gegevens Verkenner** het Oracle-schema en selecteer vervolgens **rapport maken** om een HTML-rapport met conversie statistieken en fout/waarschuwingen te genereren, indien van toepassing.
1. Bekijk het HTML-rapport voor conversie statistieken, evenals fouten en waarschuwingen. Analyseer het om conversie problemen en oplossingen te begrijpen.

   Dit rapport kan ook worden geopend vanuit de map SSMA projects, zoals geselecteerd in het eerste scherm. In het bovenstaande voor beeld gaat u naar het report.xml-bestand van: 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    en open het vervolgens in Excel om een inventaris van Oracle-objecten te ontvangen en de inspanningen die nodig zijn om schema conversies uit te voeren.


### <a name="validate-data-types"></a>Gegevens typen valideren

Valideer de standaard gegevens type toewijzingen en wijzig deze indien nodig op basis van vereisten. Voer hiervoor de volgende stappen uit: 

1. Selecteer **extra** in het menu. 
1. Selecteer de **project instellingen**. 
1. Selecteer het tabblad **type toewijzingen** . 
1. U kunt de type toewijzing voor elke tabel wijzigen door de tabel te selecteren in de **Oracle-meta gegevens Verkenner**. 



### <a name="convert-schema"></a>Schema converteren

Voer de volgende stappen uit om het schema te converteren: 

1. Beschrijving Als u dynamische of ad-hoc query's wilt converteren, klikt u met de rechter muisknop op het knoop punt en kiest u **instructie toevoegen**.
1. Kies **verbinding maken met SQL Server** in de bovenste navigatie balk en geef de verbindings Details op voor uw SQL Server op de Azure-VM. U kunt ervoor kiezen om verbinding te maken met een bestaande data base of een nieuwe naam op te geven. in dat geval wordt er een Data Base op de doel server gemaakt.
1. Klik met de rechter muisknop op het schema en kies **schema converteren**.
1. Nadat het schema is voltooid, vergelijkt en controleert u de structuur van het schema om mogelijke problemen te identificeren.

   U kunt het project lokaal opslaan voor een herbemiddeling van het offline schema. U kunt dit doen door **project opslaan** te selecteren in het menu **bestand** . Dit biedt u de mogelijkheid om de bron-en doel schema's offline te evalueren en herstel bewerkingen uit te voeren voordat u het schema kunt publiceren naar SQL Server.


## <a name="migrate"></a>Migrate

Nadat u de vereiste onderdelen hebt geïnstalleerd en de taken hebt voltooid die zijn gekoppeld aan de fase **voorafgaand** aan de migratie, bent u klaar om het schema en de gegevens migratie uit te voeren. Migratie omvat twee stappen: het schema publiceren en de gegevens migreren. 


Als u het schema wilt publiceren en de gegevens wilt migreren, voert u de volgende stappen uit: 

1. Klik met de rechter muisknop op de data base in de **meta gegevens Verkenner van SQL Server**  en kies **synchroniseren met data base**. Met deze actie wordt het Oracle-schema gepubliceerd naar SQL Server op Azure VM. 
1. Klik met de rechter muisknop op het Oracle-schema in de **Oracle-meta gegevens Verkenner** en kies **gegevens migreren**. U kunt ook migreren van gegevens uit de navigatie op de bovenste regel selecteren.
1. Geef in het dialoog venster verbindings gegevens voor Oracle en SQL Server op Azure VM op.
1. Nadat de migratie is voltooid, raadpleegt u het rapport gegevens migratie:
1. Maak verbinding met uw SQL Server op Azure VM met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) om gegevens en schema's te controleren in uw SQL Server-exemplaar. 


Naast het gebruik van SSMA kunt u ook SQL Server Integration Services (SSIS) gebruiken om de gegevens te migreren. Raadpleeg voor meer informatie: 
- Het artikel [aan de slag met SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- De White paper [SQL Server Integration Services: SSIS voor Azure en hybride gegevens verplaatsing](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Postmigratie 

Nadat u de **migratie** fase hebt voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.

De [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) is een uitbrei ding voor Visual Studio code waarmee u uw Java-bron code kunt analyseren en Data Access-API-aanroepen en-query's kan detecteren, zodat u een weer gave met één deel venster krijgt van wat u moet verhelpen voor de back-end van de nieuwe data base. Zie voor meer informatie het onderwerp [onze Java-toepassing migreren vanuit Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blog. 

### <a name="perform-tests"></a>Tests uitvoeren

De test benadering voor database migratie bestaat uit het uitvoeren van de volgende activiteiten:

1. **Validatie tests ontwikkelen**. Als u de database migratie wilt testen, moet u SQL-query's gebruiken. U moet de validatie query's maken om te worden uitgevoerd op zowel de bron-als de doel database. Uw validatie query's moeten betrekking hebben op het bereik dat u hebt gedefinieerd.

2. **Test omgeving instellen**. De test omgeving moet een kopie van de bron database en de doel database bevatten. Zorg ervoor dat u de test omgeving isoleert.

3. **Validatie tests uitvoeren**. Voer de validatie tests uit op de bron en het doel en analyseer vervolgens de resultaten.

4. **Prestatie tests uitvoeren**. Voer prestatie tests uit op basis van de bron en het doel, en analyseer en vergelijk vervolgens de resultaten.

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met de nauw keurigheid van gegevens en het controleren van de volledigheid, en het oplossen van prestatie problemen met de werk belasting.

> [!Note]
> Zie voor meer informatie over deze problemen en specifieke stappen om ze te beperken de [hand leiding voor validatie na de migratie en Optima Lise ring](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Migratie-assets 

Voor aanvullende hulp bij het volt ooien van dit migratie scenario raadpleegt u de volgende bronnen, die zijn ontwikkeld ter ondersteuning van een echt toonaangevend migratie project.

| **Titel/koppeling**                                                                                                                                          | **Beschrijving**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Beoordelings model en hulp programma voor gegevens workload](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Dit hulp programma biedt voorgestelde ' Best passend ' doel platformen, Cloud gereedheids en toepassings-en database herstel niveau voor een bepaalde werk belasting. U kunt met één klik berekeningen en rapporten genereren waarmee u grote en kleine beoordelingen aanzienlijk sneller door middel van en geautomatiseerd en uniform platform besluitvormings proces.                                                          |
| [Oracle Inventory script artefacten](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Deze asset bevat een PL/SQL-query die Oracle-systeem tabellen oplevert en een telling van objecten biedt per schema type, object type en status. Het biedt ook een ruwe schatting van ' onbewerkte gegevens ' in elk schema en de grootte van de tabellen in elk schema, met resultaten die zijn opgeslagen in een CSV-indeling.                                                                                                               |
| [SSMA Oracle Assessment verzameling automatiseren & consolidatie](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Deze resourceset gebruikt een CSV-bestand als vermelding (sources.csv in de project mappen) om de XML-bestanden te maken die nodig zijn voor het uitvoeren van de SSMA-evaluatie in de console modus. De source.csv wordt door de klant verschaft op basis van een inventaris van bestaande Oracle-exemplaren. De uitvoer bestanden zijn AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml en VariableValueFile.xml.|
| [SSMA voor veelvoorkomende fouten in Oracle en hoe u deze kunt oplossen](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Met Oracle kunt u een niet-scalaire voor waarde toewijzen in de component WHERE. SQL Server biedt echter geen ondersteuning voor dit type voor waarde. Als gevolg hiervan wordt met SQL Server Migration Assistant (SSMA) voor Oracle geen query's geconverteerd met een niet-scalaire voor waarde in de WHERE-component, in plaats daarvan een fout O2SS0001 te genereren. Dit technisch document bevat meer informatie over het probleem en manieren om dit op te lossen.          |
| [SQL Server migratie handleiding voor Oracle](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Dit document is gericht op de taken die zijn gekoppeld aan het migreren van een Oracle-schema naar de meest recente versie van SQL ServerBase. Als voor de migratie wijzigingen in functies/functionaliteit zijn vereist, moet de mogelijke gevolgen van elke wijziging in de toepassingen die gebruikmaken van de data base, zorgvuldig worden overwogen.                                                     |

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.

## <a name="next-steps"></a>Volgende stappen

- Als u de beschik baarheid van services wilt controleren die van toepassing zijn op SQL Server raadpleegt u het [Azure Global Infrastructure Center](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- Zie de artikel [service en hulpprogram ma's voor gegevens migratie](../../../dms/dms-tools-matrix.md) voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie, en voor speciale taken.

- Zie voor meer informatie over Azure SQL:
   - [Implementatieopties](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server in Azure VM's](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Reken machine totale eigendoms kosten Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Zie voor meer informatie over het Framework en de acceptatie cyclus voor Cloud migraties.
   -  [Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Aanbevolen procedures voor het migreren en aanpassen van werk belastingen naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Zie voor meer informatie over licentie verlening
   - [Bring Your Own License met de Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Krijg gratis uitgebreide ondersteuning voor SQL Server 2008 en SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Zie [Data Access Migration Toolkit (preview) voor informatie](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) over het beoordelen van de toegangs laag van de toepassing.
- Zie [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview)voor meer informatie over het uitvoeren van een test voor de toegang tot een Data Access Layer A/B.

