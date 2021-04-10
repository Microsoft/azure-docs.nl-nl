---
title: Zelf studie-een variabele toevoegen aan Azure Resource Manager Bicep-bestand
description: Voeg variabelen toe aan uw Bicep-bestand om de syntaxis te vereenvoudigen.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632472"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Zelf studie: variabelen toevoegen aan Azure Resource Manager Bicep-bestand

In deze zelf studie leert u hoe u een variabele kunt toevoegen aan uw Bicep-bestand. Variabelen vereenvoudigen uw Bicep-bestanden door u in te scha kelen een expressie één keer te schrijven en deze opnieuw te gebruiken in het Bicep-bestand. Deze zelfstudie neemt ongeveer **7 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

Het wordt aangeraden om eerst de [zelfstudie over functies](bicep-tutorial-add-functions.md) te voltooien, maar dit is niet vereist.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie had uw Bicep-bestand de volgende inhoud:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

De parameter voor de naam van het opslagaccount is moeilijk te gebruiken omdat u een unieke naam moet opgeven. Als u de eerdere zelfstudies in deze serie hebt voltooid, hebt u waarschijnlijk weinig zin meer om weer een unieke naam te bedenken. U lost dit probleem op door een variabele toe te voegen die een unieke naam voor het opslagaccount samenstelt.

## <a name="use-variable"></a>Variabele gebruiken

In het volgende voor beeld ziet u de wijzigingen voor het toevoegen van een variabele aan uw Bicep-bestand waarmee een unieke opslag accountnaam wordt gemaakt. Kopieer het hele bestand en vervang het Bicep-bestand door de inhoud ervan.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

U ziet dat het voorbeeld een variabele bevat met de naam `uniqueStorageName`. Deze variabele gebruikt drie functies voor het maken van een teken reeks waarde.

U bent bekend met de functie [resourceGroup](template-functions-resource.md#resourcegroup) . In dit geval vraagt u de eigenschap `id` op in plaats van `location`, zoals in de vorige zelfstudie. De eigenschap `id` retourneert de volledige id van de resourcegroep, met inbegrip van de abonnements-id en de naam van de resourcegroep.

Met de functie [uniqueString](template-functions-string.md#uniquestring) maakt u een hash-waarde van 13 tekens. De geretourneerde waarde wordt bepaald door de parameters die u doorgeeft. Voor deze zelfstudie gebruikt u de id van de resourcegroep als de invoer voor de hash-waarde. Dit betekent dat u dit Bicep-bestand kunt implementeren in verschillende resource groepen en een andere unieke teken reeks waarde kunt krijgen. U krijgt echter dezelfde waarde als u in dezelfde resourcegroep implementeert.

Bicep ondersteunt een [interpolatie syntaxis voor teken reeksen](https://en.wikipedia.org/wiki/String_interpolation#) . Voor deze variabele haalt de functie de tekenreeks van de parameter en de tekenreeks van de functie `uniqueString` op en voegt deze samen tot één tekenreeks.

Met de parameter `storagePrefix` kunt u een voorvoegsel doorgeven waarmee u opslagaccounts kunt identificeren. U kunt uw eigen naamconventie opstellen die het eenvoudiger maakt om opslagaccounts na implementatie te identificeren in een lange lijst met resources.

Ten slotte ziet u dat de naam van het opslagaccount nu is ingesteld op de variabele in plaats van een parameter.

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

We gaan het Bicep-bestand implementeren. Het implementeren van dit Bicep-bestand is eenvoudiger dan de vorige Bicep-bestanden omdat u alleen het voor voegsel voor de opslag naam opgeeft.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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
> [Uitvoer toevoegen](bicep-tutorial-add-outputs.md)
