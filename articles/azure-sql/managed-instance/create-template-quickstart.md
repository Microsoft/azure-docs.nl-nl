---
title: 'Azure Resource Manager: Een Azure SQL Managed Instance maken'
description: Meer informatie over het maken van een Azure SQL Managed Instance met behulp van een Azure Resource Manager-sjabloon.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283340"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Quickstart: Een Azure SQL Managed Instance maken met behulp van een ARM-sjabloon

Deze quickstart is gericht op het implementeren van een ARM-sjabloon (Azure Resource Manager-sjabloon) voor het maken van een Azure SQL Managed Instance en vNet. [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) is een intelligente, volledig beheerde, schaalbare clouddatabase met bijna 100% functiepariteit met de database-engine van SQL Server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Deze resources worden in de sjabloon gedefinieerd:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

Meer voorbeelden van sjablonen vindt u in [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer **Proberen** in het volgende PowerShell-codeblok om Azure Cloud Shell te openen.

> [!IMPORTANT]
> Het implementeren van een beheerd exemplaar is een langdurende bewerking. De implementatie van het eerste exemplaar in het subnet duurt doorgaans veel langer dan de implementatie in een subnet met bestaande beheerde exemplaren. Raadpleeg [Bewerkingen voor SQL Managed Instance-beheer](sql-managed-instance-paas-overview.md#management-operations) voor de gemiddelde inrichtingsduur.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Ga naar de [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) en controleer of het beheerde exemplaar zich in de geselecteerde resourcegroep bevindt. Omdat het maken van een beheerd exemplaar enige tijd kan duren, moet u mogelijk de koppeling **Implementaties** op de pagina **Overzicht** van uw resourcegroep controleren.

- Zie [Een verbinding vanaf een virtuele Azure-machine configureren](connect-vm-instance-configure.md) voor een quickstart over verbinding maken met SQL Managed Instance vanaf een virtuele Azure-machine.
- Zie [Een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een quickstart over verbinding maken met SQL Managed Instance vanaf een on-premises clientcomputer met behulp van een punt-naar-site-verbinding.

## <a name="clean-up-resources"></a>Resources opschonen

Behoud het beheerde exemplaar als u verder wilt gaan met de [volgende stappen](#next-steps), maar verwijder het beheerde exemplaar en de bijbehorende resources nadat u eventuele andere zelfstudies hebt voltooid. Zie [Een subnet verwijderen na het verwijderen van een beheerd exemplaar](virtual-cluster-delete.md) nadat u een beheerd exemplaar hebt verwijderd.


Zo verwijdert u de resourcegroep:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure virtuele machine configureren om verbinding te maken met SQL Managed Instance](connect-vm-instance-configure.md)
