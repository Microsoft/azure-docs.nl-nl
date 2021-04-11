---
title: 'Quickstart: Apache Kafka maken met Azure PowerShell - HDInsight'
description: In deze snelstartgids leert u hoe u met Azure PowerShell een Apache Kafka-cluster maakt in Azure HDInsight. Er wordt ook aandacht besteed aan Kafka-onderwerpen, -abonnees en -consumenten.
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: f993ffa8d0d141d04ad399c5d1d4f0fc28cc82ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505134"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-powershell"></a>Quickstart: Een Apache Kafka-cluster maken in Azure HDInsight met PowerShell

[Apache Kafka](https://kafka.apache.org/) is een open-source, gedistribueerd streamingplatform. Het wordt vaak gebruikt als een berichtenbroker, omdat het een functionaliteit biedt die vergelijkbaar is met een publicatie-/abonnementswachtrij voor berichten. 

In deze snelstartgids leert u hoe u met Azure PowerShell een [Apache Kafka](https://kafka.apache.org)-cluster maakt. U leert ook hoe u de inbegrepen hulpprogramma's gebruikt voor het verzenden en ontvangen van berichten via Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

De Kafka-API is alleen toegankelijk voor resources binnen hetzelfde virtuele netwerk. In deze snelstart benadert u het cluster rechtstreeks via SSH. Als u andere services, netwerken of virtuele machines wilt verbinden met Kafka, moet u eerst een virtueel netwerk maken en vervolgens de resources maken in het netwerk. Zie het document [Verbinding maken met Apache Kafka via een virtueel netwerk](apache-kafka-connect-vpn-gateway.md) voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* De [Az-module](/powershell/azure/) van PowerShell geïnstalleerd.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de cmdlet `Connect-AzAccount` en volg de instructies op het scherm.

```azurepowershell-interactive
# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt u gevraagd om de naam en locatie, waarna er een nieuwe resourcegroep wordt gemaakt:

```azurepowershell-interactive
$resourceGroup = Read-Host -Prompt "Enter the resource group name"
$location = Read-Host -Prompt "Enter the Azure region to use"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Create a storage account

Kafka in HDInsight maakt gebruik van Azure Managed Disks voor het opslaan van Kafka-gegevens. Daarnaast gebruikt het cluster Azure Storage voor het opslaan van gegevens zoals logboeken. Maak een nieuw opslagaccount met [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

> [!IMPORTANT]  
> Een opslagaccount van het type `BlobStorage` kan alleen worden gebruikt als secundaire opslag voor HDInsight-clusters.

```azurepowershell-interactive
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $storageName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1
```

HDInsight slaat gegevens op in het opslagaccount in een blob-container. Maak een nieuwe container met [New-AzStorageContainer](/powershell/module/Az.Storage/New-AzStorageContainer).

```azurepowershell-interactive
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey

New-AzStorageContainer -Name $containerName -Context $storageContext
```

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka-cluster maken

Maak een Apache Kafka-cluster in HDInsight met [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster).

```azurepowershell-interactive
# Create a Kafka 1.1 cluster
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials" -UserName "sshuser"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$disksPerNode=2

$kafkaConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$kafkaConfig.Add("kafka", "1.1")

New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $kafkaConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

Het kan tot 20 minuten duren om het HDInsight-cluster te maken.

Met de parameter `-DisksPerWorkerNode` configureert u de schaalbaarheid van Kafka in HDInsight. Kafka in HDInsight gebruikt de lokale schijf van de virtuele machines in het cluster voor het opslaan van gegevens. Omdat Kafka veel gebruikmaakt van invoer/uitvoer, wordt [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) gebruikt voor een hoge doorvoer en meer opslag per knooppunt.

Het type beheerde schijf is __Standaard__ (HDD) of __Premium__ (SSD). Het type schijf is afhankelijk van de VM-grootte die wordt gebruikt door de werkknooppunten (Kafka-brokers). Premium-schijven worden automatisch gebruikt met VM's uit de DS- en GS-serie. Alle andere VM-typen gebruiken standaardschijven. U kunt het type VM instellen met de parameter `-WorkerNodeSize`. Zie de documentatie over [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster) voor meer informatie over parameters.

Als u van plan bent om meer dan 32 werkknooppunten te gebruiken (bij het maken van het cluster of bij het omhoog schalen van het cluster nadat dit is gemaakt), moet u de parameter `-HeadNodeSize` gebruiken om een VM-grootte op te geven met ten minste 8 kerngeheugens en 14 GB RAM-geheugen. Zie [Prijsdetails voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over knooppuntgrootten en de bijbehorende kosten.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

1. Gebruik de volgende opdracht om verbinding te maken met het primaire hoofdknooppunt van het Kafka-cluster. Vervang `sshuser` door de SSH-gebruikersnaam. Vervang `mykafka` door de naam van uw Kafka-cluster.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Wanneer u voor het eerst verbinding maakt met het cluster, wordt in de SSH-client mogelijk de waarschuwing weergegeven dat de authenticiteit van de host niet kan worden vastgesteld. Wanneer u wordt gevraagd de host te bevestigen, typt u __yes__ en drukt u vervolgens op __Enter__ om de host toe te voegen aan de lijst met vertrouwde servers van uw SSH-client.

3. Voer het wachtwoord voor de SSH-gebruiker in wanneer hierom wordt gevraagd.

Zodra er verbinding is gemaakt, ziet u informatie die er ongeveer als volgt uitziet:

```output
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Informatie over de Apache Zookeeper- en Broker-hosts ophalen

Als u met Kafka werkt, moet u de *Zookeeper*- en *Broker*-hosts kennen. Deze hosts worden gebruikt met de Kafka-API en veel van de hulpprogramma's die bij Kafka worden meegeleverd.

In deze sectie vraagt u de hostgegevens op uit de Apache Ambari REST API in het cluster.

1. Wanneer er een SSH-verbinding is gemaakt met het cluster, gebruikt u de volgende opdracht om het hulpprogramma `jq` te installeren. Dit hulpprogramma wordt gebruikt om JSON-documenten te parseren en is handig bij het ophalen van de hostgegevens:
   
    ```bash
    sudo apt -y install jq
    ```

2. Gebruik de volgende opdracht om een omgevingsvariabele in te stellen op de clusternaam:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Wanneer u daarom wordt gevraagd, typt u de naam van het Kafka-cluster.

3. Gebruik de onderstaande opdracht om een omgevingsvariabele in te stellen met hostinformatie van Zookeeper. Met de opdracht worden alle Zookeeper-hosts opgehaald, waarna alleen de eerste twee vermeldingen worden geretourneerd. De reden hiervoor is dat u een bepaalde mate van redundantie wilt voor het geval één host onbereikbaar is.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Geef desgevraagd het wachtwoord voor het account voor clusteraanmelding op (niet het SSH-account).

4. Gebruik de volgende opdracht om te controleren of de omgevingsvariabele juist is ingesteld:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Gebruik de volgende opdracht om een omgevingsvariabele in te stellen met brokerhostinformatie van Kafka:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Geef desgevraagd het wachtwoord voor het account voor clusteraanmelding op (niet het SSH-account).

6. Gebruik de volgende opdracht om te controleren of de omgevingsvariabele juist is ingesteld:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Met deze opdracht wordt informatie geretourneerd die lijkt op de volgende tekst:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka-onderwerpen beheren

Kafka slaat gegevensstromen op in zogenaamde *onderwerpen (topics)* . U kunt het hulpprogramma `kafka-topics.sh` gebruiken om onderwerpen te beheren.

* **Als u een onderwerp wilt maken**, gebruikt u de volgende opdracht in de SSH-verbinding:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht brengt u een verbinding met Zookeeper tot stand met behulp van de hostinformatie die is opgeslagen in `$KAFKAZKHOSTS`. Vervolgens wordt er een Kafka-onderwerp gemaakt met de naam **test**. 

    * Gegevens die zijn opgeslagen in dit onderwerp worden gepartitioneerd in acht partities.

    * Elke partitie wordt gerepliceerd naar drie werkknooppunten in het cluster.

        Als u het cluster hebt gemaakt in een Azure-regio met drie foutdomeinen, gebruikt u een replicatiefactor van drie. Gebruik anders een replicatiefactor van vier.
        
        In regio's met drie foutdomeinen zorgt een replicatiefactor van drie ervoor dat replica's worden verdeeld over de foutdomeinen. In regio's met twee foutdomeinen zorgt een replicatiefactor van vier ervoor dat replica's worden verdeeld over de domeinen.
        
        Raadpleeg het document [Beschikbaarheid van virtuele Linux-machines](../../virtual-machines/availability.md) voor informatie over het aantal foutdomeinen in een regio.

        Kafka kan niet overweg met Azure-foutdomeinen. Bij het maken van partitiereplica's voor onderwerpen worden replica's mogelijk niet goed gedistribueerd voor hoge beschikbaarheid.

        Gebruik het [partitieherverdelingsprogramma van Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) voor gegarandeerde hoge beschikbaarheid. Dit hulpprogramma moet vanuit een SSH-verbinding naar het hoofdknooppunt van het Kafka-cluster worden uitgevoerd.

        Om de hoogst mogelijke beschikbaarheid van uw Kafka-gegevens te waarborgen, moet u de partitiereplica's voor uw onderwerp opnieuw indelen wanneer:

        * U een nieuw onderwerp of nieuwe partitie maakt

        * U een cluster omhoog schaalt

* Gebruik de volgende opdracht om **een lijst met onderwerpen op te vragen**:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht worden de onderwerpen weergegeven die beschikbaar zijn in het Kafka-cluster.

* Gebruik de volgende opdracht om **een onderwerp te verwijderen**:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Met deze opdracht verwijdert u het onderwerp met de naam `topicname`.

    > [!WARNING]  
    > Als u het onderwerp `test` verwijdert dat u eerder hebt gemaakt, moet u het opnieuw maken. Het onderwerp wordt namelijk gebruikt in stappen verderop in dit document.

Gebruik de volgende opdracht voor meer informatie over de opdrachten die beschikbaar zijn met het hulpprogramma `kafka-topics.sh`:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Records maken en gebruiken

Kafka slaat *records* op in onderwerpen. Records worden geproduceerd door *producenten* en worden gebruikt door *consumenten*. Producenten en consumenten communiceren met de *Kafka-brokerservice*. Elk werkknooppunt in uw HDInsight-cluster is een Kafka-brokerhost.

Gebruik de volgende stappen om records op te slaan in het testonderwerp dat u eerder hebt gemaakt. Lees deze vervolgens met behulp van een verbruiker:

1. Als u records wilt wegschrijven naar het onderwerp, gebruikt u het hulpprogramma `kafka-console-producer.sh` vanuit de SSH-verbinding:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Na deze opdracht komt u aan bij een lege regel.

2. Typ een tekstbericht op de lege regel en druk op Enter. Voer op deze manier enkele berichten in en gebruik vervolgens **Ctrl+C** om terug te keren naar de normale prompt. Elke regel wordt als afzonderlijke record naar het Kafka-onderwerp verzonden.

3. Als u records wilt lezen uit het onderwerp, gebruikt u het hulpprogramma `kafka-console-consumer.sh` vanuit de SSH-verbinding:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Met deze opdracht haalt u de records van het onderwerp op en geeft u deze weer. Met `--from-beginning` wordt de consument gevraagd om bij het begin van de stream te beginnen, zodat alle records worden opgehaald.

    Vervang `--bootstrap-server $KAFKABROKERS` door `--zookeeper $KAFKAZKHOSTS` als u een oudere versie van Kafka gebruikt.

4. Gebruik __Ctrl+C__ om de consument te stoppen.

U kunt ook programmatisch producenten en consumenten maken. Zie het document [Producer and Consumer API van Apache Kafka met HDInsight](apache-kafka-producer-consumer-api.md) voor een voorbeeld van het gebruik van deze API.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, HDInsight en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

> [!WARNING]  
> De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt.
> 
> Door een Kafka in HDInsight-cluster te verwijderen, worden alle gegevens verwijderd die zijn opgeslagen in Kafka.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Apache Spark gebruiken met Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)