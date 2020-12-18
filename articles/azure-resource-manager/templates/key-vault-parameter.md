---
title: Geheim Key Vault met sjabloon
description: Laat zien hoe u tijdens de implementatie een geheim kunt door geven van een sleutel kluis als een para meter.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 8983d51d90e07e21ac4c9489c7ed553530278d3c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672702"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Azure Key Vault gebruiken om een beveiligde parameter waarde door te geven tijdens de implementatie

In plaats van een beveiligde waarde (zoals een wacht woord) rechtstreeks in uw sjabloon of parameter bestand te plaatsen, kunt u tijdens een implementatie de waarde van een [Azure Key Vault](../../key-vault/general/overview.md) ophalen. U haalt de waarde op door te verwijzen naar de sleutel kluis en het geheim in het parameter bestand. De waarde zelf wordt nooit getoond, omdat u alleen verwijst naar de sleutelkluis-id. De sleutel kluis kan zich in een ander abonnement bevinden dan de resource groep die u implementeert.

Dit artikel richt zich op het scenario voor het door geven van een gevoelige waarde in als een sjabloon parameter. Het scenario voor het instellen van een virtuele-machine-eigenschap is niet van toepassing op de URL van een certificaat in een Key Vault. Zie [een certificaat van Azure Key Vault op een virtuele machine installeren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)voor een Quick Start-sjabloon van dat scenario.

## <a name="deploy-key-vaults-and-secrets"></a>Sleutel kluizen en geheimen implementeren

Als u toegang wilt krijgen tot een sleutel kluis tijdens `enabledForTemplateDeployment` het implementeren van de sjabloon, stelt u de sleutel kluis in op `true` .

Als u al een Key Vault hebt, moet u ervoor zorgen dat sjabloon implementaties zijn toegestaan.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Als u een nieuw Key Vault wilt maken en een geheim wilt toevoegen, gebruikt u:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Als eigenaar van de sleutel kluis hebt u automatisch toegang tot het maken van geheimen. Als de gebruiker werkt met geheimen niet de eigenaar van de sleutel kluis, verleent u toegang met:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Zie voor meer informatie over het maken van sleutel kluizen en het toevoegen van geheimen:

- [Een geheim instellen en ophalen met behulp van CLI](../../key-vault/secrets/quick-create-cli.md)
- [Een geheim instellen en ophalen met behulp van Power shell](../../key-vault/secrets/quick-create-powershell.md)
- [Een geheim instellen en ophalen met behulp van de portal](../../key-vault/secrets/quick-create-portal.md)
- [Een geheim instellen en ophalen met behulp van .NET](../../key-vault/secrets/quick-create-net.md)
- [Een geheim instellen en ophalen met behulp van Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Toegang verlenen tot de geheimen

De gebruiker die de sjabloon implementeert, moet beschikken over de `Microsoft.KeyVault/vaults/deploy/action` machtiging voor het bereik van de resource groep en de sleutel kluis. De rollen [eigenaar](../../role-based-access-control/built-in-roles.md#owner) en [Inzender](../../role-based-access-control/built-in-roles.md#contributor) verlenen deze toegang. Als u de sleutel kluis hebt gemaakt, bent u de eigenaar, zodat u over de juiste machtigingen beschikt.

De volgende procedure laat zien hoe u een rol met de minimale machtiging maakt en hoe u de gebruiker toewijst

1. Een JSON-bestand met een aangepaste roldefinitie maken:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Vervang "00000000-0000-0000-0000-000000000000" door de abonnements-ID.

2. Maak de nieuwe rol met behulp van het JSON-bestand:

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    De voor beelden wijzen de aangepaste rol toe aan de gebruiker op het niveau van de resource groep.

Wanneer u een Key Vault gebruikt met de sjabloon voor een [beheerde toepassing](../managed-applications/overview.md), moet u toegang verlenen tot de service-principal van het **toestel bron provider** . Zie [toegang Key Vault geheim bij het implementeren van Azure Managed Applications](../managed-applications/key-vault-access.md)voor meer informatie.

## <a name="reference-secrets-with-static-id"></a>Referentie geheimen met een statische ID

Met deze methode verwijst u naar de sleutel kluis in het parameter bestand, niet de sjabloon. De volgende afbeelding laat zien hoe het parameter bestand verwijst naar het geheim en dat deze waarde wordt door gegeven aan de sjabloon.

![Diagram van de statische ID van de Resource Manager-sleutel kluis integratie](./media/key-vault-parameter/statickeyvault.png)

[Zelf studie: Azure Key Vault integreren in Resource Manager Sjabloonimlementatie](./template-tutorial-use-key-vault.md) gebruikt deze methode.

Met de volgende sjabloon wordt een SQL-Server geïmplementeerd met een beheerders wachtwoord. De wachtwoord parameter is ingesteld op een veilige teken reeks. Maar de sjabloon geeft niet op waar de waarde vandaan komt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Maak nu een parameter bestand voor de voor gaande sjabloon. Geef in het parameter bestand een para meter op die overeenkomt met de naam van de para meter in de sjabloon. Voor de parameter waarde verwijst u naar het geheim van de sleutel kluis. U verwijst naar het geheim door de resource-id van de sleutel kluis en de naam van het geheim door te geven:

In het volgende parameter bestand moet het sleutel kluis geheim al bestaan en kunt u een statische waarde voor de resource-ID opgeven.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

Als u een andere versie van het geheim wilt gebruiken dan de huidige versie, gebruikt u de `secretVersion` eigenschap.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implementeer de sjabloon en geef deze door in het parameter bestand:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Referentie geheimen met een dynamische ID

In de vorige sectie is geleerd hoe u een statische Resource-ID voor het sleutel kluis geheim kunt door geven uit de para meter. In sommige scenario's moet u echter verwijzen naar een sleutel kluis geheim dat afhankelijk is van de huidige implementatie. Of u wilt parameter waarden door geven aan de sjabloon in plaats van een verwijzings parameter in het parameter bestand te maken. In beide gevallen kunt u de resource-ID voor een sleutel kluis geheim dynamisch genereren met behulp van een gekoppelde sjabloon.

U kunt de resource-ID in het parameter bestand niet dynamisch genereren omdat sjabloon expressies niet zijn toegestaan in het parameter bestand.

In de bovenliggende sjabloon voegt u de geneste sjabloon toe en geeft u een para meter door die de dynamisch gegenereerde Resource-ID bevat. In de volgende afbeelding ziet u hoe een para meter in de gekoppelde sjabloon verwijst naar het geheim.

![Dynamische ID](./media/key-vault-parameter/dynamickeyvault.png)

Met de volgende sjabloon wordt de sleutel kluis-ID dynamisch gemaakt en door gegeven als para meter.

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
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
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
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor algemene informatie over sleutel kluizen.
- Zie [Key Vault-voor beelden](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)voor volledige voor beelden van het verwijzen naar sleutel geheimen.
- Zie [complexe Cloud implementaties beheren met behulp van geavanceerde arm-sjabloon functies](/learn/modules/manage-deployments-advanced-arm-template-features/)voor een Microsoft Learn module die betrekking heeft op het door geven van een beveiligde waarde van een sleutel kluis.
