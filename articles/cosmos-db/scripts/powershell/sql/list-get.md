---
title: PowerShell-script voor het weergeven van Azure Cosmos DB SQL API-resources
description: 'Azure PowerShell-script: Azure Cosmos DB-opdrachten voor SQL-API'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 849e73e61341576c4387aba8da8a648cf396052a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680849"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Databases en containers weergeven en opvragen voor de SQL (Core)-API van Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Voor dit voor beeld is Azure PowerShell AZ 5.4.0 of later vereist. Voer `Get-Module -ListAvailable Az` uit om te zien welke versies zijn geïnstalleerd.
Als u PowerShell moet installeren, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Voer [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) uit om u aan te melden bij Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Hiermee geeft u de Cosmos DB-accounts weer of een bepaald Cosmos DB-account. |
| [Get-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Hiermee geeft u de Cosmos DB-databases in een account weer of haalt u een bepaalde Cosmos DB-database in een account op. |
| [Get-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Hiermee geeft u de Cosmos DB containers in een database weer of haalt u een bepaalde Cosmos DB-container in een database op. |
|**Azure-resourcegroepen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.