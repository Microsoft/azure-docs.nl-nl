---
title: 'Zelfstudie: snelstartsjablonen gebruiken'
description: Lees hoe u met behulp van Azure-snelstartsjablonen sjablonen kunt ontwikkelen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106900"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Zelfstudie: Azure-snelstartsjablonen sjablonen gebruiken

[Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/) is een opslagplaats voor sjablonen die door de community zijn aangeleverd. U kunt de voorbeeldsjablonen gebruiken bij de ontwikkeling van eigen sjablonen. In deze zelfstudie vindt u een brondefinitie van een websiteresource en voegt u deze toe aan uw eigen sjabloon. De zelfstudie neemt ongeveer **12 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

Het wordt aangeraden om eerst de [zelfstudie over geëxporteerde sjablonen](template-tutorial-export-template.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools, plus Azure PowerShell of Azure CLI. Zie [Hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelfstudie bestond uw sjabloon uit de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Deze sjabloon werkt goed voor het implementeren van opslagaccounts en App Service-plannen, maar misschien is het handig om er een website aan toe te voegen. U kunt vooraf gemaakte sjablonen gebruiken om snel de JSON te vinden die vereist is voor het implementeren van een resource.

## <a name="find-template"></a>Sjabloon zoeken

1. Open [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/).
1. Typ bij **Search** de zoektekst _deploy linux web app_.
1. Selecteer de tegel met de naam **Basis-web-app voor Linux**. Als u deze niet kunt vinden, is dit de [directe link](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecteer **Zoeken op GitHub**.
1. Selecteer _azuredeploy.json_.
1. Bekijk de sjabloon. Zoek in het bijzonder naar de resource `Microsoft.Web/sites`.

    ![Resource Manager-snelstartsjabloon voor website](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Bestaande sjabloon aanpassen

Voeg de snelstartsjabloon samen met de bestaande sjabloon:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

De naam van de web-app moet uniek zijn binnen Azure. Om te voorkomen dat er dubbele namen worden gebruikt, is de variabele `webAppPortalName` bijgewerkt van `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` in `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` .

Voeg aan het einde van de definitie `Microsoft.Web/serverfarms` een komma toe om de resourcedefinitie te scheiden van de definitie van `Microsoft.Web/sites`.

Deze nieuwe resource heeft een aantal functies die van belang zijn.

U ziet dat de resource een element bevat met de naam `dependsOn` dat is ingesteld op het App Service-plan. Deze instelling is vereist omdat het App Service-plan moet bestaan voordat de web-app wordt gemaakt. Het element `dependsOn` geeft aan Resource Manager door hoe de resources moeten worden gerangschikt voor implementatie.

De eigenschap `serverFarmId` maakt gebruik van de functie [resourceId](template-functions-resource.md#resourceid). Deze functie haalt de unieke id voor een resource op. In dit geval wordt de unieke id voor het App Service-plan opgehaald. De web-app is gekoppeld aan één specifiek App Service-plan.

## <a name="deploy-template"></a>Sjabloon implementeren

U kunt Azure CLI of Azure PowerShell gebruiken om een sjabloon te implementeren.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele **templateFile** hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
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
  --template-file $templateFile \
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

U hebt geleerd hoe u een snelstartsjabloon kunt gebruiken om zelf sjablonen te ontwikkelen. In de volgende zelfstudie gaat u tags toevoegen aan de resources.

> [!div class="nextstepaction"]
> [Tags toevoegen](template-tutorial-add-tags.md)
