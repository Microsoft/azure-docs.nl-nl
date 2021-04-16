---
title: 'Quickstart: Een ExpressRoute-circuit maken met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon)'
description: In deze quickstart kunt u zien hoe u een ExpressRoute-circuit maakt met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3dc1d5f5ec3dfb004468deb2bec80927c7ec189d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529880"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Quickstart: Een ExpressRoute-circuit met persoonlijke peering maken met behulp van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM) gebruikt om een ExpressRoute-circuit met persoonlijke peering te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

In deze quickstart maakt u een ExpressRoute-circuit met *Equinix* als serviceprovider. Het circuit maakt gebruik van een *Premium SKU*, met een bandbreedte van *50 Mbps* en de peeringlocatie *Washington DC-* . Persoonlijke peering wordt ingeschakeld met een primair en secundair subnet van *192.168.10.16/30* en *192.168.10.20/30*. Er wordt ook een virtueel netwerk gemaakt met een *High Performance ExpressRoute-gateway*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (wordt gebruikt voor het inschakelen van persoonlijke peering voor het circuit)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (netwerkbeveiligingsgroep wordt toegepast op de subnetten in het virtuele netwerk)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (het openbare IP-adres wordt gebruikt door de ExpressRoute-gateway)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (ExpressRoute-gateway wordt gebruikt om VNet aan het circuit te koppelen)

Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular) als u meer sjablonen wilt vinden die gerelateerd zijn aan ExpressRoute.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Wacht totdat u de prompt van de console ziet.

1. Selecteer **Kopiëren** in het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het shell-consoledeelvenster en selecteer **Plakken**.

1. Voer de waarden in.

    De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

    Het duurt ongeveer 20 minuten om de sjabloon te implementeren. Wanneer voltooid is de uitvoer vergelijkbaar met:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Uitvoer van ExpressRoute Resource Manager-sjabloon PowerShell-implementatie":::

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. De resourcegroep moet de volgende resources bevatten die hier worden weergegeven:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Resourcegroep voor implementatie van ExpressRoute":::

1. Selecteer het ExpressRoute-circuit **er-ck01** om te controleren of de status van het circuit **Ingeschakeld** is, de status van de provider **Niet ingericht** is en de status voor persoonlijke peering **Ingericht** is.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute-implementatiecircuit":::

> [!NOTE]
> U moet de provider aanroepen om het inrichtingsproces te voltooien voordat u het virtuele netwerk kunt koppelen aan het circuit.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met het ExpressRoute-circuit hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u het ExpressRoute-circuit en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* ExpressRoute-circuit
* Virtual Network
* VPN Gateway
* Openbare IP
* netwerkbeveiligingsgroepen

Ga door naar de zelfstudies over ExpressRoute voor meer informatie over het koppelen van een virtueel netwerk aan een circuit.

> [!div class="nextstepaction"]
> [Zelfstudies voor ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
