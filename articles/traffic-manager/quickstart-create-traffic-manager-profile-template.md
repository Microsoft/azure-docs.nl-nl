---
title: 'Quickstart: Een Traffic Manager maken met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon)'
description: In dit quickstart-artikel wordt beschreven hoe u een Azure Traffic Manager-profiel maakt met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: ec569781a6318062810358c2c5e17ba71efc4f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92676005"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Quickstart: Traffic Manager-profiel maken met een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) kunt gebruiken voor het maken van een Traffic Manager-profiel met externe eindpunten met behulp van de routeringsmethode op basis van prestaties.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

Er is één Azure-resource gedefinieerd in de sjabloon:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular) als u meer sjablonen wilt vinden die gerelateerd zijn aan Azure Traffic Manager.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Wacht totdat u de prompt van de console ziet.

1. Selecteer **Kopiëren** in het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het shell-consoledeelvenster en selecteer **Plakken**.

1. Voer de waarden in.

    De sjabloonimplementatie maakt een profiel met twee externe eindpunten. **Endpoint1** gebruikt een doeleindpunt van `www.microsoft.com` met locatie in **Noord-Europa**. **Endpoint2** gebruikt een doeleindpunt van `docs.microsoft.com` met locatie in **VS - zuid-centraal**.

    De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

    > [!NOTE]
    > **uniqueDNSname** moet wereldwijd een unieke naam zijn om de sjabloon te kunnen implementeren. Als de implementatie mislukt, begint u met stap 1.

    Het implementeren van de sjabloon duurt enkele minuten. Wanneer voltooid is de uitvoer vergelijkbaar met:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Uitvoer van Azure Traffic Manager Resource Manager-sjabloon PowerShell-implementatie":::

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Bepaal de DNS-naam van het Traffic Manager-profiel met behulp van [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Kopieer de waarde **RelativeDnsName**. De DNS-naam van uw Traffic Manager-profiel is `<relativednsname>.trafficmanager.net`.

1. Voer de volgende opdracht uit vanuit een lokale PowerShell door de variabele **{relativeDNSname}** te vervangen door `<relativednsname>.trafficmanager.net`.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    U moet een NameHost van `www.microsoft.com` of `docs.microsoft.com` gebruiken, afhankelijk van welke regio dichterbij is.

1. Als u wilt controleren of u het andere eindpunt kunt oplossen, moet u het eindpunt uitschakelen voor het doel dat u in de laatste stap hebt gekregen. Vervang de variabele **{endpointName}** door **endpoint1** of **endpoint2** om het doel voor respectievelijk `www.microsoft.com` of `docs.microsoft.com` uit te schakelen.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Voer de opdracht van stap 2 opnieuw uit in een lokale PowerShell. Nu moet u de andere NameHost voor het andere eindpunt ophalen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u het Traffic Manager-profiel niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u het Traffic Manager-profiel en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt.

Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)
