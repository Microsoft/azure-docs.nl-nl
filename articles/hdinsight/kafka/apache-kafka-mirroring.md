---
title: Apache Kafka-onderwerpen spie gelen-Azure HDInsight
description: Meer informatie over het gebruik van de functie spiegeling van Apache Kafka om een replica van een Kafka in HDInsight-cluster te onderhouden door onderwerpen te spie gelen naar een secundair cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: c2fce6d4ee95a56cc087d50184fcd69ac113620f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940842"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker gebruiken om Apache Kafka-onderwerpen te repliceren met Kafka in HDInsight

Meer informatie over het gebruik van de functie spiegeling van Apache Kafka om onderwerpen te repliceren naar een secundair cluster. Spiegeling kan worden uitgevoerd als een doorlopend proces of worden gebruikt als een methode voor het migreren van gegevens van het ene naar het andere cluster.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *whitelist*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

In dit voor beeld wordt spiegeling gebruikt voor het repliceren van onderwerpen tussen twee HDInsight-clusters. Beide clusters bevinden zich in verschillende virtuele netwerken in verschillende data centers.

> [!WARNING]  
> Mirroring mag niet worden beschouwd als een manier om fout tolerantie te krijgen. De offset van items in een onderwerp verschilt tussen de primaire en secundaire clusters, zodat clients de twee onderlinge verschillen niet kunnen gebruiken.
>
> Als u zich zorgen maakt over fout tolerantie, moet u de replicatie voor de onderwerpen in uw cluster instellen. Zie [aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)voor meer informatie.

## <a name="how-apache-kafka-mirroring-works"></a>Hoe Apache Kafka mirroring werkt?

