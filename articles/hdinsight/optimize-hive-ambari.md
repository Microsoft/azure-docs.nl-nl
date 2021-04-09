---
title: Apache Hive optimaliseren met Apache Ambari in azure HDInsight
description: Gebruik de Apache Ambari Web-UI om Apache Hive te configureren en te optimaliseren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 69a4e769677b6f0200f4157305a3a125f82ee76d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864814"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Apache Hive optimaliseren met Apache Ambari in azure HDInsight

Apache Ambari is een webinterface voor het beheren en controleren van HDInsight-clusters. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)voor een inleiding tot de Ambari-webgebruikersinterface.

In de volgende secties worden de configuratie opties beschreven voor het optimaliseren van algemene Apache Hive prestaties.

1. Als u Hive-configuratie parameters wilt wijzigen, selecteert u **Hive** van de services-zijbalk.
1. Navigeer naar het tabblad **configuratie** .

## <a name="set-the-hive-execution-engine"></a>De engine voor het uitvoeren van de Hive instellen

Hive bevat twee uitvoerings engines: Apache Hadoop MapReduce en Apache TEZ. TEZ is sneller dan MapReduce. HDInsight Linux-clusters hebben TEZ als de standaard uitvoerings engine. De uitvoerings engine wijzigen:

1. Typ **Execution Engine** in het vak Filter op het tabblad **configuratie** van Hive.

    :::image type="content" source="./media/optimize-hive-ambari/ambari-search-execution.png" alt-text="Engine voor het uitvoeren van Apache Ambari Search" border="true":::

1. De standaard waarde van de **optimalisatie** -eigenschap is **TEZ**.

    :::image type="content" source="./media/optimize-hive-ambari/optimization-apache-tez.png" alt-text="Optimalisatie-Apache TEZ-engine" border="true":::

## <a name="tune-mappers"></a>Toewijzings toewijzingen afstemmen

Hadoop probeert één bestand te splitsen (*toewijzen*) aan meerdere bestanden en de resulterende bestanden parallel te verwerken. Het aantal mappers is afhankelijk van het aantal splitsingen. Met de volgende twee configuratie parameters wordt het aantal splitsingen voor de TEZ-uitvoerings engine gebrand:

* `tez.grouping.min-size`: Onderste limiet voor de grootte van een gegroepeerde splitsing, met een standaard waarde van 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: De bovengrens voor de grootte van een gegroepeerde splitsing, met een standaard waarde van 1 GB (1.073.741.824 bytes).

Als richt lijn voor prestaties moet u beide para meters verlagen om de latentie te verbeteren, verg Roten voor meer door voer.

Als u bijvoorbeeld vier toewijzings taken wilt instellen voor een gegevens grootte van 128 MB, stelt u beide para meters in op 32 MB elke (33.554.432 bytes).

1. Als u de limiet parameters wilt wijzigen, gaat u naar het tabblad **configuratie** van de TEZ-service. Vouw het deel venster **Algemeen** uit en zoek `tez.grouping.max-size` de `tez.grouping.min-size` para meters en.

1. Stel beide para meters in op **33.554.432** bytes (32 MB).

    :::image type="content" source="./media/optimize-hive-ambari/apache-tez-grouping-size.png" alt-text="Ambari TEZ-groeperings grootte van Apache" border="true":::

Deze wijzigingen zijn van invloed op alle TEZ-taken op de server. Kies de juiste parameter waarden om optimaal resultaat te krijgen.

## <a name="tune-reducers"></a>Verkleiners afstemmen

Apache ORC en Snappy bieden hoge prestaties. Hive kan echter te weinig vertragers hebben, waardoor er knel punten ontstaan.

Stel, u hebt een grootte van 50 GB voor de invoer gegevens. De gegevens in de ORC-indeling met Snappy-compressie is 1 GB. Hive schat het aantal verminderers dat nodig is als: (aantal bytes invoer voor mappers/ `hive.exec.reducers.bytes.per.reducer` ).

Met de standaard instellingen is dit voor beeld vier verminderers.

