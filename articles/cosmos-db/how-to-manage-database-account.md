---
title: Informatie over het beheren van databaseaccounts in Azure Cosmos DB
description: Meer informatie over het beheren van Azure Cosmos DB-resources met behulp van de Azure Portal-, Power shell-, CLI-en Azure Resource Manager-sjablonen
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.openlocfilehash: d542e2b4e5db86fd3354514790e718f0694a09a5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489749"
---
# <a name="manage-an-azure-cosmos-account"></a>Een Azure Cosmos-account beheren
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt beschreven hoe u verschillende taken in een Azure Cosmos-account kunt beheren met behulp van de Azure-portal, Azure PowerShell, Azure CLI en Azure Resource Manager-sjablonen.

## <a name="create-an-account"></a>Een account maken

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Zie [een Azure Cosmos DB-account maken met Azure cli](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Zie [een Azure Cosmos DB-account maken met Power shell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager-sjabloon

Zie [Azure Cosmos DB-account maken met Azure Resource Manager sjablonen](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>Regio's toevoegen aan of verwijderen uit uw databaseaccount

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Ga naar uw Azure Cosmos-account en open het **wereld wijde menu gegevens repliceren** .

1. Als u regio's wilt toevoegen, selecteert u de zeshoeken op de kaart met het **+** label dat overeenkomt met de gewenste regio (s). Als u een regio wilt toevoegen, selecteert u de optie **+ regio toevoegen** en kiest u een regio in de vervolg keuzelijst.

1. Als u regio's wilt verwijderen, wist u een of meer regio's van de kaart door de blauwe zeshoeken met vinkjes te selecteren. Of selecteer het prullenbakpictogram (🗑) rechts van de regio.

1. Selecteer **OK** om uw wijzigingen op te slaan.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Menu Regio's toevoegen of verwijderen":::

In een schrijfmodus voor één regio kunt u de schrijfregio niet verwijderen. U dient dan failover toe te passen naar een andere regio voordat u de huidige schrijfregio kunt verwijderen.

In een schrijfmodus voor meerdere regio's kunt u elke regio toevoegen of verwijderen als er minstens één regio is.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Zie [regio's toevoegen of verwijderen met Azure cli](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Zie [regio's toevoegen of verwijderen met Power shell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Meerdere schrijfregio's configureren

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure Portal

Open het tabblad **gegevens globaal repliceren** en selecteer **inschakelen** om schrijf bewerkingen in meerdere regio's in te scha kelen. Wanneer u meerdere regio's schrijft, worden de regio's die u momenteel op het account hebt gelezen, gelezen en geschreven.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Het Azure Cosmos-account configureert de scherm afbeelding voor het schrijven van meerdere regio's":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Zie [meerdere-schrijf regio's inschakelen met Azure cli](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Zie [meerdere schrijven-regio's inschakelen met Power shell](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Resource Manager-sjabloon

Een account kan worden gemigreerd van een enkele schrijf regio naar meerdere schrijf regio's door de Resource Manager-sjabloon te implementeren die wordt gebruikt om het account en de instelling te maken `enableMultipleWriteLocations: true` . De volgende Azure Resource Manager sjabloon is een Maxi maal bare sjabloon waarmee een Azure Cosmos-account voor SQL-API met twee regio's en meerdere schrijf locaties kan worden geïmplementeerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Automatische failover inschakelen voor uw Azure Cosmos-account

Met de optie voor automatische failover kunnen Azure Cosmos DB een failover naar de regio met de hoogste failover-prioriteit zonder actie van de gebruiker, omdat een regio niet meer beschikbaar is. Als automatische failover is ingeschakeld, kan de prioriteit van de regio worden gewijzigd. Het account moet twee of meer regio's hebben om automatische failover mogelijk te maken.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Open in uw Azure Cosmos-account het deel venster **gegevens globaal repliceren** .

2. Selecteer bovenin het deelvenster de knop **Automatische failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Gegevens globaal repliceren":::

3. Controleer in het deelvenster **Automatische failover** of **Automatische failover inschakelen** is ingesteld op **AAN**. 

4. Selecteer **Opslaan**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Portalmenu Automatische failover":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Zie [automatische failover inschakelen met Azure cli](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Zie [automatische failover inschakelen met Power shell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Failoverprioriteiten instellen voor uw Azure Cosmos-account

Nadat een Cosmos-account is geconfigureerd voor automatische failover, kunnen de failover-prioriteit voor regio's worden gewijzigd.

> [!IMPORTANT]
> U kunt de schrijf regio (failover-prioriteit nul) niet wijzigen wanneer het account is geconfigureerd voor automatische failover. Als u de schrijf regio wilt wijzigen, moet u automatische failover uitschakelen en een hand matige failover uitvoeren.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Open in uw Azure Cosmos-account het deel venster **gegevens globaal repliceren** .

2. Selecteer bovenin het deelvenster de knop **Automatische failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Gegevens globaal repliceren":::

3. Controleer in het deelvenster **Automatische failover** of **Automatische failover inschakelen** is ingesteld op **AAN**.

4. Als u de failoverprioriteit wilt wijzigen, sleept u de leesregio's met behulp van de drie puntjes aan de linkerkant van de rij, die worden weergegeven wanneer u de muisaanwijzer erboven houdt.

5. Selecteer **Opslaan**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Portalmenu Automatische failover":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Zie [prioriteit van failover instellen met Azure cli](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Zie [prioriteit van failover instellen met Power shell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Handmatige failover uitvoeren voor een Azure Cosmos-account

> [!IMPORTANT]
> Het Azure Cosmos-account moet worden geconfigureerd voor een hand matige failover zodat deze bewerking kan worden voltooid.

Het proces voor het uitvoeren van een hand matige failover omvat het wijzigen van de schrijf regio van het account (failover priority = 0) naar een andere regio die is geconfigureerd voor het account.

> [!NOTE]
> Voor accounts met meerdere schrijf regio's kan hand matig geen failover worden uitgevoerd. Voor toepassingen die de Azure Cosmos-SDK gebruiken, detecteert de SDK wanneer een regio niet meer beschikbaar is en wordt automatisch omgeleid naar de volgende dichtstbijzijnde regio.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Ga naar uw Azure Cosmos-account en open het **wereld wijde menu gegevens repliceren** .

2. Selecteer bovenaan het menu de optie **Handmatige failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Gegevens globaal repliceren":::

3. Selecteer in het menu **Handmatige failover** de nieuwe schrijfregio. Schakel het selectievakje in om aan te geven dat u begrijpt dat met deze optie de schrijfregio wordt gewijzigd.

4. Selecteer **OK** om de failover te activeren.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Portalmenu Handmatige failover":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Zie [hand matige failover activeren met Azure cli](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Zie [trigger hand matige failover met Power shell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie en voor beelden over het beheren van het Azure Cosmos-account, de data base en containers:

* [Azure Cosmos DB beheren met Azure PowerShell](manage-with-powershell.md)
* [Azure Cosmos DB beheren met behulp van Azure CLI](manage-with-cli.md)