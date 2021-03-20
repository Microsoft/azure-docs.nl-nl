---
title: 'Quickstart: een Azure Cosmos DB en een container maken met een Azure Resource Manager-sjabloon'
description: Quickstart waarin wordt getoond hoe u een Azure Cosmos-database en een container maken met een Azure Resource Manager-sjabloon
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339662"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>Quickstart: Een Azure Cosmos DB en een container maken met behulp van een ARM-sjabloon
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB is de snelle NoSQL-database van Microsoft, met open API's voor elke schaal. U kunt Azure Cosmos DB gebruiken om snel databases voor sleutels en waarden, documentdatabases en grafiekdatabases te maken en te doorzoeken. Deze quickstart is gericht op het implementeren van een Azure Resource Manager-sjabloon (ARM-sjabloon) voor het maken van een Azure Cosmos-database en een container in die database. U kunt later gegevens opslaan in deze container.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement of gratis Azure Cosmos DB-proefaccount

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

Er worden drie Azure-resources gedefinieerd in de sjabloon:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Maak een Azure Cosmos-account.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Maak een Azure Cosmos-database.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Maak een Azure Cosmos-container.

Meer voorbeelden van Azure Cosmos DB-sjablonen vindt u in de [galerie met quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon maakt u een Azure Cosmos-account, -database en -container.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="ARM-sjabloon, Azure Cosmos DB-integratie, portal voor implementeren":::

    Gebruik de standaardwaarden om de Azure Cosmos-resources te maken, tenzij er iets anders is aangegeven.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en klik op **OK**.
    * **Locatie**: selecteer een locatie.  Bijvoorbeeld **VS - centraal**.
    * **Accountnaam**: voer een naam in voor het Azure Cosmos-account. De naam moet wereldwijd uniek zijn.
    * **Locatie**: geef aan waar u uw Azure Cosmos-account wilt maken. Het Azure Cosmos-account kan zich op dezelfde locatie bevinden als de resourcegroep.
    * **Primaire regio**: De primaire-replicaregio voor het Azure Cosmos-account.
    * **Secundaire regio**: De secundaire-replicaregio voor het Azure Cosmos-account.
    * **Standaardconsistentieniveau**: Het standaardconsistentieniveau voor het Azure Cosmos-account.
    * **Voorvoegsel max. veroudering**: Max. aantal verouderde aanvragen. Vereist voor BoundedStaleness.
    * **Max. interval in seconden**: Maximale vertragingstijd. Vereist voor BoundedStaleness.
    * **Databasenaam**: De naam van de Azure Cosmos-database.
    * **Containernaam**: De naam van de Azure Cosmos-container.
    * **Doorvoer**:  De doorvoer van de container. De minimale doorvoerwaarde is 400 RU/s.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: Selecteren.

3. Selecteer **Aankoop**. Nadat het Azure Cosmos-account is geïmplementeerd, ontvangt u een melding:

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="ARM-sjabloon, Cosmos DB-integratie, melding van portal voor implementeren":::

Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

U kunt Azure Portal gebruiken om het Azure Cosmos-account, de database en de container te controleren, of u gebruikt het volgende Azure CLI- of Azure PowerShell-script om het gemaakte geheim weer te geven.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources het beste intact laten.
Als u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee worden ook het Azure Cosmos-account en de gerelateerde resources verwijderd. De resourcegroep verwijderen met behulp van Azure CLI of Azure PowerShell:

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

In deze quickstart hebt u een Azure Cosmos-account, -database en -container gemaakt met behulp van een ARM-sjabloon en hebt u de implementatie gevalideerd. Als u meer wilt weten over Azure Cosmos DB en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

- Een [overzicht van Azure Cosmos DB](introduction.md) lezen
- Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Andere [Azure Cosmos DB Resource Manager-sjablonen](./templates-samples-sql.md) bekijken