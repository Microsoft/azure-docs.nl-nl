---
title: Resources voor Apache Spark cluster beheren in azure HDInsight
description: Meer informatie over het beheren van resources voor Spark-clusters in azure HDInsight voor betere prestaties.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: ff7cfe8ad09201df20db89e14f8c175e678e5107
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929797"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Resources voor Apache Spark cluster beheren in azure HDInsight

Meer informatie over toegang tot de interfaces zoals [Apache Ambari](https://ambari.apache.org/) ui, [Apache Hadoop garen](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) gebruikers interface, en de [Spark-geschiedenis server](./apache-azure-spark-history-server.md) die aan uw [Apache Spark](https://spark.apache.org/) cluster is gekoppeld en het afstemmen van de cluster configuratie voor optimale prestaties.

## <a name="open-the-spark-history-server"></a>De Spark-geschiedenis server openen

Spark-geschiedenis server is de Web-UI voor voltooide en actieve Spark-toepassingen. Het is een uitbrei ding van de Web-UI van Spark. Zie voor volledige informatie [Spark geschiedenis server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>De garen-gebruikers interface openen

U kunt de garen-interface gebruiken om toepassingen te bewaken die momenteel worden uitgevoerd op het Spark-cluster.

1. Open in het [Azure Portal](https://portal.azure.com/)het Spark-cluster. Zie voor meer informatie het [overzicht en weer geven van clusters](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Selecteer in **cluster dashboards** de optie **garens**. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties in voor het Spark-cluster.

    ![Gebruikers interface van garen starten](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > U kunt ook de gebruikers interface van garen starten vanuit de Ambari-gebruikers interface. Ga in de Ambari-gebruikers interface **naar de**  >  **snelle koppelingen** van een  >  **actieve**  >  **Resource Manager-gebruikers interface**.

## <a name="optimize-clusters-for-spark-applications"></a>Clusters optimaliseren voor Spark-toepassingen

De drie belang rijke para meters die kunnen worden gebruikt voor Spark-configuratie, zijn afhankelijk van de toepassings vereisten `spark.executor.instances` , `spark.executor.cores` en `spark.executor.memory` . Een uitvoerder is een proces dat wordt gestart voor een Spark-toepassing. Het wordt uitgevoerd op het worker-knoop punt en is verantwoordelijk voor het uitvoeren van de taken voor de toepassing. Het standaard aantal uitvoerende agents en de uitvoerings grootte voor elk cluster wordt berekend op basis van het aantal worker-knoop punten en de grootte van het worker-knoop punt. Deze informatie wordt opgeslagen in `spark-defaults.conf` op de hoofd knooppunten van het cluster.

De drie configuratie parameters kunnen worden geconfigureerd op cluster niveau (voor alle toepassingen die in het cluster worden uitgevoerd) of kunnen ook voor elke afzonderlijke toepassing worden opgegeven.

### <a name="change-the-parameters-using-ambari-ui"></a>De para meters wijzigen met behulp van Ambari-gebruikers interface

1. Ga in de Ambari-gebruikers interface naar **Spark2**  >  **configs**  >  **aangepaste Spark2-defaults**.

    ![Para meters instellen met Ambari Custom](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Para meters instellen met Ambari Custom")

1. De standaard waarden zijn handig om vier Spark-toepassingen gelijktijdig op het cluster uit te voeren. U kunt deze waarden wijzigen in de gebruikers interface, zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Para meters instellen met behulp van Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Para meters instellen met behulp van Ambari")

1. Selecteer **Opslaan** om de configuratie wijzigingen op te slaan. Boven aan de pagina wordt u gevraagd om alle betrokken services opnieuw op te starten. Selecteer **opnieuw opstarten**.

    ![Services opnieuw starten](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>De para meters wijzigen voor een toepassing die wordt uitgevoerd in Jupyter Notebook

Voor toepassingen die worden uitgevoerd in de Jupyter Notebook, kunt u het `%%configure` Magic gebruiken om de configuratie wijzigingen door te voeren. In het ideale geval moet u dergelijke wijzigingen aan het begin van de toepassing aanbrengen voordat u uw eerste code-cel uitvoert. Dit zorgt ervoor dat de configuratie wordt toegepast op de livy-sessie wanneer deze wordt gemaakt. Als u de configuratie in een later stadium in de toepassing wilt wijzigen, moet u de `-f` para meter gebruiken. De voortgang van de toepassing gaat echter verloren.

Het volgende code fragment laat zien hoe u de configuratie wijzigt voor een toepassing die wordt uitgevoerd in Jupyter.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Configuratie parameters moeten worden door gegeven als een JSON-teken reeks en moeten zich op de volgende regel na de Magic bevallen, zoals wordt weer gegeven in de voorbeeld kolom.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>De para meters voor een toepassing die is verzonden met Spark verzenden wijzigen

De volgende opdracht is een voor beeld van het wijzigen van de configuratie parameters voor een batch toepassing die wordt verzonden met `spark-submit` .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>De para meters wijzigen voor een toepassing die via krul wordt verzonden

De volgende opdracht is een voor beeld van het wijzigen van de configuratie parameters voor een batch toepassing die via krul wordt verzonden.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Kopieer het JAR-bestand naar uw cluster-opslag account. Kopieer het JAR-bestand niet rechtstreeks naar het hoofd knooppunt.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Deze para meters wijzigen op een Spark Thrift-server

Spark Thrift server biedt JDBC/ODBC-toegang tot een Spark-cluster en wordt gebruikt voor het uitvoeren van Spark SQL-query's. Hulpprogram ma's als Power BI, tableau, enzovoort, gebruiken ODBC-protocol om te communiceren met Spark Thrift server om Spark SQL-query's uit te voeren als een Spark-toepassing. Wanneer een Spark-cluster wordt gemaakt, worden twee exemplaren van de Spark Thrift-server gestart, één op elk hoofd knooppunt. Elke Spark Thrift-server is zichtbaar als een Spark-toepassing in de garen-gebruikers interface.

Spark Thrift server maakt gebruik van de toewijzing van de dynamische uitvoerder van Spark en daarom `spark.executor.instances` niet wordt gebruikt. In plaats daarvan gebruikt Spark Thrift `spark.dynamicAllocation.maxExecutors` server `spark.dynamicAllocation.minExecutors` om het aantal uitvoeringen op te geven. De configuratie parameters `spark.executor.cores` en `spark.executor.memory` worden gebruikt om de grootte van de uitvoerder te wijzigen. U kunt deze para meters wijzigen, zoals wordt weer gegeven in de volgende stappen:

* Vouw de categorie **Geavanceerd spark2-Thrift-sparkconf** uit om de para meters bij te werken `spark.dynamicAllocation.maxExecutors` en `spark.dynamicAllocation.minExecutors` .

    ![Spark Thrift-server configureren](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Spark Thrift-server configureren")

* Vouw de categorie **Custom spark2-Thrift-sparkconf** uit om de para meters bij te werken `spark.executor.cores` en `spark.executor.memory` .

    ![Spark Thrift-server parameter configureren](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Spark Thrift-server parameter configureren")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Het stuur programma-geheugen van de Spark Thrift-server wijzigen

Het geheugen van Spark Thrift-Server Stuur Programma's is ingesteld op 25% van de RAM-grootte van het hoofd knooppunt, op voor waarde dat de totale RAM-grootte van het hoofd knooppunt groter is dan 14 GB. U kunt de Ambari-gebruikers interface gebruiken om de geheugen configuratie van het stuur programma te wijzigen, zoals wordt weer gegeven in de volgende scherm afbeelding:

Ga in de Ambari-gebruikers interface naar **Spark2**  >  **configs**  >  **Advanced Spark2-env**. Geef vervolgens de waarde voor **spark_thrift_cmd_opts** op.

## <a name="reclaim-spark-cluster-resources"></a>Spark-cluster resources vrijmaken

Vanwege de dynamische toewijzing van Spark zijn de resources die worden gebruikt door de Thrift-server, de bronnen voor de twee toepassings Masters. Als u deze resources opnieuw wilt claimen, moet u de Thrift Server-services die worden uitgevoerd op het cluster stoppen.

1. Selecteer **Spark2** in het linkerdeel venster van de Ambari-gebruikers interface.

2. Selecteer op de volgende pagina **Spark2 Thrift-servers**.

    ![Thrift server1 opnieuw starten](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Thrift server1 opnieuw starten")

3. Als het goed is, ziet u de twee hoofd knooppunten waarop de Spark2 Thrift-server wordt uitgevoerd. Selecteer een van de hoofd knooppunten.

    ![Thrift server2 opnieuw starten](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Thrift server2 opnieuw starten")

4. Op de volgende pagina wordt een lijst weer gegeven met alle services die op die hoofd knooppunt worden uitgevoerd. Selecteer in de lijst de vervolg keuze knop naast Spark2 Thrift-server en selecteer vervolgens **stoppen**.

    ![Thrift Server3 opnieuw starten](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Thrift Server3 opnieuw starten")
5. Herhaal deze stappen ook voor de andere hoofd knooppunt.

## <a name="restart-the-jupyter-service"></a>De Jupyter-service opnieuw starten

Start de Ambari-webgebruikersinterface zoals wordt weer gegeven in het begin van het artikel. Selecteer **Jupyter** in het navigatie deel venster links, selecteer **service acties** en selecteer vervolgens **opnieuw starten**. Hiermee start u de Jupyter-service op alle hoofd knooppunten.

![Jupyter opnieuw starten](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Jupyter opnieuw starten")

## <a name="monitor-resources"></a>Resources controleren

Start de gebruikers interface van het garen zoals in het begin van het artikel wordt weer gegeven. Controleer in de tabel cluster metrische gegevens boven op het scherm de waarden voor het **gebruikte geheugen** en het **totale geheugen** in kolommen. Als de twee waarden sluiten, zijn er mogelijk onvoldoende bronnen om de volgende toepassing te starten. Hetzelfde geldt voor de kolommen **VCores used** en **VCores totaal** . Als er in de hoofd weergave een toepassing in de **geaccepteerde** status bevindt en niet wordt overgeschakeld naar een **actieve** of **mislukte** status, kan dit ook duiden op een indicatie dat er niet voldoende bronnen worden opgehaald om te starten.

![Resource limiet](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Resource limiet")

## <a name="kill-running-applications"></a>Actieve toepassingen beëindigen

1. Selecteer in de gebruikers interface van garen vanuit het linkerdeel venster **uitvoeren**. Bepaal in de lijst met actieve toepassingen welke toepassing moet worden gedood en selecteer de **id**.

    ![App1 afsluiten](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "App1 afsluiten")

2. Selecteer **Kill-toepassing** in de rechter bovenhoek en selecteer vervolgens **OK**.

    ![App2 afsluiten](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "App2 afsluiten")

## <a name="see-also"></a>Zie ook

* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Voor gegevens analisten

* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetriegegevens voor gegevens analyse met behulp van Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Voor Apache Spark-ontwikkel aars

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter Notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
