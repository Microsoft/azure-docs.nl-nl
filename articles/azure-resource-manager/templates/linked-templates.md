---
title: Koppelings sjablonen voor implementatie
description: Hierin wordt beschreven hoe u gekoppelde sjablonen gebruikt in een Azure Resource Manager sjabloon (ARM-sjabloon) om een modulaire sjabloon oplossing te maken. Toont hoe parameter waarden worden door gegeven, geef een parameter bestand op en dynamisch gemaakte Url's.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: aae3947656e475d15bc4f0da770d0398fafa13c5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880425"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources

Als u complexe oplossingen wilt implementeren, kunt u uw Azure Resource Manager-sjabloon (ARM-sjabloon) in veel gerelateerde sjablonen opsplitsen en vervolgens samen met een hoofd sjabloon implementeren. De gerelateerde sjablonen kunnen afzonderlijke bestanden of sjabloon syntaxis zijn die is inge sloten in de hoofd sjabloon. In dit artikel wordt gebruikgemaakt van de term **gekoppelde sjabloon** om te verwijzen naar een afzonderlijk sjabloon bestand waarnaar wordt verwezen via een koppeling vanuit de hoofd sjabloon. De term **geneste sjabloon** wordt gebruikt om te verwijzen naar de Inge sloten sjabloon syntaxis binnen de hoofd sjabloon.

Voor kleine tot middelgrote oplossingen is één sjabloon eenvoudiger te begrijpen en te onderhouden. U kunt alle resources en waarden in één bestand bekijken. Voor geavanceerde scenario's kunt u met gekoppelde sjablonen de oplossing opsplitsen in de beoogde onderdelen. U kunt deze sjablonen eenvoudig opnieuw gebruiken voor andere scenario's.

Zie [zelf studie: een gekoppelde sjabloon implementeren](./deployment-tutorial-linked-template.md)voor een zelf studie.

> [!NOTE]
> Voor gekoppelde of geneste sjablonen kunt u de implementatie modus alleen op [Incrementeel](deployment-modes.md)instellen. De hoofd sjabloon kan echter in de volledige modus worden geïmplementeerd. Als u de hoofd sjabloon in de modus volledig implementeert en de gekoppelde of geneste sjabloon is gericht op dezelfde resource groep, worden de resources die zijn geïmplementeerd in de gekoppelde of geneste sjabloon, opgenomen in de evaluatie van de implementatie van de volledige modus. De gecombineerde verzameling resources die zijn geïmplementeerd in de hoofd sjabloon en gekoppelde of geneste sjablonen worden vergeleken met de bestaande resources in de resource groep. Alle resources die niet in deze gecombineerde verzameling zijn opgenomen, worden verwijderd.
>
> Als de gekoppelde of geneste sjabloon is gericht op een andere resource groep, gebruikt die implementatie een incrementele modus.
>

## <a name="nested-template"></a>Geneste sjabloon

