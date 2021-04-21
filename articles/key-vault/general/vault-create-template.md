---
title: Een Azure-sleutelkluis en een toegangsbeleid voor de kluis maken met behulp van een ARM-sjabloon
description: In dit artikel wordt beschreven hoe u Azure-sleutelkluizen en toegangsbeleid voor kluizen maakt met behulp van een Azure Resource Manager sjabloon.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: d157419614ee3a3f89036177e962e5b7fc4466b2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815027"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Een Azure-sleutelkluis en toegangsbeleid voor de kluis maken met behulp van een Resource Manager sjabloon

[Azure Key Vault](../general/overview.md) is een cloudservice die een beveiligd opslag voor geheimen zoals sleutels, wachtwoorden en certificaten biedt. In dit artikel wordt het proces beschreven voor het implementeren van Azure Resource Manager arm-sjabloon (ARM-sjabloon) voor het maken van een sleutelkluis.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

De stappen in dit artikel voltooien:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.


## <a name="create-a-key-vault-resource-manager-template"></a>Een sjabloon Key Vault Resource Manager maken

De volgende sjabloon toont een eenvoudige manier om een sleutelkluis te maken. Sommige waarden worden opgegeven in de sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
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
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
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
    }
  ]
}

```

Zie voor meer informatie Key Vault arm-sjablooninstellingen [Key Vault ARM-sjabloonverwijzing.](/azure/templates/microsoft.keyvault/vaults)

> [!IMPORTANT]
> Als een sjabloon opnieuw wordt geïmplementeerd, worden bestaande toegangsbeleidsregels in de sleutelkluis overgenomen. U wordt aangeraden de eigenschap te vullen met bestaand toegangsbeleid om te voorkomen dat `accessPolicies` de toegang tot de sleutelkluis verloren gaat. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Een toegangsbeleid toevoegen aan een Key Vault Resource Manager sjabloon

U kunt toegangsbeleid implementeren in een bestaande sleutelkluis zonder de volledige sleutelkluissjabloon opnieuw te implementeren. De volgende sjabloon toont een eenvoudige manier om toegangsbeleid te maken:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Zie voor meer informatie over Key Vault-sjablooninstellingen Key Vault [ARM-sjabloonverwijzing.](/azure/templates/microsoft.keyvault/vaults/accesspolicies)

## <a name="more-key-vault-resource-manager-templates"></a>Meer Key Vault Resource Manager sjablonen

Er zijn andere Resource Manager beschikbaar voor Key Vault-objecten:

| Geheimen | Sleutels | Certificaten |
|--|--|--|
|<ul><li>[Snelstartgids](../secrets/quick-create-template.md)<li>[Verwijzing](/azure/templates/microsoft.keyvault/vaults/secrets)|N.v.t.|N.v.t.|

Meer informatie over sjablonen Key Vault vindt u hier: [Key Vault Resource Manager referentie](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>De sjablonen implementeren

U kunt de Azure Portal gebruiken om de voorgaande sjablonen te implementeren met behulp van de optie Uw eigen sjabloon **bouwen in editor,** zoals hier wordt beschreven: Resources implementeren op basis van een [aangepaste sjabloon.](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

U kunt de voorgaande sjablonen ook opslaan in bestanden en de volgende opdrachten gebruiken:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met volgende quickstarts en zelfstudies, kunt u deze resources laten staan. Wanneer u de resources niet meer nodig hebt, verwijdert u de resourcegroep. Als u de groep verwijdert, worden ook de sleutelkluis en gerelateerde resources verwijderd. Als u de resourcegroep wilt verwijderen met behulp van de Azure CLI of Azure PowerShell, moet u deze stappen uitvoeren:

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

## <a name="resources"></a>Resources

- Lees een [overzicht van Azure Key Vault](../general/overview.md).
- Meer informatie over [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Raadpleeg het [Overzicht voor Azure Key Vault-beveiliging](security-features.md)

## <a name="next-steps"></a>Volgende stappen

- [Veilige toegang tot een sleutelkluis](security-features.md)
- [Verifiëren bij een sleutelkluis](authentication.md)
- [Azure Key Vault ontwikkelaarshandleiding](developers-guide.md)
