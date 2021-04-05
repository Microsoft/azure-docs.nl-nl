---
title: Problemen met Apache Spark cluster in azure HDInsight oplossen
description: Meer informatie over problemen met Apache Spark clusters in azure HDInsight en hoe u deze omzeilt.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: c92b55d3ac7f4476b7b74d25b40150a74c6ea1cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930157"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Bekende problemen met Apache Spark cluster op HDInsight

In dit document worden alle bekende problemen voor de open bare preview-versie van HDInsight Spark bijgehouden.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache livy lekt interactieve sessie
Wanneer [Apache livy](https://livy.incubator.apache.org/) opnieuw wordt opgestart (van [Apache Ambari](https://ambari.apache.org/) of vanwege het opnieuw opstarten van de virtuele machine van hoofd knooppunt 0) met een interactieve sessie nog steeds actief is, wordt een interactieve taak sessie gelekt. Als gevolg hiervan kunnen nieuwe taken vastzitten in de geaccepteerde status.

**Risico beperking**

Gebruik de volgende procedure om het probleem te omzeilen:

1. SSH in hoofd knooppunt. Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Voer de volgende opdracht uit om de toepassings-Id's te vinden van de interactieve taken die zijn gestart via livy.

   ```bash
   yarn application –list
   ```

    De standaard taak namen worden livy als de taken zijn gestart met een interactieve livy-sessie waarvoor geen expliciete namen zijn opgegeven. Voor de livy-sessie die is gestart door [Jupyter notebook](https://jupyter.org/), begint de taak naam met `remotesparkmagics_*` .

3. Voer de volgende opdracht uit om deze taken af te breken.

   ```bash
   yarn application –kill <Application ID>
   ```

Nieuwe taken worden uitgevoerd.

## <a name="spark-history-server-not-started"></a>De Spark-geschiedenis server is niet gestart
De Spark-geschiedenis server wordt niet automatisch gestart nadat een cluster is gemaakt.  

**Risico beperking**

Start de geschiedenis server hand matig vanuit Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Machtigings probleem in de map Spark-logboek
hdiuser haalt de volgende fout op bij het verzenden van een taak met behulp van Spark-Submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

En er is geen stuur programma-logboek geschreven.

**Risico beperking**

1. Voeg hdiuser toe aan de Hadoop-groep.
2. Geef 777-machtigingen op/var/log/Spark nadat het cluster is gemaakt.
3. Update de locatie van de Spark-logboeken met behulp van Ambari als map met 777-machtigingen.  
4. Voer Spark-Submit uit als sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix connector wordt niet ondersteund

HDInsight Spark-clusters bieden geen ondersteuning voor de Spark-Phoenix-connector.

**Risico beperking**

U moet in plaats daarvan de Spark-HBase-connector gebruiken. Zie [Spark-HBase-connector gebruiken](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/)voor instructies.

## <a name="issues-related-to-jupyter-notebooks"></a>Problemen met betrekking tot Jupyter-notebooks

Hieronder vindt u enkele bekende problemen met betrekking tot Jupyter-notebooks.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notitie blokken met niet-ASCII-tekens in bestands namen

Gebruik geen niet-ASCII-tekens in Jupyter Notebook bestands namen. Als u probeert een bestand te uploaden via de Jupyter-gebruikers interface, die een niet-ASCII-bestands naam heeft, mislukt dit zonder fout bericht. Met Jupyter kunt u het bestand niet uploaden, maar wordt er geen zicht bare fout gegenereerd.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fout tijdens het laden van notitie blokken met een grotere grootte

Mogelijk wordt er een fout bericht weer geven **`Error loading notebook`** Wanneer u notitie blokken laadt die groter zijn.  

**Risico beperking**

Als deze fout wordt weer gegeven, betekent dit niet dat uw gegevens beschadigd of verloren zijn gegaan.  Uw Notebooks bevinden zich nog in `/var/lib/jupyter` de schijf en u kunt deze in het cluster gebruiken om ze te openen. Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

Zodra u met SSH verbinding hebt gemaakt met het cluster, kunt u uw notitie blokken vanuit uw cluster naar uw lokale computer (met SCP of WinSCP) kopiëren als back-up om te voor komen dat belang rijke gegevens in het notitie blok verloren gaan. U kunt vervolgens SSH-tunnels in uw hoofd knooppunt op poort 8001 gebruiken om toegang te krijgen tot Jupyter zonder de gateway te passeren.  Hier kunt u de uitvoer van uw notitie blok wissen en opnieuw opslaan om de grootte van het notitie blok te minimaliseren.

U moet een aantal aanbevolen procedures volgen om te voor komen dat deze fout zich voordoet in de toekomst:

* Het is belang rijk dat u de notitieblok grootte beperkt blijft. Elke uitvoer van uw Spark-taken die wordt teruggestuurd naar Jupyter, wordt persistent gemaakt in het notitie blok.  Het is een best practice met Jupyter in het algemeen om te voor komen dat het wordt uitgevoerd `.collect()` op grote rdd of dataframes; in plaats daarvan kunt u, als u de inhoud van een rdd wilt bekijken, overwegen `.take()` of u de uitvoer wilt verkorten `.sample()` .
* Wanneer u een notitie blok opslaat, wist u ook alle uitvoer cellen om de grootte te verkleinen.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Het eerste keer opstarten van de notebook duurt langer dan verwacht

De eerste code-instructie in Jupyter Notebook met Spark Magic kan meer dan een minuut duren.  

**Korte**

Dit gebeurt omdat wanneer de eerste code cel wordt uitgevoerd. Op de achtergrond wordt de sessie configuratie gestart en worden de context van Spark, SQL en Hive ingesteld. Nadat deze contexten zijn ingesteld, wordt de eerste instructie uitgevoerd. Dit geeft de indruk dat de instructie veel tijd duurde om te volt ooien.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Time-out bij het maken van de sessie Jupyter Notebook

Wanneer het Spark-cluster niet meer bronnen bevat, wordt er een time-out opgetreden tijdens het maken van de sessie door de Spark-en PySpark-kernels in de Jupyter Notebook.

**Oplossingen**

1. Maak enkele resources in uw Spark-cluster beschikbaar door:

   * Stop andere Spark-notebooks door naar het menu sluiten en stoppen te gaan of op Afsluiten te klikken in de notebook Verkenner.
   * Andere Spark-toepassingen van GARENs te stoppen.

2. Start het notitie blok dat u probeerde op te starten. Er moeten voldoende resources beschikbaar zijn om nu een sessie te maken.

## <a name="see-also"></a>Zie ook

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's

* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen

* [De invoegtoepassing HDInsight Tools for IntelliJ IDEA gebruiken om Spark Scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter Notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
