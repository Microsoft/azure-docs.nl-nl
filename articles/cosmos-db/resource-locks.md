---
title: Voor komen dat Azure Cosmos DB resources worden verwijderd of gewijzigd
description: Gebruik Azure resource Locks om te voor komen dat Azure Cosmos DB resources worden verwijderd of gewijzigd.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636687"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Voor komen dat Azure Cosmos DB resources worden verwijderd of gewijzigd
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Als beheerder moet u mogelijk een Azure Cosmos-account,-data base of-container vergren delen om te voor komen dat andere gebruikers in uw organisatie per ongeluk essentiële bronnen verwijderen of wijzigen. U kunt de vergrendeling instellen op CanNotDelete of ReadOnly.

- **CanNotDelete** betekent dat geautoriseerde gebruikers nog steeds een resource kunnen lezen en wijzigen, maar de resource niet kan verwijderen.
- **Alleen-lezen** houdt in dat gemachtigde gebruikers een resource kunnen lezen, maar de resource niet kunnen verwijderen of bijwerken. Het Toep assen van deze vergren deling is vergelijkbaar met het beperken van alle gemachtigde gebruikers tot de machtigingen die door de rol van lezer worden verleend.

## <a name="how-locks-are-applied"></a>Hoe vergrendelingen worden toegepast

Wanneer u een vergren deling toepast op een bovenliggend bereik, nemen alle resources binnen die scope dezelfde vergren deling. Zelfs resources die u later toevoegt, nemen de vergren deling van het bovenliggende knoop punt over. De meest beperkende vergren deling in de overname heeft prioriteit.

In tegens telling tot op rollen gebaseerd toegangs beheer van Azure kunt u beheer vergrendelingen gebruiken om een beperking toe te passen op alle gebruikers en rollen. Voor meer informatie over Azure RBAC voor Azure Cosmos DB raadpleegt u [toegangs beheer op basis van rollen in azure Cosmos DB](role-based-access-control.md).

Vergrendelingen van Resource Manager gelden alleen voor bewerkingen die zich op beheerniveau voordoen, wat bewerkingen zijn die worden verzonden naar https://management.azure.com. De vergrendelingen hebben geen invloed op hoe resources hun eigen functies uitvoeren. Resourcewijzigingen worden beperkt, maar resourcebewerkingen worden niet beperkt. Een alleen-lezen vergrendeling op een Azure Cosmos-container voor komt dat u de container kunt verwijderen of wijzigen. Het is niet mogelijk om gegevens in de container te maken, bij te werken of te verwijderen. Gegevenstransacties zijn toegestaan omdat deze bewerkingen niet naar https://management.azure.com worden verzonden.

## <a name="manage-locks"></a>Vergren delingen beheren

> [!WARNING]
> Resource vergrendeling werkt niet voor wijzigingen die zijn aangebracht door gebruikers die toegang hebben tot Azure Cosmos DB met behulp van account sleutels tenzij het Azure Cosmos-account eerst wordt vergrendeld door de eigenschap disableKeyBasedMetadataWriteAccess in te scha kelen. Voordat u deze eigenschap inschakelt, moet u ervoor zorgen dat er geen bestaande toepassingen worden verbroken die wijzigingen aanbrengen in resources met behulp van een SDK, Azure Portal of hulpprogram ma's van derden die verbinding maken via account sleutels en resources wijzigen, zoals het wijzigen van de door Voer, het bijwerken van index beleidsregels, enzovoort. Voor meer informatie en om een controle lijst te door lopen om ervoor te zorgen dat uw toepassingen blijven functioneren, kunt u geen [wijzigingen van de Azure Cosmos DB sdk's voor komen](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Sjabloon

Bij het Toep assen van een vergren deling op een Azure Cosmos DB-resource gebruikt u de volgende indelingen:

- naam `{resourceName}/Microsoft.Authorization/{lockName}`
- voert `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Als u een bestaand Azure Cosmos-account wilt wijzigen, moet u ervoor zorgen dat u de andere eigenschappen voor uw account en onderliggende resources opneemt wanneer redploying met deze eigenschap. Implementeer deze sjabloon niet als zodanig, of Hiermee worden al uw account eigenschappen opnieuw ingesteld.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Resource Manager sloten](../azure-resource-manager/management/lock-resources.md)
