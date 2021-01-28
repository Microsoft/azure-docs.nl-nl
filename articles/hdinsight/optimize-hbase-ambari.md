---
title: Apache HBase optimaliseren met Apache Ambari in azure HDInsight
description: Gebruik de Apache Ambari Web-UI om Apache HBase te configureren en te optimaliseren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 307e9decad35c20885fbe550cc1f7de58c9386c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942597"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Apache HBase optimaliseren met Apache Ambari in azure HDInsight

Apache Ambari is een webinterface voor het beheren en controleren van HDInsight-clusters. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)voor een inleiding tot de Ambari-webgebruikersinterface.

De Apache HBase-configuratie wordt gewijzigd op het tabblad **HBase configs** . In de volgende secties worden enkele belang rijke configuratie-instellingen beschreven die van invloed zijn op de prestaties van HBase.

## <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE instellen

De HBase-Heap-grootte geeft de maximale hoeveelheid heap op die in mega bytes per *regio* en op de *hoofd* servers moet worden gebruikt. De standaard waarde is 1.000 MB. Deze waarde moet worden afgestemd op de werk belasting van het cluster.

1. Als u wilt wijzigen, gaat u naar het deel venster **Geavanceerde HBase-env** op het tabblad HBase **configs** en zoekt u de `HBASE_HEAPSIZE` instelling.

1. Wijzig de standaard waarde in 5.000 MB.

    ![' Apache Ambari HBase Memory heapsize '](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Lees zware workloads optimaliseren

De volgende configuraties zijn belang rijk voor het verbeteren van de prestaties van lees-en zware werk belastingen.

### <a name="block-cache-size"></a>Grootte van blok cache

De blok cache is de Lees cache. De grootte wordt bepaald door de `hfile.block.cache.size` para meter. De standaard waarde is 0,4, 40 procent van het totale server geheugen van de regio. Hoe groter de grootte van de blok cache, hoe sneller de wille keurige Lees bewerkingen worden uitgevoerd.

1. Als u deze para meter wilt wijzigen, gaat u naar het tabblad **instellingen** op het tabblad HBase- **configuratie** en zoekt u vervolgens **% van RegionServer die zijn toegewezen aan lees buffers**.

    ![HBase geheugen blok cache grootte Apache](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Als u de waarde wilt wijzigen, selecteert u het **bewerkings** pictogram.

### <a name="memstore-size"></a>Grootte van geheugen opslag

Alle bewerkingen worden opgeslagen in de geheugen buffer, een *geheugen opslag* genoemd. Deze buffer verhoogt de totale hoeveelheid gegevens die in één bewerking naar de schijf kan worden geschreven. Het versnelt ook de toegang tot de recente bewerkingen. De grootte van de geheugen opslag wordt gedefinieerd door de volgende twee para meters:

* `hbase.regionserver.global.memstore.UpperLimit`: Hiermee definieert u het maximale percentage van de regio server die door geheugen opslag gecombineerd kunnen worden gebruikt.

* `hbase.regionserver.global.memstore.LowerLimit`: Definieert het minimale percentage van de regio server die door geheugen opslag gecombineerd kan worden gebruikt.

Als u wilt optimaliseren voor wille keurige Lees bewerkingen, kunt u de geheugen opslag boven-en ondergrenzen verlagen.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Aantal opgehaalde rijen bij scannen vanaf schijf

De `hbase.client.scanner.caching` instelling bepaalt het aantal rijen dat van de schijf wordt gelezen wanneer de `next` methode wordt aangeroepen op een scanner.  De standaardwaarde is 100. Hoe hoger het aantal, hoe minder de externe aanroepen van de client naar de regio server, wat resulteert in snellere scans. Met deze instelling wordt echter ook de geheugen belasting van de client verg root.

![Apache HBase aantal rijen opgehaald](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Stel de waarde zo in dat de tijd tussen het aanroepen van de volgende methode op een scanner groter is dan de time-out van de scanner. De duur van de time-out voor de scanner wordt gedefinieerd door de `hbase.regionserver.lease.period` eigenschap.

## <a name="optimize-write-heavy-workloads"></a>Schrijf zware workloads optimaliseren

De volgende configuraties zijn belang rijk voor het verbeteren van de prestaties van write-zware workloads.

### <a name="maximum-region-file-size"></a>Maximale bestands grootte van regio

HBase slaat gegevens op in een interne bestands indeling met de naam *HFile*. De eigenschap `hbase.hregion.max.filesize` definieert de grootte van één HFile voor een regio.  Een regio wordt in twee regio's gesplitst als de som van alle HFiles in een regio groter is dan deze instelling.

![Apache HBase HRegion Max bestands grootte](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Hoe groter de grootte van het regio bestand, hoe kleiner het aantal splitsingen is. U kunt de bestands grootte verg Roten om een waarde te bepalen die resulteert in de maximale schrijf prestaties.

### <a name="avoid-update-blocking"></a>Voor komen dat updates worden geblokkeerd

* De eigenschap `hbase.hregion.memstore.flush.size` definieert de grootte waarmee geheugen opslag wordt leeg gemaakt op de schijf. De standaard grootte is 128 MB.

* De HBase regio Block multiplier wordt gedefinieerd door `hbase.hregion.memstore.block.multiplier` . De standaardwaarde is 4. Het Maxi maal toegestane aantal is 8.

* HBase blokkeert updates als de geheugen opslag ( `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` ) bytes is.

    Met de standaard waarden voor flush grootte en blok-multiplier worden updates geblokkeerd wanneer de geheugen opslag 128 * 4 = 512 MB groot is. Als u het aantal blokkerende updates wilt verminderen, verhoogt u de waarde van `hbase.hregion.memstore.block.multiplier` .

![Apache HBase regio Block multiplier](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Grootte van geheugen opslag definiëren

De grootte van de geheugen opslag wordt gedefinieerd door de `hbase.regionserver.global.memstore.upperLimit` `hbase.regionserver.global.memstore.lowerLimit` para meters en. Als u deze waarden instelt op elkaar, vermindert het aantal pauzes tijdens het schrijven (dit leidt ook tot meer frequente leegmaak acties) en resulteert dit in betere schrijf prestaties.

## <a name="set-memstore-local-allocation-buffer"></a>Lokale toewijzings buffer voor geheugen opslag instellen

Het gebruik van de lokale toewijzings buffer voor geheugen opslag wordt bepaald door de eigenschap `hbase.hregion.memstore.mslab.enabled` . Als deze instelling is ingeschakeld (true), wordt heap-fragmentatie voor komen tijdens een zware schrijf bewerking. De standaardwaarde is waar.

![hbase. hregion. geheugen opslag. mslab. enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Clusters optimaliseren](./hdinsight-changing-configs-via-ambari.md)
* [Apache Hive optimaliseren](./optimize-hive-ambari.md)
* [Apache Pig optimaliseren](./optimize-pig-ambari.md)
