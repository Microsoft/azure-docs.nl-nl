---
title: PowerShell-script voor het maken van een container met indexering uitgeschakeld in een Azure Cosmos DB-account
description: Azure PowerShell-voorbeeldscript - Een container maken met indexering uitgeschakeld in een Azure Cosmos DB-account
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: b287bc851e560261d56ccda5d73d5319619146ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684036"
---
# <a name="create-a-container-with-indexing-turned-off-in-an-azure-cosmos-db-account-using-powershell"></a>Een container maken met indexering uitgeschakeld in een Azure Cosmos DB-account met behulp van PowerShell
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Voor dit voor beeld is Azure PowerShell AZ 5.4.0 of later vereist. Voer `Get-Module -ListAvailable Az` uit om te zien welke versies zijn geïnstalleerd.
Als u PowerShell moet installeren, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Voer [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) uit om u aan te melden bij Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-create-index-none.ps1 "Create a container with indexing turned off in an Azure Cosmos DB account")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Maakt een Cosmos DB-account. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Maakt een Cosmos DB SQL-database. |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Maakt een PSSqlIndexingPolicy-object dat wordt gebruikt als een parameter voor New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Maakt een Cosmos DB SQL-container. |
|**Azure-resourcegroepen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.