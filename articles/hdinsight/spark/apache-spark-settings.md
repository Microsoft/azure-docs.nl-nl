---
title: Spark-instellingen configureren-Azure HDInsight
description: Apache Spark-instellingen voor een Azure HDInsight-cluster weer geven en configureren
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: c2e2daf6d8c9afa6ffef03b088ec9a7dc144cf47
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504930"
---
# <a name="configure-apache-spark-settings"></a>Apache Spark-instellingen configureren

An HDInsight Spark-cluster bevat een installatie van de Apache Spark-bibliotheek.  Elk HDInsight-cluster bevat standaard configuratie parameters voor alle geïnstalleerde services, waaronder Spark.  Een belang rijk aspect van het beheren van een HDInsight-Apache Hadoop cluster is bewakings werk belasting, met inbegrip van Spark-taken. Als u Spark-taken best wilt uitvoeren, moet u rekening houden met de fysieke cluster configuratie bij het bepalen van de logische configuratie van het cluster.

Het standaard HDInsight-Apache Spark cluster bevat de volgende knoop punten: drie Apache ZooKeeper knoop punten, twee hoofd knooppunten en een of meer worker-knoop punten:

![Spark HDInsight-architectuur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Het aantal Vm's en VM-grootten voor de knoop punten in uw HDInsight-cluster kunnen invloed hebben op uw Spark-configuratie. Niet-standaard-HDInsight-configuratie waarden vereisen vaak niet-standaard Spark-configuratie waarden. Wanneer u een HDInsight Spark-cluster maakt, worden de voorgestelde VM-grootten voor elk van de onderdelen weer gegeven. Momenteel zijn de door het [geheugen geoptimaliseerde Linux VM-grootten](../../virtual-machines/sizes-memory.md) voor Azure D12 v2 of hoger.

## <a name="apache-spark-versions"></a>Apache Spark versies

Gebruik de aanbevolen Spark-versie voor uw cluster.  De HDInsight-service bevat verschillende versies van zowel Spark als HDInsight.  Elke versie van Spark bevat een set standaard cluster instellingen.  

Wanneer u een nieuw cluster maakt, zijn er meerdere Spark-versies waaruit u kunt kiezen. Voor een overzicht van de volledige lijst,  [HDInsight-onderdelen en versies](../hdinsight-component-versioning.md).

> [!NOTE]  
> De standaard versie van Apache Spark in de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een versie afhankelijkheid hebt, raadt micro soft u aan die bepaalde versie op te geven wanneer u clusters maakt met behulp van .NET SDK, Azure PowerShell en klassieke CLI van Azure.

Apache Spark heeft drie systeem configuratie locaties:

* Spark-eigenschappen bepalen de meeste toepassings parameters en kunnen worden ingesteld met behulp van een `SparkConf` -object of via Java-systeem eigenschappen.
* Omgevings variabelen kunnen worden gebruikt voor het instellen van instellingen per computer, zoals het IP-adres, door het `conf/spark-env.sh` script op elk knoop punt.
* Logboek registratie kan worden geconfigureerd via `log4j.properties` .

Wanneer u een bepaalde versie van Spark selecteert, bevat uw cluster de standaard configuratie-instellingen.  U kunt de standaard Spark-configuratie waarden wijzigen met behulp van een aangepast Spark-configuratie bestand.  Hieronder kunt u een voorbeeld bekijken.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

In het bovenstaande voor beeld wordt een aantal standaard waarden overschreven voor vijf Spark-configuratie parameters.  Deze waarden zijn de compressie-codec, Apache Hadoop MapReduce voor minimale grootte en Parquet-blok grootte. Ook de standaard waarden voor de SQL-partitie voor spaarzaam en het openen van bestands grootten.  Deze configuratie wijzigingen worden gekozen, omdat de bijbehorende gegevens en taken (in dit voor beeld genoom gegevens) bepaalde kenmerken hebben. Deze kenmerken kunnen beter gebruikmaken van deze aangepaste configuratie-instellingen.

---

## <a name="view-cluster-configuration-settings"></a>Cluster configuratie-instellingen weer geven

Controleer de huidige configuratie-instellingen voor het HDInsight-cluster voordat u prestatie optimalisatie op het cluster uitgevoerd. Start het HDInsight-dash board vanuit de Azure Portal door te klikken op de **Dashboard** koppeling in het deel venster Spark-cluster. Meld u aan met de gebruikers naam en het wacht woord van de Cluster beheerder.

De Apache Ambari-webgebruikersinterface wordt weer gegeven, met een dash board met belang rijke gegevens over het resource gebruik van het cluster.  Het Ambari-dash board toont u de Apache Spark configuratie en andere geïnstalleerde services. Het dash board bevat een tabblad van de **configuratie geschiedenis** , waar u informatie bekijkt voor geïnstalleerde services, waaronder Spark.

Als u de configuratie waarden voor Apache Spark wilt bekijken, selecteert u **configuratie geschiedenis** en selecteert u **Spark2**.  Selecteer het tabblad **configuraties** en selecteer vervolgens de `Spark` koppeling (of `Spark2` , afhankelijk van uw versie) in de lijst met Services.  U ziet een lijst met configuratie waarden voor het cluster:

![Spark-configuraties](./media/apache-spark-settings/spark-configurations.png)

Als u afzonderlijke Spark-configuratie waarden wilt bekijken en wijzigen, selecteert u een koppeling met ' Spark ' in de titel.  Configuraties voor Spark bevatten zowel aangepaste als geavanceerde configuratie waarden in deze categorieën:

* Aangepaste Spark2: standaard waarden
* Aangepaste Spark2-metrische gegevens-eigenschappen
* Geavanceerde Spark2-standaard instellingen
* Geavanceerde Spark2-env
* Geavanceerde spark2-Hive-site-opheffen

Als u een niet-standaard set configuratie waarden maakt, is de update geschiedenis zichtbaar.  Deze configuratie geschiedenis kan handig zijn om te zien welke niet-standaard configuratie optimale prestaties heeft.

> [!NOTE]  
> Als u de algemene configuratie-instellingen van het Spark-cluster wilt zien, maar niet wijzigen, selecteert u het tabblad **omgeving** op de interface op het hoogste niveau in de **gebruikers interface van Spark-taken** .

## <a name="configuring-spark-executors"></a>Spark-uitvoerendeers configureren

Het volgende diagram toont de belangrijkste Spark-objecten: het stuur programma en de bijbehorende Spark-context, en de Cluster beheer-en de *n* worker-knoop punten.  Elk werk knooppunt bevat een uitvoerder, een cache en *n* taak exemplaren.

![Cluster objecten](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark-taken gebruiken werk resources, met name geheugen, zodat het gebruikelijk is om Spark-configuratie waarden aan te passen voor uitvoerder van worker-knoop punten.

Drie belang rijke para meters die vaak worden aangepast aan het afstemmen van Spark-configuraties om de toepassings vereisten te verbeteren, zijn, `spark.executor.instances` `spark.executor.cores` en `spark.executor.memory` . Een uitvoerder is een proces dat wordt gestart voor een Spark-toepassing. Een uitvoerder wordt uitgevoerd op het worker-knoop punt en is verantwoordelijk voor de taken voor de toepassing. Het aantal worker-knoop punten en de grootte van het worker-knoop punt bepaalt het aantal uitvoerende schijven en de uitvoerings grootte. Deze waarden worden opgeslagen in `spark-defaults.conf` op de hoofd knooppunten van het cluster.  U kunt deze waarden bewerken in een cluster dat wordt uitgevoerd door **aangepaste Spark-standaard waarden** te selecteren in de Ambari-webgebruikersinterface.  Nadat u wijzigingen hebt aangebracht, wordt u door de gebruikers interface gevraagd om alle betrokken services opnieuw op te **starten** .

> [!NOTE]  
> Deze drie configuratie parameters kunnen worden geconfigureerd op cluster niveau (voor alle toepassingen die in het cluster worden uitgevoerd) en ook zijn opgegeven voor elke afzonderlijke toepassing.

Een andere bron van informatie over resources die worden gebruikt door Spark-uitvoerende modules is de gebruikers interface van Spark.  In de gebruikers interface geeft de  **uitvoerende** module samenvattings-en detail weergaven weer van de configuratie en verbruikte resources.  Bepaal of u de waarden van de runtime voor het hele cluster of een bepaalde set taak uitvoeringen wilt wijzigen.

![Spark-Uitvoerendeers](./media/apache-spark-settings/apache-spark-executors.png)

U kunt ook de Ambari-REST API gebruiken om de configuratie-instellingen voor HDInsight-en Spark-clusters programmatisch te controleren.  Meer informatie vindt u op de [AMBARI API-referentie van Apache op github](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Afhankelijk van de Spark-workload kunt u bepalen dat een Spark-configuratie die niet-standaard is, meer geoptimaliseerde uitvoeringen van Spark-taken biedt.  Voer Bench Mark-tests uit met voorbeeld werkbelastingen om niet-standaard cluster configuraties te valideren.  Suggesties voor algemene parameters die u kunt aanpassen:

|Parameter |Beschrijving|
|---|---|
|--num-uitvoerende modules|Hiermee stelt u het aantal uitvoerders in.|
|--uitvoerder-kernen|Hiermee stelt u het aantal kernen in voor elke uitvoerder. We raden u aan om middelste uitvoerders uit te voeren, omdat andere processen een deel van het beschik bare geheugen verbruiken.|
|--uitvoerbaar geheugen|Hiermee bepaalt u de geheugen grootte (Heap-grootte) van elke uitvoerder op Apache Hadoop GARENs. u moet een geheugen voor de overhead van de uitvoering van de opslag ruimte laten.|

Hier volgt een voor beeld van twee worker-knoop punten met verschillende configuratie waarden:

![Configuraties met twee knoop punten](./media/apache-spark-settings/executor-configuration.png)

In de volgende lijst ziet u de para meters voor Key Spark-bewerkings geheugen.

|Parameter |Beschrijving|
|---|---|
|spark.executor. Memory|Hiermee definieert u de totale hoeveelheid geheugen die beschikbaar is voor een uitvoerder.|
|Spark. storage. memoryFraction|(standaard ~ 60%) Hiermee definieert u de hoeveelheid geheugen die beschikbaar is voor het opslaan van persistente Rdd's.|
|Spark. wille keurige. memoryFraction|(standaard ~ 20%) Hiermee definieert u de hoeveelheid geheugen die is gereserveerd voor wille keurige volg orde.|
|Spark. storage. unrollFraction en Spark. storage. safetyFraction|(totaal ongeveer 30% van het totale geheugen): deze waarden worden intern gebruikt door Spark en mogen niet worden gewijzigd.|

GAREN beheert de maximale hoeveelheid geheugen die wordt gebruikt door de containers op elk Spark-knoop punt. In het volgende diagram ziet u de relaties per knoop punt tussen garen configuratie objecten en Spark-objecten.

![Geheugenbeheer met YARN Spark](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Para meters wijzigen voor een toepassing die wordt uitgevoerd in Jupyter Notebook

Spark-clusters in HDInsight bevatten standaard een aantal onderdelen. Elk van deze onderdelen bevat standaard configuratie waarden, die als nodig kunnen worden overschreven.

|Onderdeel |Beschrijving|
|---|---|
|Spark-kern|Spark-kern, Spark SQL, Spark streaming Api's, Graphx en Apache Spark MLlib.|
|Anaconda|Een python-pakket beheer.|
|Apache Livy|Het Apache Spark REST API, dat wordt gebruikt om externe taken te verzenden naar een HDInsight Spark-cluster.|
|Jupyter-en Apache Zeppelin-notebooks|Interactieve gebruikers interface voor interactie met uw Spark-cluster.|
|ODBC-stuurprogramma|Verbindt Spark-clusters in HDInsight-naar-business intelligence-hulpprogram ma's (BI), zoals micro soft Power BI en tableau.|

Voor toepassingen die in de Jupyter Notebook worden uitgevoerd, gebruikt u de `%%configure` opdracht om configuratie wijzigingen in het notitie blok zelf aan te brengen. Deze configuratie wijzigingen worden toegepast op de Spark-taken die worden uitgevoerd vanuit uw notebook-exemplaar. Breng deze wijzigingen aan het begin van de toepassing aan voordat u de eerste code-cel uitvoert. De gewijzigde configuratie wordt toegepast op de livy-sessie wanneer deze wordt gemaakt.

> [!NOTE]  
> Als u de configuratie in een later stadium in de toepassing wilt wijzigen, gebruikt u de `-f` para meter (Force). De voortgang van de toepassing gaat echter verloren.

De volgende code laat zien hoe u de configuratie wijzigt voor een toepassing die wordt uitgevoerd in een Jupyter Notebook.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusie

De kern configuratie-instellingen controleren om ervoor te zorgen dat uw Spark-taken op een voorspel bare en krachtige manier worden uitgevoerd. Deze instellingen helpen bij het bepalen van de aanbevolen Spark-cluster configuratie voor uw specifieke workloads.  U moet ook de uitvoering van langlopende en, of resource-Consumer Spark-taak uitvoeringen bewaken.  De meest voorkomende uitdagingen voor het geheugen van een onjuiste configuratie, zoals een onjuist formaat van de uitvoeringen. Ook langlopende bewerkingen en taken, die resulteren in Cartesische bewerkingen.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop onderdelen en versies die beschikbaar zijn met HDInsight?](../hdinsight-component-versioning.md)
* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [Apache Spark configuratie](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark op Apache Hadoop GARENs uitvoeren](https://spark.apache.org/docs/latest/running-on-yarn.html)