Met de `hive.exec.reducers.bytes.per.reducer` para meter wordt het aantal verwerkte bytes per verminderr opgegeven. De standaard waarde is 64 MB. Het afstemmen van deze waarde is groter, en kan de prestaties verbeteren. Als u het apparaat te laag afstemt, kunnen er ook te veel vertraagden worden geproduceerd, waardoor de prestaties mogelijk nadelig worden beïnvloed. Deze para meter is gebaseerd op uw specifieke gegevens vereisten, compressie-instellingen en andere omgevings factoren.

1. Als u de para meter wilt wijzigen, gaat u naar het tabblad **configuratie** van Hive en zoekt u de para meter **gegevens per verkorter** op de pagina instellingen.

    :::image type="content" source="./media/optimize-hive-ambari/ambari-data-per-reducer.png" alt-text="Apache Ambari-gegevens per Reductier" border="true":::

1. Selecteer **bewerken** om de waarde te wijzigen in 128 MB (134.217.728 bytes) en druk vervolgens op **Enter** om op te slaan.

    :::image type="content" source="./media/optimize-hive-ambari/data-per-reducer-edited.png" alt-text="Ambari-gegevens per reductie bewerking" border="true":::
  
    Op basis van een invoer grootte van 1.024 MB, met 128 MB aan gegevens per verkorter, zijn er acht verminderingen (1024/128).

1. Een onjuiste waarde voor de **gegevens per reduceerere** para meter kan leiden tot een groot aantal verminderers, wat een negatieve invloed heeft op de prestaties van query's. Als u het maximum aantal verminderers wilt beperken, stelt `hive.exec.reducers.max` u een geschikte waarde in. De standaard waarde is 1009.

## <a name="enable-parallel-execution"></a>Parallelle uitvoering inschakelen

Een Hive-query wordt uitgevoerd in een of meer fasen. Als de onafhankelijke fasen parallel kunnen worden uitgevoerd, worden de query prestaties verbeterd.

1. Als u parallelle query uitvoering wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en zoekt u naar de `hive.exec.parallel` eigenschap. De standaardwaarde is false. Wijzig de waarde in True en druk vervolgens op **Enter** om de waarde op te slaan.

1. Als u het aantal taken dat parallel moet worden uitgevoerd, wilt beperken, wijzigt u de `hive.exec.parallel.thread.number` eigenschap. De standaard waarde is 8.

    :::image type="content" source="./media/optimize-hive-ambari/apache-hive-exec-parallel.png" alt-text="Parallelle weer gave Apache Hive exec" border="true":::

## <a name="enable-vectorization"></a>Vectorization inschakelen

Hive verwerkt gegevens rijen per rij. Vectorization-doorstuur component voor het verwerken van gegevens in blokken van 1.024 rijen in plaats van één rij tegelijk. Vectorization is alleen van toepassing op de ORC-bestands indeling.

1. Als u een vector query-uitvoering wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en zoekt u naar de `hive.vectorized.execution.enabled` para meter. De standaard waarde is True voor Hive 0.13.0 of hoger.

1. Als u een vector uitvoering wilt inschakelen voor de reductie van de query, stelt `hive.vectorized.execution.reduce.enabled` u de para meter in op waar. De standaardwaarde is false.

    :::image type="content" source="./media/optimize-hive-ambari/hive-vectorized-execution.png" alt-text="Apache Hive vector uitvoering" border="true":::

## <a name="enable-cost-based-optimization-cbo"></a>Optimalisatie op basis van kosten inschakelen (CBO)

Hive volgt standaard een set regels om één optimaal query-uitvoerings plan te vinden. Met CBO (cost-based Optimization) worden meerdere plannen geëvalueerd voor het uitvoeren van een query. En wijst de kosten aan elk plan toe en bepaalt vervolgens het goedkoopste-plan om een query uit te voeren.

Om CBO in te scha kelen, gaat u naar **Hive**  >  **configuratie**  >  **instellingen** en zoek **optimalisatie op basis van kosten inschakelen**. vervolgens schakelt u de wissel knop in **op** aan.

