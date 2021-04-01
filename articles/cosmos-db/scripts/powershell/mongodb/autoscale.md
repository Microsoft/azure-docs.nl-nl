---
title: PowerShell-script om een database en verzameling met automatische schaalaanpassing voor MongoDB API voor Azure Cosmos DB te maken
description: Azure PowerShell-script om een database en verzameling met automatische schaalaanpassing voor MongoDB API voor Azure Cosmos DB te maken
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 64f4a4ce0e5ef35a0ba8dc314bbe6fb4f1d1030b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675435"
---
# <a name="create-a-database-and-collection-with-autoscale-for-azure-cosmos-db---mongodb-api"></a>Een database en verzameling met automatische schaalaanpassing maken voor Azure Cosmos DB - MongoDB-API
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Voor dit voor beeld is Azure PowerShell AZ 5.4.0 of later vereist. Voer `Get-Module -ListAvailable Az` uit om te zien welke versies zijn geïnstalleerd.
Als u PowerShell moet installeren, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Voer [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) uit om u aan te melden bij Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-autoscale.ps1 "Create a database and collection with autoscale for MongoDB API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Hiermee maakt u een Cosmos DB-account. |
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Hiermee maakt u een MongoDB API-database. |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Hiermee maakt u een MongoDB API-index. |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Hiermee maakt u een MongoDB API-verzameling. |
|**Azure-resourcegroepen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.
