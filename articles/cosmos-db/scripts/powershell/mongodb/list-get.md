---
title: PowerShell-script voor het ophalen van databases en grafieken met de API van Azure Cosmos DB voor MongoDB
description: 'Azure PowerShell-script: Azure Cosmos DB-opdrachten voor MongoDB-API'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 630801d500107b468d6a14dbf0c410934851f94c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677980"
---
# <a name="list-and-get-databases-and-graphs-for-azure-cosmos-db---mongodb-api"></a>Databases en grafieken weergeven en opvragen voor Azure Cosmos DB - MongoDB-API
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Voor dit voor beeld is Azure PowerShell AZ 5.4.0 of later vereist. Voer `Get-Module -ListAvailable Az` uit om te zien welke versies zijn geïnstalleerd.
Als u PowerShell moet installeren, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Voer [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) uit om u aan te melden bij Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-list-get.ps1 "List or get databases or collections for MongoDB API")]

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
| [Get-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabase) | Hiermee geeft u de MongoDB API-databases in een account weer of haalt u een bepaalde MongoDB API-database in een account op. |
| [Get-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollection) | Hiermee geeft u de MongoDB-API-verzamelingen weer of haalt u een bepaalde MongoDB API-verzameling in een database op. |
|**Azure-resourcegroepen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.