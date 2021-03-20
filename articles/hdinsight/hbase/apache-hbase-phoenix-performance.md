---
title: Phoenix-prestaties in azure HDInsight
description: Aanbevolen procedures voor het optimaliseren van Apache Phoenix prestaties voor Azure HDInsight-clusters
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: ffba3b986b35c375d0404d9d2bae5af79f93c54f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944798"
---
# <a name="apache-phoenix-performance-best-practices"></a>Best practices voor Apache Phoenix-prestaties

Het belangrijkste aspect van [Apache Phoenix](https://phoenix.apache.org/) prestaties is het optimaliseren van de onderliggende [Apache HBase](https://hbase.apache.org/). Phoenix maakt een relationeel gegevens model hierop HBase waarmee SQL-query's worden geconverteerd naar HBase-bewerkingen, zoals scans. Het ontwerp van het tabel schema, de selectie en volg orde van de velden in uw primaire sleutel en uw gebruik van indices zijn allemaal van invloed op de prestaties van Breda.

## <a name="table-schema-design"></a>Ontwerp van tabel schema

Wanneer u een tabel in Phoenix maakt, wordt die tabel opgeslagen in een HBase-tabel. De tabel HBase bevat groepen kolommen (kolom families) die samen worden geopend. Een rij in de Phoenix-tabel is een rij in de tabel HBase, waarbij elke rij bestaat uit versie cellen die zijn gekoppeld aan een of meer kolommen. Logisch gezien is een enkele HBase-rij een verzameling sleutel-waardeparen, elk met dezelfde rowkey-waarde. Dat wil zeggen dat elk sleutel-waardepaar een rowkey-kenmerk heeft en dat de waarde van het kenmerk rowkey hetzelfde is voor een bepaalde rij.

Het schema ontwerp van een Bredase tabel omvat het ontwerp van de primaire sleutel, het ontwerpen van de kolom familie, het ontwerpen van afzonderlijke kolommen en de manier waarop de gegevens worden gepartitioneerd.

### <a name="primary-key-design"></a>Primair sleutel ontwerp

De primaire sleutel die is gedefinieerd voor een tabel in Phoenix bepaalt hoe gegevens worden opgeslagen in de rowkey van de onderliggende HBase-tabel. In HBase is de enige manier om toegang te krijgen tot een bepaalde rij met de rowkey. Daarnaast worden gegevens die zijn opgeslagen in een HBase-tabel gesorteerd op de rowkey. Phoenix bouwt de rowkey-waarde door de waarden van elk van de kolommen in de rij samen te voegen in de volg orde waarin ze in de primaire sleutel zijn gedefinieerd.

Een tabel voor contact personen heeft bijvoorbeeld de voor naam, achternaam, telefoon nummer en het adres, allemaal in dezelfde kolom familie. U kunt een primaire sleutel definiëren op basis van een oplopend Volg nummer:

|rowkey|       adres|   telefoon| voornaam| achternaam|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Davids|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Als u echter vaak een query uitvoert op lastName, wordt deze primaire sleutel mogelijk niet goed uitgevoerd, omdat voor elke query een volledige tabel scan is vereist om de waarde van elke lastName te lezen. In plaats daarvan kunt u een primaire sleutel definiëren voor de kolommen lastName, firstName en Social Security Number. Deze laatste kolom is om twee inwoners op hetzelfde adres te dubbel zinnigheid met dezelfde naam, zoals een vader en zoon.

|rowkey|       adres|   telefoon| voornaam| achternaam| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Davids| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Met deze nieuwe primaire sleutel worden de door Breda gegenereerde rij sleutels:

|rowkey|       adres|   telefoon| voornaam| achternaam| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Davids-Jan-111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Davids| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

In de eerste rij hierboven worden de gegevens voor de rowkey weer gegeven:

|rowkey|       sleutel|   waarde|
|------|--------------------|---|
|  Davids-Jan-111|adres |1111 San Gabriel Dr.|  
|  Davids-Jan-111|telefoon |1-425-000-0002|  
|  Davids-Jan-111|voornaam |Jan|  
|  Davids-Jan-111|achternaam |Davids|  
|  Davids-Jan-111|socialSecurityNum |111|

In deze rowkey wordt nu een dubbele kopie van de gegevens opgeslagen. Houd rekening met de grootte en het aantal kolommen dat u in uw primaire sleutel hebt opgenomen, omdat deze waarde is opgenomen in elke cel in de onderliggende HBase-tabel.

Als de primaire sleutel waarden heeft die gestaag verhogen, moet u ook de tabel met *zout buckets* maken om te voor komen dat u schrijf HOTS pots maakt. Zie [partitie gegevens](#partition-data).

### <a name="column-family-design"></a>Ontwerp van kolom familie

Als sommige kolommen vaker worden geopend dan andere, moet u meerdere kolom families maken om de veelgebruikte kolommen te scheiden van kolommen die zelden worden gebruikt.

Als bepaalde kolommen meestal samen worden geopend, plaatst u deze kolommen in dezelfde kolom familie.

### <a name="column-design"></a>Kolom ontwerp

* Blijf VARCHAR kolommen onder ongeveer 1 MB vanwege de I/O-kosten van grote kolommen. Wanneer query's worden verwerkt, worden de resultatenset-cellen volledig geHBased voordat ze worden verzonden naar de client. de client ontvangt deze volledig voordat deze aan de toepassings code wordt door gegeven.
* Sla kolom waarden op met een compacte indeling, zoals protobuf, AVRO, msgpack of BSON. JSON wordt niet aanbevolen, omdat het groter is.
* Overweeg het comprimeren van gegevens voordat de opslag ruimte en I/O-kosten kan worden geknipt.

### <a name="partition-data"></a>Partitiegegevens

Met Phoenix kunt u het aantal regio's bepalen waar uw gegevens worden gedistribueerd, waardoor de lees-en schrijf prestaties aanzienlijk toenemen. Wanneer u een Phoenix-tabel maakt, kunt u een zout of vooraf uw gegevens splitsen.

Als u een tabel wilt zouten tijdens het maken, geeft u het aantal zout buckets op:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Met dit zouten wordt de tabel op basis van de waarden van primaire sleutels gesplitst, waarbij de waarden automatisch worden gekozen. 

Als u wilt bepalen waar de tabel zich bevindt, kunt u de tabel vooraf splitsen door de waarden voor het bereik op te geven waarmee de splitsing plaatsvindt. Als u bijvoorbeeld een tabel splitsing langs drie regio's wilt maken:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Indexontwerp

Een Phoenix-index is een HBase-tabel waarin een kopie van sommige of alle gegevens uit de geïndexeerde tabel wordt opgeslagen. Een index verbetert de prestaties voor specifieke typen query's.

Wanneer u meerdere indexen hebt gedefinieerd en vervolgens een query uitvoert op een tabel, selecteert Breda automatisch de beste index voor de query. De primaire index wordt automatisch gemaakt op basis van de primaire sleutels die u selecteert.

Voor verwachte query's kunt u ook secundaire indexen maken door hun kolommen op te geven.

Bij het ontwerpen van uw indexen:

* Maak alleen de indexen die u nodig hebt.
* Beperk het aantal indexen op regel matig bijgewerkte tabellen. Updates van een tabel worden omgezet in schrijf bewerkingen naar zowel de hoofd tabel als de index tabellen.

## <a name="create-secondary-indexes"></a>Secundaire indexen maken

Secundaire indexen kunnen de Lees prestaties verbeteren door te scha kelen wat een volledige tabel scan is in een zoek opdracht naar een punt, tegen de kosten van opslag ruimte en schrijf snelheid. Secundaire indexen kunnen worden toegevoegd of verwijderd nadat het maken van de tabel is gemaakt en wijzigingen aan bestaande query's hoeven niet te worden doorgevoerd: query's worden alleen sneller uitgevoerd. Afhankelijk van uw behoeften kunt u overwegen om gedekte indexen, functionele indexen of beide te maken.

### <a name="use-covered-indexes"></a>Gedekte indexen gebruiken

Gedekte indexen zijn indexen die gegevens uit de rij bevatten naast de waarden die worden geïndexeerd. Nadat u de gewenste index vermelding hebt gevonden, hebt u geen toegang nodig tot de primaire tabel.

In de tabel met contact personen kunt u bijvoorbeeld een secundaire index maken op alleen de kolom socialSecurityNum. Met deze secundaire index worden query's sneller uitgevoerd die filteren op socialSecurityNum waarden, maar het ophalen van andere veld waarden vereist een andere Lees bewerking voor de hoofd tabel.

|rowkey|       adres|   telefoon| voornaam| achternaam| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Davids-Jan-111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Davids| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Als u echter doorgaans de voor naam en achternaam wilt opzoeken op basis van de socialSecurityNum, kunt u een gedekte index maken die de voor naam en achternaam als werkelijke gegevens in de index tabel bevat:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Met deze gedekte index kan de volgende query alle gegevens ophalen, alleen door te lezen uit de tabel met de secundaire index:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Functionele indexen gebruiken

Met functionele indexen kunt u een index maken voor een wille keurige expressie die u verwacht in query's te gebruiken. Als er een functionele index aanwezig is en een query die expressie gebruikt, kan de index worden gebruikt om de resultaten op te halen in plaats van de gegevens tabel.

U kunt bijvoorbeeld een index maken waarmee u niet-hoofdletter gevoelige zoek acties kunt uitvoeren op de gecombineerde voor naam en achternaam van een persoon:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Query ontwerp

De belangrijkste overwegingen in query ontwerp zijn:

* Inzicht in het query plan en controleer het verwachte gedrag.
* Efficiënt verbinding maken.

### <a name="understand-the-query-plan"></a>Meer informatie over het query plan

Gebruik in [sqlline gebruiken](http://sqlline.sourceforge.net/)de uitleg gevolgd door uw SQL-query om het plan van de bewerkingen te bekijken dat door Breda wordt uitgevoerd. Controleer of het plan:

* Maakt gebruik van uw primaire sleutel als dat nodig is.
* Gebruikt de juiste secundaire indexen in plaats van de gegevens tabel.
* Maakt gebruik van bereik SCAN of slaat de SCAN zo mogelijk over, in plaats van de tabel SCAN.

#### <a name="plan-examples"></a>Voor beelden plannen

Stel, u hebt een tabel met de naam vluchten waarin informatie over de vertraging wordt opgeslagen.

Om alle vluchten te selecteren met een airlineid van `19805` , waarbij airlineid een veld is dat zich niet in de primaire sleutel of in een index bevindt:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Voer de uitleg opdracht als volgt uit:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

Het query plan ziet er als volgt uit:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

In dit plan noteert u de woord groep FULL SCAN OVER vluchten. Deze zin duidt op de uitvoering van een tabel SCAN over alle rijen in de tabel, in plaats van het gebruik van de optie SCAN of OVERs laan voor het scannen van een efficiënt bereik.

Stel nu dat u wilt zoeken naar vluchten op 2 januari 2014 voor de drager `AA` waarvan de flightnum groter is dan 1. We gaan ervan uit dat de kolommen Year, month, DayOfMonth, carrier en flightnum bestaan in de voorbeeld tabel en alle deel uitmaken van de samengestelde primaire sleutel. De query ziet er als volgt uit:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Laten we het plan voor deze query bekijken met:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Het resulterende plan is:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

De waarden tussen vier Kante haken zijn het waarden bereik voor de primaire sleutels. In dit geval worden de waarden van het bereik verholpen met jaar 2014, maand 1 en dayofmonth 2, maar waarden toestaan voor flightnum vanaf 2 en omhoog ( `*` ). Met dit query plan wordt bevestigd dat de primaire sleutel wordt gebruikt zoals verwacht.

Maak vervolgens een index op de tabel vluchten `carrier2_idx` met de naam alleen op het veld transporteur. Deze index omvat ook flightdate, tailnum, Origin en flightnum als gedekte kolommen waarvan de gegevens ook worden opgeslagen in de index.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Stel dat u de provider wilt ophalen samen met de flightdate en tailnum, zoals in de volgende query:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

U ziet dat deze index wordt gebruikt:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

Voor een volledige lijst van de items die kunnen worden weer gegeven in de resultaten van de uitleg van het plan, raadpleegt u de sectie uitleg plannen in de [Apache Phoenix Tuning Guide (Engelstalig](https://phoenix.apache.org/tuning_guide.html)).

### <a name="join-efficiently"></a>Efficiënt verbinding maken

Over het algemeen wilt u voor komen dat u deelneemt, tenzij een zijde klein is, met name bij query's die vaak worden uitgevoerd.

Als dat nodig is, kunt u grote samen voegingen doen met de `/*+ USE_SORT_MERGE_JOIN */` Hint, maar een grote samen voeging is een dure bewerking in grote aantallen rijen. Als de totale grootte van alle tabellen aan de rechter kant het beschik bare geheugen zou overschrijden, gebruikt u de `/*+ NO_STAR_JOIN */` Hint.

## <a name="scenarios"></a>Scenario's

In de volgende richt lijnen worden enkele algemene patronen beschreven.

### <a name="read-heavy-workloads"></a>Lees zware werk belastingen

Voor lees-zware use-cases, moet u ervoor zorgen dat u indexen gebruikt. U kunt ook gedekte indexen maken om Lees tijd te besparen.

### <a name="write-heavy-workloads"></a>Schrijf zware workloads

Voor schrijf zware werk belastingen waarbij de primaire sleutel gestaag neemt, maakt u zout buckets om te voor komen dat HOTS pots worden geschreven, tegen de kosten van een algemene Lees doorvoer vanwege de extra scans die nodig zijn. Wanneer u UPSERT gebruikt om een groot aantal records te schrijven, moet u de records automatisch door voeren en vervolgens batch-up uitvoeren.

### <a name="bulk-deletes"></a>Bulksgewijs verwijderen

Wanneer u een grote gegevensset verwijdert, schakelt u automatisch door voeren in voordat u de Verwijder query uitgeeft, zodat de client de rij-sleutels niet hoeft te onthouden voor alle verwijderde rijen. Met autocommit wordt voor komen dat de client de rijen buffert die worden beïnvloed door de verwijdering, zodat Phoenix ze rechtstreeks op de regio servers kan verwijderen zonder de kosten van het retour neren ervan naar de client.

### <a name="immutable-and-append-only"></a>Onveranderbaar en alleen toevoegen

Als uw scenario de schrijf snelheid voor gegevens integriteit nagaat, kunt u overwegen om het Write-Ahead logboek uit te scha kelen bij het maken van uw tabellen:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Zie [Apache Phoenix-grammatica](https://phoenix.apache.org/language/index.html#options)voor meer informatie over deze en andere opties.

## <a name="next-steps"></a>Volgende stappen

* [Apache Phoenix-afstemmings gids](https://phoenix.apache.org/tuning_guide.html)
* [Secundaire indexen](https://phoenix.apache.org/secondary_indexing.html)
