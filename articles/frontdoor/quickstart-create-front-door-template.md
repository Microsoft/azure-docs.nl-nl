---
title: 'Quickstart: Een Azure Front Door-service maken op basis van een Azure Resource Manager-sjabloon (ARM)'
description: In deze quickstart wordt beschreven hoe u een Azure Front Door-service maakt op basis van een Azure Resource Manager-sjabloon (ARM-sjabloon).
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 223006193219afe4179f3161d5e60e6439207b22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896052"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Quickstart: Front Door maken op basis van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon ) gebruikt om een front door te maken voor het instellen van hoge beschikbaarheid voor een webeindpunt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* IP of FQDN van een website of webtoepassing.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

In deze quickstart maakt u een Front Door-configuratie met één back-end en één standaardpad dat overeenkomt met `/*`.

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

Er is één Azure-resource gedefinieerd in de sjabloon:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Wacht totdat u de prompt van de console ziet.

1. Selecteer **Kopiëren** in het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het shell-consoledeelvenster en selecteer **Plakken**.

1. Voer de waarden in.

    Met de sjabloonimplementatie wordt een front door met één back-end gemaakt. In dit voorbeeld wordt `microsoft.com` gebruikt als **backendAddress**.

    De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

    > [!NOTE]
    > **frontDoorName** moet een globaal unieke naam zijn om de sjabloon te kunnen implementeren. Als de implementatie mislukt, begint u met stap 1.

    Het implementeren van de sjabloon duurt enkele minuten. Wanneer voltooid is de uitvoer vergelijkbaar met:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Uitvoer van PowerShell-implementatie van Resource Manager-sjabloon van Front Door":::

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. Selecteer de front door die u eerder hebt gemaakt en klik op de link **Frontend-host**. Er wordt een webbrowser geopend waarin u wordt omgeleid naar de back-end-FQDN die u tijdens het maken hebt gedefinieerd.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Overzicht van Front Door-portal":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de Front Door-service niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u Front Door en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u front door gemaakt.

Ga verder naar de zelfstudies over Front Door voor informatie over het toevoegen van een aangepast domein aan uw Front Door.

> [!div class="nextstepaction"]
> [Zelfstudies over Front Door](front-door-custom-domain.md)
