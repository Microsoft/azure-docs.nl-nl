---
title: 'Quickstart: CLI gebruiken om een beheerd Azure-exemplaar voor een Apache Cassandra-cluster te maken'
description: Gebruik deze quickstart om een Azure Managed Instance voor Apache Cassandra-cluster te maken met behulp van Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 53fe53e1406bfcde1f2d8c7b2a1ce8369303426f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379363"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Quickstart: Een Azure Managed Instance voor Apache Cassandra-cluster maken met behulp van Azure CLI (preview)

Azure Managed Instance voor Apache Cassandra biedt geautomatiseerde implementatie- en schaalbewerkingen voor beheerde opensource Apache Cassandra-datacenters. Deze service helpt u bij het versnellen van hybride scenario's en het verminderen van doorlopend onderhoud.

> [!IMPORTANT]
> Azure Managed Instance voor Apache Cassandra is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze quickstart wordt gedemonstreerd hoe u de Azure CLI-opdrachten gebruikt om een cluster te maken met Azure Managed Instance voor Apache Cassandra. U ziet ook hoe u een datacenter maakt en knooppunten omhoog of omlaag schaalt binnen het datacenter.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) connectiviteit met uw zelf-hostende of on-premises omgeving. Zie het artikel Connect an on-premises network to Azure (Een on-premises netwerk verbinden met Azure) voor meer informatie over het verbinden van [on-premises omgevingen met Azure.](/azure/architecture/reference-architectures/hybrid-networking/)

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Voor dit artikel is azure CLI versie 2.17.1 of hoger vereist. Als u een Azure Cloud Shell, is de meest recente versie al geïnstalleerd.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Een beheerd exemplaarcluster maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/)

1. Stel uw abonnements-id in Azure CLI in:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Maak vervolgens een Virtual Network met een toegewezen subnet in uw resourcegroep:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```
    > [!NOTE]
    > Voor de implementatie van een azure managed instance voor Apache Cassandra is internettoegang vereist. De implementatie mislukt in omgevingen waar internettoegang is beperkt. Zorg ervoor dat u de toegang binnen uw VNet niet blokkeert tot de volgende essentiële Azure-services die nodig zijn om beheerde Cassandra goed te laten werken:
    > - Azure Storage
    > - Azure KeyVault
    > - Microsoft Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Azure-beveiliging

1. Pas enkele speciale machtigingen toe op de Virtual Network, die vereist zijn voor het beheerde exemplaar. Gebruik de opdracht , en te `az role assignment create` vervangen door de juiste `<subscription ID>` `<resource group name>` `<VNet name>` waarden:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > De `assignee` waarden en in de vorige opdracht zijn vaste waarden. Voer deze waarden exact in `role` zoals vermeld in de opdracht . Als u dit niet doet, leidt dit tot fouten bij het maken van het cluster. Als er fouten optreden bij het uitvoeren van deze opdracht, hebt u mogelijk geen machtigingen om deze uit te voeren. Neem contact op met uw beheerder voor machtigingen.

1. Maak vervolgens het cluster in uw zojuist gemaakte Virtual Network met behulp van [de opdracht az managed-cassandra cluster create.](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) Voer de volgende opdracht uit als de waarde van `delegatedManagementSubnetId` de variabele:

   > [!NOTE]
   > De waarde van de variabele die u hieronder oplevert, is precies hetzelfde als de waarde van die u hebt opgegeven `delegatedManagementSubnetId` `--scope` in de bovenstaande opdracht:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Maak ten slotte een datacenter voor het cluster met drie knooppunten met behulp van [de opdracht az managed-cassandra datacenter create:](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create)

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Zodra het datacenter is gemaakt, kunt u de knooppunten in het datacenter omhoog of omlaag schalen door de opdracht [az managed-cassandra datacenter update uit te](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) voeren. Wijzig de waarde van `node-count` parameter in de gewenste waarde:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Verbinding maken met uw cluster

Azure Managed Instance voor Apache Cassandra maakt geen knooppunten met openbare IP-adressen. Als u verbinding wilt maken met uw zojuist gemaakte Cassandra-cluster, moet u een andere resource in het virtuele netwerk maken. Deze resource kan een toepassing zijn of een virtuele machine met het opensource-queryhulpprogramma [CQLSH van](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) Apache geïnstalleerd. U kunt een Resource Manager [gebruiken om](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) een virtuele Ubuntu-machine te implementeren. Nadat deze is geïmplementeerd, gebruikt u SSH om verbinding te maken met de computer en installeert u CQLSH, zoals wordt weergegeven in de volgende opdrachten:

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

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout wordt weergegeven bij het toepassen van machtigingen op uw Virtual Network, zoals Kan gebruiker of service-principal niet vinden in grafiekdatabase voor *'e5007d2c-4b13-4a74-9b6a-605d99f03501',* kunt u dezelfde machtiging handmatig toepassen vanuit de Azure Portal. Als u machtigingen wilt toepassen vanuit de portal, gaat u naar het deelvenster Toegangsbeheer **(IAM)** van uw bestaande virtuele netwerk en voegt u een roltoewijzing voor 'Azure Cosmos DB' toe aan de rol Netwerkbeheerder. Als er twee vermeldingen worden weergegeven wanneer u zoekt naar 'Azure Cosmos DB', voegt u beide vermeldingen toe, zoals wordt weergegeven in de volgende afbeelding: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Machtigingen toepassen" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> De Azure Cosmos DB roltoewijzing wordt alleen gebruikt voor implementatiedoeleinden. Azure Managed Instanced voor Apache Cassandra heeft geen back-Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht gebruiken om de resourcegroep, het beheerde exemplaar en alle gerelateerde resources te verwijderen wanneer u deze `az group delete` niet meer nodig hebt:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Managed Instance voor Apache Cassandra-cluster maakt met behulp van Azure CLI. U kunt nu aan de slag met het cluster:

> [!div class="nextstepaction"]
> [Een beheerd Apache Spark implementeren met Azure Databricks](deploy-cluster-databricks.md)