---
title: PowerShell-script voor het bijwerken van het standaardconsistentieniveau van een Azure Cosmos-account
description: Voorbeeld van Azure PowerShell-script - Standaardconsistentieniveau voor een Azure Cosmos DB-account bijwerken met behulp van PowerShell
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: f9d6036a31f2482ca69f06c72bf224490fe8bbc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684349"
---
# <a name="update-the-regions-on-an-azure-cosmos-db-account-using-powershell"></a>De regio's van een Azure Cosmos DB-account bijwerken met behulp van PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Voor dit voor beeld is Azure PowerShell AZ 5.4.0 of later vereist. Voer `Get-Module -ListAvailable Az` uit om te zien welke versies zijn geïnstalleerd.
Als u PowerShell moet installeren, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Voer [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) uit om u aan te melden bij Azure.

## <a name="sample-script"></a>Voorbeeldscript

> [!NOTE]
> Het is niet mogelijk om in dezelfde bewerking regio's te wijzigen en andere eigenschappen van een Cosmos-account aan te passen. Dit moet worden gedaan met twee afzonderlijke bewerkingen.
> [!NOTE]
> Dit voorbeeld laat zien hoe u een SQL API-account gebruikt. Als u dit voorbeeld voor andere API's wilt gebruiken, kopieert u de gerelateerde eigenschappen en past u deze toe op uw API-specifieke script.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Update an Azure Cosmos DB account")]

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
| [Update-AzCosmosDBAccount](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Hiermee werkt u een Cosmos DB-account bij. |
|**Azure-resourcegroepen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.