:::image type="content" source="./media/optimize-hive-ambari/hdinsight-cbo-config.png" alt-text="Optimalisatie op basis van HDInsight-kosten" border="true":::

De volgende aanvullende configuratie parameters verhogen Hive-query prestaties wanneer CBO is ingeschakeld:

* `hive.compute.query.using.stats`

    Als deze eigenschap is ingesteld op True, gebruikt Hive statistieken die zijn opgeslagen in de meta Store om eenvoudige query's te beantwoorden, zoals `count(*)` .

    :::image type="content" source="./media/optimize-hive-ambari/hive-compute-query-using-stats.png" alt-text="Reken query Apache Hive met behulp van statistieken" border="true":::

* `hive.stats.fetch.column.stats`

    Kolom statistieken worden gemaakt wanneer CBO is ingeschakeld. Hive maakt gebruik van kolom statistieken, die zijn opgeslagen in de meta Store, voor het optimaliseren van query's. Het ophalen van kolom statistieken voor elke kolom duurt langer wanneer het aantal kolommen hoog is. Als deze instelling is ingesteld op False, wordt het ophalen van kolom statistieken uit de meta Store uitgeschakeld.

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png" alt-text="Apache Hive stats kolom statistieken instellen" border="true":::

* `hive.stats.fetch.partition.stats`

    Standaard partitie statistieken, zoals het aantal rijen, de grootte van de gegevens en de bestands grootte, worden opgeslagen in de meta Store. Als deze eigenschap is ingesteld op True, worden de partitie statistieken opgehaald uit de meta Store. Als false is ingesteld, wordt de bestands grootte opgehaald uit het bestands systeem. En het aantal rijen wordt opgehaald uit het schema van de rij.

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png" alt-text="Statistieken voor de Hive-partitie instellen" border="true":::

## <a name="enable-intermediate-compression"></a>Tussenliggende compressie inschakelen

Met toewijzings taken maakt u tussenliggende bestanden die worden gebruikt door de verkorter taken. Tussenliggende compressie verkleint de tussenliggende bestands grootte.

Hadoop-taken zijn meestal I/O-knel punten. Het comprimeren van gegevens kan I/O en de algehele netwerk overdracht versnellen.

De beschik bare compressie typen zijn:

| Indeling | Hulpprogramma | Algoritme | Bestands extensie | Splitsbaar? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Yes |
| LZO | `Lzop` | LZO | `.lzo` | Ja, indien geïndexeerd |
| Snappy | N.v.t. | Snappy | Snappy | No |

Een algemene regel is dat de compressie methode splitsbaar belang rijk is, anders worden enkele toewijzingen gemaakt. Als de invoer gegevens tekst is, `bzip2` is de beste optie. Voor de indeling ORC is Snappy de snelste compressie optie.

1. Als u tussenliggende compressie wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en stelt `hive.exec.compress.intermediate` u de para meter in op True. De standaardwaarde is false.

    :::image type="content" source="./media/optimize-hive-ambari/hive-exec-compress-intermediate.png" alt-text="' Hive exec comp '" border="true":::

    > [!NOTE]  
    > Als u tussenliggende bestanden wilt comprimeren, kiest u een compressie-codec met lagere CPU-kosten, zelfs als de codec geen hoge compressie-uitvoer heeft.

1. Als u de tussenliggende compressie-codec wilt instellen, voegt u de aangepaste eigenschap toe `mapred.map.output.compression.codec` aan het `hive-site.xml` `mapred-site.xml` bestand of.

