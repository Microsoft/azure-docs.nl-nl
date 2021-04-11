---
title: 'Quickstart: Een openbare load balancer maken - Azure-sjabloon'
titleSuffix: Azure Load Balancer
description: In deze quickstart leest u hoe u een load balancer kunt maken met een Azure Resource Manager-sjabloon.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 7067d7d76815103541fa4654da2bf977ba03a4ef
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056246"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Quickstart: Een openbare load balancer maken om taken van VM's te verdelen met behulp van een ARM-sjabloon

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines (VM's). 

In deze quickstart wordt getoond hoe u taken van VM's kunt verdelen met een standaard load balancer.

Als u een ARM-sjabloon gebruikt, hoeft u minder stappen te voltooien in vergelijking met andere implementatiemethoden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/).

SKU's voor openbare IP-adressen en SKU's voor load balancers moeten overeenkomen. Wanneer u een standaard load balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end wordt geconfigureerd voor de standaard load balancer. Als u een basis load balancer wilt maken, gebruikt u [deze sjabloon](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Microsoft adviseert een standaard-SKU voor productieworkloads.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): voor de load balancer, de bastion-host, en voor elk van de drie virtuele machines.
- [**Microsoft.Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3).
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3).
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): gebruik dit om de IIS (Internet Information Server) en de webpagina's te configureren.

Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular) als u meer sjablonen wilt vinden die gerelateerd zijn aan Azure Load Balancer.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Wacht totdat u de prompt van de console ziet.

1. Selecteer **Kopiëren** in het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het shell-consoledeelvenster en selecteer **Plakken**.

1. Voer de waarden in.

   Met de sjabloonimplementatie worden drie beschikbaarheidszones gemaakt. Beschikbaarheidszones worden alleen ondersteund in [bepaalde regio's](../availability-zones/az-overview.md). Gebruik een van de ondersteunde regio's. Als u twijfelt, voert u **centralus** in.

   De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd. U hebt de naam van de resourcegroep nodig in de volgende sectie.

Het duurt ongeveer 10 minuten om de sjabloon te implementeren. Wanneer voltooid is de uitvoer vergelijkbaar met:

![Uitvoer van Azure Standard Load Balancer Resource Manager-sjabloon PowerShell-implementatie](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. U kunt ook Azure Portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. Selecteer de load balancer. De standaardnaam is de naam van het project met **-lb** eraan toegevoegd.

1. Kopieer alleen het IP-adresgedeelte van het openbare IP-adres en plak het in de adresbalk van de browser.

   ![Azure Standard Load Balancer Resource Manager-sjabloon openbaar IP](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    In de browser wordt de standaardpagina van de Internet Information Services-webserver (IIS) weergegeven.

   ![IIS-webserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Als u wilt zien hoe de load balancer verkeer distribueert naar alle drie de VM's, kunt u vernieuwing van uw webbrowser afdwingen op de clientcomputer.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u ze niet meer nodig hebt, verwijdert u de: 

* Resourcegroep
* Load balancer
* Gerelateerde resources

Ga naar Azure Portal, selecteer de resourcegroep die de load balancer bevat en selecteer vervolgens **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gedaan:

* U hebt een virtueel netwerk gemaakt voor de load balancer en virtuele machines.
* U hebt een Azure Bastion-host gemaakt voor beheer.
* Een standaard load balancer gemaakt en er VM's aan gekoppeld.
* De load balancer-verkeersregel en de statustest geconfigureerd.
* De load balancer getest.

Voor meer informatie gaat u door naar de zelfstudies voor Azure Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
