---
title: Apache Hadoop clusters maken met behulp van Azure CLI-Azure HDInsight
description: Meer informatie over het maken van Azure HDInsight-clusters met behulp van de platformoverschrijdende Azure CLI.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9028d85346611341afec0d0598f27a77e4f37fdf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715493"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-clusters maken met behulp van Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Met de stappen in dit document wordt uitgelegd hoe u een HDInsight 3,6-cluster maakt met behulp van de Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Een cluster maken

1. Aanmelden bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **Probeer het** in de rechterbovenhoek van het codeblok. Anders voert u de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Stel omgevingsvariabelen in. Het gebruik van variabelen in dit artikel is gebaseerd op bash. Er zijn kleine variaties nodig voor andere omgevingen. Zie [AZ-hdinsight-Create](/cli/azure/hdinsight#az-hdinsight-create) voor een volledige lijst met mogelijke para meters voor het maken van een cluster.

    |Parameter | Beschrijving |
    |---|---|
    |`--workernode-count`| Het aantal worker-knoop punten in het cluster. In dit artikel wordt de variabele gebruikt `clusterSizeInNodes` als de waarde die wordt door gegeven aan `--workernode-count` . |
    |`--version`| De versie van het HDInsight-cluster. In dit artikel wordt de variabele gebruikt `clusterVersion` als de waarde die wordt door gegeven aan `--version` . Zie ook: [ondersteunde HDInsight-versies](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Type HDInsight-cluster, zoals Hadoop, interactivehive, hbase, Kafka, Storm, Spark, Rserver, mlservices.  In dit artikel wordt de variabele gebruikt `clusterType` als de waarde die wordt door gegeven aan `--type` . Zie ook: [cluster typen en configuratie](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|De versies van verschillende Hadoop-onderdelen, in versies die zijn gescheiden in de indeling ' onderdeel = versie '. In dit artikel wordt de variabele gebruikt `componentVersion` als de waarde die wordt door gegeven aan `--component-version` . Zie ook: [Hadoop-onderdelen](./hdinsight-component-versioning.md).|

    Vervang `RESOURCEGROUPNAME` ,,, `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` en `PASSWORD` door de gewenste waarden. Wijzig de waarden voor de andere variabelen naar wens. Voer vervolgens de CLI-opdrachten in.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Maak de resourcegroep](/cli/azure/group#az-group-create) door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Gebruik de opdracht voor een lijst met geldige locaties `az account list-locations` en gebruik vervolgens een van de locaties van de `name` waarde.

4. [Maak een Azure Storage-account](/cli/azure/storage/account#az-storage-account-create) door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Pak de primaire sleutel uit het Azure Storage-account](/cli/azure/storage/account/keys#az-storage-account-keys-list) in en sla deze op in een variabele door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Maak een Azure Storage-container](/cli/azure/storage/container#az-storage-container-create) door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Maak het HDInsight-cluster](/cli/azure/hdinsight#az-hdinsight-create) door de volgende opdracht in te voeren:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight-clusters worden geleverd in verschillende typen, die overeenkomen met de werk belasting of technologie waarvoor het cluster is afgestemd. Er is geen ondersteunde methode voor het maken van een cluster dat meerdere typen combineert, zoals Storm en HBase op één cluster.

    Het kan enkele minuten duren voordat het cluster is gemaakt. Meestal ongeveer 15.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Voer alle of enkele van de volgende opdrachten in om resources te verwijderen:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

Nu u een HDInsight-cluster hebt gemaakt met behulp van de Azure CLI, gebruikt u de volgende informatie om te leren werken met uw cluster:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Java-topologieën ontwikkelen voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-onderdelen gebruiken in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en bewaken met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
