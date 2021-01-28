---
title: Apache Oozie-werk stromen & Enter prise Security-Azure HDInsight
description: Beveilig Apache Oozie-werk stromen met behulp van de Enterprise Security Package van Azure HDInsight. Meer informatie over het definiëren van een Oozie-werk stroom en het verzenden van een Oozie-taak.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: 559779e3979ab2ada2191f55cfc06ceb2470bd7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933477"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Apache Oozie uitvoeren in azure HDInsight-clusters met Enterprise Security Package

Apache Oozie is een werk stroom-en coördinatie systeem waarmee Apache Hadoop taken worden beheerd. Oozie is geïntegreerd met de Hadoop-stack en biedt ondersteuning voor de volgende taken:

- Apache MapReduce
- Apache-Pig
- Apache Hive
- Apache Sqoop

U kunt Oozie ook gebruiken om taken te plannen die specifiek zijn voor een systeem, zoals Java-Program ma's of shell scripts.

## <a name="prerequisite"></a>Vereiste

Een Azure HDInsight Hadoop cluster met Enterprise Security Package (ESP). Zie [HDInsight-clusters configureren met ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Zie voor gedetailleerde instructies over het gebruik van Oozie op niet-ESP-clusters [Apache Oozie-werk stromen gebruiken in azure HDInsight op basis van Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Verbinding maken met een ESP-cluster

Zie [verbinding maken met HDInsight (Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over Secure Shell (SSH).

1. Verbinding maken met het HDInsight-cluster met behulp van SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Gebruik de opdracht om de geslaagde Kerberos-verificatie te controleren `klist` . Als dat niet het geval is, gebruikt `kinit` u om Kerberos-verificatie te starten.

1. Meld u aan bij de HDInsight-gateway om het OAuth-token te registreren dat vereist is voor toegang tot Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Een status antwoord code van **200 OK** duidt op een geslaagde registratie. Controleer de gebruikers naam en het wacht woord als er een niet-geautoriseerde reactie wordt ontvangen, zoals 401.

## <a name="define-the-workflow"></a>De werk stroom definiëren

Oozie-werk stroom definities worden geschreven in Apache Hadoop process Definition Language (hPDL). hPDL is een XML-proces definitie taal. Voer de volgende stappen uit om de werk stroom te definiëren:

1. De werk ruimte van een domein gebruiker instellen:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Vervang door `DomainUser` de gebruikers naam van het domein.
   Vervang door `DomainUserPath` het pad naar de basismap voor de domein gebruiker.
   Vervang door `ClusterVersion` de versie van het cluster data platform.

2. Gebruik de volgende instructie voor het maken en bewerken van een nieuw bestand:

   ```bash
   nano workflow.xml
   ```

3. Wanneer de nano editor wordt geopend, voert u de volgende XML in als de bestands inhoud:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. Vervang `clustername` door de naam van het cluster.

5. Selecteer **CTRL + X** om het bestand op te slaan. Voer **Y** in. Selecteer vervolgens **Enter**.

    De werk stroom bestaat uit twee delen:

   - **Referentie.** In deze sectie vindt u de referenties die worden gebruikt voor het verifiëren van Oozie-acties:

     In dit voor beeld wordt verificatie gebruikt voor Hive-acties. Zie [actie verificatie](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)voor meer informatie.

     Met de referentie service kunnen Oozie-acties de gebruiker imiteren om toegang te krijgen tot Hadoop-Services.

   - **Optreden.** Deze sectie heeft drie acties: toewijzing-verminderen, Hive Server 2 en Hive server 1:

     - Met de actie voor het verminderen van de kaart wordt een voor beeld van een Oozie-pakket voor de kaart, waardoor de geaggregeerde woorden telling wordt uitgevoerd.

     - Met de acties Hive Server 2 en Hive server 1 wordt een query uitgevoerd op een voor beeld van een Hive-tabel die wordt meegeleverd met HDInsight.

     De Hive-acties maken gebruik van de referenties die zijn gedefinieerd in de sectie referenties voor verificatie met behulp van het sleutel woord `cred` in het actie-element.

6. Gebruik de volgende opdracht om het `workflow.xml` bestand te kopiëren naar `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Vervang door de `domainuser` gebruikers naam voor het domein.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definieer het eigenschappen bestand voor de Oozie-taak

1. Gebruik de volgende instructie voor het maken en bewerken van een nieuw bestand voor taak eigenschappen:

    ```bash
    nano job.properties
    ```

2. Wanneer de nano editor wordt geopend, gebruikt u de volgende XML als de inhoud van het bestand:

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Gebruik de `adl://home` URI voor de `nameNode` eigenschap als u Azure data Lake Storage gen1 hebt als uw primaire cluster opslag. Als u Azure Blob Storage gebruikt, wijzigt u in `wasb://home` . Als u Azure Data Lake Storage Gen2 gebruikt, wijzigt u in `abfs://home` .
   - Vervang door de `domainuser` gebruikers naam voor het domein.
   - Vervang door `ClusterShortName` de korte naam voor het cluster. Als de cluster naam bijvoorbeeld https:// *[voor beeld koppeling]* sechadoopcontoso.azurehdisnight.net is, zijn de `clustershortname` eerste zes tekens van het cluster: **sechad**.
   - Vervang door `jdbcurlvalue` de JDBC-URL uit de Hive-configuratie. Een voor beeld is JDBC: hive2://headnodehost: 10001/; transportMode = http.
   - Als u het bestand wilt opslaan, selecteert u CTRL + X, voert u in `Y` en selecteert u vervolgens **Enter**.

   Dit eigenschappen bestand moet lokaal aanwezig zijn bij het uitvoeren van Oozie-taken.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Aangepaste Hive-scripts maken voor Oozie-taken

U kunt de twee Hive-scripts voor Hive server 1 en Hive Server 2 maken, zoals wordt weer gegeven in de volgende secties.

### <a name="hive-server-1-file"></a>Bestand van Hive-server 1

1. Een bestand maken en bewerken voor acties van Hive-server 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Maak het script:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Sla het bestand op Apache Hadoop Distributed File System (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Bestand van Hive Server 2

1. Een veld maken en bewerken voor acties van Hive-Server 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Maak het script:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Sla het bestand op in HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie-taken verzenden

Het verzenden van Oozie-taken voor ESP-clusters is vergelijkbaar met het verzenden van Oozie-taken in niet-ESP-clusters.

Zie [Apache Oozie gebruiken met Apache Hadoop om een werk stroom te definiëren en uit te voeren op Azure HDInsight op basis van Linux](../hdinsight-use-oozie-linux-mac.md)voor meer informatie.

## <a name="results-from-an-oozie-job-submission"></a>Resultaten van een Oozie-taak verzen ding

Oozie-taken worden uitgevoerd voor de gebruiker. De audit logboeken van Apache Hadoop garen en Apache zwerver geven dus de taken weer die worden uitgevoerd als de geïmiteerde gebruiker. De uitvoer van de opdracht regel interface van een Oozie-taak ziet eruit als in de volgende code:

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                        Status    Ext ID            ExtStatus                 ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK    -                         OK             -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK    job_1529975666160_0051    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK    job_1529975666160_0053    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive    OK     job_1529975666160_0055    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end       OK     -                         OK             -
-----------------------------------------------------------------------------------------------
```

In de controle logboeken van zwerver voor Hive Server 2 acties wordt weer gegeven Oozie het uitvoeren van de actie voor de gebruiker. De weer gaven zwerver en GARENs zijn alleen zichtbaar voor de Cluster beheerder.

## <a name="configure-user-authorization-in-oozie"></a>Gebruikers autorisatie in Oozie configureren

Oozie heeft zelf een configuratie voor gebruikers autorisatie waarmee gebruikers de taken van andere gebruikers kunnen stoppen of verwijderen. Als u deze configuratie wilt inschakelen, stelt `oozie.service.AuthorizationService.security.enabled` u de in op `true` . 

Zie voor meer informatie [Apache Oozie-installatie en-configuratie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Voor onderdelen als Hive server 1 waarbij de zwerver-invoeg toepassing niet beschikbaar of ondersteund is, is alleen een ruwe HDFS-autorisatie mogelijk. Nauw keurige autorisatie is alleen beschikbaar via zwerver-invoeg toepassingen.

## <a name="get-the-oozie-web-ui"></a>De Oozie-webgebruikersinterface ophalen

De Oozie-webgebruikersinterface biedt een webgebaseerde weer gave van de status van Oozie-taken in het cluster. Als u de webgebruikersinterface wilt ophalen, voert u de volgende stappen uit in ESP-clusters:

1. Voeg een [Edge-knoop punt](../hdinsight-apps-use-edge-node.md) toe en Schakel [SSH Kerberos-verificatie](../hdinsight-hadoop-linux-use-ssh-unix.md)in.

2. Volg de stappen in de [Oozie-webgebruikersinterface](../hdinsight-use-oozie-linux-mac.md) om SSH-Tunneling naar het Edge-knoop punt in te scha kelen en toegang te krijgen tot de webgebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

- [Gebruik Apache Oozie met Apache Hadoop om een werk stroom te definiëren en uit te voeren op Azure HDInsight op basis van Linux](../hdinsight-use-oozie-linux-mac.md).
- [Verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
