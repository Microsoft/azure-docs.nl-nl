---
title: Quick Start-CLI gebruiken om een door Azure beheerd exemplaar te maken voor Apache Cassandra-cluster
description: Gebruik deze Quick Start om een Azure Managed instance voor Apache Cassandra-cluster te maken met behulp van Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 6de2e0f1744b333a830fbe500e2df51e7eaca62d
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419069"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Quick Start: een door Azure beheerd exemplaar maken voor Apache Cassandra-cluster met behulp van Azure CLI (preview)

Een door Azure beheerde instantie voor Apache Cassandra biedt geautomatiseerde implementatie-en schaal bewerkingen voor beheerde open source Apache Cassandra-data centers. Met deze service kunt u hybride scenario's versnellen en doorlopend onderhoud verminderen.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze Quick start ziet u hoe u de Azure CLI-opdrachten gebruikt om een cluster te maken met een door Azure beheerd exemplaar voor Apache Cassandra. Ook wordt er een Data Center gemaakt en worden de knoop punten binnen het Data Center omhoog of omlaag geschaald.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) met connectiviteit voor uw zelf-hostende of on-premises omgeving. Zie het artikel [een on-premises netwerk verbinden met Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) voor meer informatie over het verbinden van on-premises omgevingen met Azure.

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Voor dit artikel is de Azure CLI-versie 2.12.1 of hoger vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Een beheerd exemplaar cluster maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/)

1. Uw abonnements-ID instellen in azure CLI:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Maak vervolgens een Virtual Network met een toegewezen subnet in uw resource groep:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Pas enkele speciale machtigingen toe op de Virtual Network en het subnet, die vereist zijn voor het beheerde exemplaar. Gebruik de `az role assignment create` opdracht, vervang `<subscription ID>` , `<resource group name>` , `<VNet name>` en `<subnet name>` met de juiste waarden:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > De `assignee` `role` waarden en in de vorige opdracht zijn vaste waarden. Voer deze waarden precies zoals vermeld in de opdracht in. Als u dit niet doet, leidt dit tot fouten bij het maken van het cluster. Als er fouten optreden tijdens het uitvoeren van deze opdracht, bent u mogelijk niet gemachtigd om deze uit te voeren. Neem contact op met uw beheerder voor machtigingen.

1. Maak vervolgens het cluster in uw nieuw gemaakte Virtual Network. Voer de volgende opdracht uit en zorg ervoor dat u de `Resource ID` waarde die in de vorige opdracht is opgehaald als de waarde van `delegatedManagementSubnetId` Variable gebruikt:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Tot slot maakt u een Data Center voor het cluster met drie knoop punten:

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. Als u het Data Center hebt gemaakt, kunt u de volgende opdracht uitvoeren als u omhoog wilt schalen of de knoop punten in het Data Center omlaag wilt schalen. Wijzig de waarde van `node-count` para meter in de gewenste waarde:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Verbinding maken met uw cluster

Met Azure Managed instance voor Apache Cassandra worden geen knoop punten met open bare IP-adressen gemaakt. Als u verbinding wilt maken met het zojuist gemaakte Cassandra-cluster, moet u een andere resource in het virtuele netwerk. Deze resource kan een toepassing zijn, of een virtuele machine met open source query tool [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) geïnstalleerd. U kunt een [Resource Manager-sjabloon](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) gebruiken om een virtuele Ubuntu-machine te implementeren. Nadat de implementatie is geïmplementeerd, gebruikt u SSH om verbinding te maken met de computer en installeert u CQLSH, zoals wordt weer gegeven in de volgende opdrachten:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de `az group delete` opdracht gebruiken om de resource groep, het beheerde exemplaar en alle gerelateerde resources te verwijderen:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een door Azure beheerde instantie voor Apache Cassandra-cluster maakt met behulp van Azure CLI. U kunt nu aan de slag gaan met het cluster:

> [!div class="nextstepaction"]
> [Een beheerd Apache Spark cluster implementeren met Azure Databricks](deploy-cluster-databricks.md)
