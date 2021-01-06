---
title: Een Azure-sleutel kluis en een kluis toegangs beleid maken met behulp van ARM-sjabloon
description: In dit artikel wordt beschreven hoe u Azure-sleutel kluizen en kluis toegangs beleid maakt met behulp van een Azure Resource Manager sjabloon.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: e19277aa3639031371e0e8dd28f4dd849efd4597
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97933928"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Een Azure-sleutel kluis en een beleid voor kluis toegang maken met behulp van een resource manager-sjabloon

[Azure Key Vault](../general/overview.md) is een Cloud service die een veilig archief levert voor geheimen zoals sleutels, wacht woorden en certificaten. In dit artikel wordt het proces beschreven voor het implementeren van een Azure Resource Manager sjabloon (ARM-sjabloon) om een sleutel kluis te maken.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="create-a-key-vault-resource-manager-template"></a>Een Key Vault Resource Manager-sjabloon maken

In de volgende sjabloon ziet u een eenvoudige manier om een sleutel kluis te maken. Sommige waarden zijn opgegeven in de sjabloon.

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

Zie voor meer informatie over Key Vault-sjabloon instellingen [Key Vault arm-sjabloon verwijzing](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Als een sjabloon opnieuw wordt geïmplementeerd, worden alle bestaande toegangs beleid in de sleutel kluis genegeerd. U wordt aangeraden de `accessPolicies` eigenschap met een bestaand toegangs beleid in te vullen om te voor komen dat de toegang tot de sleutel kluis verloren gaat. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Een toegangs beleid toevoegen aan een Key Vault Resource Manager-sjabloon

U kunt toegangs beleid implementeren voor een bestaande sleutel kluis zonder dat u de volledige sleutel kluis sjabloon opnieuw hoeft te implementeren. De volgende sjabloon toont een eenvoudige manier om toegangs beleid te maken:

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
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Zie voor meer informatie over Key Vault-sjabloon instellingen [Key Vault arm-sjabloon verwijzing](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Meer Key Vault Resource Manager-sjablonen

Er zijn andere Resource Manager-sjablonen beschikbaar voor Key Vault objecten:

| Geheimen | Sleutels | Certificaten |
|--|--|--|
|<ul><li>[Snelstartgids](../secrets/quick-create-template.md)<li>[Verwijzing](/azure/templates/microsoft.keyvault/vaults/secrets)|N.v.t.|N.v.t.|

U kunt hier meer Key Vault sjablonen vinden: [Key Vault Resource Manager-referentie](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>De sjablonen implementeren

U kunt de Azure Portal gebruiken om de voor gaande sjablonen te implementeren met behulp van de optie **uw eigen sjabloon bouwen in editor** , zoals hier wordt beschreven: [resources implementeren vanuit een aangepaste sjabloon](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

U kunt de voor gaande sjablonen ook opslaan in bestanden en deze opdrachten gebruiken:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met de volgende Quick starts en zelf studies, kunt u deze resources op locatie laten staan. Als u de resources niet langer nodig hebt, verwijdert u de resource groep. Als u de groep verwijdert, worden de sleutel kluis en gerelateerde resources ook verwijderd. Voer de volgende stappen uit om de resource groep te verwijderen met behulp van Azure CLI of Azure PowerShell:

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
- Bekijk het [Azure Key Vault Security-overzicht](security-overview.md)

## <a name="next-steps"></a>Volgende stappen

- [Veilige toegang tot een sleutelkluis](secure-your-key-vault.md)
- [Verifiëren bij een sleutel kluis](authentication.md)
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)
