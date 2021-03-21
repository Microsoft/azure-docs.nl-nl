---
title: 'Zelfstudie: parameters aan een sjabloon toevoegen'
description: Voeg parameters toe aan uw ARM-sjabloon (Azure Resource Manager) om deze herbruikbaar te maken.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e983f8499cbeaf400a8da6f48d7f6c8b75c4795a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107059"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Zelfstudie: Parameters aan uw ARM-sjabloon toevoegen

In de [vorige zelfstudie](template-tutorial-add-resource.md) hebt u geleerd hoe u een opslagaccount aan de sjabloon toevoegt en deze implementeert. In deze zelfstudie leert u hoe u de ARM-sjabloon (Azure Resource Manager) kunt verbeteren door er parameters aan toe te voegen. Deze zelfstudie neemt ongeveer **14 minuten** in beslag.

## <a name="prerequisites"></a>Vereisten

U wordt aangeraden om eerst de [zelfstudie over resources](template-tutorial-add-resource.md) te voltooien, maar dit is niet verplicht.

U moet beschikken over Visual Studio Code met de extensie Resource Manager Tools (hulpprogramma's voor resourcebeheer), plus Azure PowerShell of Azure CLI. Zie [Hulpprogramma's voor sjablonen](template-tutorial-create-first-template.md#get-tools) voor meer informatie.

## <a name="review-template"></a>Sjabloon controleren

Aan het einde van de vorige zelfstudie had uw sjabloon de volgende JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Mogelijk hebt u gemerkt dat er een probleem is met deze sjabloon. De naam van het opslagaccount is in code vastgelegd. U kunt deze sjabloon alleen gebruiken om telkens hetzelfde opslagaccount te implementeren. Als u een opslagaccount met een andere naam wilt implementeren, moet u een nieuwe sjabloon maken. Dit is uiteraard geen praktische manier om uw implementaties te automatiseren.

## <a name="make-template-reusable"></a>Sjabloon herbruikbaar maken

Om uw sjabloon herbruikbaar te maken, voegen we een parameter toe waarmee u de naam van een opslagaccount kunt doorgeven. De gemarkeerde JSON in het volgende voorbeeld laat zien wat er in uw sjabloon is gewijzigd. De parameter `storageName` wordt geïdentificeerd als een tekenreeks. De maximale lengte is ingesteld op 24 tekens om ervoor te zorgen dat namen niet te lang worden.

Kopieer het hele bestand en vervang uw sjabloon door de inhoud ervan.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Sjabloon implementeren

We gaan de sjabloon implementeren. In het volgende voorbeeld wordt de sjabloon geïmplementeerd met Azure CLI of PowerShell. Merk op dat u de naam van het opslagaccount opgeeft als een waarde in de implementatieopdracht. Geef voor het opslagaccount dezelfde naam op als de naam die u in de vorige zelfstudie hebt gebruikt.

Zie [Resourcegroep maken](template-tutorial-create-first-template.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voorbeeld wordt ervan uitgegaan dat u de variabele `templateFile` hebt ingesteld op het pad naar het sjabloonbestand, zoals weergegeven in de [eerste zelfstudie](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Inzicht in resource-updates

In de vorige sectie hebt u een opslagaccount geïmplementeerd met dezelfde naam als de resource die u eerder hebt gemaakt. U vraagt zich wellicht af hoe de resource wordt beïnvloed door de herimplementatie.

Als de resource al bestaat en er geen wijziging wordt gedetecteerd in de eigenschappen, wordt er geen actie ondernomen. Als de resource al bestaat en een eigenschap is gewijzigd, wordt de resource bijgewerkt. Als de resource niet bestaat, wordt deze gemaakt.

Op deze manier kunnen alle benodigde resources voor een Azure-oplossing worden opgenomen in uw sjabloon. U kunt de sjabloon veilig opnieuw implementeren in de wetenschap dat resources alleen worden gewijzigd of gemaakt wanneer dat nodig is. Alsu bijvoorbeeld bestanden aan uw opslagaccount hebt toegevoegd, kunt u het opslagaccount opnieuw implementeren zonder dat die bestanden verloren gaan.

## <a name="customize-by-environment"></a>Aanpassen aan de omgeving

Met parameters kunt u de implementatie aanpassen door waarden op te geven die voor een specifieke omgeving zijn aangepast. U kunt bijvoorbeeld verschillende waarden doorgeven voor een implementatie in een ontwikkel-, test- of productieomgeving.

In de vorige sjabloon is altijd het opslagaccount **Standard_LRS** geïmplementeerd. Mogelijk wilt u de flexibiliteit om verschillende SKU's te implementeren, afhankelijk van de omgeving. In het volgende voorbeeld ziet u de benodigde wijzigingen voor het toevoegen van een parameter voor de SKU. Kopieer het hele bestand en plak het over uw sjabloon.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

De parameter `storageSKU` heeft een standaardwaarde. Deze waarde wordt gebruikt wanneer tijdens de implementatie geen waarde is opgegeven. Het bevat ook een lijst met toegestane waarden. Deze waarden komen overeen met de benodigde waarden voor het maken van een opslagaccount. U wilt niet dat gebruikers van uw sjabloon SKU's doorgeven die niet werken.

## <a name="redeploy-template"></a>Sjabloon opnieuw implementeren

U kunt de app nu opnieuw implementeren. Omdat de standaard-SKU is ingesteld op **Standard_LRS**, hoeft u geen waarde voor die parameter op te geven.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

Om de flexibiliteit van uw sjabloon te bekijken, implementeren we deze opnieuw. Stel de SKU-parameter nu in op **Standard_GRS**. U kunt een nieuwe naam doorgeven om een ander opslagaccount te maken, of dezelfde naam gebruiken om uw bestaande opslagaccount bij te werken. Beide opties werken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Ten slotte gaan we nog een test uitvoeren en zien wat er gebeurt wanneer u een niet-toegestane SKU-waarde doorgeeft. In dit geval testen we het scenario waarbij een gebruiker van uw sjabloon denkt dat **basic** een van de SKU's is.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

De opdracht mislukt onmiddellijk met een foutbericht waarin wordt aangegeven welke waarden zijn toegestaan. De fout wordt in Resource Manager geïdentificeerd voordat de implementatie wordt gestart.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de volgende zelfstudie, hoeft u de resourcegroep niet te verwijderen.

Als u nu stopt, wilt u de geïmplementeerde resources wellicht opschonen door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt de sjabloon die in de [eerste zelfstudie](template-tutorial-create-first-template.md) is gemaakt, verbeterd door er parameters aan toe te voegen. In de volgende zelfstudie leert u meer over sjabloonfuncties.

> [!div class="nextstepaction"]
> [Sjabloonfuncties toevoegen](template-tutorial-add-functions.md)
