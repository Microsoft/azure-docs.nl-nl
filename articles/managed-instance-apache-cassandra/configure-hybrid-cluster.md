---
title: 'Quick Start: een hybride cluster met een beheerd exemplaar van Azure configureren voor Apache Cassandra'
description: Deze Quick Start laat zien hoe u een hybride cluster met een beheerd exemplaar van Azure kunt configureren voor Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: b022bff9db87c248881cd18cc21569aaef8f404a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562117"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Snelstartgids: een hybride cluster met een beheerd exemplaar van Azure configureren voor Apache Cassandra (preview)

Een door Azure beheerde instantie voor Apache Cassandra biedt geautomatiseerde implementatie-en schaal bewerkingen voor beheerde open source Apache Cassandra-data centers. Met deze service kunt u hybride scenario's versnellen en doorlopend onderhoud verminderen.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze Quick start ziet u hoe u de Azure CLI-opdrachten gebruikt om een hybride cluster te configureren. Als u bestaande data centers hebt in een on-premises of zelf-hostende omgeving, kunt u met Azure Managed instance voor Apache Cassandra andere data centers aan het cluster toevoegen en deze onderhouden.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Voor dit artikel is de Azure CLI-versie 2.12.1 of hoger vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) met connectiviteit voor uw zelf-hostende of on-premises omgeving. Zie het artikel [een on-premises netwerk verbinden met Azure](/azure/architecture/reference-architectures/hybrid-networking/) voor meer informatie over het verbinden van on-premises omgevingen met Azure.

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Een hybride cluster configureren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar uw Virtual Network-resource.

1. Open het tabblad **subnetten** en maak een nieuw subnet. Zie het [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) -artikel voor meer informatie over de velden in het formulier **subnet toevoegen** :

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Voeg een nieuw subnet aan uw Virtual Network toe." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Nu gaan we enkele speciale machtigingen Toep assen op het VNet en het subnet waarvoor het beheerde exemplaar van Cassandra vereist is, met behulp van Azure CLI. Gebruik de `az role assignment create` opdracht, vervang `<subscription ID>` , `<resource group name>` , `<VNet name>` en `<subnet name>` met de juiste waarden:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > De `assignee` `role` waarden en in de vorige opdracht zijn respectievelijk een vast Service principe en rol-id's.

1. Daarna zullen we resources configureren voor het hybride cluster. Omdat u al een cluster hebt, is de cluster naam hier alleen een logische bron om de naam van uw bestaande cluster te identificeren. Zorg ervoor dat u de naam van uw bestaande cluster gebruikt bij `clusterName` het definiëren van en `clusterNameOverride` variabelen in het volgende script. U hebt ook de Seed-knoop punten, open bare client certificaten (als u een open bare/persoonlijke sleutel op uw Cassandra-eind punt hebt geconfigureerd) en Gossip-certificaten van uw bestaande cluster nodig.

   > [!NOTE]
   > De waarde van de `delegatedManagementSubnetId` variabele die u hieronder opgeeft, is precies hetzelfde als de waarde `--scope` die u hebt opgegeven in de bovenstaande opdracht:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > U moet weten waar uw bestaande open bare en/of Gossip-certificaten worden bewaard. Als u niet zeker weet, kunt u uitvoeren `keytool -list -keystore <keystore-path> -rfc -storepass <password>` om de certificaten af te drukken. 

1. Nadat de cluster bron is gemaakt, voert u de volgende opdracht uit om de details van de Cluster installatie op te halen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. De vorige opdracht retourneert informatie over de beheerde-exemplaar omgeving. U hebt de Gossip-certificaten nodig zodat u ze op de knoop punten in uw bestaande Data Center kunt installeren. In de volgende scherm afbeelding ziet u de uitvoer van de vorige opdracht en de indeling van certificaten:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Haal de certificaat details op uit het cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Maak vervolgens een nieuw Data Center in het hybride cluster. Zorg ervoor dat u de variabele waarden vervangt door de cluster Details:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Nu het nieuwe Data Center is gemaakt, voert u de opdracht Data Center weer geven uit om de details ervan weer te geven:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Met de vorige opdracht worden de Seed-knoop punten van het nieuwe Data Center uitgevoerd. Voeg de Seed-knoop punten van het nieuwe Data Center toe aan de configuratie van uw bestaande Data Center in het bestand *Cassandra. yaml* . En installeer de Gossip-certificaten voor beheerde exemplaren die u eerder hebt verzameld:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Details van Data Center ophalen." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Als u meer data centers wilt toevoegen, kunt u de bovenstaande stappen herhalen, maar u hebt alleen de Seed-knoop punten nodig. 

1. Gebruik tot slot de volgende CQL-query om de replicatie strategie bij te werken in elke spatie om alle data centers in het cluster op te nemen:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   U moet ook de wachtwoord tabellen bijwerken:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout optreedt bij het Toep assen van machtigingen voor uw Virtual Network, zoals het vinden van de *gebruiker of Service-Principal in Graph Data Base voor e5007d2c-4b13-4a74-9B6A-605d99f03501*, kunt u dezelfde machtiging hand matig Toep assen vanuit de Azure Portal. Als u machtigingen wilt Toep assen vanuit de portal, gaat u naar het deel venster **toegangs beheer (IAM)** van uw bestaande virtuele netwerk en voegt u een roltoewijzing voor ' Azure Cosmos db ' toe aan de rol ' netwerk beheerder '. Als er twee vermeldingen worden weer gegeven wanneer u zoekt naar ' Azure Cosmos DB ', voegt u beide vermeldingen toe, zoals wordt weer gegeven in de volgende afbeelding: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Machtigingen Toep assen" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> De roltoewijzing Azure Cosmos DB wordt alleen voor implementatie doeleinden gebruikt. Door Azure beheerde instanties voor Apache Cassandra hebben geen back-end-afhankelijkheden op Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze beheerde-exemplaar cluster niet meer wilt gebruiken, verwijdert u deze met de volgende stappen:

1. Selecteer **resource groepen** in het menu aan de linkerkant van Azure Portal.
1. Selecteer de resourcegroep die u eerder voor deze quickstart hebt gemaakt uit de lijst.
1. Selecteer **resource groep verwijderen** in het deel venster **overzicht** van de resource groep.
3. Selecteer in het volgende venster de naam van de resourcegroep die u wilt verwijderen en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een hybride cluster maakt met behulp van Azure CLI en Azure Managed instance voor Apache Cassandra. U kunt nu aan de slag gaan met het cluster.

> [!div class="nextstepaction"]
> [Een beheerd exemplaar van Azure beheren voor Apache Cassandra-resources met behulp van Azure CLI](manage-resources-cli.md)
