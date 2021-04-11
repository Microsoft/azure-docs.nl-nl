---
title: 'Quick Start: een Azure-route server maken met behulp van een sjabloon voor Azure Resource Manager (ARM-sjabloon)'
description: In deze Quick start ziet u hoe u een Azure route server maakt met behulp van Azure Resource Manager sjabloon (ARM-sjabloon).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 6f56b9fb1f6a1f5a1fe0811617fb20412c52fd72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106452193"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Quick Start: een Azure-route server maken met een ARM-sjabloon

In deze Quick Start wordt beschreven hoe u een Azure Resource Manager sjabloon (ARM-sjabloon) kunt gebruiken voor het implementeren van een Azure route server in een nieuw of bestaand virtueel netwerk.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-route-server).

In deze Quick Start implementeert u een Azure-route server in een nieuw of bestaand virtueel netwerk. Er wordt een toegewijd subnet `RouteServerSubnet` met de naam gemaakt om de route server te hosten. De route server wordt ook geconfigureerd met het peer-ASN en het IP-adres van de peer om een BGP-peering tot stand te brengen.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Micro soft. Network/virtualNetworks/subnetten**](/azure/templates/microsoft.network/virtualNetworks/subnets) (twee subnetten, een met de naam `routeserversubnet` )
* [**Micro soft. Network/virtualHubs**](/azure.templates/microsoft.network/virtualhubs) (route Server-implementatie)
* [**Micro soft. Network/virtualHubs/ipConfigurations**](/azure.templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Micro soft. Network/virtualHubs/bgpConnections**](/azure.templates/microsoft.network/virtualhubs/bgpConnections) (peer-ASN en peer-IP-configuratie)


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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Uitvoer van route Server Resource Manager-sjabloon Power shell-implementatie.":::

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. De resource groep mag alleen het virtuele netwerk bevatten:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="De resource groep voor de route Server implementatie met het virtuele netwerk.":::

1. Ga naar https://aka.ms/routeserver.

1. Selecteer de route server met de naam **routeserver** om te controleren of de implementatie is geslaagd.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Scherm afbeelding van de pagina overzicht van route server.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met de route server niet langer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u de route server en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* Route server
* Virtual Network
* Subnet

Nadat u de Azure route server hebt gemaakt, gaat u verder met meer informatie over hoe Azure route server communiceert met ExpressRoute en VPN-gateways: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute-en Azure VPN-ondersteuning](expressroute-vpn-support.md)
