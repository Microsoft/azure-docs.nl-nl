---
title: Apache Kafka TLS-versleutelings & authenticatie-Azure HDInsight
description: TLS-versleuteling instellen voor communicatie tussen Kafka-clients en Kafka-Brokers, en tussen Kafka-brokers. SSL-verificatie van clients instellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 9a95970647a26ea80db9f63fb8523c6a65cc5e06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012302"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>TLS-versleuteling en-verificatie instellen voor Apache Kafka in azure HDInsight

In dit artikel wordt beschreven hoe u Transport Layer Security (TLS)-versleuteling (voorheen bekend als Secure Sockets Layer (SSL)-versleuteling, kunt instellen tussen Apache Kafka-clients en Apache Kafka makelaars. Ook wordt uitgelegd hoe u verificatie van clients instelt (ook wel twee richtings-TLS genoemd).

> [!Important]
> Er zijn twee clients die u kunt gebruiken voor Kafka-toepassingen: een Java-client en een-console-client. Alleen de Java-client `ProducerConsumer.java` kan TLS gebruiken voor zowel produceren als verbruiken. De console-producer-client `console-producer.sh` werkt niet met TLS.

> [!Note]
> De Kafka-console producent van HDInsight met versie 1,1 biedt geen ondersteuning voor SSL.

## <a name="apache-kafka-broker-setup"></a>Setup van Apache Kafka Broker

Het installatie programma voor Kafka TLS Broker maakt op de volgende manier gebruik van vier HDInsight-cluster-Vm's:

* hoofd knooppunt 0-certificerings instantie (CA)
* worker-knoop punt 0, 1 en 2-Brokers

> [!Note] 
> In deze hand leiding worden zelfondertekende certificaten gebruikt, maar de veiligste oplossing is het gebruik van certificaten die zijn uitgegeven door vertrouwde certificerings instanties.

De samen vatting van het installatie proces van de Broker is als volgt:

1. De volgende stappen worden herhaald op elk van de drie worker-knoop punten:

    1. Genereer een certificaat.
    1. Maak een aanvraag voor certificaat ondertekening.
    1. Verzend de aanvraag voor certificaat ondertekening naar de certificerings instantie (CA).
    1. Meld u aan bij de CA en onderteken de aanvraag.
    1. SCP het ondertekende certificaat terug naar het worker-knoop punt.
    1. SCP het open bare certificaat van de certificerings instantie naar het worker-knoop punt.

1. Als u alle certificaten hebt, plaatst u de Certificaats in het certificaat archief.
1. Ga naar Ambari en wijzig de configuraties.

Gebruik de volgende gedetailleerde instructies om de Broker-installatie te volt ooien:

> [!Important]
> In de volgende code fragmenten wnX is een afkorting voor een van de drie worker-knoop punten en moet worden vervangen door `wn0` , `wn1` of `wn2` waar nodig. `WorkerNode0_Name` en `HeadNode0_Name` moet worden vervangen door de namen van de betreffende computers.

1. Voer de eerste installatie uit op het hoofd knooppunt 0, dat voor HDInsight de rol van de certificerings instantie (CA) zal vullen.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Voer dezelfde initiële installatie uit voor elk van de Brokers (worker-knoop punten 0, 1 en 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Voer op elk van de worker-knoop punten de volgende stappen uit met behulp van het code fragment hieronder.
    1. Maak een-opslag en vul deze in met een nieuw persoonlijk certificaat.
    1. Maak een aanvraag voor certificaat ondertekening.
    1. SCP de aanvraag voor certificaat ondertekening aan de CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Voer op de CA-computer de volgende opdracht uit om CA-certificaat-en CA-sleutel bestanden te maken:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Ga naar de CA-computer en Onderteken alle ontvangen aanvragen voor certificaat ondertekening:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Stuur de ondertekende certificaten terug naar de worker-knoop punten van de CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Verzend het open bare certificaat van de certificerings instantie naar elk worker-knoop punt.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Voeg op elk worker-knoop punt het open bare CAs-certificaat toe aan de truststore en de-opslag. Voeg vervolgens een eigen ondertekend certificaat toe aan de opslag van het werk knooppunt

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Kafka-configuratie bijwerken voor het gebruik van TLS en het opnieuw opstarten van Brokers

U hebt nu elke Kafka-Broker met een Store en truststore ingesteld en de juiste certificaten geïmporteerd. Wijzig vervolgens de eigenschappen van gerelateerde Kafka-configuratie met behulp van Ambari en start vervolgens de Kafka-Brokers opnieuw.

Voer de volgende stappen uit om de configuratie wijziging te volt ooien:

1. Meld u aan bij de Azure Portal en selecteer uw Azure HDInsight Apache Kafka-cluster.
1. Ga naar de Ambari-gebruikers interface door te klikken op **Ambari start** onder **cluster dashboards**.
1. Stel de eigenschap **listeners** onder **Kafka Broker** in op`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Onder **Geavanceerd Kafka-Broker** stelt u de eigenschap **Security. intercompany Broker. Protocol** in op `SSL`

    ![Kafka SSL-configuratie-eigenschappen bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Stel de eigenschap **SSL. client. auth** onder **aangepast Kafka-Broker** in op `required` . Deze stap is alleen vereist als u verificatie en versleuteling instelt.

    ![Kafka SSL-configuratie-eigenschappen bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Voor HDI versie 3,6 gaat u naar de Ambari-gebruikers interface en voegt u de volgende configuraties toe onder **Geavanceerde Kafka-env** en de **Kafka-env-sjabloon** eigenschap.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Hier ziet u de scherm opname waarin de gebruikers interface van Ambari-configuratie met deze wijzigingen wordt weer gegeven.

    Voor HDI-versie 3,6:

    ![De eigenschap Kafka-env-sjabloon bewerken in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Voor HDI-versie 4,0:

     ![De Kafka-env-sjabloon eigenschap bewerken in Ambari vier](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Start alle Kafka-Brokers opnieuw op.

## <a name="client-setup-without-authentication"></a>Client installatie (zonder verificatie)

Als u geen verificatie nodig hebt, is het overzicht van de stappen voor het instellen van alleen TLS-versleuteling:

1. Meld u aan bij de CA (actief hoofd knooppunt).
1. Kopieer het CA-certificaat naar de client computer vanaf de CA-computer (wn0).
1. Meld u aan bij de client computer (HN1) en navigeer naar de `~/ssl` map.
1. Importeer het CA-certificaat naar het truststore.
1. Importeer het CA-certificaat naar de-opslag.

Deze stappen worden beschreven in de volgende code fragmenten.

1. Meld u aan bij het CA-knoop punt.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Kopieer het CA-certificaat naar de client computer

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Meld u aan bij de client computer (stand-by-hoofd knooppunt).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importeer het CA-certificaat naar het truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importeer het CA-certificaat in de-opslag.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Maak het bestand `client-ssl-auth.properties` op de client computer (HN1). Deze moet de volgende regels bevatten:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Start de admin-client met producer-en consumenten opties om te controleren of zowel producenten als consumenten op poort 9093 werken. Raadpleeg de sectie [verificatie](apache-kafka-ssl-encryption-authentication.md#verification) hieronder voor de stappen die nodig zijn om de installatie te controleren met behulp van de console producer/Consumer.

## <a name="client-setup-with-authentication"></a>Client installatie (met verificatie)

> [!Note]
> De volgende stappen zijn alleen vereist als u zowel TLS **-code ring als verificatie** instelt. Als u alleen versleuteling wilt instellen, raadpleegt u [client installatie zonder verificatie](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

De volgende vier stappen zijn een overzicht van de taken die nodig zijn om de client installatie te volt ooien:

1. Meld u aan bij de client computer (stand-by-hoofd knooppunt).
1. Een Java-opslag archief maken en een ondertekend certificaat voor de Broker ophalen. Kopieer vervolgens het certificaat naar de virtuele machine waarop de CA wordt uitgevoerd.
1. Schakel over naar de CA-computer (actief hoofd knooppunt) om het client certificaat te ondertekenen.
1. Ga naar de client computer (stand-by hoofd knooppunt) en navigeer naar de `~/ssl` map. Kopieer het ondertekende certificaat naar de client computer.

De details van elke stap worden hieronder gegeven.

1. Meld u aan bij de client computer (stand-by-hoofd knooppunt).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Verwijder alle bestaande SSL-mappen.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Maak een Java-opslag archief en maak een aanvraag voor certificaat ondertekening. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. De aanvraag voor certificaat ondertekening kopiëren naar de CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Schakel over naar de CA-computer (actief hoofd knooppunt) en onderteken het client certificaat.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Kopieer het ondertekende client certificaat van de CA (actief hoofd knooppunt) naar de client computer.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Kopieer het CA-certificaat naar de client computer

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Meld u aan bij de client computer (stand-by-hoofd knooppunt) en navigeer naar de SSL-Directory.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Maak een client archief met ondertekend certificaat en importeer het CA-certificaat in de opslag-en truststore op de client computer (HN1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Maak een bestand `client-ssl-auth.properties` op de client computer (HN1). Deze moet de volgende regels bevatten:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verificatie

Voer de volgende stappen uit op de client computer.

> [!Note]
> Als HDInsight 4,0 en Kafka 2,1 is geïnstalleerd, kunt u de producent/consumenten van de console gebruiken om uw instellingen te controleren. Als dat niet het geval is, voert u de Kafka-producent uit op poort 9092, verzendt u berichten naar het onderwerp en gebruikt u vervolgens de Kafka-consumer op poort 9093 die gebruikmaakt van TLS.

### <a name="kafka-21-or-above"></a>Kafka 2,1 of hoger

1. Maak een onderwerp als het nog niet bestaat.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Start de producent van de console en geef het pad op `client-ssl-auth.properties` als een configuratie bestand voor de producent.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Open een andere SSH-verbinding met de client computer en start de consument van de console en geef het pad op `client-ssl-auth.properties` als een configuratie bestand voor de consumer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Maak een onderwerp als het nog niet bestaat.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Start de producent van de console en geef het pad op naar client-SSL-auth. properties als een configuratie bestand voor de producent.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Open een andere SSH-verbinding met de client computer en start de consument van de console en geef het pad op `client-ssl-auth.properties` als een configuratie bestand voor de consumer.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Volgende stappen

* [Wat is Apache Kafka in HDInsight?](apache-kafka-introduction.md)
