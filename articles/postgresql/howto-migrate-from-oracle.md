---
title: Migreren vanuit Oracle
titleSuffix: Azure Database for PostgreSQL
description: In deze hand leiding leert u hoe u uw Oracle-schema kunt migreren naar Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: ec6cf87b3fd326c905b4843dc30ae6ce15379305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609188"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle migreren naar Azure Database for PostgreSQL

In deze hand leiding leert u hoe u uw Oracle-schema kunt migreren naar Azure Database for PostgreSQL. 

Zie de bronnen voor de [migratie handleiding](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)voor gedetailleerde en uitgebreide richt lijnen voor migratie. 

## <a name="prerequisites"></a>Vereisten

Als u uw Oracle-schema naar Azure Database for PostgreSQL wilt migreren, moet u het volgende doen: 

- Controleer of uw bron omgeving wordt ondersteund. 
- Down load de nieuwste versie van [ora2pg](https://ora2pg.darold.net/). 
- De nieuwste versie van de [DBD-module](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Overzicht

PostgreSQL is een van de meest geavanceerde open source-data bases van de wereld. In dit artikel wordt beschreven hoe u het gratis hulp programma ora2pg kunt gebruiken om een Oracle-Data Base te migreren naar PostgreSQL. U kunt ora2pg, een gratis hulp programma, gebruiken om een Oracle-of MySQL-data base te migreren naar een met PostgreSQL compatibel schema. Het hulp programma verbindt de Oracle-data base, scant deze automatisch en haalt de structuur of gegevens op. Daarna genereert ora2pg SQL-scripts die u in uw PostgreSQL-Data Base kunt laden. ora2pg kan worden gebruikt voor taken van reverse engineering een Oracle-data base, het uitvoeren van een enorme migratie van de bedrijfs database of het repliceren van een aantal Oracle-gegevens naar een PostgreSQL-data base. Het is eenvoudig te gebruiken en vereist geen enkele kennis van Oracle data bases dan de mogelijkheid om de para meters op te geven die nodig zijn om verbinding te maken met de Oracle-data base.

> [!NOTE]
> Zie de [ora2pg-documentatie](https://ora2pg.darold.net/documentation.html)voor meer informatie over het gebruik van de meest recente versie van ora2pg.

### <a name="typical-ora2pg-migration-architecture"></a>Typische architectuur voor ora2pg-migratie

![Scherm afbeelding van de ora2pg-migratie architectuur.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Na het inrichten van de virtuele machine en Azure Database for PostgreSQL zijn er twee configuraties nodig voor het inschakelen van de connectiviteit: **Azure-Services toestaan** en **SSL-verbinding afdwingen**, als volgt weer gegeven:

- Blade **verbindings beveiliging** -> **toegang tot Azure-Services toestaan** -> op

- Blade **verbindings beveiliging** -> **SSL-instellingen**  ->  **SSL-verbinding afdwingen** -> uitgeschakeld

### <a name="recommendations"></a>Aanbevelingen

- Als u de prestaties van de evaluatie-of export bewerkingen op de Oracle-server wilt verbeteren, verzamelt u statistieken als volgt:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Gegevens exporteren met behulp van de Kopieer opdracht in plaats van invoegen.

- Vermijd het exporteren van tabellen met hun FKs, beperkingen en indexen. Dit zorgt ervoor dat de gegevens in PostgreSQL worden geïmporteerd.

- Maak gerealiseerde weer gaven met behulp van de **component no data** en vernieuw deze later.

- Implementeer, indien mogelijk, unieke indexen in gerealiseerde weer gaven, waardoor het vernieuwen sneller verloopt met de syntaxis `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>Premigratie 

Nadat u hebt gecontroleerd of uw bron omgeving wordt ondersteund en u ervoor hebt gezorgd dat u voldoet aan alle vereisten, bent u klaar om de fase voorafgaand aan de migratie te starten. Dit onderdeel van het proces bestaat uit het uitvoeren van een inventarisatie van de data bases die u moet migreren, het beoordelen van die data bases voor mogelijke migratie problemen of blok keringen en het oplossen van items die u mogelijk hebt gedetecteerd. Voor heterogene-migraties zoals Oracle naar Azure Database for PostgreSQL, moet deze fase ook de schema (s) in de bron database (s) converteren om compatibel te zijn met de doel omgeving.

### <a name="discover"></a>Ontdekken

Het doel van de Discover-fase is het identificeren van bestaande gegevens bronnen en Details over de functies die worden gebruikt voor een beter inzicht in en het plannen van de migratie. Dit proces omvat het scannen van het netwerk om alle Oracle-exemplaren van uw organisatie samen te stellen met de versie en de functies die in gebruik zijn.

Micro soft Oracle prebeoordeling-scripts worden uitgevoerd op basis van de Oracle-data base. De scripts voorafgaand aan de beoordeling zijn een set query's die de Oracle-meta gegevens opleveren en het volgende biedt:

- Data base-inventaris, inclusief aantallen objecten per schema, type en status.

- Een ruwe schatting van onbewerkte gegevens in elk schema (op basis van statistieken).

- Grootte van de tabellen in elk schema.

- Het aantal regels code per pakket, functie, procedure, enzovoort.

Down load de gerelateerde scripts van de [ora2pg-website](http://ora2pg.darold.net/).

### <a name="assess"></a>Evalueren

Na het volt ooien van de inventaris van de Oracle-data base (s) om een idee te krijgen van de omvang van de data base en wat de uitdagingen zijn, is de volgende stap het uitvoeren van de evaluatie.

Het schatten van de kosten van een migratie proces van Oracle naar PostgreSQL is niet eenvoudig. Om een goede evaluatie van deze migratie kosten te verkrijgen, inspecteert ora2pg alle database objecten, alle functies en opgeslagen procedures om te detecteren of er nog objecten en PL/SQL-code zijn die niet automatisch door ora2pg kunnen worden geconverteerd.

ora2pg heeft een inhouds analyse modus die de Oracle-data base inspecteert om een tekst rapport te genereren over wat de Oracle-Data Base bevat en wat niet kan worden geëxporteerd.

Als u de **analyse-en rapport** modus wilt activeren, gebruikt u het geëxporteerde type `SHOW_REPORT` zoals wordt weer gegeven in de volgende opdracht:

```
ora2pg -t SHOW_REPORT
```

Nadat de data base is geanalyseerd, kan ora2pg, met de mogelijkheid om SQL-en PL/SQL-code te converteren van de Oracle-syntaxis naar PostgreSQL, verder gaan door de code problemen te schatten en de tijd die nodig is om een volledige database migratie uit te voeren.

Als u de migratie kosten in Manus-dagen wilt schatten, kunt u met ora2pg een configuratie-instructie gebruiken met de naam ESTIMATE_COST, die ook kan worden ingeschakeld op de opdracht regel:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

De standaard migratie-eenheid vertegenwoordigt circa vijf minuten voor een PostgreSQL expert. Als dit uw eerste migratie is, kunt u deze hoger verkrijgen met de configuratie-instructie COST_UNIT_VALUE of de opdracht regel optie--cost_unit_value.

In de laatste regel van het rapport wordt de totale geschatte migratie code in dagen weer gegeven na het aantal migratie-eenheden dat voor elk object is geschat.

Deze migratie-eenheid vertegenwoordigt ongeveer vijf minuten voor een PostgreSQL expert. Als dit de eerste migratie is, kunt u de standaard waarde verhogen met de configuratie-instructie COST_UNIT_VALUE of de opdracht regel optie--cost_unit_value. Bekijk de evaluatie van enkele variaties van de evaluatie a-tabellen; b) column Assessment c) schema bepaling met behulp van standaard cost_unit (5 min) d) schema bepaling met 10 min als kosten eenheid.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

De uitvoer van de schema-evaluatie wordt als volgt geïllustreerd:

**Migratie niveau: B-5**

Migratie niveaus:

A-migratie die automatisch kan worden uitgevoerd

B-migratie met code herschrijven en Human dagen kosten tot vijf dagen

C-migratie met code herschrijven en Human dagen kosten meer dan 5 dagen

Technische niveaus:

1 = trivial: er zijn geen opgeslagen functies en geen triggers

2 = eenvoudig: er zijn geen opgeslagen functies maar met triggers zonder hand matig opnieuw schrijven

3 = eenvoudige: opgeslagen functies en/of triggers, niet hand matig herschrijven

4 = hand matig: er zijn geen opgeslagen functies maar met triggers of weer gaven met code opnieuw schrijven

5 = moeilijk: opgeslagen functies en/of triggers bij het herschrijven van code

De evaluatie bestaat uit een letter (A of B) om op te geven of de migratie hand matig moet worden herschrijfd of niet, en een getal tussen 1 en 5 om het niveau van de technische moeilijkheids graad aan te geven. U hebt een extra optie-human_days_limit om het aantal Human-dagen limiet op te geven waar het migratie niveau moet worden ingesteld op C om aan te geven dat het een enorme hoeveelheid werk en een volledig project beheer met migratie ondersteuning nodig heeft. De standaard waarde is 10 Human dagen. U kunt de configuratie-instructie HUMAN_DAYS_LIMIT gebruiken om deze standaard waarde permanent te wijzigen.

Deze functie is ontwikkeld om te helpen beslissen welke data base het eerst kan worden gemigreerd en wat het team is dat moet worden aangekocht.

### <a name="convert"></a>Converteren

Bij migraties met een minimale uitval tijd blijft de bron die u migreert, veranderen, waarbij het doel in termen van gegevens en schema wordt weer gegeven nadat de eenmalige migratie is uitgevoerd. Tijdens de **Data Sync** -fase moet u ervoor zorgen dat alle wijzigingen in de bron bijna in realtime worden vastgelegd en toegepast op het doel. Nadat u hebt gecontroleerd of alle wijzigingen in de bron zijn toegepast op het doel, kunt u cutover van de bron naar de doel omgeving.

In deze stap van de migratie vindt de conversie of vertaling van de Oracle-code + DDLS naar PostgreSQL plaats. Het ora2pg-hulp programma exporteert automatisch de Oracle-objecten in een PostgreSQL-indeling. Voor die objecten die worden gegenereerd, wordt niet gecompileerd in de PostgreSQL-data base zonder hand matige wijzigingen.  
Het proces waarin wordt uitgelegd welke elementen hand matige interventie nodig hebben, bestaat uit het compileren van de bestanden die zijn gegenereerd door ora2pg met de PostgreSQL-data base, het logboek controleren en de benodigde wijzigingen aanbrengen totdat alle schema structuur compatibel is met de PostgreSQL-syntaxis.


#### <a name="create-migration-template"></a>Migratie sjabloon maken 

Eerst kunt u het beste de migratie sjabloon maken die uit het vak is gemaakt met ora2pg. De twee opties--project_base en-init_project wanneer deze worden gebruikt, geven aan dat ora2pg een project sjabloon moet maken met een werk structuur, een configuratie bestand en een script voor het exporteren van alle objecten uit de Oracle-data base. Zie de [ora2pg-documentatie](https://ora2pg.darold.net/documentation.html)voor meer informatie.

   Gebruik de volgende opdracht: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Voorbeelduitvoer: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

De bronnen/map bevat de Oracle-code, het schema/de map bevat de code die is geporteerd naar PostgreSQL. De rapporten/map bevat de HTML-rapporten met de evaluatie van de migratie kosten.


Nadat de project structuur is gemaakt, wordt er een algemeen configuratie bestand gemaakt. Definieer de Oracle-database verbinding en de relevante configuratie parameters in de configuratie.  Raadpleeg de ora2pg-documentatie voor informatie over wat er kan worden geconfigureerd in het configuratie bestand en hoe.


#### <a name="export-oracle-objects"></a>Oracle-objecten exporteren

Vervolgens exporteert u de Oracle-objecten als PostgreSQL-objecten door het bestand export_schema. sh uit te voeren.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
   SET namespace="/app/migration/mig_project"
   
   ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
   %namespace%/config/ora2pg.conf
   ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
   %namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
   ora2pg -p -t PACKAGE -o packages.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
   ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
   ora2pg -p -t PROCEDURE -o procs.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
   ora2pg -t SEQUENCE -o sequences.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
   ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
   %namespace%/config/ora2pg.conf
   ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
   %namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
   ```

   Als u de gegevens wilt extra heren, gebruikt u de volgende opdracht:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Bestanden compileren

Compileer ten slotte alle bestanden op Azure Database for PostgreSQL server. Het is nu mogelijk om de DDL-bestanden die hand matig zijn gegenereerd, te laden of het tweede script import_all. sh te gebruiken om deze bestanden interactief te importeren.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   De opdracht gegevens importeren:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Controleer tijdens het compileren van bestanden de logboeken en corrigeer de benodigde syntaxis die ora2pg niet kan worden geconverteerd.

Raadpleeg het technische document over [Azure database for PostgreSQL migratie oplossingen](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) voor ondersteuning bij het oplossen van problemen.

## <a name="migrate"></a>Migrate 

Nadat u de vereiste onderdelen hebt geïnstalleerd en de taken hebt voltooid die zijn gekoppeld aan de fase **voorafgaand** aan de migratie, bent u klaar om het schema en de gegevens migratie uit te voeren.

### <a name="migrate-schema-and-data"></a>Schema en gegevens migreren

Nadat de oplossingen zijn geïmplementeerd, is een stabiele versie van de data base gereed voor implementatie.

Op dit moment is het nodig om de *psql* -import opdrachten uit te voeren, wijst u de bestanden met de gewijzigde code aan om de database objecten te compileren op de postgresql-data base en de gegevens te importeren.

In deze stap kan een zekere mate van parallellisme bij het importeren van de gegevens worden geïmplementeerd.

### <a name="data-sync-and-cutover"></a>Gegevens synchronisatie en Cutover

Met on-time-migraties (minimale downtime) wordt de bron die u migreert, gewijzigd, waarbij het doel in termen van gegevens en schema wordt weer gegeven nadat de eenmalige migratie is uitgevoerd. Tijdens de **Data Sync** -fase moet u ervoor zorgen dat alle wijzigingen in de bron bijna in realtime worden vastgelegd en toegepast op het doel. Nadat u hebt gecontroleerd of alle wijzigingen in de bron zijn toegepast op het doel, kunt u cutover van de bron naar de doel omgeving.

Als u vanaf maart 2019 een online migratie wilt uitvoeren, kunt u overwegen Attunity repliceren te gebruiken voor micro soft-migraties of Realtimeplatform.

Voor *Delta/incrementele* migratie met behulp van ora2pg, bestaat de techniek uit het Toep assen van elke tabel een query waarbij een filter (knippen) wordt toegepast op datum of tijd, enzovoort, en daarna het volt ooien van de migratie van een tweede query waarmee de rest van de gegevens worden gemigreerd.

Migreer in de bron gegevens tabel eerst alle historische gegevens. Een voor beeld van dat is:

```
select * from table1 where filter_data < 01/01/2019
```

U kunt een query uitvoeren op de wijzigingen die zijn aangebracht sinds de eerste migratie door een opdracht uit te voeren die vergelijkbaar is met de volgende:

```
select * from table1 where filter_data >= 01/01/2019
```

In dit geval is het raadzaam om de validatie te verbeteren door de gegevens pariteit aan beide zijden, bron en doel te controleren.

## <a name="post-migration"></a>Postmigratie 

Nadat u de **migratie** fase hebt voltooid, moet u een reeks taken na migratie door lopen om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. In sommige gevallen moeten wijzigingen in de toepassingen worden uitgevoerd.

### <a name="perform-tests"></a>Tests uitvoeren

Nadat de gegevens zijn gemigreerd naar het doel, voert u tests uit op basis van de data bases om te controleren of de toepassingen na de migratie goed worden uitgevoerd op het doel.

Als u zeker wilt zijn dat de bron en het doel correct worden gemigreerd, voert u de scripts hand matige gegevens validatie uit op basis van de Oracle-bron-en PostgreSQL-doel databases.

In het ideale geval, als de bron-en doel databases een netwerkpad hebben, moet ora2pg worden gebruikt voor gegevens validatie. U kunt met behulp van de TEST actie controleren of alle objecten uit de Oracle-Data Base zijn gemaakt onder PostgreSQL. Voer de opdracht uit zoals weer gegeven:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimaliseren

De fase na de migratie is van cruciaal belang voor het afstemmen van alle problemen met de nauw keurigheid van gegevens en het controleren van de volledigheid, en het oplossen van prestatie problemen met de werk belasting.

## <a name="migration-assets"></a>Migratie-assets 

Voor aanvullende hulp bij het volt ooien van dit migratie scenario raadpleegt u de volgende bronnen, die zijn ontwikkeld ter ondersteuning van een echt toonaangevend migratie project.

| **Titel koppeling** | **Beschrijving**    |
| -------------- | ------------------ |
| [Oracle to Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Dit document is bedoeld om architecten, consultants, Dba's en gerelateerde rollen te bieden met een hand leiding voor het snel migreren van werk belastingen van Oracle naar Azure Database for PostgreSQL met behulp van ora2pg-hulp programma. |
| [Tijdelijke oplossingen voor migratie van Oracle naar Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Dit document is bedoeld om architecten, consultants, Dba's en gerelateerde rollen te bieden met een hand leiding voor snelle vaststelling/probleem oplossing bij het migreren van werk belastingen van Oracle naar Azure Database for PostgreSQL. |
| [Stappen voor het installeren van ora2pg in Windows of Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Dit document is bedoeld om te worden gebruikt als een snelle installatie handleiding voor het inschakelen van de migratie van schema & gegevens van Oracle naar Azure Database for PostgreSQL met behulp van ora2pg in Windows of Linux. Volledige informatie over het hulp programma vindt u op http://ora2pg.darold.net/documentation.html . |

Deze resources zijn ontwikkeld als onderdeel van het data SQL expert-programma, dat wordt gesponsord door het technische team van de Azure-gegevens groep. Het kern Handvest van het data SQL expert-programma is het deblokkeren en versnellen van complexe modernisering en het concurreren van de migratie mogelijkheden van het gegevens platform naar het Azure-gegevens platform van micro soft. Als u denkt dat uw organisatie graag deelneemt aan het data SQL expert-programma, neemt u contact op met uw account team en vraagt u om een benoeming in te dienen.


### <a name="contact-support"></a>Contact opnemen met ondersteuning

Als u hulp nodig hebt bij uw migraties buiten het ora2pg-hulp programma, neemt u contact op met de [ @Ask Azure DB voor postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) -alias voor informatie over andere migratie opties.

## <a name="next-steps"></a>Volgende stappen

- Zie de artikel [service en hulpprogram ma's voor gegevens migratie](https://docs.microsoft.com/azure/dms/dms-tools-matrix)voor een matrix van de services en hulpprogram Ma's van micro soft en van derden die beschikbaar zijn om u te helpen bij verschillende scenario's voor data base-en gegevens migratie (en speciale taken).

Raadpleeg voor meer informatie: 
- [Documentatie voor Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentatie voor ora2pg](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL-website](https://www.postgresql.org/)
- [Ondersteuning voor autonome trans acties in PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Voor video-inhoud: 
- [Overzicht van de migratie traject en de hulpprogram ma's/services die worden aanbevolen voor het uitvoeren van de evaluatie en migratie](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).