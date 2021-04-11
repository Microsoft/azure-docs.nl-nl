---
title: 'Oracle to Azure Database for PostgreSQL: migratie handleiding'
titleSuffix: Azure Database for PostgreSQL
description: In deze hand leiding leert u hoe u uw Oracle-schema kunt migreren naar Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968931"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Oracle migreren naar Azure Database for PostgreSQL

In deze hand leiding leert u hoe u uw Oracle-schema kunt migreren naar Azure Database for PostgreSQL. 

Zie de bronnen voor de [migratie handleiding](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)voor gedetailleerde en uitgebreide richt lijnen voor migratie. 

## <a name="prerequisites"></a>Vereisten

Als u uw Oracle-schema naar Azure Database for PostgreSQL wilt migreren, moet u het volgende doen: 

- Controleer of uw bron omgeving wordt ondersteund. 
- Down load de nieuwste versie van [ora2pg](https://ora2pg.darold.net/). 
- De nieuwste versie van de [DBD-module](https://www.cpan.org/modules/by-module/DBD/)hebben. 


## <a name="overview"></a>Overzicht

PostgreSQL is een van de meest geavanceerde open source-data bases van de wereld. In dit artikel wordt beschreven hoe u het gratis hulp programma ora2pg kunt gebruiken om een Oracle-Data Base te migreren naar PostgreSQL. U kunt ora2pg gebruiken om een Oracle-data base of MySQL-data base te migreren naar een PostgreSQL-compatibel schema. 

Het hulp programma ora2pg verbindt uw Oracle-data base, scant deze automatisch en haalt de structuur of gegevens op. Vervolgens genereert ora2pg SQL-scripts die u in uw PostgreSQL-Data Base kunt laden. U kunt ora2pg gebruiken voor taken, zoals reverse engineering, een Oracle-data base, het migreren van een enorme bedrijfs database of het repliceren van een aantal Oracle-gegevens naar een PostgreSQL-data base. Het hulp programma is eenvoudig te gebruiken en vereist geen Oracle data base-kennis naast de mogelijkheid om de para meters op te geven die nodig zijn om verbinding te maken met de Oracle-data base.

> [!NOTE]
> Zie de [ora2pg-documentatie](https://ora2pg.darold.net/documentation.html)voor meer informatie over het gebruik van de meest recente versie van ora2pg.

### <a name="typical-ora2pg-migration-architecture"></a>Typische architectuur voor ora2pg-migratie

![Scherm afbeelding van de ora2pg-migratie architectuur.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Nadat u de virtuele machine en Azure Database for PostgreSQL hebt ingericht, hebt u twee configuraties nodig om de connectiviteit ertussen in te scha kelen: **toegang tot Azure-Services toestaan** en **SSL-verbinding afdwingen**: 

- Blade **verbindings beveiliging** > **toegang tot Azure-Services toestaan**  >  **op**

- De Blade **verbindings beveiliging** > **SSL-instellingen** SSL-  >  **verbinding afdwingen** is  >  **uitgeschakeld**

### <a name="recommendations"></a>Aanbevelingen

- Voor het verbeteren van de prestaties van de evaluatie-of export bewerkingen op de Oracle-Server, verzamelen statistieken:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Gegevens exporteren met behulp `COPY` van de opdracht in plaats van `INSERT` .

- Vermijd het exporteren van tabellen met hun refererende sleutels (FKs), beperkingen en indexen. Deze elementen vertragen het proces van het importeren van gegevens in PostgreSQL.

- Maak gerealiseerde weer gaven met behulp van de *component no data*. Vernieuw vervolgens de weer gaven later.

- Gebruik, indien mogelijk, unieke indexen in gerealiseerde weer gaven. Deze indexen kunnen het vernieuwen versnellen wanneer u de syntaxis gebruikt `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Premigratie 

Nadat u hebt gecontroleerd of uw bron omgeving wordt ondersteund en dat u alle vereiste onderdelen hebt gedistribueerd, bent u klaar om de voorbereidende migratie te starten. U gaat als volgt aan de slag: 

1. Inventariseer de data bases die u nodig hebt om te migreren. 
1. Beoordeel deze data bases op mogelijke migratie problemen of blok keringen.
1. Los alle items op die u hebt gedetecteerd. 
 
Voor heterogene-migraties zoals Oracle naar Azure Database for PostgreSQL, moet u in deze fase ook de schema's van de bron database maken die compatibel zijn met de doel omgeving.

### <a name="discover"></a>Ontdekken

Het doel van de detectie fase is het identificeren van bestaande gegevens bronnen en Details over de functies die worden gebruikt. Deze fase helpt u om de migratie beter te begrijpen en te plannen. Het proces omvat het scannen van het netwerk om alle Oracle-exemplaren van uw organisatie te identificeren samen met de versie en de functies die in gebruik zijn.

Micro soft prebeoordelings scripts voor Oracle worden uitgevoerd op basis van de Oracle-data base. De prebeoordelings scripts doorzoeken de Oracle-meta gegevens. De scripts bieden:

- Een Data Base-inventaris, met inbegrip van aantallen objecten per schema, type en status.
- Een ruwe schatting van de onbewerkte gegevens in elk schema, op basis van statistieken.
- De grootte van de tabellen in elk schema.
- Het aantal code regels per pakket, functie, procedure, enzovoort.

Down load de gerelateerde scripts van de [ora2pg-website](https://ora2pg.darold.net/).

### <a name="assess"></a>Evalueren

Nadat u de Oracle-data bases hebt geïnventariseerd, hebt u een idee van de grootte van de data base en mogelijke uitdagingen. De volgende stap is om de evaluatie uit te voeren.

Het schatten van de kosten van een migratie van Oracle naar PostgreSQL is niet eenvoudig. Voor het beoordelen van de migratie kosten, controleert ora2pg alle database objecten, functies en opgeslagen procedures voor objecten en PL/SQL-code die niet automatisch kunnen worden geconverteerd.

Het hulp programma ora2pg heeft een analyse modus voor inhoud die de Oracle-data base inspecteert om een tekst rapport te genereren. In het rapport wordt beschreven wat de Oracle-Data Base bevat en wat niet kan worden geëxporteerd.

Als u de *analyse-en rapport* modus wilt activeren, gebruikt u het geëxporteerde type `SHOW_REPORT` zoals wordt weer gegeven in de volgende opdracht:

```
ora2pg -t SHOW_REPORT
```

Het hulp programma ora2pg kan SQL-en PL/SQL-code converteren van de Oracle-syntaxis naar PostgreSQL. Nadat de data base is geanalyseerd, kan ora2pg dus de code problemen en de benodigde tijd voor het migreren van een volledige data base schatten.

Voor een schatting van de migratie kosten in Human dagen kunt u met ora2pg een configuratie instructie gebruiken met de naam `ESTIMATE_COST` . U kunt deze instructie ook inschakelen via een opdracht prompt:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

De standaard migratie-eenheid staat ongeveer vijf minuten voor een PostgreSQL expert. Als deze migratie uw eerste is, kunt u de standaard migratie-eenheid verhogen met behulp van de configuratie-instructie `COST_UNIT_VALUE` of de `--cost_unit_value` opdracht regel optie.

In de laatste regel van het rapport wordt de totale geschatte migratie code in Human dagen weer gegeven. De schatting volgt het aantal geraamde migratie eenheden voor elk object.

Deze migratie-eenheid vertegenwoordigt ongeveer vijf minuten voor een PostgreSQL expert. Als deze migratie uw eerste is, kunt u de standaard waarde verhogen met behulp van de configuratie-instructie `COST_UNIT_VALUE` of de opdracht regel optie `--cost_unit_value` . 

In het volgende code voorbeeld ziet u enkele evaluatie variaties: 
* Analyse van tabellen
* Evaluatie van kolommen
* Schema-evaluatie waarbij gebruik wordt gemaakt van een standaardkosten eenheid van vijf minuten
* Schema-evaluatie waarbij een kosten eenheid van 10 minuten wordt gebruikt

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Dit is de uitvoer van het migratie niveau B-5 van de schema-evaluatie:

* Migratie niveaus:

  * Een-migratie die automatisch kan worden uitgevoerd
    
  * B-migratie met code herschrijven en Human dagen kosten tot vijf dagen
    
  * C-migratie met code herschrijven en Human dagen kosten meer dan vijf dagen
    
* Technische niveaus:

   * 1 = trivial: er zijn geen opgeslagen functies en geen triggers

   * 2 = eenvoudig: er zijn geen opgeslagen functies, maar triggers; niet hand matig herschrijven

   * 3 = eenvoudige: opgeslagen functies en/of triggers; niet hand matig herschrijven

   * 4 = hand matig: er zijn geen opgeslagen functies, maar triggers of weer gaven met code herschrijf bewerkingen

   * 5 = moeilijk: opgeslagen functies en/of triggers bij het herschrijven van code

De evaluatie bestaat uit: 
* Een letter (A of B) om op te geven of de migratie hand matig moet worden herschreven.

* Een getal tussen 1 en 5 om de technische moeilijkheids graad aan te geven. 

Een andere optie, `-human_days_limit` , geeft de limiet van Human dagen aan. Stel hier het migratie niveau in op C om aan te geven dat de migratie een grote hoeveelheid werk, volledig project beheer en migratie ondersteuning nodig heeft. De standaard waarde is 10 Human dagen. U kunt de configuratie-instructie gebruiken `HUMAN_DAYS_LIMIT` om deze standaard waarde permanent te wijzigen.

Deze schema-evaluatie is ontwikkeld om gebruikers te helpen beslissen welke data base het eerst moet worden gemigreerd en welke teams ze willen gebruiken.

### <a name="convert"></a>Converteren

Bij migraties met een minimale downtime wordt de migratie bron gewijzigd. Na de eenmalige migratie wordt het doel met betrekking tot gegevens en schema Zorg er tijdens de *Data Sync* -fase voor dat alle wijzigingen in de bron bijna in realtime worden vastgelegd en toegepast op het doel. Nadat u hebt gecontroleerd of alle wijzigingen zijn toegepast op het doel *, kunt u* overgaan van de bron naar de doel omgeving.

In deze stap van de migratie worden de Oracle-code en DDL-scripts geconverteerd of vertaald naar PostgreSQL. Het ora2pg-hulp programma exporteert automatisch de Oracle-objecten in een PostgreSQL-indeling. Sommige van de gegenereerde objecten kunnen niet worden gecompileerd in de PostgreSQL-data base zonder hand matige wijzigingen.  

Als u wilt weten welke elementen hand matige interventie nodig hebben, compileert u eerst de bestanden die door ora2pg worden gegenereerd op basis van de PostgreSQL-data base. Controleer het logboek en breng de benodigde wijzigingen aan totdat de schema structuur compatibel is met de PostgreSQL-syntaxis.


#### <a name="create-a-migration-template"></a>Een migratie sjabloon maken 

Het is raadzaam om de migratie sjabloon die ora2pg biedt te gebruiken. Wanneer u de opties gebruikt `--project_base` en `--init_project` , maakt ora2pg een project sjabloon met een werk structuur, een configuratie bestand en een script voor het exporteren van alle objecten uit de Oracle-data base. Zie de [ora2pg-documentatie](https://ora2pg.darold.net/documentation.html)voor meer informatie.

Gebruik de volgende opdracht: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Hier volgt een voor beeld van de uitvoer: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

De `sources/` Directory bevat de Oracle-code. De `schema/` map bevat de code die is geporteerd naar postgresql. En de `reports/` Directory bevat de HTML-rapporten en de evaluatie van de migratie kosten.


Nadat de project structuur is gemaakt, wordt er een algemeen configuratie bestand gemaakt. Definieer de Oracle-database verbinding en de relevante configuratie parameters in het configuratie bestand. Zie de [ora2pg-documentatie](https://ora2pg.darold.net/documentation.html)voor meer informatie over het configuratie bestand.


#### <a name="export-oracle-objects"></a>Oracle-objecten exporteren

Vervolgens exporteert u de Oracle-objecten als PostgreSQL-objecten door het bestand *export_schema. sh* uit te voeren.

```
cd /app/migration/mig_project
./export_schema.sh
```

Voer de volgende opdracht hand matig uit.

```
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

Gebruik de volgende opdracht om de gegevens uit te pakken.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Bestanden compileren

Compileer tot slot alle bestanden op de Azure Database for PostgreSQL-server. U kunt ervoor kiezen om de hand matig gegenereerde DDL-bestanden te laden of het tweede script *import_all. sh* gebruiken om deze bestanden interactief te importeren.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Hier volgt de opdracht voor het importeren van gegevens:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Terwijl de bestanden worden gecompileerd, controleert u de logboeken en corrigeert u de syntaxis die ora2pg niet zelf kan converteren.

Zie [Oracle to Azure database for PostgreSQL Migration omzeiling](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)(Engelstalig) voor meer informatie.

## <a name="migrate"></a>Migrate 

Nadat u de benodigde vereisten hebt gevolgd en u de stappen voor de voorafgaande migratie hebt voltooid, kunt u het schema en de gegevens migratie starten.

### <a name="migrate-schema-and-data"></a>Schema en gegevens migreren

Wanneer u de benodigde oplossingen hebt gemaakt, is een stabiele versie van de data base gereed voor de implementatie. Voer de `psql` import opdrachten uit en wijs de bestanden toe die de gewijzigde code bevatten. Met deze taak worden de database objecten gecompileerd op basis van de PostgreSQL-data base en worden de gegevens geïmporteerd.

In deze stap kunt u een niveau van parallelle uitvoering implementeren bij het importeren van de gegevens.

### <a name="sync-data-and-cut-over"></a>Gegevens synchroniseren en knippen

In on-line migraties (minimale downtime) blijft de migratie bron wijzigen. Na de eenmalige migratie wordt het doel met betrekking tot gegevens en schema 

Zorg er tijdens de *Data Sync* -fase voor dat alle wijzigingen in de bron bijna in realtime worden vastgelegd en toegepast op het doel. Nadat u hebt gecontroleerd of alle wijzigingen zijn toegepast, kunt u overgaan van de bron naar de doel omgeving.

Neem contact op met de ondersteuning voor een online migratie AskAzureDBforPostgreSQL@service.microsoft.com .

In een *Delta/incrementele* migratie waarbij ora2pg wordt gebruikt, gebruikt u voor elke tabel een query die filtert (*delen*) op datum, tijd of een andere para meter. Voltooi vervolgens de migratie met behulp van een tweede query waarmee de resterende gegevens worden gemigreerd.

Migreer in de bron gegevens tabel eerst alle historische gegevens. Hier volgt een voorbeeld:

```
select * from table1 where filter_data < 01/01/2019
```

U kunt de wijzigingen sinds de eerste migratie opvragen door een opdracht uit te voeren zoals deze:

```
select * from table1 where filter_data >= 01/01/2019
```

In dit geval is het raadzaam om de validatie te verbeteren door de gegevens pariteit aan beide zijden, de bron en het doel te controleren.

## <a name="postmigration"></a>Postmigration 

Na de *migratie* fase voltooit u de postmigration-taken om ervoor te zorgen dat alles zo soepel en efficiënt mogelijk werkt.

### <a name="remediate-applications"></a>Toepassingen herstellen

Nadat de gegevens zijn gemigreerd naar de doel omgeving, moeten alle toepassingen die de bron voorheen hebben verbruikt, het doel gaan gebruiken. Het installatie programma vereist soms wijzigingen in de toepassingen.

### <a name="test"></a>Testen

Nadat de gegevens zijn gemigreerd naar het doel, voert u tests uit op de data bases om te controleren of de toepassingen goed werken met het doel. Zorg ervoor dat de bron en het doel juist zijn gemigreerd door de hand matige gegevens validatie scripts uit te voeren op de Oracle-bron-en PostgreSQL-doel databases.

In het ideale geval, als de bron-en doel databases een netwerkpad hebben, moet ora2pg worden gebruikt voor gegevens validatie. U kunt de `TEST` actie gebruiken om ervoor te zorgen dat alle objecten uit de Oracle-Data Base zijn gemaakt in postgresql. 

Voer deze opdracht uit:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optimaliseren

De postmigration-fase is van cruciaal belang voor het afstemmen van alle problemen met gegevens nauwkeurigheid en het controleren van de volledigheid. In deze fase worden ook prestatie problemen met de workload opgelost.

## <a name="migration-assets"></a>Migratie-assets 

Zie de volgende bronnen voor meer informatie over dit migratie scenario. Ze ondersteunen de betrokkenheid van een migratie project van een echte wereld.

| Resource | Beschrijving    |
| -------------- | ------------------ |
| [Migratie Cookbook van Oracle naar Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | In dit document kunnen architecten, consultants, database beheerders en gerelateerde rollen snel workloads migreren van Oracle naar Azure Database for PostgreSQL met behulp van ora2pg. |
| [Tijdelijke oplossingen voor migratie van Oracle naar Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Dit document helpt architecten, consultants, database beheerders en gerelateerde rollen om snel problemen op te lossen of te omzeilen tijdens het migreren van werk belastingen van Oracle naar Azure Database for PostgreSQL. |
| [Stappen voor het installeren van ora2pg in Windows of Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | In dit document vindt u een korte hand leiding voor het migreren van schema en gegevens van Oracle naar Azure Database for PostgreSQL met behulp van ora2pg in Windows of Linux. Zie de [ora2pg-documentatie](http://ora2pg.darold.net/documentation.html)voor meer informatie. |

Het IT-team van data SQL heeft deze resources ontwikkeld. Het kern Handvest van dit team is het deblokkeren en versnellen van complexe modernisering voor data platform migratie projecten naar het Microsoft Azure-gegevens platform.

## <a name="more-support"></a>Meer ondersteuning

Neem contact op met [ @Ask Azure DB voor postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)voor meer informatie over de migratie van ora2pg-hulpprogram ma's.

## <a name="next-steps"></a>Volgende stappen

Zie [Services en hulpprogram ma's voor gegevens migratie](https://docs.microsoft.com/azure/dms/dms-tools-matrix)voor een matrix van services en hulpprogram ma's voor data base-en gegevens migratie en voor speciale taken.

Documentatie: 
- [Documentatie voor Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [documentatie voor ora2pg](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL-website](https://www.postgresql.org/)
- [Ondersteuning voor autonome trans acties in PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
