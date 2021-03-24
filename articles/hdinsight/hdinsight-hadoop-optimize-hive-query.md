---
title: Hive-query's in azure HDInsight optimaliseren
description: In dit artikel wordt beschreven hoe u uw Apache Hive query's optimaliseert in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/28/2020
ms.openlocfilehash: 551d985ea78e83397e507676c5fd7ecfce12ff7b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864241"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Apache Hive-query's in Azure HDInsight optimaliseren

In dit artikel worden enkele van de meest voorkomende prestatie optimalisaties beschreven die u kunt gebruiken om de prestaties van uw Apache Hive query's te verbeteren.

## <a name="cluster-type-selection"></a>Selectie van cluster type

In azure HDInsight kunt u Apache Hive query's uitvoeren op een aantal verschillende cluster typen. 

Kies het juiste cluster type om de prestaties te optimaliseren voor de behoeften van uw werk belasting:

* Kies het cluster type voor **interactieve** query's dat u wilt optimaliseren voor `ad hoc` interactieve query's. 
* Kies Apache **Hadoop** -cluster type om te optimaliseren voor Hive-query's die als batch proces worden gebruikt. 
* In **Spark** -en **HBase** -cluster typen kunnen ook Hive-query's worden uitgevoerd. Dit kan handig zijn als u die werk belastingen uitvoert. 

Zie [Wat is Apache Hive en HiveQL in azure HDInsight?](hadoop/hdinsight-use-hive.md)voor meer informatie over het uitvoeren van Hive-query's op verschillende typen HDInsight-clusters.

## <a name="scale-out-worker-nodes"></a>Werk knooppunten uitschalen

Als u het aantal worker-knoop punten in een HDInsight-cluster verhoogt, kan het werken met meer toewijzingen en minderers parallel worden uitgevoerd. Er zijn twee manieren om uitschalen in HDInsight te verg Roten:

* Wanneer u een cluster maakt, kunt u het aantal worker-knoop punten opgeven met behulp van de Azure Portal, Azure PowerShell of de opdracht regel interface.  Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. Op de volgende scherm afbeelding ziet u de configuratie van het worker-knoop punt op de Azure Portal:
  
    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png" alt-text="Knoop punten van Azure Portal cluster grootte":::

* Nadat u hebt gemaakt, kunt u het aantal worker-knoop punten ook bewerken om een cluster verder te schalen zonder dat u er een hoeft te maken:

    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png " alt-text="Grootte van Azure Portal schaal cluster":::

Zie [hdinsight-clusters schalen](hdinsight-scaling-best-practices.md) voor meer informatie over het schalen van hdinsight.

## <a name="use-apache-tez-instead-of-map-reduce"></a>Apache TEZ gebruiken in plaats van toewijzings reductie

