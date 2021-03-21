---
title: Een beheerd exemplaar van Azure beheren voor Apache Cassandra-resources met behulp van Azure CLI
description: Meer informatie over de algemene opdrachten voor het automatiseren van het beheer van uw door Azure beheerde exemplaar voor Apache Cassandra met behulp van Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: 3e44625d23a302c58ea065a4fc3ecec5605e60b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564521"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Een beheerd exemplaar van Azure beheren voor Apache Cassandra-resources met behulp van Azure CLI (preview)

In dit artikel worden algemene opdrachten beschreven voor het automatiseren van het beheer van uw door Azure beheerde exemplaar voor Apache Cassandra-clusters met behulp van Azure CLI.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Voor dit artikel is de Azure CLI-versie 2.17.1 of hoger vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.
>
> Het beheren van een beheerd exemplaar van Azure voor Apache Cassandra-resources kan niet worden gewijzigd omdat dit inbreuk maakt op de manier waarop Azure Resource Manager met resource-Uri's werkt.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Door Azure beheerde instantie voor Apache Cassandra-clusters

In de volgende secties wordt beschreven hoe u een beheerd exemplaar van Azure beheert voor Apache Cassandra-clusters, waaronder:

* [Een beheerd exemplaar cluster maken](#create-cluster)
* [Een beheerd exemplaar cluster verwijderen](#delete-cluster)
* [De cluster Details ophalen](#get-cluster-details)
* [De status van het cluster knooppunt ophalen](#get-cluster-status)
* [Clusters op resource groep weer geven](#list-clusters-resource-group)
* [Clusters weer geven op abonnements-ID](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Een beheerd exemplaar cluster maken

Een door Azure beheerd exemplaar maken voor Apache Cassandra-cluster met behulp van de opdracht [AZ Managed-Cassandra cluster create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Een beheerd exemplaar cluster verwijderen

Verwijder een cluster met behulp van de opdracht [AZ Managed-Cassandra cluster delete](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_delete) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>De cluster Details ophalen

Cluster Details ophalen met behulp van de opdracht [AZ Managed-Cassandra cluster show](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_show) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>De status van het cluster knooppunt ophalen

Cluster Details ophalen met behulp van de opdracht [AZ Managed-Cassandra cluster node-status](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_node_status) :

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>De clusters op resource groep weer geven

Clusters op resource groep weer geven met behulp van de opdracht [AZ Managed-Cassandra cluster List](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_list) :

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Clusters weer geven op abonnements-ID

Clusters op abonnements-ID weer geven met behulp van de opdracht [AZ Managed-Cassandra cluster List](/cli/azure/ext/cosmosdb-preview/managed-cassandra?view=azure-cli-latest&preserve-view=true) :

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Het beheerde exemplaar van data centers

In de volgende secties wordt beschreven hoe u een beheerd exemplaar van Azure beheert voor Apache Cassandra-data centers, met inbegrip van:

* [Een Data Center maken](#create-datacenter)
* [Een Data Center verwijderen](#delete-datacenter)
* [Details van Data Center ophalen](#get-datacenter-details)
* [Een Data Center bijwerken of schalen](#update-datacenter)
* [Data centers in een cluster ophalen](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Een Data Center maken

Maak een Data Center met behulp van de opdracht [AZ Managed-Cassandra Data Center Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Een Data Center verwijderen

Een Data Center verwijderen met behulp van de opdracht [AZ Managed-Cassandra Data Center delete](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_delete) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Details van Data Center ophalen

Details van Data Center ophalen met behulp van de opdracht [AZ Managed-Cassandra Data Center show](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_show) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Een Data Center bijwerken of schalen

Een Data Center bijwerken of schalen (op schaal wijzigen nodeCount waarde) met behulp van de opdracht [AZ Managed-Cassandra Data Center update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>De data centers in een cluster ophalen

Data centers in een cluster ophalen met behulp van de opdracht [AZ Managed-Cassandra Data Center List](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_list) :

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Volgende stappen

* [Een beheerd exemplaar cluster maken op basis van de Azure Portal](create-cluster-portal.md)
* [Een beheerd Apache Spark cluster implementeren met Azure Databricks](deploy-cluster-databricks.md)
