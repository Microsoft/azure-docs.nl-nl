---
title: 'Quickstart: Een hybride cluster configureren met Azure Managed Instance voor Apache Cassandra'
description: In deze quickstart ziet u hoe u een hybride cluster configureert met Azure Managed Instance voor Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 9f3ad2a5d5b275ff611653855eff73bd36afda9f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379414"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Quickstart: Een hybride cluster configureren met Azure Managed Instance voor Apache Cassandra (preview)

Azure Managed Instance voor Apache Cassandra biedt geautomatiseerde implementatie- en schaalbewerkingen voor beheerde opensource Apache Cassandra-datacenters. Deze service helpt u bij het versnellen van hybride scenario's en het verminderen van doorlopend onderhoud.

> [!IMPORTANT]
> Azure Managed Instance voor Apache Cassandra is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze quickstart wordt gedemonstreerd hoe u de Azure CLI-opdrachten gebruikt om een hybride cluster te configureren. Als u bestaande datacenters hebt in een on-premises of zelf-hostende omgeving, kunt u Azure Managed Instance voor Apache Cassandra gebruiken om andere datacenters toe te voegen aan dat cluster en deze te onderhouden.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Voor dit artikel is azure CLI versie 2.12.1 of hoger vereist. Als u een Azure Cloud Shell, is de meest recente versie al geïnstalleerd.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) connectiviteit met uw zelf-hostende of on-premises omgeving. Zie het artikel Connect an on-premises network to Azure (Een on-premises netwerk verbinden met Azure) voor meer informatie over het verbinden van [on-premises omgevingen met Azure.](/azure/architecture/reference-architectures/hybrid-networking/)

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Een hybride cluster configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) en navigeer naar uw Virtual Network resource.

1. Open het **tabblad Subnetten** en maak een nieuw subnet. Zie het volgende artikel voor meer **informatie** over de velden in het [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) subnet toevoegen:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Voeg een nieuw subnet toe aan uw Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > Voor de implementatie van een azure managed instance voor Apache Cassandra is internettoegang vereist. De implementatie mislukt in omgevingen waarin internettoegang wordt beperkt. Zorg ervoor dat u de toegang binnen uw VNet niet blokkeert tot de volgende essentiële Azure-services die nodig zijn om beheerde Cassandra goed te laten werken:
    > - Azure Storage
    > - Azure KeyVault
    > - Microsoft Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Azure-beveiliging

1. Nu gaan we een aantal speciale machtigingen toepassen op het VNet en subnet dat cassandra Managed Instance nodig heeft, met behulp van Azure CLI. Gebruik de opdracht , en te `az role assignment create` vervangen door de juiste `<subscription ID>` `<resource group name>` `<VNet name>` waarden:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > De `assignee` waarden en in de vorige opdracht zijn respectievelijk vaste `role` service-principe en rol-id's.

1. Vervolgens configureren we resources voor ons hybride cluster. Omdat u al een cluster hebt, is de clusternaam hier alleen een logische resource om de naam van uw bestaande cluster te identificeren. Zorg ervoor dat u de naam van uw bestaande cluster gebruikt bij het definiëren van `clusterName` variabelen en in het volgende `clusterNameOverride` script. U hebt ook de seed-knooppunten, openbare clientcertificaten (als u een openbare/persoonlijke sleutel op uw cassandra-eindpunt hebt geconfigureerd) en de certificaatcertificaten van uw bestaande cluster nodig.

   > [!NOTE]
   > De waarde van de variabele die u hieronder oplevert, is precies hetzelfde als de waarde van die u `delegatedManagementSubnetId` hebt opgegeven in de `--scope` bovenstaande opdracht:

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
    > U moet weten waar uw bestaande openbare en/of certificaatcertificaten worden bewaard. Als u het niet zeker weet, moet u kunnen uitvoeren om `keytool -list -keystore <keystore-path> -rfc -storepass <password>` de certificaten af te drukken. 

1. Nadat de clusterresource is gemaakt, moet u de volgende opdracht uitvoeren om de installatiedetails van het cluster op te halen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. De vorige opdracht retourneert informatie over de omgeving van het beheerde exemplaar. U hebt de certificaatcertificaten nodig, zodat u ze kunt installeren op de knooppunten in uw bestaande datacenter. In de volgende schermopname ziet u de uitvoer van de vorige opdracht en de indeling van certificaten:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Haal de certificaatgegevens op uit het cluster." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Maak vervolgens een nieuw datacenter in het hybride cluster. Zorg ervoor dat u de waarden van de variabele vervangt door de details van uw cluster:

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

1. Nu het nieuwe datacenter is gemaakt, kunt u de opdracht datacenter weergeven uitvoeren om de details ervan weer te geven:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Met de vorige opdracht worden de seed-knooppunten van het nieuwe datacenter uitgevoerd. Voeg de seed-knooppunten van het nieuwe datacenter toe aan de configuratie van uw bestaande datacenter in het *bestand cassandra.yaml.* En installeer de certificaatcertificaten voor beheerde exemplaren die u eerder hebt verzameld:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Gegevens van datacenters op te halen." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Als u meer datacenters wilt toevoegen, kunt u de bovenstaande stappen herhalen, maar hebt u alleen de seed-knooppunten nodig. 

1. Gebruik ten slotte de volgende CQL-query om de replicatiestrategie in elke keyspace bij te werken om alle datacenters in het cluster op te nemen:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   U moet ook de wachtwoordtabellen bijwerken:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout wordt weergegeven bij het toepassen van machtigingen op uw Virtual Network, zoals Kan gebruiker of service-principal niet vinden in grafiekdatabase voor *'e5007d2c-4b13-4a74-9b6a-605d99f03501',* kunt u dezelfde machtiging handmatig toepassen vanuit de Azure Portal. Als u machtigingen wilt toepassen vanuit de portal, gaat u naar het deelvenster Toegangsbeheer **(IAM)** van uw bestaande virtuele netwerk en voegt u een roltoewijzing voor 'Azure Cosmos DB' toe aan de rol Netwerkbeheerder. Als er twee vermeldingen worden weergegeven wanneer u zoekt naar 'Azure Cosmos DB', voegt u beide vermeldingen toe, zoals wordt weergegeven in de volgende afbeelding: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Machtigingen toepassen" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> De Azure Cosmos DB roltoewijzing wordt alleen gebruikt voor implementatiedoeleinden. Azure Managed Instanced voor Apache Cassandra heeft geen back-Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit beheerde exemplaarcluster niet meer gaat gebruiken, verwijdert u het met de volgende stappen:

1. Selecteer resourcegroepen in Azure Portal menu aan **de linkerkant.**
1. Selecteer de resourcegroep die u eerder voor deze quickstart hebt gemaakt uit de lijst.
1. Selecteer resourcegroep verwijderen in **het** deelvenster Overzicht **van de resourcegroep.**
3. Selecteer in het volgende venster de naam van de resourcegroep die u wilt verwijderen en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een hybride cluster maakt met behulp van Azure CLI en Azure Managed Instance voor Apache Cassandra. U kunt nu aan de slag met het cluster.

> [!div class="nextstepaction"]
> [Azure Managed Instance voor Apache Cassandra-resources beheren met behulp van Azure CLI](manage-resources-cli.md)
