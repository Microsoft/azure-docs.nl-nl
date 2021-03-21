---
title: Zelf studie-Quick Start-sjablonen gebruiken voor het ontwikkelen van Azure Resource Manager Bicep
description: Meer informatie over het gebruik van Azure Quick Start-sjablonen voor het volt ooien van uw Bicep-ontwikkeling.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632426"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Zelf studie: Azure Quick Start-sjablonen gebruiken voor het ontwikkelen van Azure Resource Manager Bicep

[Quick](https://azure.microsoft.com/resources/templates/) start-sjablonen voor Azure is een opslag plaats van de Community bijdraagt aan json-sjablonen. U kunt de voorbeeld sjablonen in uw Bicep-ontwikkeling gebruiken. In deze zelf studie vindt u een bron definitie van een website, compileert u deze naar Bicep en voegt u deze toe aan uw Bicep-bestand. Dit neemt ongeveer **12 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

Het wordt aangeraden om eerst de [zelfstudie over geëxporteerde sjablonen](bicep-tutorial-export-template.md) te voltooien, maar dit is niet verplicht.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie had uw Bicep-bestand de volgende inhoud:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Dit Bicep-bestand werkt voor het implementeren van opslag accounts en app service-plannen, maar u wilt mogelijk een website toevoegen. U kunt vooraf gemaakte sjablonen gebruiken om snel de JSON te vinden die vereist is voor het implementeren van een resource. Voordat Azure Quick Start-sjablonen Bicep-voor beelden bieden, kunt u conversie hulpprogramma's gebruiken om JSON-sjablonen te converteren naar Bicep-bestanden.

## <a name="find-template"></a>Sjabloon zoeken

Momenteel bieden de Azure Quick Start-sjablonen alleen JSON-sjablonen. Er zijn hulpprogram ma's die u kunt gebruiken om JSON-sjablonen te decompileren voor Bicep-sjablonen.

1. Open [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/).
1. Typ bij **Search** de zoektekst _deploy linux web app_.
1. Selecteer de tegel met de naam **Basis-web-app voor Linux**. Als u deze niet kunt vinden, is dit de [directe link](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecteer **Zoeken op GitHub**.
1. Selecteer _azuredeploy.json_. Dit is de sjabloon die u kunt gebruiken.
1. Selecteer **RAW** en maak vervolgens een kopie van de URL.
1. Blader naar **https://bicepdemo.z22.web.core.windows.net/** , selecteer **Decompile** en geef vervolgens de URL van de RAW-sjabloon op.
1. Bekijk de Bicep-sjabloon. Zoek in het bijzonder naar de resource `Microsoft.Web/sites`.

    ![Resource Manager-snelstartsjabloon voor website](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Er zijn een aantal belang rijke Bicep-functies die u op deze nieuwe resource kunt noteren als u hebt gewerkt aan JSON-sjablonen.

    In ARM JSON-sjablonen moet u hand matig resource afhankelijkheden opgeven met de eigenschap _dependsOn_ . De website bron is afhankelijk van de resource van het app service-plan. Als u met Bicep naar een eigenschap van de resource verwijst met behulp van de symbolische naam, wordt de eigenschap dependsOn automatisch toegevoegd.

    U kunt eenvoudig verwijzen naar de resource-ID van de symbolische naam van het app service-plan (appServicePlanName.id) dat wordt vertaald naar de functie resourceId (...) in de gecompileerde JSON-sjabloon.

## <a name="revise-existing-bicep-file"></a>Bestaand Bicep-bestand herzien

De ontcompile Quick Start-sjabloon samen voegen met het bestaande Bicep-bestand. Net zoals u in de vorige zelf studie hebt gedaan, werkt u de symbolische naam van de resource bij en de naam van de resource die overeenkomt met uw naam Conventie.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

De naam van de web-app moet uniek zijn binnen Azure. Om te voorkomen dat er dubbele namen worden gebruikt, is de variabele `webAppPortalName` bijgewerkt van `var webAppPortalName_var = '${webAppName}-webapp'` in `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

Gebruik Azure CLI of Azure PowerShell voor het implementeren van een Bicep-sjabloon.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele **bicepFile** hebt ingesteld op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een Quick Start-sjabloon kunt gebruiken voor uw Bicep-ontwikkeling. In de volgende zelfstudie gaat u tags toevoegen aan de resources.

> [!div class="nextstepaction"]
> [Tags toevoegen](bicep-tutorial-add-tags.md)
