---
title: Service-eindpunt beleid configureren-Azure HDInsight
description: Meer informatie over het configureren van beleids regels voor service-eind punten voor uw virtuele netwerk met Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940622"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Beleids regels voor het configureren van virtuele netwerk services voor Azure HDInsight

Dit artikel bevat informatie over het implementeren van service-eindpunt beleidsregels voor virtuele netwerken met Azure HDInsight.

## <a name="background"></a>Achtergrond

Met Azure HDInsight kunt u clusters maken in uw eigen virtuele netwerk. Als u uitgaand verkeer van uw virtuele netwerk naar andere Azure-Services zoals opslag accounts wilt toestaan, kunt u [beleids regels voor service-eind punten](../virtual-network/virtual-network-service-endpoint-policies-overview.md)maken. Beleids regels voor service-eind punten die worden gemaakt via de Azure Portal, bieden u echter alleen de mogelijkheid om een beleid te maken voor één account, voor alle accounts in een abonnement of voor alle accounts in een resource groep.

Als beheerde service verzamelt Azure HDInsight echter gegevens en logboek bestanden van elk cluster in specifieke opslag accounts in elke regio. Om ervoor te zorgen dat deze gegevens HDInsight kunnen bereiken vanuit uw virtuele netwerk, is het nodig om service-eindpunt beleid te maken waarmee uitgaand verkeer naar specifieke gegevens verzamelings punten die worden beheerd door Azure HDInsight, wordt toegestaan.

## <a name="service-endpoint-policies-for-hdinsight"></a>Service-eindpunt beleid voor HDInsight

Deze beleids regels voor service-eind punten ondersteunen de volgende functionaliteit:

- Verzameling logboeken en telemetrie voor het maken van clusters, het uitvoeren van taken en platform bewerkingen, zoals schalen.
- Virtuele harde schijven (Vhd's) koppelen aan nieuwe cluster knooppunten voor het inrichten van software en bibliotheken in uw cluster.

Als er geen service-eindpunt beleid is gemaakt om deze gegevens stroom in te scha kelen, kan het maken van een cluster mislukken en kan Azure HDInsight geen ondersteuning bieden voor uw clusters.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Service-eindpunt beleid maken voor HDInsight

Zorg ervoor dat het juiste service-eindpunt beleid aan het virtuele netwerk is gekoppeld voordat u nieuwe clusters maakt. Anders kan het maken van het cluster mislukken of treedt er een fout op.

Gebruik het volgende proces om het benodigde service-eindpunt beleid te maken:

1. Bepaal de regio waar u uw HDInsight-cluster gaat maken.
1. Zoek deze regio in de [lijst met service-eindpunt beleids resources](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), die alle resource groepen voor HDInsight-beheer opslag accounts bevat.
1. Selecteer de lijst met resource groepen voor uw regio. Hieronder ziet u een voor beeld van de resources voor `Canada Central` :

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Voeg die lijst met resource groepen in het installatie script in dat is geschreven in azure CLI of Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Als u liever uw service-eindpunt beleid instelt met behulp van Power shell, gebruikt u het volgende code fragment.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
* [Virtueel netwerkapparaat configureren](./network-virtual-appliance.md)