[Apache TEZ](https://tez.apache.org/) is een alternatieve uitvoerings engine voor de MapReduce-engine. Voor HDInsight-clusters op basis van Linux is TEZ standaard ingeschakeld.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png" alt-text="Overzichts diagram HDInsight Apache TEZ":::

TEZ is sneller omdat:

* **Voer gerichte acyclische grafiek (dag) uit als één taak in de MapReduce-engine**. Voor de DAG moet elke set mappers worden gevolgd door één set verminderers. Deze vereiste zorgt ervoor dat meerdere MapReduce-taken worden uitgeschakeld voor elke Hive-query. TEZ heeft geen dergelijke beperking en kan complexe DAG verwerken als één taak voor het minimaliseren van de opstart overhead van taken.
* **Vermijd overbodige schrijf bewerkingen**. Meerdere taken worden gebruikt voor het verwerken van dezelfde Hive-query in de MapReduce-engine. De uitvoer van elke MapReduce-taak wordt naar HDFS geschreven voor tussenliggende gegevens. Omdat TEZ het aantal taken voor elke Hive-query minimaliseert, is het mogelijk om onnodige schrijf bewerkingen te voor komen.
* **Minimaliseert de opstart vertraging**. TEZ is een betere manier om de vertraging op te lossen door te verminderen van het aantal mappers dat moet worden gestart en verbetert ook de optimalisatie in.
* **Containers worden opnieuw gebruikt**. Wanneer mogelijke TEZ, worden containers opnieuw gebruikt om ervoor te zorgen dat de latentie van het starten van containers wordt gereduceerd.
* **Continue optimalisatie technieken**. De traditionele optimalisatie is uitgevoerd tijdens de compilatie fase. Er is echter meer informatie over de invoer beschikbaar waarmee tijdens runtime betere optimalisatie mogelijk is. TEZ maakt gebruik van voortdurende optimalisatie technieken waarmee het plan verder in de runtime fase kan worden geoptimaliseerd.

Zie [Apache TEZ](https://tez.apache.org/)(Engelstalig) voor meer informatie over deze concepten.

U kunt alle Hive-query TEZ inschakelen door de volgende set-opdracht voor het voor voegsel van de query in te stellen:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive partitioneren

I/O-bewerkingen zijn de belangrijkste prestatie knelpunt voor het uitvoeren van Hive-query's. De prestaties kunnen worden verbeterd als de hoeveelheid gegevens die moet worden gelezen, kan worden verminderd. Standaard worden in Hive-query's volledige Hive-tabellen gecontroleerd. Voor query's waarbij alleen een kleine hoeveelheid gegevens moeten worden gescand (bijvoorbeeld query's met filters), maakt dit gedrag onnodig overhead. Met hive-partitionering kunnen Hive-query's alleen toegang krijgen tot de benodigde hoeveelheid gegevens in Hive-tabellen.

Hive-partitionering wordt geïmplementeerd door de onbewerkte gegevens opnieuw in te delen in nieuwe directory's. Elke partitie heeft een eigen Bestands Directory. De partities worden gedefinieerd door de gebruiker. Het volgende diagram illustreert het partitioneren van een Hive-tabel op basis van het kolom *jaar*. Voor elk jaar wordt een nieuwe map gemaakt.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png" alt-text="HDInsight-Apache Hive partitioneren":::

Enkele overwegingen voor partitioneren:

* **Onder partitie** -partitionering op kolommen met slechts een paar waarden kunnen slechts enkele partities ontstaan. Als u bijvoorbeeld partitioneert op geslacht, worden er twee partities gemaakt voor het maken (mannelijk en vrouwelijk), dus Verminder de latentie met een maximum van de helft.
* Het is **niet meer dan een partitie** : aan de andere extreem, waardoor het maken van een partitie in een kolom met een unieke waarde (bijvoorbeeld userid) meerdere partities veroorzaakt. Bij overschrijding van de partitie wordt het namenode van het cluster veel zwaar belast, omdat het een groot aantal directory's moet afhandelen.
* **Vermijd gegevens scheefheid** : Kies uw partitie sleutel zodanig dat alle partities even groot zijn. Bijvoorbeeld: partitioneren op *status* kolom kan de distributie van gegevens scheef trekken. Omdat de status van Californië een populatie heeft die bijna 30x beter worden is van Vermont, kan de grootte van de partitie mogelijk afnemen en kunnen de prestaties enorm verschillen.

Als u een partitie tabel wilt maken, gebruikt u de *gepartitioneerde by* -component:

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

Zodra de gepartitioneerde tabel is gemaakt, kunt u statische partitionering of dynamische partitionering maken.

* **Statische partitionering** betekent dat u al Shard gegevens in de juiste directory's hebt. Met statische partities voegt u Hive-partities hand matig toe op basis van de maplocatie. Het volgende code fragment is een voor beeld.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamische partitionering** houdt in dat componenten automatisch partities voor u moeten maken. Omdat u de partitie tabel al hebt gemaakt op basis van de faserings tabel, hoeft u alleen gegevens in de gepartitioneerde tabel in te voegen:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Zie [gepartitioneerde tabellen](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)voor meer informatie.

## <a name="use-the-orcfile-format"></a>De ORCFile-indeling gebruiken

Hive ondersteunt verschillende bestands indelingen. Bijvoorbeeld:

* **Text**: de standaard bestands indeling en werkt met de meeste scenario's.
* **Avro**: geschikt voor interoperabiliteits scenario's.
* **Orc/Parquet**: het meest geschikt voor prestaties.

De indeling ORC (geoptimaliseerde rij in kolommen) is een zeer efficiënte manier om Hive-gegevens op te slaan. Vergeleken met andere indelingen heeft ORC de volgende voor delen:

* ondersteuning voor complexe typen, waaronder DateTime en complexe en semi-gestructureerde typen.
* Maxi maal 70% compressie.
* indexeert elke 10.000 rijen, waarmee rijen overs Laan worden toegestaan.
* een aanzienlijke verwijdering tijdens runtime.

Als u de ORC-indeling wilt inschakelen, maakt u eerst een tabel met de-component *opgeslagen als Orc*:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Vervolgens voegt u gegevens in de tabel ORC in vanuit de faserings tabel. Bijvoorbeeld:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

Meer informatie over de ORC-indeling vindt u in de [hand leiding voor de Apache Hive taal](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Met Vectorization kan een batch van 1024 rijen samen worden verwerkt in plaats van één rij tegelijk te verwerken. Dit betekent dat eenvoudige bewerkingen sneller worden uitgevoerd omdat minder interne code moet worden uitgevoerd.

Als u de vectorization voor voegsel van uw Hive-query wilt inschakelen met de volgende instelling:

```hive
set hive.vectorized.execution.enabled = true;
```

Zie voor meer informatie [Vector query's uitvoeren](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Andere optimalisatie methoden

Er zijn meer optimalisatie methoden die u kunt overwegen, bijvoorbeeld:

* **Bucket van onderdelen:** een techniek waarmee grote gegevens sets kunnen worden geclusterd of gesegmenteerd om de query prestaties te optimaliseren.
* **Deelname aan optimalisatie:** optimalisatie van de query-uitvoerings planning van de component om de efficiëntie van deelname te verbeteren en de behoefte aan gebruikers hints te verminderen. Zie [deelname aan optimalisatie](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)voor meer informatie.
* **Verkleiners verhogen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende algemene optimalisatie methoden voor Hive-query's geleerd. Lees de volgende artikelen voor meer informatie:

* [Apache Hive optimaliseren](./optimize-hive-ambari.md)
* [Vertraagde vlucht gegevens analyseren met behulp van interactieve Query's in HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Twitter-gegevens analyseren met Apache Hive in HDInsight](hdinsight-analyze-twitter-data-linux.md)