Als u een sjabloon wilt nesten, moet u een implementatie van een [resource](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de `template` eigenschap de syntaxis van de sjabloon op.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

In het volgende voor beeld wordt een opslag account met een geneste sjabloon geïmplementeerd.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Expressie-evaluatie bereik in geneste sjablonen

Wanneer u een geneste sjabloon gebruikt, kunt u opgeven of sjabloonexpressies worden geëvalueerd binnen het bereik van de bovenliggende sjabloon of de geneste sjabloon. Het bereik bepaalt hoe para meters, variabelen en functies, zoals [resourceGroup](template-functions-resource.md#resourcegroup) en [abonnementen](template-functions-resource.md#subscription) , worden opgelost.

U stelt het bereik in via de `expressionEvaluationOptions` eigenschap. De `expressionEvaluationOptions` eigenschap is standaard ingesteld op `outer` , wat inhoudt dat het bereik van de bovenliggende sjabloon wordt gebruikt. Stel de waarde in op om te `inner` zorgen dat expressies worden geëvalueerd binnen het bereik van de geneste sjabloon.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

> [!NOTE]
>
> Wanneer Scope is ingesteld op `outer` , kunt u de functie niet gebruiken `reference` in de sectie outputs van een geneste sjabloon voor een resource die u in de geneste sjabloon hebt geïmplementeerd. Als u de waarden voor een geïmplementeerde resource in een geneste sjabloon wilt retour neren, gebruikt u `inner` bereik of converteert u uw geneste sjabloon naar een gekoppelde sjabloon.

In de volgende sjabloon ziet u hoe sjabloon expressies worden omgezet volgens het bereik. Het bevat een variabele met `exampleVar` de naam die is gedefinieerd in zowel de bovenliggende sjabloon als de geneste sjabloon. Hiermee wordt de waarde van de variabele geretourneerd.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

De waarde van `exampleVar` wijzigingen, afhankelijk van de waarde van de `scope` eigenschap in `expressionEvaluationOptions` . In de volgende tabel ziet u de resultaten voor beide bereiken.

| Evaluatie bereik | Uitvoer |
| ----- | ------ |
| wend | van geneste sjabloon |
| Outer (of standaard) | van bovenliggende sjabloon |

In het volgende voor beeld wordt een SQL Server geïmplementeerd en wordt een sleutel kluis geheim opgehaald dat moet worden gebruikt voor het wacht woord. Het bereik is ingesteld op `inner` omdat het de sleutel kluis-id dynamisch maakt (Zie `adminPassword.reference.keyVault` in de buitenste sjablonen `parameters` ) en geeft deze door als een para meter voor de geneste sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Wees voorzichtig met het gebruik van beveiligde parameter waarden in een geneste sjabloon. Als u het bereik instelt op Outer, worden de beveiligde waarden opgeslagen als tekst zonder opmaak in de implementatie geschiedenis. Een gebruiker die de sjabloon in de implementatie geschiedenis bekijkt, kan de beveiligde waarden zien. Gebruik in plaats daarvan het binnenste bereik of Voeg aan de bovenliggende sjabloon de resources toe die beveiligde waarden nodig hebben.

Het volgende fragment toont welke waarden veilig zijn en wat niet veilig is.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>Een gekoppelde sjabloon

Als u een sjabloon wilt koppelen, moet u een implementatie van een [resource](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de `templateLink` eigenschap de URI op van de sjabloon die u wilt toevoegen. Het volgende voor beeld is een koppeling naar een sjabloon in een opslag account.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Bij het verwijzen naar een gekoppelde sjabloon kan de waarde van `uri` geen lokaal bestand zijn of een bestand dat alleen beschikbaar is op uw lokale netwerk. Azure Resource Manager moet toegang hebben tot de sjabloon. Geef een URI-waarde op die kan worden gedownload als HTTP of HTTPS.

U kunt verwijzen naar sjablonen met behulp van para meters die HTTP of HTTPS bevatten. Een voor beeld: een gemeen schappelijk patroon is het gebruik van de `_artifactsLocation` para meter. U kunt de gekoppelde sjabloon instellen met een expressie als:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Als u een koppeling maakt naar een sjabloon in GitHub, gebruikt u de onbewerkte URL. De koppeling heeft de volgende indeling: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Als u de RAW-koppeling wilt ophalen, selecteert u **RAW**.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Onbewerkte URL selecteren":::

### <a name="parameters-for-linked-template"></a>Para meters voor gekoppelde sjabloon

U kunt de para meters voor de gekoppelde sjabloon opgeven in een extern bestand of in inline. Als u een extern parameter bestand wilt opgeven, gebruikt u de `parametersLink` eigenschap:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Gebruik de eigenschap om parameter waarden inline door te geven `parameters` .

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

U kunt niet zowel inline-para meters als een koppeling naar een parameter bestand gebruiken. De implementatie mislukt met een fout als beide `parametersLink` en `parameters` zijn opgegeven.

### <a name="use-relative-path-for-linked-templates"></a>Relatief pad gebruiken voor gekoppelde sjablonen

`relativePath`Met de eigenschap van `Microsoft.Resources/deployments` kunt u gemakkelijker gekoppelde sjablonen maken. Deze eigenschap kan worden gebruikt om een extern gekoppelde sjabloon te implementeren op een locatie ten opzichte van het bovenliggende item. Deze functie vereist dat alle sjabloon bestanden worden klaargezet en beschikbaar zijn op een externe URI, zoals GitHub of Azure Storage-account. Wanneer de hoofd sjabloon wordt aangeroepen met behulp van een URI van Azure PowerShell of Azure CLI, is de onderliggende implementatie-URI een combi natie van de bovenliggende en relativePath.

> [!NOTE]
> Wanneer u een templateSpec maakt, worden de sjablonen die door de `relativePath` eigenschap worden verwezen, verpakt in de templateSpec-resource door Azure PowerShell of Azure cli. Het is niet vereist dat de bestanden gefaseerd worden uitgevoerd. Zie [een sjabloon specificatie maken met gekoppelde sjablonen](./template-specs.md#create-a-template-spec-with-linked-templates)voor meer informatie.

Ga als volgt te voor een mapstructuur:

![relatief pad van gekoppelde sjabloon van Resource Manager](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

In de volgende sjabloon ziet u hoe *mainTemplate.jsop* implements *nestedChild.js* wordt weer gegeven in de voor gaande afbeelding.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

In de volgende implementatie is de URI van de gekoppelde sjabloon in de voor gaande sjabloon **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

Als u gekoppelde sjablonen wilt implementeren met een relatief pad dat is opgeslagen in een Azure-opslag account, gebruikt u de `QueryString` / `query-string` para meter om de SAS-token op te geven die moet worden gebruikt met de para meter TemplateUri. Deze para meter wordt alleen ondersteund door Azure CLI versie 2,18 of hoger en Azure PowerShell versie 5,4 of hoger.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

Zorg ervoor dat er geen voorloop '? ' voor komt in de query string. De implementatie voegt één toe wanneer de URI voor de implementaties wordt geassembleerd.

## <a name="template-specs"></a>Sjabloonspecificaties

In plaats van uw gekoppelde sjablonen te onderhouden op een toegankelijk eind punt, kunt u een [sjabloon specificatie](template-specs.md) maken waarmee de hoofd sjabloon en de gekoppelde sjablonen worden verpakt in één entiteit die u kunt implementeren. De sjabloon specificatie is een resource in uw Azure-abonnement. Het is eenvoudig om de sjabloon veilig te delen met gebruikers in uw organisatie. U gebruikt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) om toegang te verlenen tot de sjabloon specificatie. Deze functie is momenteel beschikbaar als preview-versie.

Zie voor meer informatie:

- [Zelf studie: een sjabloon specificatie met gekoppelde sjablonen maken](./template-specs-create-linked.md).
- [Zelf studie: een sjabloon specificatie als gekoppelde sjabloon implementeren](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Afhankelijkheden

Net als bij andere resource typen kunt u afhankelijkheden instellen tussen de gekoppelde sjablonen. Als de resources in één gekoppelde sjabloon moeten worden geïmplementeerd vóór resources in een tweede gekoppelde sjabloon, stelt u de tweede sjabloon in die afhankelijk is van de eerste.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

U hoeft de `contentVersion` eigenschap voor de eigenschap of niet op te geven `templateLink` `parametersLink` . Als u geen opgeeft `contentVersion` , wordt de huidige versie van de sjabloon geïmplementeerd. Als u een waarde voor de inhouds versie opgeeft, moet deze overeenkomen met de versie in de gekoppelde sjabloon. anders mislukt de implementatie met een fout.

## <a name="using-variables-to-link-templates"></a>Variabelen gebruiken om sjablonen te koppelen

In de vorige voor beelden zijn in code vastgelegde URL-waarden voor de sjabloon koppelingen weer gegeven. Deze benadering werkt mogelijk voor een eenvoudige sjabloon, maar werkt niet goed voor een groot aantal modulaire sjablonen. In plaats daarvan kunt u een statische variabele maken om een basis-URL voor de hoofd sjabloon op te slaan en vervolgens dynamisch Url's te maken voor de gekoppelde sjablonen van die basis-URL. Het voor deel van deze benadering is dat u de sjabloon eenvoudig kunt verplaatsen of splitsen omdat u alleen de statische variabele in de hoofd sjabloon hoeft te wijzigen. In de hoofd sjabloon worden de juiste Uri's door gegeven in de sjabloon die is samengesteld.

In het volgende voor beeld ziet u hoe u een basis-URL gebruikt om twee Url's te maken voor gekoppelde sjablonen ( `sharedTemplateUrl` en `vmTemplateUrl` ).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

U kunt ook [Implementatie ()](template-functions-deployment.md#deployment) gebruiken om de basis-URL voor de huidige sjabloon op te halen en gebruiken om de URL voor andere sjablonen op dezelfde locatie op te halen. Deze aanpak is nuttig als de locatie van uw sjabloon verandert of als u wilt voor komen dat de Url's van harde code in het sjabloon bestand worden gebruikt. De `templateLink` eigenschap wordt alleen geretourneerd wanneer u een koppeling maakt naar een externe sjabloon met een URL. Als u een lokale sjabloon gebruikt, is deze eigenschap niet beschikbaar.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Uiteindelijk gebruikt u de variabele in de `uri` eigenschap van een `templateLink` eigenschap.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Kopiëren gebruiken

Als u meerdere exemplaren van een resource met een geneste sjabloon wilt maken, voegt u het `copy` element toe op het niveau van de `Microsoft.Resources/deployments` resource. Als de scope zo is `inner` , kunt u de kopie binnen de geneste sjabloon toevoegen.

In de volgende voorbeeld sjabloon ziet u hoe u `copy` met een geneste sjabloon kunt gebruiken.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>Waarden ophalen uit de gekoppelde sjabloon

Als u een uitvoer waarde van een gekoppelde sjabloon wilt ophalen, haalt u de eigenschaps waarde op met de syntaxis zoals: `"[reference('deploymentName').outputs.propertyName.value]"` .

Bij het ophalen van een uitvoer eigenschap van een gekoppelde sjabloon, mag de naam van de eigenschap geen streepje bevatten.

De volgende voor beelden laten zien hoe u kunt verwijzen naar een gekoppelde sjabloon en een uitvoer waarde kunt ophalen. De gekoppelde sjabloon retourneert een eenvoudig bericht. Eerst de gekoppelde sjabloon:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

Met de hoofd sjabloon wordt de gekoppelde sjabloon geïmplementeerd en wordt de geretourneerde waarde opgehaald. Er wordt verwezen naar de implementatie bron op naam en de naam van de eigenschap die wordt geretourneerd door de gekoppelde sjabloon.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

In het volgende voor beeld ziet u een sjabloon waarmee een openbaar IP-adres wordt geïmplementeerd en de resource-ID van de Azure-resource wordt geretourneerd voor die open bare IP:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Als u het open bare IP-adres uit de vorige sjabloon wilt gebruiken bij het implementeren van een load balancer, koppelt u deze aan de sjabloon en declareert u een afhankelijkheid van de `Microsoft.Resources/deployments` resource. Het open bare IP-adres op de load balancer wordt ingesteld op de uitvoer waarde van de gekoppelde sjabloon.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Implementatie geschiedenis

Resource Manager verwerkt elke sjabloon als een afzonderlijke implementatie in de implementatie geschiedenis. Een hoofd sjabloon met drie gekoppelde of geneste sjablonen wordt weer gegeven in de implementatie geschiedenis als:

![Implementatie geschiedenis](./media/linked-templates/deployment-history.png)

U kunt deze afzonderlijke vermeldingen in de geschiedenis gebruiken om uitvoer waarden na de implementatie op te halen. Met de volgende sjabloon maakt u een openbaar IP-adres en voert u het IP-adres uit:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

De volgende sjabloon is een koppeling naar de vorige sjabloon. Er worden drie open bare IP-adressen gemaakt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Na de implementatie kunt u de uitvoer waarden ophalen met het volgende Power shell-script:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Of Azure CLI-script in een bash-shell:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Een externe sjabloon beveiligen

Hoewel de gekoppelde sjabloon extern beschikbaar moet zijn, hoeft deze niet algemeen beschikbaar te zijn voor het publiek. U kunt uw sjabloon toevoegen aan een privé-opslag account dat alleen toegankelijk is voor de eigenaar van het opslag account. Vervolgens maakt u een SAS-token (Shared Access Signature) om toegang tijdens de implementatie in te scha kelen. U voegt die SAS-token toe aan de URI voor de gekoppelde sjabloon. Hoewel het token wordt door gegeven als een beveiligde teken reeks, wordt de URI van de gekoppelde sjabloon, met inbegrip van de SAS-token, geregistreerd in de implementatie bewerkingen. Stel een verloop voor het token in om de bloot stelling te beperken.

Het parameter bestand kan ook worden beperkt tot toegang via een SAS-token.

Op dit moment kunt u geen koppeling maken met een sjabloon in een opslag account dat zich achter een [Azure Storage firewall](../../storage/common/storage-network-security.md)bevindt.

> [!IMPORTANT]
> In plaats van uw gekoppelde sjabloon met een SAS-token te beveiligen, kunt u overwegen om een [sjabloon specificatie](template-specs.md)te maken. De sjabloon spec slaat de hoofd sjabloon en de gekoppelde sjablonen veilig op als een resource in uw Azure-abonnement. U kunt Azure RBAC gebruiken om toegang te verlenen aan gebruikers die de sjabloon moeten implementeren.

In het volgende voor beeld ziet u hoe u een SAS-token kunt door geven wanneer u een koppeling maakt naar een sjabloon:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

In Power shell krijgt u een token voor de container en implementeert u de sjablonen met de volgende opdrachten. U ziet dat de `containerSasToken` para meter is gedefinieerd in de sjabloon. Het is geen para meter in de `New-AzResourceGroupDeployment` opdracht.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Voor Azure CLI in een bash-shell krijgt u een token voor de container en implementeert u de sjablonen met de volgende code:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Voorbeeld sjablonen

In de volgende voor beelden ziet u veelvoorkomende toepassingen van gekoppelde sjablonen.

|Hoofd sjabloon  |Een gekoppelde sjabloon |Beschrijving  |
|---------|---------| ---------|
|[Hallo wereld](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retourneert een teken reeks uit een gekoppelde sjabloon. |
|[Load Balancer met openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Hiermee wordt het open bare IP-adres uit de gekoppelde sjabloon geretourneerd en wordt die waarde ingesteld in load balancer. |
|[Meerdere IP-adressen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [gekoppelde sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Maakt verschillende open bare IP-adressen in gekoppelde sjabloon.  |

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een gekoppelde sjabloon implementeren](./deployment-tutorial-linked-template.md)als u een zelf studie wilt door lopen.
* Zie [de order voor het implementeren van resources in arm-sjablonen definiëren](define-resource-dependency.md)voor meer informatie over het definiëren van de implementatie volgorde voor uw resources.
* Zie [resource-iteratie in arm-sjablonen](copy-resources.md)voor meer informatie over het definiëren van één resource, maar het maken van een groot aantal exemplaren.
* Voor stappen voor het instellen van een sjabloon in een opslag account en het genereren van een SAS-token raadpleegt u [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md) of [resources implementeren met arm-sjablonen en Azure cli](deploy-cli.md).
