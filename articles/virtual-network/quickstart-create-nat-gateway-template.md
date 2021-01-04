---
title: 'Een NAT-gateway maken: Resource Manager-sjabloon'
titleSuffix: Azure Virtual Network NAT
description: In deze quickstart leest u hoe u een NAT-gateway kunt maken met de ARM-sjabloon (Azure Resource Manager-sjabloon).
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 68e08b0f029e6297beee85135b4af1e4575d5470
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703786"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Quickstart: NAT-gateway maken - ARM-sjabloon

Aan de slag met Virtual Network NAT met behulp van een ARM-sjabloon (Azure Resource Manager-sjabloon). Met deze sjabloon implementeert u een virtueel netwerk, een NAT-gatewayresource en een virtuele Ubuntu-machine. De virtuele Ubuntu-machine wordt geïmplementeerd op een subnet dat is gekoppeld aan de NAT-gatewayresource.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Deze sjabloon is geconfigureerd voor het maken van een:

* Virtueel netwerk
* NAT-gatewayresource
* Virtuele Ubuntu-machine

De virtuele Ubuntu-machine wordt geïmplementeerd op een subnet dat is gekoppeld aan de NAT-gatewayresource.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Er worden negen Azure-resources gedefinieerd in de sjabloon:

* **[Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)** : Hiermee maakt u een netwerkbeveiligingsgroep.
* **[Microsoft.Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : Hiermee maakt u een beveiligingsregel.
* **[Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)** : Hiermee maakt u een openbaar IP-adres.
* **[Microsoft.Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)** : Hiermee maakt u een openbaar IP-voorvoegsel.
* **[Microsoft.Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)** : Hiermee maakt u een virtuele machine.
* **[Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)** : Hiermee maakt u een virtueel netwerk.
* **[Microsoft.Network/natGateways](/azure/templates/microsoft.network/natgateways)** : Hiermee maakt u een NAT-gatewayresource.
* **[Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)** : Hiermee maakt u een virtueel subnet.
* **[Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)** : Hiermee maakt u een netwerkinterface.

## <a name="deploy-the-template"></a>De sjabloon implementeren

**Azure-CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure-portal**

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De standaardnaam van de resourcegroep is **myResourceGroupNAT**

1. Controleer of de volgende resources zijn gemaakt in de resourcegroep:

    ![Resourcegroep van Virtual Network NAT](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Resources opschonen

**Azure-CLI**

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure-portal**

U kunt de resourcegroep, de NAT-gateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt. Selecteer de resourcegroep **myResourceGroupNAT** met de NAT-gateway en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* NAT-gatewayresource
* Virtueel netwerk
* Virtuele Ubuntu-machine

De virtuele machine wordt geïmplementeerd op een virtueel netwerk dat is gekoppeld aan de NAT-gateway.

Als u meer wilt weten over Virtual Network NAT en Azure Resource Manager vindt u meer informatie in de onderstaande artikelen.

* Een [overzicht van Virtual Network NAT](nat-overview.md) lezen
* Meer informatie over [NAT Gateway-resource](nat-gateway-resource.md)
* Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