1. Een aangepaste instelling toevoegen:

    a. Navigeer naar **Hive**  >  **configurations**  >  **Geavanceerde**  >  **aangepaste Hive-site**.

    b. Selecteer **eigenschap toevoegen...** aan de onderkant van het aangepaste Hive-site deel venster.

    c. Voer in het venster Eigenschappen toevoegen `mapred.map.output.compression.codec` als sleutel en `org.apache.hadoop.io.compress.SnappyCodec` als de waarde in.

    d. Selecteer **Toevoegen**.

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property.png" alt-text="' Apache Hive aangepaste eigenschap toevoegen '" border="true":::

    Met deze instelling wordt het tussenliggende bestand gecomprimeerd met Snappy-compressie. Zodra de eigenschap is toegevoegd, wordt deze weer gegeven in het deel venster aangepaste Hive-site.

    > [!NOTE]  
    > Met deze procedure wordt het `$HADOOP_HOME/conf/hive-site.xml` bestand gewijzigd.

## <a name="compress-final-output"></a>Laatste uitvoer comprimeren

De uiteindelijke Hive-uitvoer kan ook worden gecomprimeerd.

1. Als u de laatste Hive-uitvoer wilt comprimeren, gaat u naar het tabblad **configuratie** van Hive en stelt `hive.exec.compress.output` u de para meter in op waar. De standaardwaarde is false.

1. Als u de compressie-codec voor uitvoer wilt kiezen, voegt `mapred.output.compression.codec` u de aangepaste eigenschap toe aan het deel venster aangepaste Hive-site, zoals wordt beschreven in stap 3 van de vorige sectie.

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property2.png" alt-text="Aangepaste eigenschap ADD2 Apache Hive" border="true":::

## <a name="enable-speculative-execution"></a>Speculatieve uitvoering inschakelen

Speculatieve uitvoering start een bepaald aantal dubbele taken voor het detecteren en weigeren van de lijst met langzame taak tracering. Bij het verbeteren van de algehele taak uitvoering door afzonderlijke taak resultaten te optimaliseren.

Speculatieve uitvoering moet niet worden ingeschakeld voor langlopende MapReduce-taken met grote hoeveel heden invoer.

* Als u speculatieve uitvoering wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en stelt `hive.mapred.reduce.tasks.speculative.execution` u de para meter in op True. De standaardwaarde is false.

    :::image type="content" source="./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png" alt-text="' Hive mapred verminderen taken speculatieve uitvoering '" border="true":::

## <a name="tune-dynamic-partitions"></a>Dynamische partities afstemmen

Hive maakt het mogelijk om dynamische partities te maken bij het invoegen van records in een tabel, zonder dat elke partitie vooraf wordt gedefinieerd. Deze mogelijkheid is een krachtig onderdeel. Hoewel dit kan leiden tot het maken van een groot aantal partities. En een groot aantal bestanden voor elke partitie.

1. Voor Hive kunnen dynamische partities worden uitgevoerd, de `hive.exec.dynamic.partition` parameter waarde moet True zijn (de standaard instelling).

1. Wijzig de dynamische partitie modus in *strict*. In de strikte modus moet ten minste één partitie statisch zijn. Met deze instelling wordt voor komen dat query's zonder het partitie filter in de component WHERE, dat wil zeggen, *strikte* query's die alle partities scannen, worden voor komen. Ga naar het tabblad **configuratie** van Hive en stel `hive.exec.dynamic.partition.mode` in op **strikt**. De standaard waarde is niet **strikt**.

1. Wijzig de para meter om het aantal dynamische partities te beperken dat moet worden gemaakt `hive.exec.max.dynamic.partitions` . De standaard waarde is 5000.

1. Als u het totale aantal dynamische partities per knoop punt wilt beperken, wijzigt u `hive.exec.max.dynamic.partitions.pernode` . De standaard waarde is 2000.

## <a name="enable-local-mode"></a>Lokale modus inschakelen

Met de lokale modus kunnen componenten alle taken van een taak op één computer uitvoeren. Of soms in één proces. Deze instelling verbetert de query prestaties als de invoer gegevens klein zijn. En de overhead van het starten van query's verbruikt een aanzienlijk percentage van de algemene query-uitvoering.

