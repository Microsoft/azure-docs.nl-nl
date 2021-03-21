---
title: Hive-tabellen maken en gegevens laden uit Blob Storage-team data Science process
description: Gebruik Hive-query's om Hive-tabellen te maken en gegevens te laden uit Azure Blob-opslag. Partitioneer Hive-tabellen en gebruik de ORC-indeling (Optimized Row in kolommen) om de query prestaties te verbeteren.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5d61c0f5f26bc46b9c4a5bc4a793df1e10710004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006726"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive-tabellen maken en gegevens laden vanuit Azure Blob Storage

Dit artikel bevat algemene Hive-query's waarmee Hive-tabellen worden gemaakt en gegevens worden geladen vanuit Azure Blob-opslag. Er worden ook enkele richt lijnen gegeven voor het partitioneren van Hive-tabellen en het gebruik van de ORC-opmaak (Optimized Row in kolommen) om de query prestaties te verbeteren.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u het volgende hebt:

* Er is een Azure Storage-account gemaakt. Zie [over Azure Storage-accounts](../../storage/common/storage-introduction.md)als u instructies nodig hebt.
* Er is een aangepast Hadoop-cluster ingericht met de HDInsight-service.  Zie [installatie clusters in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)als u instructies nodig hebt.
* Externe toegang tot het cluster ingeschakeld, aangemeld en de Hadoop Command-Line-console geopend. Zie [Apache Hadoop-clusters beheren](../../hdinsight/hdinsight-administer-use-portal-linux.md)als u instructies nodig hebt.

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob Storage
Als u een virtuele machine van Azure hebt gemaakt met behulp van de instructies in [een virtuele Azure-machine instellen voor geavanceerde analyses](../../machine-learning/data-science-virtual-machine/overview.md), moet dit script bestand zijn gedownload naar de map *C: \\ gebruikers \\ \<user name\> \\ documenten \\ Data Science scripts* op de virtuele machine. Deze Hive-query's vereisen alleen dat u een gegevens schema en Azure Blob-opslag configuratie opgeeft in de juiste velden die kunnen worden verzonden.

We gaan ervan uit dat de gegevens voor Hive-tabellen een **gedecomprimeerde** tabel indeling hebben en dat de gegevens zijn geüpload naar de standaard container van het opslag account dat wordt gebruikt door het Hadoop-cluster.

Als u wilt oefenen met de **NYC**, moet u het volgende doen:

