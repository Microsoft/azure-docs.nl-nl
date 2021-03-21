---
title: Zelf studie-Tags toevoegen aan resources in Azure Resource Manager Bicep-bestand
description: Tags toevoegen aan resources die u in uw Bicep-bestanden implementeert. Gebruik tags om resources logisch te ordenen.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632596"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Zelf studie: Tags toevoegen in Azure Resource Manager Bicep-bestanden

In deze zelf studie leert u hoe u labels kunt toevoegen aan resources in uw Bicep-bestanden. [Tags](../management/tag-resources.md) zijn een hulpmiddel bij het logisch ordenen van uw resources. De tagwaarden worden weergegeven in kostenrapporten. Deze zelfstudie neemt ongeveer **8 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

Het wordt aangeraden om eerst de [zelfstudie over snelstartsjablonen](bicep-tutorial-quickstart-template.md) te voltooien, maar dit is niet verplicht.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

In het vorige Bicep-bestand is een opslag account, App Service plan en web-app geïmplementeerd.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Na de implementatie van deze resources wilt u mogelijk de kosten bijhouden en resources zoeken die tot een bepaalde categorie behoren. U kunt tags toevoegen om deze taken uit te voeren.

## <a name="add-tags"></a>Tags toevoegen

U tagt resources om waarden toe te voegen waarmee u het gebruik van de resources kunt identificeren. U kunt bijvoorbeeld tags toevoegen die de omgeving en het project vermelden. Maar u kunt ook tags toevoegen die een kostenplaats aangeven of het team dat eigenaar is van de resource. U kunt alle waarden invoeren voor tags die voor uw organisatie van belang zijn.

In het volgende voor beeld ziet u de wijzigingen in het Bicep-bestand. Kopieer het hele bestand en vervang het Bicep-bestand door de inhoud ervan.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

Het is tijd om het Bicep-bestand te implementeren en de resultaten te bekijken.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. Selecteer een van de resources, zoals de resource van het opslagaccount. U ziet dat de resource nu tags heeft.

   ![Tags weergeven](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u tags toegevoegd aan de resources. In de volgende zelf studie leert u hoe u parameter bestanden kunt gebruiken om het door geven van waarden aan de implementatie te vereenvoudigen.

> [!div class="nextstepaction"]
> [Parameterbestand gebruiken](bicep-tutorial-use-parameter-file.md)
