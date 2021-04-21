---
title: 'Quickstart: Een profiel en eindpunt maken - Resource Manager-sjabloon'
titleSuffix: Azure Content Delivery Network
description: In deze quickstart leert u hoe u een Azure Content Delivery Network-profiel en eindpunt maakt Resource Manager sjabloon
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: 31f0510ed50c9d48a46524a353d7c872b368f75c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779035"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Quickstart: Een Azure CDN-profiel en -eindpunt maken - ARM-sjabloon

Ga aan de slag met Azure Content Delivery Network (CDN) door een Azure Resource Manager-sjabloon (ARM-sjabloon) te gebruiken. Met de sjabloon wordt een profiel en een eindpunt geïmplementeerd.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Deze sjabloon is geconfigureerd voor het maken van een:

* Profiel
* Eindpunt

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

Er is één Azure-resource gedefinieerd in de sjabloon:

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>De sjabloon implementeren

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portal

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Resourcegroepen** in het linkerdeelvenster.

3. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De standaardnaam van de resourcegroep is **myResourceGroupCDN**

4. Controleer of de volgende resources zijn gemaakt in de resourcegroep:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN-resourcegroep" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="azure-cli"></a>Azure CLI

U kunt de opdracht [az group delete](/cli/azure/group#az_group_delete) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

U kunt de resourcegroep, het CDN-profiel en alle gerelateerde resources verwijderen wanneer u ze niet meer nodig hebt. Selecteer de resourcegroep **myResourceGroupCDN** die het CDN-profiel en het eindpunt bevat en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

* CDN-profiel
* Eindpunt

Als u meer wilt weten over Azure CDN en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

> [!div class="nextstepaction"]
> [Zelfstudie: CDN gebruiken om statische inhoud van een web-app te leveren](cdn-add-to-web-app.md)
