---
title: Clusters Azure HDInsight beheren met Behulp van Azure CLI
description: Meer informatie over het gebruik van de Azure CLI voor het beheren van Azure HDInsight clusters. Clustertypen zijn Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query en ML Services.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: 14b88700f3968e3bfdc788abb2fc9ce90634068e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770341"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Clusters Azure HDInsight beheren met Behulp van Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Meer informatie over het gebruik [van Azure CLI](/cli/azure/) voor het beheren Azure HDInsight clusters. De Azure-opdrachtregelinterface (CLI) is de platformoverschrijdende opdrachtregelervaring voor het beheren van Azure-resources.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI. Zie Azure CLI installeren voor stappen als u de Azure [CLI](/cli/azure/install-azure-cli) nog niet hebt geïnstalleerd.

* Een Apache Hadoop-cluster in HDInsight. Zie [Get Started with HDInsight on Linux (Aan de slag met HDInsight op Linux).](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **Probeer het** in de rechterbovenhoek van het codeblok. Anders voert u de onderstaande opdracht in:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Clusters weergeven

Gebruik [az hdinsight list om](/cli/azure/hdinsight#az_hdinsight_list) clusters weer te maken. Bewerk de onderstaande opdrachten door te `RESOURCE_GROUP_NAME` vervangen door de naam van uw resourcegroep en voer vervolgens de opdrachten in:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Cluster tonen

Gebruik [az hdinsight show om](/cli/azure/hdinsight#az_hdinsight_show) informatie weer te geven voor een opgegeven cluster. Bewerk de onderstaande opdracht door `RESOURCE_GROUP_NAME` en te vervangen door de relevante informatie en voer vervolgens de opdracht `CLUSTER_NAME` in:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik [az hdinsight delete om](/cli/azure/hdinsight#az_hdinsight_delete) een opgegeven cluster te verwijderen. Bewerk de onderstaande opdracht door `RESOURCE_GROUP_NAME` en te vervangen door de relevante informatie en voer vervolgens de opdracht `CLUSTER_NAME` in:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

U kunt een cluster ook verwijderen door de resourcegroep met het cluster te verwijderen. Houd er rekening mee dat hiermee alle resources in de groep worden verwijderd, inclusief het standaardopslagaccount.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Clusters schalen

Gebruik [az hdinsight resize om](/cli/azure/hdinsight#az_hdinsight_resize) de grootte van het opgegeven HDInsight-cluster aan te geven op de opgegeven grootte. Bewerk de onderstaande opdracht door `RESOURCE_GROUP_NAME` en te vervangen door de relevante `CLUSTER_NAME` informatie. Vervang `WORKERNODE_COUNT` door het gewenste aantal werkknooppunten voor uw cluster. Zie [HDInsight-clusters](./hdinsight-scaling-best-practices.md)schalen voor meer informatie over het schalen van clusters. Voer de opdracht in:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u verschillende beheertaken voor HDInsight-clusters uitvoert. Lees de volgende artikelen voor meer informatie:

* [Apache Hadoop-clusters in HDInsight beheren met behulp van de Azure Portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met behulp van Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Aan de slag met de Azure CLI](/cli/azure/get-started-with-azure-cli)
