---
title: 'Zelfstudie: een variabele aan een sjabloon toevoegen'
description: Voeg variabelen toe aan uw ARM-sjabloon (Azure Resource Manager) om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46ed1fc55a108bf80089d249abc58bc5d1a6479a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106951"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Zelfstudie: variabelen aan uw ARM-sjabloon toevoegen

In deze zelfstudie leert u hoe u een variabele toevoegt aan uw ARM-sjabloon (Azure Resource Manager). Variabelen vereenvoudigen uw sjablonen doordat u één keer een expressie hoeft te schrijven om deze overal in de sjabloon opnieuw te gebruiken. Deze zelfstudie neemt ongeveer **7 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

Het wordt aangeraden om eerst de [zelfstudie over functies](template-tutorial-add-functions.md) te voltooien, maar dit is niet vereist.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools, plus Azure PowerShell of Azure CLI. Zie [Hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelfstudie bestond uw sjabloon uit de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

De parameter voor de naam van het opslagaccount is moeilijk te gebruiken omdat u een unieke naam moet opgeven. Als u de eerdere zelfstudies in deze serie hebt voltooid, hebt u waarschijnlijk weinig zin meer om weer een unieke naam te bedenken. U lost dit probleem op door een variabele toe te voegen die een unieke naam voor het opslagaccount samenstelt.

## <a name="use-variable"></a>Variabele gebruiken

In het volgende voorbeeld zijn de wijzigingen gemarkeerd die nodig zijn om een variabele aan uw sjabloon toe te voegen waarmee u een unieke naam voor een opslagaccount kunt maken. Kopieer het hele bestand en vervang uw sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

U ziet dat het voorbeeld een variabele bevat met de naam `uniqueStorageName`. Deze variabele maakt gebruik van vier functies om een tekenreekswaarde samen te stellen.

U bent al bekend met de functie [parameters](template-functions-deployment.md#parameters), dus we gaan er hier niet verder op in.

U hebt ook al gewerkt met de functie [resourceGroup](template-functions-resource.md#resourcegroup). In dit geval vraagt u de eigenschap `id` op in plaats van `location`, zoals in de vorige zelfstudie. De eigenschap `id` retourneert de volledige id van de resourcegroep, met inbegrip van de abonnements-id en de naam van de resourcegroep.

Met de functie [uniqueString](template-functions-string.md#uniquestring) maakt u een hash-waarde van 13 tekens. De geretourneerde waarde wordt bepaald door de parameters die u doorgeeft. Voor deze zelfstudie gebruikt u de id van de resourcegroep als de invoer voor de hash-waarde. Dit betekent dat u deze sjabloon kunt implementeren in verschillende resourcegroepen om steeds een andere, unieke tekenreekswaarde te verkrijgen. U krijgt echter dezelfde waarde als u in dezelfde resourcegroep implementeert.

De functie [concat](template-functions-string.md#concat) accepteert waarden en combineert deze. Voor deze variabele haalt de functie de tekenreeks van de parameter en de tekenreeks van de functie `uniqueString` op en voegt deze samen tot één tekenreeks.

Met de parameter `storagePrefix` kunt u een voorvoegsel doorgeven waarmee u opslagaccounts kunt identificeren. U kunt uw eigen naamconventie opstellen die het eenvoudiger maakt om opslagaccounts na implementatie te identificeren in een lange lijst met resources.

Ten slotte ziet u dat de naam van het opslagaccount nu is ingesteld op de variabele in plaats van een parameter.

## <a name="deploy-template"></a>Sjabloon implementeren

We gaan de sjabloon nu implementeren. Het implementeren van deze sjabloon is eenvoudiger dan de vorige sjablonen omdat u alleen het voorvoegsel voor de naam van het opslagaccount hoeft op te geven.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

## <a name="verify-deployment"></a>Implementatie verifiëren

U kunt de implementatie controleren door de resourcegroep te bekijken vanuit de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkermenu.
1. Selecteer de resourcegroep waarin de sjabloon is geïmplementeerd.
1. U ziet dat er resource voor een opslagaccount is geïmplementeerd. De naam van het opslagaccount is **store** plus een reeks willekeurige tekens.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een variabele toegevoegd waarmee een unieke naam voor een opslagaccount wordt gemaakt. In de volgende zelfstudie retourneert u een waarde uit het geïmplementeerde opslagaccount.

> [!div class="nextstepaction"]
> [Uitvoer toevoegen](template-tutorial-add-outputs.md)