Als u de lokale modus wilt inschakelen, voegt `hive.exec.mode.local.auto` u de para meter toe aan het aangepaste Hive-site paneel, zoals wordt beschreven in stap 3 van de sectie [tussenliggende compressie inschakelen](#enable-intermediate-compression) .

:::image type="content" source="./media/optimize-hive-ambari/hive-exec-mode-local-auto.png" alt-text="Modus voor Apache Hive uitvoering lokale automatisch" border="true":::

## <a name="set-single-mapreduce-multigroup-by"></a>Eén MapReduce instellen op meerdere groepen

Als deze eigenschap is ingesteld op True, genereert een query met meerdere groepen met algemene groep-op sleutels één MapReduce-taak.  

Als u dit gedrag wilt inschakelen, voegt `hive.multigroupby.singlereducer` u de para meter toe aan het deel venster aangepaste Hive-site, zoals wordt beschreven in stap 3 van de sectie [tussenliggende compressie inschakelen](#enable-intermediate-compression) .

:::image type="content" source="./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png" alt-text="Hive-set met één MapReduce meerdere groepen op" border="true":::

## <a name="additional-hive-optimizations"></a>Aanvullende Hive-optimalisaties

In de volgende secties worden extra optimalisaties voor onderdelen beschreven die u kunt instellen.

### <a name="join-optimizations"></a>Deelname aan optimalisaties

Het standaard type voor samen voegen in Hive is een *wille keurige samen voeging*. In Hive leest speciale mappers de invoer en wordt een koppelings sleutel/-waardepaar naar een tussenliggend bestand verzenden. Hadoop sorteert en voegt deze paren samen in een wille keurige fase. Deze fase in wille keurige volg orde is duur. Het selecteren van de juiste koppeling op basis van uw gegevens kan de prestaties aanzienlijk verbeteren.

| Type samen voeging | Wanneer | Hoe | Hive-instellingen | Opmerkingen |
| --- | --- | --- | --- | --- |
| In wille keurige volg orde | <ul><li>Standaard keuze</li><li>Werkt altijd</li></ul> | <ul><li>Lees bewerkingen van een deel van een van de tabellen</li><li>Buckets en sorteren op koppelings sleutel</li><li>Hiermee wordt één Bucket naar elke reductie verzonden</li><li>De deelname aan de minder kant wordt uitgevoerd</li></ul> | Er is geen belang rijke Hive-instelling vereist | Werkt elke keer |
| Kaart toevoegen | <ul><li>Een tabel kan in het geheugen passen</li></ul> | <ul><li>Hiermee wordt een kleine tabel in de hash-tabel van het geheugen gelezen</li><li>Streamt via een deel van het grote bestand</li><li>Koppelt elke record uit een hash-tabel</li><li>Samen voegingen zijn alleen door de Mapper</li></ul> | `hive.auto.confvert.join=true` | Snel, maar beperkt |
| Bucket voor samen voegen sorteren | Als beide tabellen zijn: <ul><li>Op dezelfde manier gesorteerd</li><li>Hetzelfde gebuckd</li><li>Deel nemen aan de gesorteerde/gebuckeerde kolom</li></ul> | Elk proces: <ul><li>Hiermee wordt een Bucket uit elke tabel gelezen</li><li>De rij met de laagste waarde verwerkt</li></ul> | `hive.auto.convert.sortmerge.join=true` | Handig |

### <a name="execution-engine-optimizations"></a>Optimalisaties van uitvoerings engine

Aanvullende aanbevelingen voor het optimaliseren van de engine voor het uitvoeren van de Hive:

| Instelling | Aanbevolen | Standaard HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Waar = veiliger, langzamer; ONWAAR = sneller | onjuist |
| `tez.am.resource.memory.mb` | 4-GB bovengrens voor de meeste | Automatisch afgestemd |
| `tez.session.am.dag.submit.timeout.secs` | 300 + | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000 + | 10.000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000 + | 20.000 |

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache Hive-query's in Azure HDInsight optimaliseren](./hdinsight-hadoop-optimize-hive-query.md)
* [Clusters optimaliseren](./optimize-hive-ambari.md)
* [Apache HBase optimaliseren](./optimize-hbase-ambari.md)
* [Apache Pig optimaliseren](./optimize-pig-ambari.md)
