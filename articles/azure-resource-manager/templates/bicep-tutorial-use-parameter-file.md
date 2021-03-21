---
title: Zelf studie-parameter bestand gebruiken om Azure Resource Manager Bicep-bestand te implementeren
description: Gebruik parameter bestanden die de waarden bevatten die moeten worden gebruikt voor het implementeren van uw Bicep-bestand.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca3a73cde9549bfcdfd47bc4f1955904fac69d1c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632353"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Zelf studie: parameter bestanden gebruiken om Azure Resource Manager Bicep-bestand te implementeren

In deze zelfstudie leert u hoe u [parameterbestanden](parameter-files.md) kunt gebruiken om de waarden op te slaan die tijdens de implementatie worden doorgegeven. In de vorige zelfstudies hebt u inline parameters gebruikt bij de implementatieopdracht. Deze benadering werkt voor het testen van uw Bicep-bestand, maar bij het automatiseren van implementaties kan het gemakkelijker worden om een set waarden voor uw omgeving door te geven. Met parameterbestanden kunt u gemakkelijker parameterwaarden voor een specifieke omgeving inpakken. U gebruikt hetzelfde JSON-parameter bestand wanneer u een JSON-sjabloon implementeert. In deze zelfstudie maakt u parameterbestanden voor ontwikkel- en productieomgevingen. Dit neemt ongeveer **12 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over tags](bicep-tutorial-add-tags.md) te voltooien, maar dit is niet verplicht.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Uw Bicep-bestand heeft veel para meters die u tijdens de implementatie kunt opgeven. Aan het einde van de vorige zelf studie ziet uw Bicep-bestand er als volgt uit:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Dit Bicep-bestand werkt goed, maar nu wilt u de para meters die u doorgeeft, eenvoudig beheren voor het Bicep-bestand.

## <a name="add-parameter-files"></a>Parameterbestanden toevoegen

Parameter bestanden zijn JSON-bestanden met een structuur die vergelijkbaar is met JSON-sjablonen. In het bestand geeft u de parameterwaarden op die u tijdens de implementatie wilt doorgeven.

In het parameter bestand geeft u waarden op voor de para meters in uw Bicep-bestand. De naam van elke para meter in het parameter bestand moet overeenkomen met de naam van een para meter in uw Bicep-bestand. De naam is hoofdletter gevoelig, maar om eenvoudig de overeenkomende waarden te zien, raden we u aan om de behuizing uit het Bicep-bestand te zoeken.

U hoeft niet voor alle parameters een waarde op te geven. Als een ongespecificeerde parameter een standaardwaarde heeft, wordt die waarde gebruikt tijdens de implementatie. Als een parameter geen standaardwaarde heeft en er geen waarde is opgegeven in het parameterbestand, wordt u gevraagd een waarde op te geven tijdens de implementatie.

U kunt geen parameter naam opgeven in het parameter bestand dat niet overeenkomt met een parameter naam in het Bicep-bestand. Er wordt een foutbericht weergegeven wanneer er onbekende parameters worden opgegeven.

Maak in Visual Studio Code een nieuw bestand met de volgende inhoud. Sla het bestand op met de naam _azuredeploy.parameters.dev.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Dit bestand is het parameterbestand voor de ontwikkelomgeving. U ziet dat **Standard_LRS** wordt gebruikt als opslagaccount, namen van resources worden voorzien van het voorvoegsel **dev** en de tag `Environment` wordt ingesteld op **Dev**.

Maak nogmaals een nieuw bestand, ditmaal met de volgende inhoud. Sla het bestand op met de naam _azuredeploy.parameters.prod.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Dit bestand is het parameterbestand voor de productieomgeving. Notice that it uses **Standard_GRS** for the storage account, names resources with a **contoso** prefix, and sets the `Environment` tag to **Production**. In een echte productieomgeving zou u ook een app-service willen gebruiken met een andere SKU dan een gratis SKU, maar in deze zelfstudie blijven we die SKU gebruiken.

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

Gebruik Azure CLI of Azure PowerShell om het Bicep-bestand te implementeren.

In deze zelf studie gaan we twee nieuwe resource groepen maken. Eén voor de ontwikkelomgeving en één voor de productieomgeving.

Voor de variabelen sjabloon en para meter, vervang `{path-to-the-bicep-file}` , `{path-to-azuredeploy.parameters.dev.json}` , en de accolades vervangen `{path-to-azuredeploy.parameters.prod.json}` `{}` door uw Bicep-bestand en de paden voor de parameter bestanden.

Eerst implementeren de sjabloon in de ontwikkelomgeving.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

Nu implementeren we de sjabloon in de productieomgeving.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroepen te bekijken vanuit de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. U ziet de twee nieuwe resourcegroepen die u in deze zelfstudie hebt geïmplementeerd.
1. Selecteer een van beide resourcegroepen en bekijk de geïmplementeerde resources. U ziet dat ze overeenkomen met de waarden die u hebt opgegeven in het parameterbestand voor die omgeving.

## <a name="clean-up-resources"></a>Resources opschonen

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie gebruikt u een parameter bestand voor het implementeren van uw Bicep-bestand. In de volgende zelf studie leert u hoe u uw Bicep-bestanden kunt modularize.

> [!div class="nextstepaction"]
> [Modules toevoegen](./bicep-tutorial-add-modules.md)
