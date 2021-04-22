---
title: Azure Managed Instance voor Apache Cassandra-resources beheren met behulp van Azure CLI
description: Meer informatie over de algemene opdrachten voor het automatiseren van het beheer van uw Azure Managed Instance voor Apache Cassandra met behulp van Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875445"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Azure Managed Instance voor Apache Cassandra-resources beheren met behulp van Azure CLI (preview)

In dit artikel worden algemene opdrachten beschreven voor het automatiseren van het beheer van uw Azure Managed Instance voor Apache Cassandra-clusters met behulp van Azure CLI.

> [!IMPORTANT]
> Azure Managed Instance voor Apache Cassandra is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Voor dit artikel is azure CLI versie 2.17.1 of hoger vereist. Als u een Azure Cloud Shell, is de meest recente versie al geïnstalleerd.
>
> De naam van Azure Managed Instance voor Apache Cassandra-resources kan niet worden gewijzigd, omdat dit in strijd is met hoe Azure Resource Manager met resource-URI's werkt.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Azure Managed Instance voor Apache Cassandra-clusters

In de volgende secties wordt gedemonstreerd hoe u Azure Managed Instance voor Apache Cassandra-clusters beheert, waaronder:

* [Een beheerd exemplaarcluster maken](#create-cluster)
* [Een beheerd exemplaarcluster verwijderen](#delete-cluster)
* [De clusterdetails op halen](#get-cluster-details)
* [De status van het clusterknooppunt op te halen](#get-cluster-status)
* [Clusters per resourcegroep opslijst](#list-clusters-resource-group)
* [Clusters op abonnements-id in een lijst zetten](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Een beheerd exemplaarcluster maken

Maak een Azure Managed Instance voor een Apache Cassandra-cluster met behulp van [de opdracht az managed-cassandra cluster create:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create)

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

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Een beheerd exemplaarcluster verwijderen

Verwijder een cluster met de opdracht [az managed-cassandra cluster delete:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>De clusterdetails op halen

Haal clusterdetails op met [behulp van de opdracht az managed-cassandra cluster show:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>De status van het clusterknooppunt op te halen

Haal clusterdetails op met [behulp van de opdracht az managed-cassandra cluster node-status:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status)

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>De clusters per resourcegroep opn genoemd

Gebruik de opdracht az [managed-cassandra cluster list](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list) om clusters per resourcegroep weer te geven:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Clusters op abonnements-id in een lijst zetten

Vermeld clusters op abonnements-id met behulp van [de opdracht az managed-cassandra cluster list:](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true)

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>De datacentra van het beheerde exemplaar

In de volgende secties wordt gedemonstreerd hoe u Azure Managed Instance voor Apache Cassandra-datacenters beheert, waaronder:

* [Een datacenter maken](#create-datacenter)
* [Een datacenter verwijderen](#delete-datacenter)
* [Datacentergegevens op halen](#get-datacenter-details)
* [Een datacenter bijwerken of schalen](#update-datacenter)
* [Datacenters in een cluster opsommen](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Een datacenter maken

Maak een datacenter met behulp van [de opdracht az managed-cassandra datacenter create:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create)

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

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Een datacenter verwijderen

Verwijder een datacenter met de opdracht [az managed-cassandra datacenter delete:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Datacentergegevens op halen

Haal gegevens van het datacenter op met [behulp van de opdracht az managed-cassandra datacenter show:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Een datacenter bijwerken of schalen

Werk een datacenter bij of schaal deze (om de waarde van nodeCount te wijzigen) met behulp van [de opdracht az managed-cassandra datacenter update:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update)

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

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>De datacenters in een cluster opsommen

Haal datacenters op in een cluster met behulp van [de opdracht az managed-cassandra datacenter list:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Volgende stappen

* [Een beheerd exemplaarcluster maken op Azure Portal](create-cluster-portal.md)
* [Een beheerd Apache Spark cluster implementeren met Azure Databricks](deploy-cluster-databricks.md)
