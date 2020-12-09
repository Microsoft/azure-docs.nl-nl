---
title: 'Quickstart: Een Azure Database for PostgresSQL Flexible Server maken: ARM-sjabloon'
description: In deze quickstart leert u hoe u een Azure Database for PostgresSQL Flexible Server maakt met behulp van een ARM-sjabloon.
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: 542528bb0a3f76705e61f28338ccf1460159871d
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749076"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Quickstart: Een ARM-sjabloon gebruiken om een Azure Database for PostgreSQL te maken - Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Flexible Server is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. U kunt een ARM-sjabloon (Azure Resource Manager) gebruiken om een flexibele PostgreSQL-server in te richten om meerdere servers of meerdere databases op een server te implementeren.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account kunt maken, bijwerken en verwijderen. U kunt beheerfuncties gebruiken, zoals toegangscontrole, vergrendelingen en tags, om uw resources te beveiligen en te organiseren na de implementatie. Zie [Overzicht van sjabloonimplementatie](../../azure-resource-manager/templates/overview.md) voor meer informatie over Azure Resource Manager-sjablonen.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>De sjabloon controleren

Een Azure Database for PostgresSQL-server is de bovenliggende resource voor een of meer databases in een regio. Deze levert de beheerbeleidsregels die van toepassing zijn op de betreffende databases: aanmeldingen, firewall, gebruikers, rollen en configuraties.

Maak een _postgres-flexible-server-template.json_-bestand en kopieer er het volgende JSON-script in.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"        
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Deze resources worden in de sjabloon gedefinieerd:

- Microsoft.DBforPostgreSQL/flexibleServers

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer **Proberen** in het volgende PowerShell-codeblok om Azure Cloud Shell te openen.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Volg deze stappen om te controleren of uw server in Azure is gemaakt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Zoek en selecteer **Azure Database for PostgreSQL Flexible Servers (Preview)**  in de [Azure-portal](https://portal.azure.com).
1. Selecteer de nieuwe server in de databaselijst om de **Overzicht**-pagina voor het beheren van de server weer te geven.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Voer de naam van de nieuwe server in om de details van uw flexibele Azure Database for PostgreSQL-server weer te geven.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Voer de naam en de resourcegroep van de nieuwe server in om details over uw flexibele Azure Database for PostgreSQL-server weer te geven.

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Behoud deze resourcegroep, server en individuele database als u naar de [Volgende stappen](#next-steps) wilt gaan. De volgende stappen laten zien hoe u verbinding maakt met en een query uitvoert op uw database met behulp van verschillende methoden.

Zo verwijdert u de resourcegroep:

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer de resourcegroep die u wilt verwijderen in de [portal](https://portal.azure.com).

1. Selecteer **Resourcegroep verwijderen**.
1. Typ de naam van de resourcegroep om het verwijderen te bevestigen

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De database migreren met behulp van dump en herstel](../howto-migrate-using-dump-and-restore.md)
