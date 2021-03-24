---
title: Apache Pig optimaliseren met Apache Ambari in azure HDInsight
description: Gebruik de Apache Ambari-webgebruikersinterface om Apache varken te configureren en te optimaliseren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 43159360342063af1682a984db037ae286721411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871160"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Apache Pig optimaliseren met Apache Ambari in azure HDInsight

Apache Ambari is een webinterface voor het beheren en controleren van HDInsight-clusters. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)voor een inleiding tot de Ambari-webgebruikersinterface.

Apache varken-eigenschappen kunnen worden gewijzigd in de Ambari-webgebruikersinterface om Pig-query's af te stemmen. Als u de Pig-eigenschappen van Ambari wijzigt, worden de Pig-eigenschappen in het bestand direct gewijzigd `/etc/pig/2.4.2.0-258.0/pig.properties` .

1. Als u de eigenschappen van varken wilt wijzigen, gaat u naar het tabblad Pig- **configuratie** en vouwt u het deel venster **Geavanceerde Pig-eigenschappen** uit.

1. Zoek, verwijder de opmerking en wijzig de waarde van de eigenschap die u wilt wijzigen.

1. Selecteer **Opslaan** in de rechter bovenhoek van het venster om de nieuwe waarde op te slaan. Voor sommige eigenschappen is het opnieuw opstarten van de service vereist.

    :::image type="content" source="./media/optimize-pig-ambari/advanced-pig-properties.png" alt-text="Geavanceerde eigenschappen van Apache-Pig" border="true":::

> [!NOTE]  
> Alle instellingen op sessie niveau overschrijven eigenschaps waarden in het `pig.properties` bestand.

## <a name="tune-execution-engine"></a>Uitvoerings engine afstemmen

Er zijn twee uitvoerings engines beschikbaar voor het uitvoeren van Pig-scripts: MapReduce en TEZ. TEZ is een geoptimaliseerde engine en is veel sneller dan MapReduce.

1. Als u de uitvoerings Engine wilt wijzigen, gaat u naar het deel venster **Geavanceerde Pig-eigenschappen** en gaat u naar de eigenschap `exectype` .

1. De standaard waarde is **MapReduce**. Wijzig deze in **TEZ**.

## <a name="enable-local-mode"></a>Lokale modus inschakelen

Net als bij Hive wordt de lokale modus gebruikt om taken te versnellen met relatief kleinere hoeveel heden gegevens.

1. Als u de lokale modus wilt inschakelen, stelt `pig.auto.local.enabled` u in op **waar**. De standaardwaarde is false.

1. Taken met een grootte van invoer gegevens die kleiner zijn dan de waarde van de `pig.auto.local.input.maxbytes` eigenschap, worden beschouwd als kleine taken. De standaard waarde is 1 GB.

## <a name="copy-user-jar-cache"></a>Gebruiker jar-cache kopiëren

Varken kopieert de JAR-bestanden die zijn vereist door Udf's naar een gedistribueerde cache om ze beschikbaar te maken voor taak knooppunten. Deze potten veranderen niet regel matig. Als deze optie is ingeschakeld, `pig.user.cache.enabled` kunnen potten in een cache worden geplaatst om ze opnieuw te gebruiken voor taken die door dezelfde gebruiker worden uitgevoerd. Deze instelling resulteert in een kleine toename van de taak prestaties.

1. Stel in op waar om in te scha kelen `pig.user.cache.enabled` . De standaardwaarde is false.

1. Als u het basispad van de in de cache geplaatste potten wilt instellen, stelt `pig.user.cache.location` u het pad naar het basispad in. De standaardwaarde is `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Prestaties optimaliseren met geheugen instellingen

De volgende geheugen instellingen kunnen helpen de prestaties van het Pig-script te optimaliseren.

* `pig.cachedbag.memusage`: De hoeveelheid geheugen die aan een Bag is toegewezen. Een Bag is verzameling Tuples. Een tuple is een geordende set velden en een veld is een stukje gegevens. Als de gegevens in een Bag zich buiten het opgegeven geheugen bevinden, wordt deze overgelopen naar de schijf. De standaard waarde is 0,2. Dit is 20 procent van het beschik bare geheugen. Dit geheugen wordt gedeeld in alle strafpot van een toepassing.

* `pig.spill.size.threshold`: Zakken die groter zijn dan deze drempel waarde voor de grootte van overloop (in bytes) worden overgelopen naar de schijf. De standaard waarde is 5 MB.

## <a name="compress-temporary-files"></a>Tijdelijke bestanden comprimeren

Pig genereert tijdelijke bestanden tijdens het uitvoeren van taken. Het comprimeren van de tijdelijke bestanden resulteert in een prestatie verhoging bij het lezen of schrijven van bestanden naar schijf. De volgende instellingen kunnen worden gebruikt voor het comprimeren van tijdelijke bestanden.

* `pig.tmpfilecompression`: Als deze optie is ingesteld op True, wordt tijdelijke bestands compressie ingeschakeld. De standaardwaarde is false.

* `pig.tmpfilecompression.codec`: De compressie-codec die moet worden gebruikt voor het comprimeren van de tijdelijke bestanden. De aanbevolen compressie-codecs zijn LZO en Snappy voor een lager CPU-gebruik.

## <a name="enable-split-combining"></a>Splitsen combi neren inschakelen

Wanneer deze functie is ingeschakeld, worden kleine bestanden gecombineerd voor minder toewijzings taken. Deze instelling verbetert de efficiëntie van taken met veel kleine bestanden. Stel in op waar om in te scha kelen `pig.noSplitCombination` . De standaardwaarde is false.

## <a name="tune-mappers"></a>Toewijzings toewijzingen afstemmen

Het aantal mappers wordt bepaald door het wijzigen van de eigenschap `pig.maxCombinedSplitSize` . Met deze eigenschap geeft u de grootte van de gegevens die moeten worden verwerkt door één toewijzings taak. De standaard waarde is de standaard blok grootte van het bestands systeem. Het verhogen van deze waarde resulteert in een lager aantal toewijzings taken.

## <a name="tune-reducers"></a>Verkleiners afstemmen

Het aantal verminderers wordt berekend op basis van de para meter `pig.exec.reducers.bytes.per.reducer` . De para meter bepaalt het aantal bytes dat per vermindering is verwerkt, standaard 1 GB. Als u het maximum aantal verminderers wilt beperken, stelt u de `pig.exec.reducers.max` eigenschap standaard in op 999.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Clusters optimaliseren](./hdinsight-changing-configs-via-ambari.md)
* [Apache HBase optimaliseren](./optimize-hbase-ambari.md)
* [Apache Hive optimaliseren](./optimize-hive-ambari.md)
