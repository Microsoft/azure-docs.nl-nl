---
title: Bicep-bestanden maken-Visual Studio code
description: Visual Studio code en de Bicep-extensie voor Bicep-bestanden gebruiken voor de implementatie van Azure-resources
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 4d1064351ddfacdebfa67fd9b2f517f592de3a7c
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612880"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Quick Start: Bicep-bestanden maken met Visual Studio code

De Bicep-extensie voor Visual Studio code biedt taal ondersteuning en automatisch aanvullen van resources. Met deze hulpprogram ma's kunt u [Bicep](./bicep-overview.md) -bestanden maken en valideren. In deze Quick Start gebruikt u de extensie om een volledig Bicep-bestand te maken. Wanneer u dit doet, worden de uitbreidings mogelijkheden, zoals validatie en voltooiingen, uitgevoerd.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

Als u deze Snelstartgids wilt volt ooien, hebt u [Visual Studio code](https://code.visualstudio.com/)nodig, waarbij de [Bicep-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) is geïnstalleerd. U hebt ook de nieuwste [Azure cli](/cli/azure/) of de nieuwste [Azure PowerShell-module](/powershell/azure/new-azureps-module-az) geïnstalleerd en geverifieerd.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-bicep-file"></a>Een Bicep-bestand maken

Maak en open met Visual Studio code een nieuw bestand met de naam *azuredeploy. Bicep*.

## <a name="add-an-azure-resource"></a>Een Azure-resource toevoegen

Voeg een Basic Storage-account bron toe aan het Bicep-bestand.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

De resource declaratie bestaat uit vier onderdelen:

- **resource**: sleutel woord.
- **symbolische naam** (STG): symbolische naam is een id voor het verwijzen naar de resource in uw Bicep-bestand. Het is niet wat de naam van de resource is wanneer deze wordt geïmplementeerd. De naam van de resource wordt gedefinieerd door de eigenschap **naam** .  Zie het vierde onderdeel in deze lijst.
- **resource type** ( Microsoft.Storage/storageAccounts@2019-06-01 ): het bestaat uit de resource provider (micro soft. Storage), resource type (Storage accounts) en apiVersion (2019-06-01). Elke resourceprovider publiceert eigen API-versies. Met andere woorden, elk type heeft een specifieke waarde. U kunt meer typen en apiVersions vinden voor verschillende Azure-resources van [arm-sjabloon verwijzing](/azure/templates/).
- **Eigenschappen** (alles binnen = {...}): Geef de eigenschappen voor het bron type op. Elke resource heeft een `name` eigenschap. De meeste resources hebben ook de eigenschap `location`. Hiermee wordt de regio ingesteld waarin de resource wordt geïmplementeerd. De overige eigenschappen variëren per resourcetype en API-versie.

## <a name="completion-and-validation"></a>Voltooiing en validatie

Een van de krachtigste mogelijkheden van de extensie is de integratie met Azure-schema's. Azure-schema's voorzien de extensie van mogelijkheden voor validatie en resourcebewuste voltooiing. We gaan het opslagaccount wijzigen om validatie en voltooiing in actie te zien.

Werk eerst het type opslagaccount bij naar een ongeldige waarde, zoals `megaStorage`. U ziet dat met deze actie een waarschuwing wordt gegenereerd die aangeeft dat `megaStorage` geen geldige waarde is.

![Afbeelding van een ongeldige opslagconfiguratie](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Als u de voltooiings mogelijkheden wilt gebruiken, verwijdert u `megaStorage` de cursor binnen de enkele aanhalings tekens en drukt u op `ctrl`  +  `space` . Met deze actie wordt een voltooiingslijst met geldige waarden weergegeven.

![Afbeelding van de automatische voltooiing van de extensie](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Parameters toevoegen

Maak nu een parameter en gebruik deze om de naam van het opslagaccount op te geven.

Voeg de volgende code toe aan het begin van het bestand:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Namen van Azure-opslagaccounts hebben een minimumlengte van 3 en een maximumlengte van 24 tekens. Gebruik `minLength` en `maxLength` om de juiste waarden op te geven.

Wijzig nu in de opslagresource de naam van de eigenschap om de parameter te gebruiken. Als u dit wilt doen, verwijdert u de naam van de huidige opslag Resource, inclusief de enkele aanhalings tekens. Druk op `ctrl`  +  `space` . Selecteer de para meter **storageAccountName** in de lijst. U ziet dat er rechtstreeks naar de para meters kan worden verwezen door hun namen te gebruiken in Bicep. Voor de JSON-sjablonen is een para meter ()-functie vereist.

![Installatie kopie met automatische aanvulling bij het gebruik van para meters in Bicep-resources](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Het Bicep-bestand implementeren

Open de geïntegreerde Visual Studio code-Terminal met behulp van de `ctrl`  +  ```` ` ```` toetscombinatie, wijzig de huidige map in de locatie waar het Bicep-bestand zich bevindt en gebruik vervolgens de module Azure CLI of Azure PowerShell om het Bicep-bestand te implementeren.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer de Azure-resources niet meer nodig zijn, gebruikt u de Azure CLI of Azure PowerShell-module om de quickstart-resourcegroep te verwijderen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studies voor beginners Bicep](./bicep-tutorial-create-first-bicep.md)