Spiegeling werkt met behulp van het hulp programma [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (onderdeel van Apache Kafka) om records te gebruiken uit onderwerpen op het primaire cluster en vervolgens een lokale kopie te maken op het secundaire cluster. MirrorMaker maakt gebruik van een (of meer) *consumenten* die van het primaire cluster worden gelezen en een *producent* die naar het lokale (secundaire) cluster schrijft.

De meest nuttige instelling voor het spie gelen van de spiegeling voor herstel na nood gevallen maakt gebruik van Kafka-clusters in verschillende Azure-regio's. Hiervoor zijn de virtuele netwerken waar de clusters zich bevinden, gekoppeld aan elkaar.

Het volgende diagram illustreert het spiegel proces en hoe de communicatie stromen tussen clusters:

![Diagram van het spiegel proces](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

De primaire en secundaire clusters kunnen verschillen in het aantal knoop punten en partities, en verschuivingen in de onderwerpen zijn ook verschillend. Spiegeling houdt de sleutel waarde bij die wordt gebruikt voor partitioneren, zodat de volg orde van de records per sleutel wordt bewaard.

### <a name="mirroring-across-network-boundaries"></a>Spie gelen tussen netwerk grenzen

Als u een mirror moet maken tussen Kafka-clusters in verschillende netwerken, zijn er de volgende aanvullende overwegingen:

* **Gateways**: de netwerken moeten kunnen communiceren op het niveau van de TCP/IP.

* **Server adressen**: u kunt uw cluster knooppunten adresseren met behulp van hun IP-adressen of volledig gekwalificeerde domein namen.

    * **IP-adressen**: als u uw Kafka-clusters configureert voor het gebruik van IP-adres reclame, kunt u door gaan met de installatie van het spie gelen van de IP-adressen van de knoop punten Broker en Zookeeper.
    
    * **Domein namen**: als u uw Kafka-clusters niet configureert voor het adverteren van IP-adressen, moeten de clusters verbinding met elkaar kunnen maken met behulp van FQDN-namen (Fully Qualified Domain Name). Hiervoor is een Domain Name System-server (DNS) vereist in elk netwerk dat is geconfigureerd voor het door sturen van aanvragen naar de andere netwerken. Wanneer u een Azure-Virtual Network maakt, moet u in plaats van de automatische DNS-naam die bij het netwerk wordt gebruikt, een aangepaste DNS-server en het IP-adres voor de server opgeven. Nadat de Virtual Network is gemaakt, moet u vervolgens een virtuele Azure-machine maken die dat IP-adres gebruikt, vervolgens DNS-software installeren en configureren.

    > [!WARNING]  
    > Maak en configureer de aangepaste DNS-server voordat u HDInsight in de Virtual Network installeert. Er is geen aanvullende configuratie vereist voor HDInsight voor het gebruik van de DNS-server die is geconfigureerd voor de Virtual Network.

Zie [een vnet-naar-VNet-verbinding configureren](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)voor meer informatie over het verbinden van twee virtuele netwerken van Azure.

## <a name="mirroring-architecture"></a>Architectuur voor spie gelen

Deze architectuur bevat twee clusters in verschillende resource groepen en virtuele netwerken: een **primair** en **secundair**.

### <a name="creation-steps"></a>Stappen voor maken

1. Maak twee nieuwe resource groepen:

    |Resourcegroep | Locatie |
    |---|---|
    | Kafka-primair-RG | VS - centraal |
    | Kafka-secundair-RG | VS - noord-centraal |

1. Maak een nieuw virtueel netwerk **Kafka-primair-vnet** in **Kafka-Primary-RG**. Behoud de standaard instellingen.
1. Maak een nieuw virtueel netwerk **Kafka-secundair-vnet** in **Kafka-Secondary-RG**, ook met de standaard instellingen.

1. Twee nieuwe Kafka-clusters maken:

    | Clusternaam | Resourcegroep | Virtual Network | Opslagaccount |
    |---|---|---|---|
    | Kafka-primair-cluster | Kafka-primair-RG | Kafka-primair-vnet | kafkaprimarystorage |
    | Kafka-secundair-cluster | Kafka-secundair-RG | Kafka-secundair-vnet | kafkasecondarystorage |

1. Maak peerings voor virtuele netwerken. Met deze stap maakt u twee peerings: één van **Kafka-Primary-vnet** naar **Kafka-Secondary-vnet** en één keer van **Kafka-Secondary-vnet** naar **Kafka-Primary-vnet**.
    1. Selecteer het virtuele netwerk **Kafka-Primary-vnet** .
    1. Selecteer **peerings** onder **instellingen**.
    1. Selecteer **Toevoegen**.
    1. Geef in het scherm **peering toevoegen** de details op, zoals wordt weer gegeven in de onderstaande scherm afbeelding.

        ![HDInsight Kafka vnet-peering toevoegen](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>IP-reclame configureren

Configureer IP-reclame om een client in staat te stellen verbinding te maken met behulp van IP-adressen van Broker in plaats van domein namen.

1. Ga naar het Ambari-dash board voor het primaire cluster: `https://PRIMARYCLUSTERNAME.azurehdinsight.net` .
1. Selecteer **Services**  >  **Kafka**. CliSelectck het tabblad **configuraties** .
1. Voeg de volgende configuratie regels toe aan de onderste **sjabloon sectie Kafka-env** . Selecteer **Opslaan**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Voer een opmerking in het scherm **configuratie opslaan** in en klik op **Opslaan**.
1. Als u wordt gevraagd om een configuratie waarschuwing, klikt u op **door gaan**.
1. Selecteer **OK** op de **wijzigingen in de configuratie opslaan**.
1. Selecteer **opnieuw opstarten opnieuw** starten  >  **alle beïnvloed** in de melding **opnieuw opstarten vereist** . Selecteer **Bevestig opnieuw opstarten**.

    ![Apache Ambari alle betrokken software opnieuw opstarten](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configureer Kafka om te Luis teren op alle netwerk interfaces.
    
1. Blijf op het tabblad **configuratie** onder **Services**  >  **Kafka**. Stel in het gedeelte **Kafka-Broker** de eigenschap **listeners** in op `PLAINTEXT://0.0.0.0:9092` .
1. Selecteer **Opslaan**.
1. Selecteer **opnieuw opstarten** en **Bevestig opnieuw opstarten**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Registreer Broker IP-adressen en Zookeeper-adressen voor primair cluster.

1. Selecteer **hosts** op het Ambari-dash board.
1. Noteer de IP-adressen voor de brokers en Zookeepers. De Broker knooppunten hebben **wn** als de eerste twee letters van de hostnaam en de Zookeeper-knoop punten hebben **ZK** als de eerste twee letters van de hostnaam.

    ![IP-adressen van Apache Ambari-weergave knooppunt](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Herhaal de vorige drie stappen voor het tweede cluster **Kafka-secundair-cluster**: IP-reclame configureren, listeners instellen en noteer de Broker-en Zookeeper-IP-adressen.

## <a name="create-topics"></a>Onderwerpen maken

1. Verbinding maken met het **primaire** cluster via SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Vervang **sshuser** door de SSH-gebruikers naam die wordt gebruikt bij het maken van het cluster. Vervang **PRIMARYCLUSTER** door de basis naam die wordt gebruikt bij het maken van het cluster.

    Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

1. Gebruik de volgende opdracht om een variabele te maken met de Apache Zookeeper-hosts voor het primaire cluster. De teken reeksen zoals `ZOOKEEPER_IP_ADDRESS1` moeten worden vervangen door de werkelijke IP-adressen die eerder zijn geregistreerd, zoals `10.23.0.11` en `10.23.0.7` . Als u de FQDN-resolutie met een aangepaste DNS-server gebruikt, voert u [de volgende stappen uit om de](apache-kafka-get-started.md#getkafkainfo) namen van Broker en Zookeeper op te halen.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Als u een onderwerp met de naam wilt maken `testtopic` , gebruikt u de volgende opdracht:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Gebruik de volgende opdracht om te controleren of het onderwerp is gemaakt:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Het antwoord bevat `testtopic` .

1. Gebruik de volgende informatie om de Zookeeper van de host te bekijken (het **primaire**) cluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Dit retourneert informatie die lijkt op de volgende tekst:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Sla deze informatie op. Deze wordt gebruikt in de volgende sectie.

## <a name="configure-mirroring"></a>Spiegeling configureren

1. Verbinding maken met het **secundaire** cluster met behulp van een andere SSH-sessie:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Vervang **sshuser** door de SSH-gebruikers naam die wordt gebruikt bij het maken van het cluster. Vervang **SECONDARYCLUSTER** door de naam die is gebruikt bij het maken van het cluster.

    Zie [SSH-sleutels gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

1. Een `consumer.properties` bestand wordt gebruikt voor het configureren van de communicatie met het **primaire** cluster. Gebruik de volgende opdracht om het bestand te maken:

    ```bash
    nano consumer.properties
    ```

    Gebruik de volgende tekst als de inhoud van het `consumer.properties` bestand:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Vervang **PRIMARY_ZKHOSTS** door de IP-adressen van de Zookeeper van het **primaire** cluster.

    In dit bestand wordt de consument informatie beschreven die moet worden gebruikt bij het lezen van het primaire Kafka-cluster. Zie configuratie van [consumenten](https://kafka.apache.org/documentation#consumerconfigs) op Kafka.apache.org voor meer informatie.

    Als u het bestand wilt opslaan, gebruikt u **CTRL + X**, **Y** en **voert** u in.

1. Voordat u de producent configureert die met het secundaire cluster communiceert, stelt u een variabele in voor de IP-adressen van de Broker van het **secundaire** cluster. Gebruik de volgende opdrachten om deze variabele te maken:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    De opdracht `echo $SECONDARY_BROKERHOSTS` moet informatie retour neren die overeenkomt met de volgende tekst:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Een `producer.properties` bestand wordt gebruikt om het **secundaire** cluster te communiceren. Gebruik de volgende opdracht om het bestand te maken:

    ```bash
    nano producer.properties
    ```

    Gebruik de volgende tekst als de inhoud van het `producer.properties` bestand:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Vervang **SECONDARY_BROKERHOSTS** door de in de vorige stap gebruikte Broker-IP-adressen.

    Zie [producent configuraties](https://kafka.apache.org/documentation#producerconfigs) op Kafka.apache.org voor meer informatie.

1. Gebruik de volgende opdrachten om een omgevings variabele te maken met de IP-adressen van de Zookeeper-hosts voor het secundaire cluster:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Met de standaard configuratie voor Kafka in HDInsight is het automatisch maken van onderwerpen niet toegestaan. U moet een van de volgende opties gebruiken voordat u het spiegel proces start:

    * **De onderwerpen maken op het secundaire cluster**: met deze optie kunt u ook het aantal partities en de replicatie factor instellen.

        U kunt de volgende opdracht gebruiken om eerder onderwerpen te maken:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Vervang door `testtopic` de naam van het onderwerp dat u wilt maken.

    * **Het cluster configureren voor het automatisch maken van een onderwerp**: met deze optie kan MirrorMaker automatisch onderwerpen maken, maar dit kan worden gemaakt met een ander aantal partities of replicatie factor dan het primaire onderwerp.

        Voer de volgende stappen uit om het secundaire cluster zo te configureren dat er automatisch onderwerpen worden gemaakt:

        1. Ga naar het Ambari-dash board voor het secundaire cluster: `https://SECONDARYCLUSTERNAME.azurehdinsight.net` .
        1. Klik op **Services**  >  **Kafka**. Klik op het tabblad **configuratie** .
        1. Voer in het veld __filter__ een waarde van in `auto.create` . Hiermee wordt de lijst met eigenschappen gefilterd en wordt de instelling weer gegeven `auto.create.topics.enable` .
        1. Wijzig de waarde van `auto.create.topics.enable` in waar en selecteer vervolgens __Opslaan__. Voeg een notitie toe en selecteer vervolgens __Opslaan__ opnieuw.
        1. Selecteer de __Kafka__ -service, selecteer __opnieuw opstarten__ en selecteer vervolgens __alle betrokkenen opnieuw opstarten__. Selecteer __Bevestig opnieuw opstarten__ als dit wordt gevraagd.

        ![Kafka inschakelen voor automatisch maken](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>MirrorMaker starten

1. Gebruik van de SSH-verbinding met het **secundaire** cluster de volgende opdracht om het MirrorMaker-proces te starten:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De para meters die in dit voor beeld worden gebruikt, zijn:

    |Parameter |Beschrijving |
    |---|---|
    |--consumer.config|Hiermee geeft u het bestand op dat de eigenschappen van de gebruiker bevat. Deze eigenschappen worden gebruikt voor het maken van een consumer die leest van het *primaire* Kafka-cluster.|
    |--producer.config|Hiermee geeft u het bestand op dat de eigenschappen van de producent bevat. Deze eigenschappen worden gebruikt voor het maken van een producent die naar het *secundaire* Kafka-cluster schrijft.|
    |--White List|Een lijst met onderwerpen die MirrorMaker repliceren van het primaire cluster naar de secundaire.|
    |--num. streams|Het aantal te maken Consumer threads.|

    De consument op het secundaire knoop punt wacht nu op het ontvangen van berichten.

2. Van de SSH-verbinding met het **primaire** cluster, gebruikt u de volgende opdracht om een producent te starten en berichten te verzenden naar het onderwerp:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Wanneer u een lege regel met een cursor aankomt, typt u een paar tekst berichten. De berichten worden verzonden naar het onderwerp op het **primaire** cluster. Wanneer u klaar bent, gebruikt u **CTRL + C** om het proces van de producent te beëindigen.

3. Gebruik op basis van de SSH-verbinding met het **secundaire** cluster **CTRL + C** om het MirrorMaker-proces te beëindigen. Het kan enkele seconden duren voordat het proces is beëindigd. Gebruik de volgende opdracht om te controleren of de berichten naar de secundaire zijn gerepliceerd:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    De lijst met onderwerpen bevat nu een `testtopic` MirrorMaster die wordt gemaakt wanneer het onderwerp van het primaire cluster wordt gespiegeld met de secundaire. De berichten die worden opgehaald uit het onderwerp, zijn dezelfde als die u hebt ingevoerd op het primaire cluster.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Met de stappen in dit document worden clusters in verschillende Azure-resource groepen gemaakt. Als u alle gemaakte resources wilt verwijderen, kunt u de twee gemaakte resource groepen verwijderen: **Kafka-Primary-RG** en **Kafka-secondary_rg**. Als u de resource groepen verwijdert, worden alle resources verwijderd die zijn gemaakt door dit document te volgen, met inbegrip van clusters, virtuele netwerken en opslag accounts.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) kunt gebruiken om een replica van een [Apache Kafka](https://kafka.apache.org/) cluster te maken. Gebruik de volgende koppelingen om andere manieren te ontdekken voor het werken met Kafka:

* [Apache Kafka MirrorMaker-documentatie](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) op cwiki.apache.org.
* [Best practices voor Kafka spiegel Maker](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Aan de slag met Apache Kafka op HDInsight](apache-kafka-get-started.md)
* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verbinding maken met Apache Kafka via een Azure-Virtual Network](apache-kafka-connect-vpn-gateway.md)
