---
title: Toegang tot de toepassings logboeken van Apache Hadoop garen-Azure HDInsight
description: Meer informatie over het openen van toepassings logboeken op basis van een op Linux gebaseerd HDInsight-cluster (Apache Hadoop) met behulp van de opdracht regel en een webbrowser.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 9558e6c5ddd58b1d5fd70da03187caef50d1275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865567"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Toegang tot Apache Hadoop GARENs van toepassings logboeken op HDInsight op basis van Linux

Meer informatie over toegang tot de logboeken voor [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (nog een andere resource-onderhandelings toepassing) op een Apache Hadoop cluster in azure HDInsight.

## <a name="what-is-apache-yarn"></a>Wat is Apache GARENs?

GARENs ondersteunt meerdere programmeer modellen (Apache Hadoop MapReduce zijn) door het ontkoppelen van resource beheer vanuit toepassings planning/-bewaking. GARENs maakt gebruik van een Global *`ResourceManager`* (RM), per worker-knoop punt *NodeManagers* (NMs) en per toepassing *ApplicationMasters* (AMS). De per-toepassing onderhandelt bronnen (CPU, geheugen, schijf, netwerk) voor het uitvoeren van uw toepassing met de RM. De RM werkt met NMs voor het verlenen van deze resources, die als *containers* worden verleend. De AM is verantwoordelijk voor het volgen van de voortgang van de containers die hieraan zijn toegewezen door de RM. Een toepassing kan veel containers vereisen, afhankelijk van de aard van de toepassing.

Elke toepassing kan bestaan uit meerdere *toepassings pogingen*. Als een toepassing mislukt, wordt deze mogelijk opnieuw geprobeerd als een nieuwe poging. Elke poging wordt uitgevoerd in een container. In een zin bevat een container de context voor de basis eenheid van het werk dat door een garen toepassing wordt uitgevoerd. Alle werkzaamheden die in de context van een container worden uitgevoerd, worden uitgevoerd op het knoop punt voor één werk nemer waarop de container is opgegeven. Zie [Hadoop: garens schrijven](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)of [Apache Hadoop garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) voor meer informatie.

Als u uw cluster wilt schalen om een grotere verwerking door voer te ondersteunen, kunt u automatisch [schalen](hdinsight-autoscale-clusters.md) gebruiken of [uw clusters hand matig schalen met een aantal verschillende talen](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Tijdlijn server-garen

De [tijdlijn server van Apache HADOOP garen](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) biedt algemene informatie over voltooide toepassingen

De lijn van een garen tijdlijn server bevat het volgende type gegevens:

* De toepassings-ID, een unieke id van een toepassing
* De gebruiker die de toepassing heeft gestart
* Informatie over pogingen om de toepassing te volt ooien
* De containers die worden gebruikt door een bepaalde toepassings poging

## <a name="yarn-applications-and-logs"></a>Toepassingen en logboeken van garen

Toepassings Logboeken (en de bijbehorende container Logboeken) zijn van cruciaal belang bij het opsporen van fouten in problemen met Hadoop-toepassingen. GARENs bieden een goed kader voor het verzamelen, samen voegen en opslaan van toepassings logboeken met logboek aggregatie.

Met de functie voor het samen voegen van Logboeken wordt toegang tot toepassings logboeken meer deterministisch. Hiermee worden logboeken samengevoegd over alle containers op een werk knooppunt en worden deze opgeslagen als één geaggregeerd logboek bestand per worker-knoop punt. Het logboek wordt opgeslagen op het standaard bestandssysteem nadat een toepassing is voltooid. Uw toepassing kan honderden of duizenden containers gebruiken, maar logboeken voor alle containers die worden uitgevoerd op één worker-knoop punt worden altijd geaggregeerd naar één bestand. Er is slechts één logboek per werk knooppunt dat door uw toepassing wordt gebruikt. Logboek aggregatie is standaard ingeschakeld op HDInsight-clusters versie 3,0 en hoger. Samengevoegde logboeken bevinden zich in de standaard opslag voor het cluster. Het volgende pad is het HDFS-pad naar de logboeken:

```
/app-logs/<user>/logs/<applicationId>
```

In het pad `user` is de naam van de gebruiker die de toepassing heeft gestart. De `applicationId` is de unieke id die is toegewezen aan een toepassing door de garens RM.

De geaggregeerde logboeken zijn niet direct leesbaar, omdat ze zijn geschreven in een TFile, binaire indeling geïndexeerd door container. Gebruik de garen `ResourceManager` Logboeken of cli-hulpprogram ma's om deze logboeken te bekijken als tekst zonder opmaak voor toepassingen of containers die van belang zijn.

## <a name="yarn-logs-in-an-esp-cluster"></a>Garens Logboeken in een ESP-cluster

Er moeten twee configuraties worden toegevoegd aan de aangepaste `mapred-site` Ambari.

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Ga in de Ambari-gebruikers interface naar **MapReduce2**  >  **configs**  >  **Advanced**  >  **Custom mapred-site**.

1. Voeg *een* van de volgende sets eigenschappen toe:

    **Set 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Set 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Sla de wijzigingen op en start alle betrokken services opnieuw op.

## <a name="yarn-cli-tools"></a>Hulp middelen voor garen-CLI

1. Gebruik de [ssh-opdracht](./hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Een lijst met alle toepassings-Id's van de actieve garen toepassingen met de volgende opdracht:

    ```bash
    yarn top
    ```

    Noteer de toepassings-ID uit de `APPLICATIONID` kolom waarvan de logboeken moeten worden gedownload.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. U kunt deze logboeken als tekst zonder opmaak weer geven door een van de volgende opdrachten uit te voeren:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Geef de &lt; applicationId>, &lt; gebruiker-who-started-Application>, &lt; containerId> en &lt; work-node-Address> informatie op wanneer u deze opdrachten uitvoert.

### <a name="other-sample-commands"></a>Andere voorbeeld opdrachten

1. Down load de logboeken voor garen-containers voor alle toepassings modellen met de onderstaande opdracht. Met deze stap wordt het logboek bestand gemaakt `amlogs.txt` met de naam in tekst indeling.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Down load garens van de-container alleen voor de nieuwste toepassings Master met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Down load garen-container logboeken voor de eerste twee toepassings modellen met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Down load alle garen container logboeken met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Down load het garen-container logboek voor een bepaalde container met de volgende opdracht:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>GAREN `ResourceManager` gebruikers interface

De garen- `ResourceManager` UI wordt uitgevoerd op de cluster-hoofd knooppunt. Het is toegankelijk via de Ambari-webgebruikersinterface. Voer de volgende stappen uit om de GARENs-logboeken weer te geven:

1. Navigeer in uw webbrowser naar `https://CLUSTERNAME.azurehdinsight.net` . Vervang CLUSTERNAME door de naam van uw HDInsight-cluster.

2. Selecteer in de lijst met services aan de linkerkant de optie **garens**.

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png" alt-text="Apache Ambari garen-service geselecteerd":::

3. Selecteer in de vervolg keuzelijst **snelle koppelingen** een van de cluster hoofd knooppunten en selecteer **`ResourceManager Log`** .

    :::image type="content" source="./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png" alt-text="Snelle koppelingen voor Apache Ambari-garens":::

    Er wordt een lijst weer gegeven met koppelingen naar GARENs in de logboeken.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop-architectuur in HDInsight](hdinsight-hadoop-architecture.md)
* [Problemen met Apache Hadoop YARN oplossen met behulp van Azure HDInsight](hdinsight-troubleshoot-yarn.md)
