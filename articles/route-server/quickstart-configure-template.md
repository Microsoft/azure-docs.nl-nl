---
title: 'Quickstart: Een Azure Route-server maken met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon)'
description: In deze quickstart ziet u hoe u een Azure Route-server maakt met behulp van Azure Resource Manager-sjabloon (ARM-sjabloon).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388037"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Quickstart: Een Azure Route-server maken met behulp van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een AZURE RESOURCE MANAGER-sjabloon (ARM-sjabloon) gebruikt om een Azure Route-server te implementeren in een nieuw of bestaand virtueel netwerk.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-route-server).

In deze quickstart implementeert u een Azure Route-server in een nieuw of bestaand virtueel netwerk. Er wordt een toegewezen subnet `RouteServerSubnet` met de naam gemaakt om de routeserver te hosten. De routeserver wordt ook geconfigureerd met de Peer ASN en peer-IP om een BGP-peering tot stand te stellen.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnetten**](/azure/templates/microsoft.network/virtualNetworks/subnets) (twee subnetten, één met de `routeserversubnet` naam )
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (routeserverimplementatie)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (Peer ASN en Peer IP-configuratie)


Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular) als u meer sjablonen wilt vinden die gerelateerd zijn aan ExpressRoute.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Uitvoer van de PowerShell Resource Manager sjabloon voor powershell-implementatie.":::

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. De resourcegroep mag alleen het virtuele netwerk bevatten:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Route Server implementatie resourcegroep met virtueel netwerk.":::

1. Ga naar https://aka.ms/routeserver.

1. Selecteer de routeserver met de **naam routeserver** om te controleren of de implementatie is geslaagd.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Schermopname van de overzichtspagina van Route Server.":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de routeserver hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de routeserver en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* Routeserver
* Virtual Network
* Subnet

Nadat u de Azure Route-server hebt maken, gaat u verder met informatie over hoe Azure Route Server communiceert met ExpressRoute en VPN Gateways: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute en Azure VPN-ondersteuning](expressroute-vpn-support.md)