* **down load** de 24 [NYCe taxi-reis gegevens](https://www.andresmh.com/nyctaxitrips) bestanden (12 retour bestanden en 12-ritbedrag bestanden),
* **pak** alle bestanden uit in CSV-bestanden en klik vervolgens
* **Upload** deze naar de standaard (of geschikte container) van het Azure Storage-account; opties voor een dergelijk account worden weer gegeven bij [gebruik Azure Storage met het onderwerp Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Het proces voor het uploaden van de CSV-bestanden naar de standaard container op het opslag account vindt u op deze [pagina](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Hive-query's verzenden
Hive-query's kunnen worden verzonden met behulp van:

* [Hive-query's verzenden via Hadoop-opdracht regel in hoofd knooppunt van Hadoop-cluster](#headnode)
* [Hive-query's verzenden met de Hive-editor](#hive-editor)
* [Hive-query's met Azure PowerShell-opdrachten verzenden](#ps)

Hive-query's zijn SQL-like. Als u bekend bent met SQL, is het mogelijk dat het [onderdeel voor SQL-gebruikers Cheat Sheet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) nuttig is.

Bij het verzenden van een Hive-query kunt u ook de bestemming van de uitvoer van Hive-query's beheren, of deze zich op het scherm bevindt of naar een lokaal bestand op het hoofd knooppunt of naar een Azure-Blob.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Hive-query's verzenden via Hadoop-opdracht regel in hoofd knooppunt van Hadoop-cluster
Als de Hive-query complex is, leidt het verzenden van deze rechtstreeks in het hoofd knooppunt van het Hadoop-cluster ertoe toe dat deze sneller kan worden omzeild dan het verzenden met een Hive-editor of Azure PowerShell scripts.

Meld u aan bij het hoofd knooppunt van het Hadoop-cluster, open de Hadoop-opdracht regel op het bureau blad van het hoofd knooppunt en voer de opdracht in `cd %hive_home%\bin` .

U kunt op drie manieren Hive-query's verzenden in de Hadoop-opdracht regel:

* rechtstreeks
* '. HQL-bestanden gebruiken
* met de Hive-opdracht console

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Hive-query's rechtstreeks verzenden in de Hadoop-opdracht regel.
U kunt de opdracht uitvoeren als `hive -e "<your hive query>;` om eenvoudige Hive-query's rechtstreeks in Hadoop-opdracht regel in te dienen. Hier volgt een voor beeld waarin het rode vak een overzicht geeft van de opdracht waarmee de Hive-query wordt verzonden. in het groene vak wordt de uitvoer van de Hive-query beschreven.

![Opdracht voor het verzenden van Hive-query met uitvoer van Hive-query](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Hive-query's in. HQL-bestanden verzenden
Wanneer de Hive-query ingewik kelder is en meerdere lijnen heeft, is het bewerken van query's in opdracht regel of Hive-opdracht console niet praktisch. U kunt ook een tekst editor gebruiken in het hoofd knooppunt van het Hadoop-cluster om de Hive-query's in een. HQL-bestand op te slaan in een lokale map van het hoofd knooppunt. Vervolgens kan de Hive-query in het bestand '. HQL ' als volgt worden verzonden met behulp van het `-f` argument:

```console
hive -f "<path to the '.hql' file>"
```

![Hive-query in een. HQL-bestand](./media/move-hive-tables/run-hive-queries-3.png)

**Scherm afdruk van voortgangs status onderdrukken van Hive-query's**

Na het uitvoeren van Hive-query op de Hadoop-opdracht regel wordt de voortgang van de taak toewijzen/verminderen standaard afgedrukt op het scherm. Als u de scherm afdruk van de voortgang van de taak toewijzing/verminderen wilt onderdrukken, kunt u `-S` als volgt een argument ("S" in hoofd letters) op de opdracht regel gebruiken:

```console
hive -S -f "<path to the '.hql' file>"
hive -S -e "<Hive queries>"
```

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive-query's in Hive-opdracht console verzenden.
U kunt ook eerst de Hive-opdracht console invoeren door de opdracht `hive` in Hadoop opdracht regel uit te voeren en vervolgens Hive-query's in Hive-opdracht console te verzenden. Hier volgt een voorbeeld. In dit voor beeld markeren de twee rode vakjes de opdrachten die worden gebruikt om de Hive-opdracht console in te voeren en de Hive-query die respectievelijk in de Hive-opdracht console is ingediend. In het groene vak wordt de uitvoer van de Hive-query gemarkeerd.

![Component-opdracht console openen en opdracht invoeren, Hive-query-uitvoer weer geven](./media/move-hive-tables/run-hive-queries-2.png)

In de vorige voor beelden worden de Hive-query resultaten rechtstreeks op het scherm uitgevoerd. U kunt de uitvoer ook schrijven naar een lokaal bestand op het hoofd knooppunt of naar een Azure-Blob. Vervolgens kunt u andere hulpprogram ma's gebruiken om de uitvoer van Hive-query's verder te analyseren.

**Resultaten van de Hive-query van de uitvoer naar een lokaal bestand.**
Als u de resultaten van de Hive-query wilt uitvoeren naar een lokale map op het hoofd knooppunt, moet u de Hive-query in de Hadoop-opdracht regel als volgt indienen:

```console
hive -e "<hive query>" > <local path in the head node>
```

In het volgende voor beeld wordt de uitvoer van Hive-query naar een bestand `hivequeryoutput.txt` in de map geschreven `C:\apps\temp` .

![Scherm afbeelding toont de uitvoer van de Hive-query in een Hadoop-opdracht regel venster.](./media/move-hive-tables/output-hive-results-1.png)

**Resultaten van de query van de component uitvoer naar een Azure-Blob**

U kunt ook de resultaten van de Hive-query uitvoeren naar een Azure-Blob in de standaard container van het Hadoop-cluster. De Hive-query hiervoor is als volgt:

```console
insert overwrite directory wasb:///<directory within the default container> <select clause from ...>
```

In het volgende voor beeld wordt de uitvoer van Hive-query geschreven naar een BLOB-map `queryoutputdir` binnen de standaard container van het Hadoop-cluster. Hier hoeft u alleen de naam van de map op te geven, zonder de naam van de blob. Er wordt een fout gegenereerd als u namen van mappen en blobs opgeeft, zoals `wasb:///queryoutputdir/queryoutput.txt` .

![Scherm afbeelding toont de vorige opdracht in het opdracht regel venster Hadoop.](./media/move-hive-tables/output-hive-results-2.png)

Als u de standaard container van het Hadoop-cluster met Azure Storage Explorer opent, ziet u de uitvoer van de Hive-query, zoals weer gegeven in de volgende afbeelding. U kunt het filter (gemarkeerd door rood vak) Toep assen om alleen de BLOB op te halen met de opgegeven letters in namen.

![Azure Storage Explorer de uitvoer van de Hive-query wordt weer gegeven](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Hive-query's verzenden met de Hive-editor
U kunt ook de query console (Hive-editor) gebruiken door een URL van het formulier *https: \/ / \<Hadoop cluster name> . azurehdinsight.net/Home/HiveEditor* in te voeren in een webbrowser. U moet zijn aangemeld met de informatie in deze console en daarom moet u uw Hadoop-cluster referenties hier opgeven.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Hive-query's met Azure PowerShell-opdrachten verzenden
U kunt ook Power shell gebruiken om Hive-query's te verzenden. Zie [Hive-taken verzenden met Power shell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)voor instructies.

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Hive-data base en-tabellen maken
De Hive-query's worden gedeeld in de [github-opslag plaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) en kunnen van daaruit worden gedownload.

Dit is de Hive-query waarmee een Hive-tabel wordt gemaakt.

```hiveql
create database if not exists <database name>;
CREATE EXTERNAL TABLE if not exists <database name>.<table name>
(
    field1 string,
    field2 int,
    field3 float,
    field4 double,
    ...,
    fieldN string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");
```

Hier volgen de beschrijvingen van de velden die u nodig hebt om in te sluiten en andere configuraties:

* **\<database name\>**: de naam van de data base die u wilt maken. Als u alleen de standaard database wilt gebruiken, kan de query "*Data Base maken...*" worden wegge laten.
* **\<table name\>**: de naam van de tabel die u in de opgegeven Data Base wilt maken. Als u de standaard database wilt gebruiken, kan de tabel rechtstreeks worden aangeduid door *\<table name\>* zonder \<database name\> .
* **\<field separator\>**: het scheidings teken dat velden in het gegevens bestand begrenst dat naar de Hive-tabel moet worden geüpload.
* **\<line separator\>**: het scheidings teken waarmee regels in het gegevens bestand worden gelimiteerd.
* **\<storage location\>**: de locatie van de Azure Storage om de gegevens van Hive-tabellen op te slaan. Als u geen *locatie \<storage location\>* opgeeft, worden de data base en de tabellen standaard opgeslagen in *Hive/Warehouse/* Directory in de standaard container van het Hive-cluster. Als u de opslag locatie wilt opgeven, moet de opslag locatie zich in de standaard container voor de data base en de tabellen bevinden. Deze locatie moet worden aangeduid als locatie relatief ten opzichte van de standaard container van het cluster in de indeling *' wasb:/// \<directory 1> /'* of *' wasb:/// \<directory 1> / \<directory 2> /'*, enzovoort. Nadat de query is uitgevoerd, worden de relatieve directory's in de standaard container gemaakt.
* **TBLPROPERTIES ("Skip. header. line. Count" = "1")**: als het gegevens bestand een header-regel bevat, moet u deze eigenschap toevoegen **aan het einde** van de query *Create Table* . Anders wordt de header regel geladen als een record in de tabel. Als het gegevens bestand geen header-regel heeft, kan deze configuratie worden wegge laten in de query.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Gegevens laden in Hive-tabellen
Dit is de Hive-query waarmee gegevens worden geladen in een Hive-tabel.

```hiveql
LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;
```

* **\<path to blob data\>**: Als het blobbestand dat moet worden geüpload naar de Hive-tabel zich in de standaard container van het HDInsight Hadoop-cluster bevindt, *\<path to blob data\>* moet de indeling *' wasb:// \<directory in this container> / \<blob file name> '* zijn. Het blobbestand kan ook een extra container van het HDInsight Hadoop-cluster zijn. In dit geval *\<path to blob data\>* moet de indeling *' wasb:// \<container name> @ \<storage account name> . blob.core.Windows.net/ \<blob file name> '* zijn.

  > [!NOTE]
  > De BLOB-gegevens die naar de Hive-tabel moeten worden geüpload, moeten zich in de standaard-of extra container van het opslag account voor het Hadoop-cluster bevinden. Als dat niet het geval is, kan de query voor het *laden van gegevens* geen toegang krijgen tot de gegevens.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Geavanceerde onderwerpen: gepartitioneerde tabel-en opgeslagen Hive-gegevens in ORC-indeling
Als de gegevens groot zijn, is het partitioneren van de tabel nuttig voor query's die slechts enkele partities van de tabel hoeven te scannen. Het is bijvoorbeeld redelijk de logboek gegevens van een website op datums te partitioneren.

Naast het partitioneren van Hive-tabellen is het ook handig om de Hive-gegevens op te slaan in de ORC-indeling (Optimized Row in kolom vorm). Voor meer informatie over de ORC-indeling raadpleegt <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">u Orc-bestanden gebruiken om de prestaties te verbeteren wanneer de Hive gegevens leest, schrijft en verwerkt</a>.

### <a name="partitioned-table"></a>Gepartitioneerde tabel
Hier volgt de Hive-query waarmee een gepartitioneerde tabel wordt gemaakt en er gegevens in worden geladen.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
(field1 string,
...
fieldN string
)
PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
    PARTITION (<partitionfieldname>=<partitionfieldvalue>);
```

Bij het uitvoeren van query's op gepartitioneerde tabellen wordt aanbevolen om de partitie voorwaarde toe te voegen aan het **begin** van de `where` component, waardoor de efficiëntie van de zoek opdracht wordt verbeterd.

```hiveql
select
    field1, field2, ..., fieldN
from <database name>.<partitioned table name>
where <partitionfieldname>=<partitionfieldvalue> and ...;
```

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Hive-gegevens opslaan in de ORC-indeling
U kunt gegevens niet rechtstreeks laden vanuit Blob-opslag in Hive-tabellen die zijn opgeslagen in de ORC-indeling. Hier volgen de stappen die u moet uitvoeren om gegevens van Azure-blobs te laden naar Hive-tabellen die zijn opgeslagen in de ORC-indeling.

Een externe tabel maken **die is opgeslagen als TEXTFILE** en gegevens uit de Blob-opslag in de tabel laden.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' STORED AS TEXTFILE
    LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;
```

Maak een interne tabel met hetzelfde schema als de externe tabel in stap 1, met hetzelfde veld scheidings teken en sla de Hive-gegevens op in de ORC-indeling.

```hiveql
CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;
```

Selecteer gegevens uit de externe tabel in stap 1 en voeg deze in de tabel ORC in

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name>
    SELECT * FROM <database name>.<external textfile table name>;
```

> [!NOTE]
> Als de TEXTFILE-tabel *\<database name\> . \<external textfile table name\>* heeft partities. in stap 3 selecteert de `SELECT * FROM <database name>.<external textfile table name>` opdracht de partitie variabele als een veld in de geretourneerde gegevensset. Deze in te voegen in de *\<database name\> . \<ORC table name\>* mislukt sinds *\<database name\> . \<ORC table name\>* heeft niet de partitie variabele als een veld in het tabel schema. In dit geval moet u de velden die u wilt invoegen specifiek selecteren *\<database name\> . \<ORC table name\>* als volgt:
>
>

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
    SELECT field1, field2, ..., fieldN
    FROM <database name>.<external textfile table name>
    WHERE <partition variable>=<partition value>;
```

Het is veilig om de te verwijderen *\<external text file table name\>* Wanneer u de volgende query gebruikt nadat alle gegevens zijn ingevoegd in *\<database name\> . \<ORC table name\>*:

```hiveql
    DROP TABLE IF EXISTS <database name>.<external textfile table name>;
```

Nadat u deze procedure hebt voltooid, moet u een tabel met gegevens in de ORC-indeling maken die kan worden gebruikt.  
