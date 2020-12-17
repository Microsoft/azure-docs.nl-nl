---
title: 'Zelfstudie: tags toevoegen aan resources in een sjabloon'
description: Leer hoe u tags toevoegt aan resources die u in uw ARM-sjabloon (Azure Resource Manager) implementeert. Gebruik tags om resources logisch te ordenen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 625a88c0ee946b1ca67737d9cc67b638699d12f0
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107001"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Zelfstudie: tags toevoegen in uw ARM-sjabloon

In deze zelfstudie leert u hoe u tags toevoegt aan resources in uw ARM-sjabloon (Azure Resource Manager). [Tags](../management/tag-resources.md) zijn een hulpmiddel bij het logisch ordenen van uw resources. De tagwaarden worden weergegeven in kostenrapporten. Deze zelfstudie neemt ongeveer **8 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

Het wordt aangeraden om eerst de [zelfstudie over snelstartsjablonen](template-tutorial-quickstart-template.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools, plus Azure PowerShell of Azure CLI. Zie [Hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Met de vorige sjabloon hebt u een opslagaccount, App Service-plan en web-app geïmplementeerd.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Na de implementatie van deze resources wilt u mogelijk de kosten bijhouden en resources zoeken die tot een bepaalde categorie behoren. U kunt tags toevoegen om deze taken uit te voeren.

## <a name="add-tags"></a>Tags toevoegen

U tagt resources om waarden toe te voegen waarmee u het gebruik van de resources kunt identificeren. U kunt bijvoorbeeld tags toevoegen die de omgeving en het project vermelden. Maar u kunt ook tags toevoegen die een kostenplaats aangeven of het team dat eigenaar is van de resource. U kunt alle waarden invoeren voor tags die voor uw organisatie van belang zijn.

In het volgende voorbeeld zijn de aanpassingen van de sjabloon gemarkeerd. Kopieer het hele bestand en vervang uw sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Sjabloon implementeren

U kunt nu de sjabloon implementeren en de resultaten bekijken.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

   ![Tags weergeven](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u tags toegevoegd aan de resources. In de volgende zelfstudie leert u hoe u parameterbestanden kunt gebruiken om het doorgeven van waarden aan de sjabloon te vereenvoudigen.

> [!div class="nextstepaction"]
> [Parameterbestand gebruiken](template-tutorial-use-parameter-file.md)
