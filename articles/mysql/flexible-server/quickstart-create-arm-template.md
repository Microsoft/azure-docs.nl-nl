---
title: 'Quickstart: Een Azure Database for MySQL - Flexible Server maken: ARM-sjabloon'
description: In deze quickstart leert u hoe u een Azure Database for MySQL - Flexible Server maakt met behulp van een ARM-sjabloon.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: 5be0bf21514dd92c2f980081bb57d873895bbb91
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099928"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Quickstart: Een ARM-sjabloon gebruiken om een Azure Database for MySQL - Flexible Server (Preview) te maken

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Azure Database for MySQL - Flexible Server (Preview) is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. U kunt een ARM-sjabloon (Azure Resource Manager) gebruiken om een flexibele server in te richten om meerdere servers of meerdere databases op een server te implementeren.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>De sjabloon controleren

Een flexibele Azure Database for MySQL-server is de bovenliggende resource voor een of meer databases in een regio. Deze levert de beheerbeleidsregels die van toepassing zijn op de betreffende databases: aanmeldingen, firewall, gebruikers, rollen en configuraties.

Maak een _mysql-flexible-server-template.json_-bestand en kopieer dit JSON-script hierin.

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
      "type": "string",
      "defaultValue": "Disabled"
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
    "api": "2020-07-01-preview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
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
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
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

- Microsoft.DBforMySQL/flexibleServers

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer **Proberen** in het volgende PowerShell-codeblok om [Azure Cloud Shell](../../cloud-shell/overview.md) te openen.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Volg deze stappen om te controleren of uw server in Azure is gemaakt.

### <a name="azure-portal"></a>Azure Portal

1. Zoek en selecteer **Azure Database for MySQL-servers** in de [Azure-portal](https://portal.azure.com).
1. Selecteer uw nieuwe server in de lijst met databases. De pagina **Overzicht** voor uw nieuwe Azure Database for MySQL-server wordt weergegeven.

### <a name="powershell"></a>PowerShell

U moet de naam van de nieuwe server invoeren om de details van uw flexibele Azure Database for MySQL-server weer te geven.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

U moet de naam en de resourcegroep van de nieuwe server invoeren om details over uw flexibele Azure Database for MySQL-server weer te geven.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```
## <a name="exporting-arm-template-from-the-portal"></a>ARM-sjabloon vanuit de portal exporteren
U kunt [een arm-sjabloon exporteren](../../azure-resource-manager/templates/export-template-portal.md) vanuit de Azure Portal. Er zijn twee manieren om een sjabloon te exporteren:

- [Exporteren uit resource groep of resource](../../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Met deze optie wordt een nieuwe sjabloon gegenereerd op basis van bestaande resources. De geëxporteerde sjabloon is een moment opname van de huidige status van de resource groep. U kunt een volledige resource groep exporteren of specifieke resources binnen die resource groep.
- [Exporteren vóór implementatie of van geschiedenis](../../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Met deze optie wordt een exacte kopie van een sjabloon opgehaald die wordt gebruikt voor de implementatie.

Bij het exporteren van de sjabloon ```"parameters":{ }``` ziet u in de sectie van de sjabloon dat ```administratorLogin``` en ```administratorLoginPassword``` zal niet om veiligheids redenen worden opgenomen. U **moet** deze para meters aan uw sjabloon toevoegen voordat u de sjabloon implementeert, anders mislukt de sjabloon.

```
"administratorLogin": {
      "type": "String"
    },
"administratorLoginPassword": {
      "type": "SecureString"
    },
```

## <a name="clean-up-resources"></a>Resources opschonen

Behoud deze resourcegroep, server en individuele database als u naar de [Volgende stappen](#next-steps) wilt gaan. De volgende stappen laten zien hoe u verbinding maakt met en een query uitvoert op uw database met behulp van verschillende methoden.

Zo verwijdert u de resourcegroep:

### <a name="azure-portal"></a>Azure Portal

1. Zoek en selecteer [Resourcegroepen](https://portal.azure.com) in **Azure Portal**.
1. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.
1. Selecteer op de pagina **Overzicht** van uw resourcegroep de optie **Resourcegroep verwijderen**.
1. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een ARM-sjabloon leidt:

> [!div class="nextstepaction"]
> [Uw eerste ARM-sjabloon maken en implementeren](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Zie voor een stapsgewijze zelfstudie voor het bouwen van een app met App Service met behulp van MySQL:

> [!div class="nextstepaction"]
>[Een PHP-web-app (Laravel) bouwen met MySQL](tutorial-php-database-app.md)
