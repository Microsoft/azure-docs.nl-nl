---
title: 'Azure-quickstart: een Azure-sleutelkluis en een sleutel maken met behulp van een Azure Resource Manager-sjabloon | Microsoft Docs'
description: In deze quickstart ontdekt u hoe u Azure-sleutelkluizen maakt, en hoe u hieraan sleutels toevoegt met behulp van een ARM-sjabloon (Azure Resource Manager).
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 048482c6b52d3fd9225224bd3b4ff3ee66bf24fd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815356"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>Quickstart: Een Azure-sleutelkluis en een sleutel maken met behulp van een ARM-sjabloon 

[Azure Key Vault](../general/overview.md) is een cloudservice die een veilig archief biedt voor geheimen, zoals sleutels, wachtwoorden, certificaten en andere geheimen. Deze quickstart behandelt het implementeren van een ARM-sjabloon (Azure Resource Manager-sjabloon) voor het maken van een sleutelkluis en een sleutel.

> [!NOTE]
> Deze functie is niet beschikbaar voor Azure Government.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van dit artikel, is het volgende vereist:

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Aan de gebruiker moet een ingebouwde Azure-rol worden toegewezen, bijvoorbeeld inzender. [Klik hier voor meer informatie](../../role-based-access-control/role-assignments-portal.md)
- Uw Azure AD-gebruikersobject-id is in de sjabloon nodig om machtigingen te kunnen configureren. Met de volgende procedure wordt de object-id (GUID) opgehaald.

    1. Voer de volgende Azure PowerShell- of de Azure CLI-opdracht uit door **Proberen** te selecteren en het script vervolgens in het shell-deelvenster te plakken. Plak het script door met de rechtermuisknop op de shell te klikken en **Plakken** te selecteren.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. Noteer de object-id. U hebt deze nodig in de volgende sectie van deze quickstart.

## <a name="review-the-template"></a>De sjabloon controleren

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

In de sjabloon worden twee resources gedefinieerd:

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

In [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular) vindt u meer voorbeelden van Azure Key Vault-sjablonen.

## <a name="deploy-the-template"></a>De sjabloon implementeren
U kunt [Azure Portal,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal)Azure PowerShell, Azure CLI of REST API. Zie Sjablonen implementeren voor meer informatie [over implementatiemethoden.](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt Azure Portal gebruiken om de sleutelkluis en de sleutel te controleren of het volgende Azure CLI- of Azure PowerShell-script gebruiken om de gemaakte sleutel weer te geven.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen met behulp van Azure CLI of Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis en een sleutel gemaakt met behulp van een ARM-sjabloon en hebt u de implementatie gevalideerd. Als u meer wilt weten over Key Vault en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Meer informatie over [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-features.md)
