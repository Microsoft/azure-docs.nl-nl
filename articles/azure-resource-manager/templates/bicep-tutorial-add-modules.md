---
title: Zelf studie-modules toevoegen aan Azure Resource Manager Bicep-bestand
description: Gebruik modules om complexe details van de onbewerkte resource verklaring te integreren.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6efd9c230df49c83adc17361082af85b0ef9edc5
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633135"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Zelf studie: modules toevoegen aan Azure Resource Manager Bicep-bestand

In de [vorige zelf studie](bicep-tutorial-use-parameter-file.md)hebt u geleerd hoe u een parameter bestand kunt gebruiken om uw Bicep-bestand te implementeren. In deze zelf studie leert u hoe u Bicep-modules kunt gebruiken om complexe details van de onbewerkte resource verklaring te integreren. De modules kunnen worden gedeeld en opnieuw worden gebruikt binnen uw oplossing.  Dit neemt ongeveer **12 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden de [zelf studie over het parameter bestand](bicep-tutorial-use-parameter-file.md)te volt ooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie had uw Bicep-bestand de volgende inhoud:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Dit Bicep-bestand werkt goed. Maar voor grotere oplossingen wilt u uw Bicep-bestand in veel gerelateerde modules verstoren zodat u deze modules kunt delen en hergebruiken. Met het huidige Bicep-bestand wordt een opslag account, een app service-plan en een website geïmplementeerd.  We gaan het opslag account scheiden in een module.

## <a name="create-bicep-module"></a>Bicep-module maken

Elk Bicep-bestand kan worden gebruikt als een module, dus er is geen specifieke syntaxis voor het definiëren van een module. Maak een _Storage. Bicep_ -bestand met de volgende inhoud:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Deze module bevat de bron van het opslag account en de bijbehorende para meters en variabelen. De waarden voor de _locatie_ parameter en de _resource Tags_ -para meters zijn verwijderd. Deze waarden worden door gegeven vanuit het hoofd bestand Bicep.

## <a name="consume-bicep-module"></a>Bicep-module gebruiken

Vervang de resource definitie van het opslag account in de bestaande _azuredeploy. Bicep_ door de volgende Bicep-inhoud:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **module**: sleutel woord.
- **symbolische naam** (STG): dit is een id voor de module.
- **module bestand**: het pad naar de module in dit voor beeld wordt opgegeven met een relatief pad (./storage.Bicep). Alle paden in Bicep moeten worden opgegeven met het scheidings teken voor de slash (/) om ervoor te zorgen dat een consistente compilatie van meerdere platformen mogelijk is. De Windows back slash ( \) teken wordt niet ondersteund.

Als u een opslag eindpunt wilt ophalen, werkt u de uitvoer in _azuredeploy. Bicep_ bij naar de volgende Bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

De voltooide azuredeploy. Bicep heeft de volgende inhoud:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik Azure CLI of Azure PowerShell om de sjabloon te implementeren.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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
2. Voer de naam van de resourcegroep in het veld **Filter by name** in. Als u deze serie hebt voltooid, hebt u drie resourcegroepen om te verwijderen: **myResourceGroup**, **myResourceGroupDev** en **myResourceGroupProd**.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze inleiding over het implementeren van Bicep-bestanden in azure voltooid. Laat het ons weten als u opmerkingen en suggesties hebt in de feedbacksectie. Bedankt!

De volgende serie zelfstudies gaat dieper in op het implementeren van sjablonen.

> [!div class="nextstepaction"]
> [Modules toevoegen](./bicep-tutorial-add-modules.md)
