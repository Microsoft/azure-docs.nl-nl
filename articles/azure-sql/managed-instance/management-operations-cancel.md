---
title: Beheer bewerkingen annuleren
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over het annuleren van Azure SQL Managed instance Management-bewerkingen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861541"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>De Azure SQL Managed instance Management-bewerkingen worden geannuleerd
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance biedt de mogelijkheid om sommige [beheer bewerkingen](management-operations-overview.md)te annuleren, bijvoorbeeld wanneer u een nieuw beheerd exemplaar of eigenschappen van een update-exemplaar implementeert. 

## <a name="overview"></a>Overzicht

 Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Implementatie van instanties (nieuwe instantie maken).
- Update van instantie (instantie-eigenschappen wijzigen, zoals vCores of gereserveerde opslag).
- Instantie verwijderen.

U kunt de [voortgang en status van beheer bewerkingen bewaken](management-operations-monitor.md) en zo nodig enkele annuleren. 

De volgende tabel bevat een overzicht van de beheer bewerkingen, ongeacht of u deze kunt annuleren en de typische totale duur ervan:

Categorie  |Bewerking  |Geannuleerd  |Geschatte annulerings duur  |
|---------|---------|---------|---------|
|Implementatie |Instantie maken |Ja |90% van de bewerkingen is binnen 5 minuten voltooid. |
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Algemeen) |Nee |  |
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Bedrijfskritiek) |Ja |90% van de bewerkingen is binnen 5 minuten voltooid. |
|Bijwerken |Rekenkracht van instantie (vCores) omhoog en omlaag schalen (Algemeen) |Ja |90% van de bewerkingen is binnen 5 minuten voltooid. |
|Bijwerken |Rekenkracht van instantie (vCores) omhoog en omlaag schalen (Bedrijfskritiek) |Ja |90% van de bewerkingen is binnen 5 minuten voltooid. |
|Bijwerken |Wijziging van de servicelaag van de instantie (van Algemeen naar Bedrijfskritiek en omgekeerd) |Ja |90% van de bewerkingen is binnen 5 minuten voltooid. |
|Verwijderen |Verwijdering van exemplaar |Nee |  |
|Verwijderen |Verwijderen van virtueel cluster (als een door de gebruiker geïnitieerde bewerking) |Nee |  |

## <a name="cancel-management-operation"></a>Beheer bewerking annuleren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om beheer bewerkingen te annuleren met de Azure Portal:

1. Ga naar de [Azure Portal](https://portal.azure.com)
1. Ga naar de Blade **overzicht** van uw SQL Managed instance. 
1. Selecteer het **meldingen** venster naast de actieve bewerking om de pagina **actieve bewerking** te openen. 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Selecteer het vak actieve bewerking om de pagina actieve bewerking te openen.":::

1. Selecteer **de bewerking annuleren** onder aan de pagina. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Selecteer Annuleren om de bewerking te annuleren.":::

1. Bevestig dat u de bewerking wilt annuleren. 


Als de annulerings aanvraag slaagt, wordt de beheer bewerking geannuleerd en resulteert dit in een fout. U krijgt een melding dat de annulering slaagt of mislukt.

![Het resultaat van de bewerking wordt geannuleerd](./media/management-operations-cancel/canceling-operation-result.png)


Als de annulerings aanvraag mislukt of de knop Annuleren niet actief is, betekent dit dat de beheer bewerking een status heeft die niet kan worden geannuleerd en die binnenkort wordt voltooid.  De beheer bewerking wordt voortgezet totdat deze is voltooid.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als Azure PowerShell nog niet is geïnstalleerd, raadpleegt u [de module Azure PowerShell installeren](/powershell/azure/install-az-ps).

Als u een beheer bewerking wilt annuleren, moet u de naam van de beheer bewerking opgeven. Daarom moet u eerst de opdracht Get gebruiken om de lijst met bewerkingen op te halen en vervolgens de specifieke bewerking annuleren.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Zie [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) en [Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)voor gedetailleerde uitleg over opdrachten.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

Als u de beheer bewerking wilt annuleren, moet u de naam van de beheer bewerking opgeven. Daarom moet u eerst de opdracht Get gebruiken om de lijst met bewerkingen op te halen en vervolgens de specifieke bewerking annuleren.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Zie [AZ SQL mi op](/cli/azure/sql/mi/op)voor gedetailleerde opdracht uitleg.

---

## <a name="canceled-deployment-request"></a>De implementatie aanvraag is geannuleerd

Met API versie 2020-02-02, zodra de aanvraag voor het maken van een instantie wordt geaccepteerd, bestaat het exemplaar als een resource, ongeacht de voortgang van het implementatie proces (status van beheerd exemplaar is **inrichten**). Als u de aanvraag voor de exemplaar implementatie annuleert (nieuwe instantie maken), wordt het beheerde exemplaar van de **inrichtings** status naar **FailedToCreate**.

Instanties die niet zijn gemaakt, zijn nog steeds aanwezig als resource en: 

- Worden niet in rekening gebracht
- Niet meerekenen naar resource limieten (subnet-of vCore-quotum)


> [!NOTE]
> Als u de ruis in de lijst met resources of beheerde instanties wilt minimaliseren, verwijdert u instanties die niet zijn geïmplementeerd of instanties met Geannuleerde implementaties. 


## <a name="next-steps"></a>Volgende stappen

- Zie de [Quickstart-gids](instance-create-quickstart.md) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [Veelvoorkomende SQL-functies](../database/features-comparison.md) voor een lijst met functies en vergelijkingen.
- Zie [VNet-configuratie van SQL Managed Instance](connectivity-architecture-overview.md) voor meer informatie over VNet-configuratie.
- Zie [Beheerd exemplaar maken](instance-create-quickstart.md) voor een quickstart waarmee u een beheerd exemplaar kunt maken en een database vanuit een back-upbestand kunt herstellen.
- Zie [Migratie van SQL Managed Instance met behulp van Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) voor een zelfstudie over Azure Database Migration Service voor migratie.
