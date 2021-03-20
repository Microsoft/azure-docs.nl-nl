---
title: Verbinding maken met Apache Beeline-Azure HDInsight of deze installeren
description: Meer informatie over hoe u verbinding maakt met de Apache Beeline-client om Hive-query's uit te voeren met Hadoop op HDInsight. Beeline is een hulp programma voor het werken met HiveServer2 over JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: ab5dedf6718dad4f16fde59d905e2e59be5c495f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944459"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Verbinding maken met Apache Beeline op HDInsight of lokaal installeren

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) is een Hive-client die is opgenomen in de hoofd knooppunten van uw HDInsight-cluster. In dit artikel wordt beschreven hoe u verbinding maakt met de Beeline-client die is geïnstalleerd op uw HDInsight-cluster voor verschillende soorten verbindingen. Ook wordt beschreven hoe u [de Beeline-client lokaal installeert](#install-beeline-client). 

## <a name="types-of-connections"></a>Typen verbindingen

### <a name="from-an-ssh-session"></a>Vanuit een SSH-sessie

Wanneer u verbinding maakt vanuit een SSH-sessie met een cluster hoofd knooppunt, kunt u vervolgens verbinding maken met het `headnodehost` adres op de poort `10001` :

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Via een Azure-Virtual Network

Wanneer u vanaf een client verbinding maakt met HDInsight via een Azure-Virtual Network, moet u de Fully Qualified Domain Name (FQDN) van het hoofd knooppunt van het cluster opgeven. Omdat deze verbinding rechtstreeks met de cluster knooppunten tot stand is gebracht, gebruikt de verbinding poort `10001` :

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Vervang door `<headnode-FQDN>` de Fully Qualified Domain name van een cluster hoofd knooppunt. Als u de Fully Qualified Domain Name van een hoofd knooppunt wilt vinden, gebruikt u de informatie in het bestand [HDInsight beheren met behulp van het Apache Ambari rest API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) -document.

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Naar HDInsight Enterprise Security Package (ESP)-cluster met behulp van Kerberos

Wanneer u vanaf een client verbinding maakt met een Enterprise Security Package (ESP)-cluster dat is gekoppeld aan Azure Active Directory (AAD)-DS op een computer in dezelfde realm van het cluster, moet u ook de domein naam `<AAD-Domain>` en de naam van een domein gebruikers account opgeven met machtigingen voor toegang tot het cluster `<username>` :

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Vervang door `<username>` de naam van een account in het domein met machtigingen voor toegang tot het cluster. Vervang door `<AAD-DOMAIN>` de naam van de Azure Active Directory (Aad) waaraan het cluster is gekoppeld. Gebruik een letterlijke teken reeks voor de `<AAD-DOMAIN>` waarde, anders wordt de referentie niet gevonden. Controleer `/etc/krb5.conf` zo nodig de realm-namen.

De JDBC-URL vinden vanuit Ambari:

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, waarbij `CLUSTERNAME` de naam van uw cluster is. Zorg ervoor dat HiveServer2 wordt uitgevoerd.

1. Het klem bord gebruiken om de HiveServer2 JDBC-URL te kopiëren.

### <a name="over-public-or-private-endpoints"></a>Via open bare of persoonlijke eind punten

Wanneer u verbinding maakt met een cluster met behulp van de open bare of persoonlijke eind punten, moet u de naam van het cluster aanmeldings account (standaard `admin` ) en het wacht woord opgeven. Bijvoorbeeld, met behulp van Beeline van een-client systeem om verbinding te maken met het `clustername.azurehdinsight.net` adres. Deze verbinding wordt tot stand gebracht `443` en is versleuteld met behulp van TLS/SSL.

Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang door `admin` het cluster aanmeldings account voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com ). Vervang door `password` het wacht woord voor het account voor het aanmelden bij het cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

