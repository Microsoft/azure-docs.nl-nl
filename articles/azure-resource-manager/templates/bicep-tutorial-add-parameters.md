---
title: Zelf studie-para meters toevoegen aan Azure Resource Manager Bicep-bestand
description: Voeg para meters toe aan uw Bicep-bestand om het opnieuw te kunnen worden gebruikt.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 603aa8f8bdb8136f4418d8f9a77bb40ec39243c0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748096"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Zelf studie: para meters toevoegen aan Azure Resource Manager Bicep-bestand

In de [vorige zelf studie](bicep-tutorial-create-first-bicep.md)hebt u geleerd hoe u een opslag account implementeert. In deze zelf studie leert u hoe u het Bicep-bestand kunt verbeteren door para meters toe te voegen. Deze zelfstudie neemt ongeveer **14 minuten** in beslag.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Vereisten

Het is raadzaam om het [bestand eerste Bicep te maken](bicep-tutorial-create-first-bicep.md), maar dit is niet vereist.

U moet Visual Studio code hebben met de Bicep-extensie en een Azure PowerShell of Azure CLI. Zie [Bicep-hulpprogram ma's](bicep-tutorial-create-first-bicep.md#get-tools)voor meer informatie.

## <a name="review-bicep-file"></a>Bicep-bestand controleren

Aan het einde van de vorige zelf studie ziet uw Bicep er als volgt uit:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Mogelijk hebt u gemerkt dat er een probleem is met dit Bicep-bestand. De naam van het opslagaccount is in code vastgelegd. U kunt dit Bicep-bestand alleen gebruiken om elk keer hetzelfde opslag account te implementeren. Als u een opslag account met een andere naam wilt implementeren, moet u een nieuw Bicep-bestand maken. Dit is uiteraard geen praktische manier om uw implementaties te automatiseren.

## <a name="make-bicep-file-reusable"></a>Het Bicep-bestand herbruikbaar maken

Als u wilt dat uw Bicep-bestand opnieuw kan worden gebruikt, gaan we een para meter toevoegen die u kunt gebruiken om de naam van een opslag account door te geven. In het gemarkeerde Bicep in het volgende voor beeld ziet u wat er is gewijzigd in het bestand. De parameter `storageName` wordt geïdentificeerd als een tekenreeks. De maximale lengte is ingesteld op 24 tekens om ervoor te zorgen dat namen niet te lang worden.

Kopieer het hele bestand en vervang dit door de volgende inhoud.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

U kunt verwijzen naar de para meters rechtstreeks met behulp van hun namen in Bicep, vergeleken met het vereisen van [para meters (' opslag naam ')] in een ARM JSON-sjabloon.

## <a name="deploy-bicep-file"></a>Bicep-bestand implementeren

We gaan het Bicep-bestand implementeren. In het volgende voor beeld wordt het Bicep-bestand geïmplementeerd met Azure CLI of Power shell. Merk op dat u de naam van het opslagaccount opgeeft als een waarde in de implementatieopdracht. Geef voor het opslagaccount dezelfde naam op als de naam die u in de vorige zelfstudie hebt gebruikt.

Zie [Resourcegroep maken](bicep-tutorial-create-first-bicep.md#create-resource-group) als u de resourcegroep nog niet hebt gemaakt. In het voor beeld wordt ervan uitgegaan dat u de variabele hebt ingesteld `bicepFile` op het pad naar het Bicep-bestand, zoals wordt weer gegeven in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u deze implementatie-cmdlet wilt uitvoeren, moet u over de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell beschikken.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Inzicht in resource-updates

In de vorige sectie hebt u een opslagaccount geïmplementeerd met dezelfde naam als de resource die u eerder hebt gemaakt. U vraagt zich wellicht af hoe de resource wordt beïnvloed door de herimplementatie.

Als de resource al bestaat en er geen wijziging wordt gedetecteerd in de eigenschappen, wordt er geen actie ondernomen. Als de resource al bestaat en een eigenschap is gewijzigd, wordt de resource bijgewerkt. Als de resource niet bestaat, wordt deze gemaakt.

Deze manier van het afhandelen van updates betekent dat uw Bicep-bestand alle resources kan bevatten die u nodig hebt voor een Azure-oplossing. U kunt het Bicep-bestand veilig opnieuw implementeren en weet dat de resources zijn gewijzigd of alleen worden gemaakt wanneer dit nodig is. Alsu bijvoorbeeld bestanden aan uw opslagaccount hebt toegevoegd, kunt u het opslagaccount opnieuw implementeren zonder dat die bestanden verloren gaan.

## <a name="customize-by-environment"></a>Aanpassen aan de omgeving

Met parameters kunt u de implementatie aanpassen door waarden op te geven die voor een specifieke omgeving zijn aangepast. U kunt bijvoorbeeld verschillende waarden doorgeven voor een implementatie in een ontwikkel-, test- of productieomgeving.

In het vorige Bicep-bestand is altijd een **Standard_LRS** Storage-account geïmplementeerd. Mogelijk wilt u de flexibiliteit om verschillende SKU's te implementeren, afhankelijk van de omgeving. In het volgende voorbeeld ziet u de benodigde wijzigingen voor het toevoegen van een parameter voor de SKU. Kopieer het hele bestand en plak het in uw Bicep-bestand.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

De parameter `storageSKU` heeft een standaardwaarde. Deze waarde wordt gebruikt wanneer tijdens de implementatie geen waarde is opgegeven. Het bevat ook een lijst met toegestane waarden. Deze waarden komen overeen met de benodigde waarden voor het maken van een opslagaccount. U wilt niet dat gebruikers van uw Bicep-bestand door geven in Sku's die niet werken.

## <a name="redeploy-bicep-file"></a>Bicep-bestand opnieuw implementeren

U kunt de app nu opnieuw implementeren. Omdat de standaard-SKU is ingesteld op **Standard_LRS**, hoeft u geen waarde voor die parameter op te geven.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Als de implementatie is mislukt, gebruikt u de schakeloptie `verbose` voor informatie over de resources die worden gemaakt. Gebruik de schakeloptie `debug` voor meer informatie over foutopsporing.

Voor een overzicht van de flexibiliteit van uw Bicep-bestand kunt u de implementatie opnieuw uitvoeren. Stel de SKU-parameter nu in op **Standard_GRS**. U kunt een nieuwe naam doorgeven om een ander opslagaccount te maken, of dezelfde naam gebruiken om uw bestaande opslagaccount bij te werken. Beide opties werken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Ten slotte gaan we nog een test uitvoeren en zien wat er gebeurt wanneer u een niet-toegestane SKU-waarde doorgeeft. In dit geval testen we het scenario waarbij een gebruiker van uw Bicep-bestand een van de **sku's vormt.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

U hebt het Bicep-bestand dat in de [eerste zelf studie](bicep-tutorial-create-first-bicep.md) is gemaakt verbeterd door para meters toe te voegen. In de volgende zelf studie leert u meer over Bicep-functies.

> [!div class="nextstepaction"]
> [Functies toevoegen](bicep-tutorial-add-functions.md)
