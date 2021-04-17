---
title: Parameterbestand maken
description: Parameterbestand maken voor het doorgeven van waarden tijdens de implementatie van een Azure Resource Manager sjabloon
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531214"
---
# <a name="create-resource-manager-parameter-file"></a>Een Resource Manager-parameterbestand maken

In plaats van parameters door te geven als inlinewaarden in uw script, kunt u een JSON-bestand gebruiken dat de parameterwaarden bevat. In dit artikel wordt beschreven hoe u een parameterbestand maakt dat u gebruikt met een JSON-sjabloon of Bicep-bestand.

## <a name="parameter-file"></a>Parameterbestand

Een parameterbestand heeft de volgende indeling:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

U ziet dat parameterwaarden in het parameterbestand worden opgeslagen als tekst zonder tekst. Deze aanpak werkt voor waarden die niet gevoelig zijn, zoals een resource-SKU. Tekst zonder tekst werkt niet voor gevoelige waarden, zoals wachtwoorden. Als u een parameter wilt doorgeven die een gevoelige waarde bevat, moet u de waarde opslaan in een sleutelkluis. Verwijs vervolgens naar de sleutelkluis in het parameterbestand. De gevoelige waarde wordt veilig opgehaald tijdens de implementatie.

Het volgende parameterbestand bevat een tekstwaarde zonder tekst en een gevoelige waarde die zijn opgeslagen in een sleutelkluis.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Zie Use Azure Key Vault to pass secure parameter value during deployment (Beveiligde parameterwaarde doorgeven tijdens de implementatie) voor meer informatie over het gebruik van waarden [uit een sleutelkluis.](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Parameterwaarden definiëren

Als u wilt bepalen hoe u de parameternamen en -waarden definieert, opent u uw JSON- of Bicep-sjabloon. Bekijk de sectie parameters van de sjabloon. In de volgende voorbeelden worden de parameters van JSON- en Bicep-sjablonen weergeven.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

In het parameterbestand is de naam van elke parameter het eerste detail dat opvalt. De parameternamen in het parameterbestand moeten overeenkomen met de parameternamen in uw sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Let op het parametertype. De parametertypen in het parameterbestand moeten dezelfde typen gebruiken als uw sjabloon. In dit voorbeeld zijn beide parametertypen tekenreeksen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Controleer de sjabloon op parameters met een standaardwaarde. Als een parameter een standaardwaarde heeft, kunt u een waarde opgeven in het parameterbestand, maar dit is niet vereist. De waarde van het parameterbestand overschrijven de standaardwaarde van de sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Controleer de toegestane waarden van de sjabloon en eventuele beperkingen, zoals de maximale lengte. Deze waarden geven het bereik van waarden op dat u voor een parameter kunt opgeven. In dit voorbeeld mag `storagePrefix` maximaal 11 tekens bevatten en moet een `storageAccountType` toegestane waarde worden opgegeven.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> Uw parameterbestand kan alleen waarden bevatten voor parameters die zijn gedefinieerd in de sjabloon. Als uw parameterbestand extra parameters bevat die niet overeenkomen met de parameters van de sjabloon, ontvangt u een foutmelding.

## <a name="parameter-type-formats"></a>Parametertype-indelingen

In het volgende voorbeeld ziet u de indelingen van verschillende parametertypen: tekenreeks, geheel getal, booleaanse waarde, matrix en object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-template-with-parameter-file"></a>Sjabloon implementeren met parameterbestand

Vanuit Azure CLI geeft u een lokaal parameterbestand door met en `@` de naam van het parameterbestand. Bijvoorbeeld `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Zie Resources implementeren met [ARM-sjablonen](./deploy-cli.md#parameters)en Azure CLI voor meer informatie. Voor het _implementeren van BICEP-bestanden_ hebt u Azure CLI versie 2.20 of hoger nodig.

Vanaf Azure PowerShell u een lokaal parameterbestand doorgeven met behulp van de `TemplateParameterFile` parameter .

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Zie Deploy [resources with ARM templates (Resources implementeren met ARM-sjablonen) en Azure PowerShell .](./deploy-powershell.md#pass-parameter-values) Als u _.bicep-bestanden_ wilt implementeren, Azure PowerShell versie 5.6.0 of hoger gebruiken.

> [!NOTE]
> Het is niet mogelijk om een parameterbestand te gebruiken met de aangepaste sjabloonblade in de portal.

> [!TIP]
> Als u het [Azure-resourcegroepproject in Visual Studio](create-visual-studio-deployment-project.md)gebruikt, moet u ervoor zorgen dat voor het parameterbestand de buildactie **is** ingesteld op **Inhoud**.

## <a name="file-name"></a>Bestandsnaam

De algemene naamconventie voor het parameterbestand is om _parameters op te nemen_ in de sjabloonnaam. Als de naam van uw sjabloon bijvoorbeeld is _azuredeploy.jsop_, heeft uw parameterbestand de _azuredeploy.parameters.jsop_. Met deze naamconventie kunt u de verbinding tussen de sjabloon en de parameters bekijken.

Als u wilt implementeren in verschillende omgevingen, maakt u meer dan één parameterbestand. Wanneer u de parameterbestanden een naam geeft, identificeert u het gebruik ervan, zoals ontwikkeling en productie. Gebruik bijvoorbeeld deazuredeploy.parameters-dev.js _aan_ enazuredeploy.parameters-prod.js _resources_ te implementeren.

## <a name="parameter-precedence"></a>Parameter prioriteit

U kunt inlineparameters en een lokaal parameterbestand gebruiken in dezelfde implementatiebewerking. U kunt bijvoorbeeld enkele waarden opgeven in het lokale parameterbestand en tijdens de implementatie inline andere waarden toevoegen. Als u waarden opgeeft voor een parameter in zowel het lokale parameterbestand als inline, heeft de inlinewaarde prioriteit.

Het is mogelijk om een extern parameterbestand te gebruiken door de URI aan het bestand op te geven. Wanneer u een extern parameterbestand gebruikt, kunt u geen andere waarden inline of uit een lokaal bestand doorgeven. Alle inlineparameters worden genegeerd. Geef alle parameterwaarden op in het externe bestand.

## <a name="parameter-name-conflicts"></a>Parameternaamconflicten

Als uw sjabloon een parameter bevat met dezelfde naam als een van de parameters in de PowerShell-opdracht, geeft PowerShell de parameter uit uw sjabloon weer met het postfix `FromTemplate` . Een parameter met de naam `ResourceGroupName` in uw sjabloon conflicteert bijvoorbeeld met de `ResourceGroupName` parameter in de cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) U wordt gevraagd om een waarde op te geven voor `ResourceGroupNameFromTemplate` . Gebruik parameternamen die niet worden gebruikt voor implementatieopdrachten om deze verwarring te voorkomen.

## <a name="next-steps"></a>Volgende stappen

- Zie Parameters in ARM-sjablonen voor meer informatie over het definiëren van [parameters in een sjabloon.](template-parameters.md)
- Zie Use Azure Key Vault to pass secure parameter value during deployment (Beveiligde parameterwaarde doorgeven tijdens de implementatie) voor meer informatie over het gebruik van waarden [uit een sleutelkluis.](key-vault-parameter.md)