of voor een persoonlijk eind punt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Persoonlijke eind punten verwijzen naar een basis load balancer, die alleen toegankelijk is vanaf de VNETs die in dezelfde regio is gepeerd. Zie [beperkingen voor wereld wijde VNet-peering en load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U kunt de `curl` opdracht gebruiken met de `-v` optie voor het oplossen van verbindings problemen met open bare of persoonlijke eind punten voordat u Beeline gebruikt.

### <a name="use-beeline-with-apache-spark"></a>Beeline gebruiken met Apache Spark

Apache Spark biedt een eigen implementatie van HiveServer2, die ook wel de Spark Thrift-server wordt genoemd. Deze service maakt gebruik van Spark SQL om query's op te lossen in plaats van Hive. En kunnen betere prestaties leveren, afhankelijk van uw query.

#### <a name="through-public-or-private-endpoints"></a>Via open bare of privé-eind punten

Het gebruikte connection string is iets anders. In plaats van `httpPath=/hive2` het te gebruiken `httpPath/sparkhive2` . Vervang `clustername` door de naam van uw HDInsight-cluster. Vervang door `admin` het cluster aanmeldings account voor uw cluster. Gebruik voor ESP-clusters de volledige UPN (bijvoorbeeld user@domain.com ). Vervang door `password` het wacht woord voor het account voor het aanmelden bij het cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

of voor een persoonlijk eind punt:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Persoonlijke eind punten verwijzen naar een basis load balancer, die alleen toegankelijk is vanaf de VNETs die in dezelfde regio is gepeerd. Zie [beperkingen voor wereld wijde VNet-peering en load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. U kunt de `curl` opdracht gebruiken met de `-v` optie voor het oplossen van verbindings problemen met open bare of persoonlijke eind punten voordat u Beeline gebruikt.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Van cluster kop of binnen Azure Virtual Network met Apache Spark

Wanneer u rechtstreeks verbinding maakt met het hoofd knooppunt van het cluster of van een resource binnen hetzelfde Azure-Virtual Network als het HDInsight-cluster, moet de poort `10002` worden gebruikt voor de Spark Thrift-server in plaats van `10001` . In het volgende voor beeld ziet u hoe u rechtstreeks verbinding maakt met het hoofd knooppunt:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Beeline-client installeren

Hoewel Beeline is opgenomen op de hoofd knooppunten, wilt u deze mogelijk lokaal installeren.  De installatie stappen voor een lokale computer zijn gebaseerd op een [Windows-subsysteem voor Linux](/windows/wsl/install-win10).

1. Pakket lijsten bijwerken. Voer de volgende opdracht in uw bash-shell in:

    ```bash
    sudo apt-get update
    ```

1. Installeer Java als dat niet is geïnstalleerd. U kunt controleren met de `which java` opdracht.

    1. Als er geen Java-pakket is geïnstalleerd, voert u de volgende opdracht in:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Open het bashrc-bestand (vaak gevonden in ~/.bashrc): `nano ~/.bashrc` .

    1. Wijzig het bashrc-bestand. Voeg de volgende regel toe aan het einde van het bestand:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Druk vervolgens op **CTRL + X**, vervolgens op **j** en vervolgens op ENTER.

1. Down load Hadoop-en Beeline-archieven, voer de volgende opdrachten in:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. De archieven uitpakken, voert u de volgende opdrachten in:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Wijzig het bashrc-bestand verder. U moet het pad naar de locatie waar de archieven zijn uitgepakt, identificeren. Als u het [Windows-subsysteem voor Linux](/windows/wsl/install-win10)gebruikt en u de stappen precies hebt uitgevoerd, is het pad `/mnt/c/Users/user/` waar `user` uw gebruikers naam is.

    1. Open het bestand: `nano ~/.bashrc`

    1. Wijzig de onderstaande opdrachten met het juiste pad en voer deze vervolgens aan het einde van het bashrc-bestand in:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Druk vervolgens op **CTRL + X**, vervolgens op **j** en vervolgens op ENTER.

1. Sluit de bash-sessie en open deze opnieuw.

1. Test de verbinding. Gebruik de verbindings indeling van [meer dan open bare of persoonlijke eind punten](#over-public-or-private-endpoints).

## <a name="next-steps"></a>Volgende stappen

* Zie [Apache Beeline gebruiken met Apache Hive](apache-hadoop-use-hive-beeline.md) voor voor beelden van het gebruik van de Beeline-client met Apache Hive.
* Zie [Apache Hive gebruiken met Apache Hadoop in hdinsight](hdinsight-use-hive.md) voor meer algemene informatie over Hive in hdinsight