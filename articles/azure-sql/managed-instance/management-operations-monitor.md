---
title: Beheer bewerkingen bewaken
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over verschillende manieren voor het bewaken van Azure SQL Managed instance Management-bewerkingen.
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
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589350"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed instance Management-bewerkingen bewaken
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance biedt bewaking van [beheer bewerkingen](management-operations-overview.md) die u gebruikt om nieuwe beheerde instanties te implementeren, instantie-eigenschappen bij te werken of instanties te verwijderen wanneer deze niet meer nodig zijn. 

## <a name="overview"></a>Overzicht

Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Implementatie van instanties (nieuwe instantie maken).
- Update van instantie (instantie-eigenschappen wijzigen, zoals vCores of gereserveerde opslag).
- Instantie verwijderen.

De meeste beheer bewerkingen zijn [langlopende bewerkingen](management-operations-overview.md#duration). Daarom moet u de status controleren of de voortgang van de bewerkings stappen volgen. 

Er zijn verschillende manieren om bewerkingen voor beheer van beheerde exemplaren te controleren:

- [Implementaties van resource groepen](../../azure-resource-manager/templates/deployment-history.md)
- [Activiteitenlogboek](../../azure-monitor/essentials/activity-log.md)
- [Managed instance Operations API](#managed-instance-operations-api)


De volgende tabel vergelijkt de bewakings opties voor beheer bewerkingen: 

| Optie | Bewaartermijn | Ondersteunt annuleren | Maken | Bijwerken | Verwijderen | Annuleren | Stappen |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Implementaties van resource groepen | Oneindig<sup>1</sup> | Geen<sup>2</sup> | Zichtbaar | Zichtbaar | Niet zichtbaar | Zichtbaar | Niet zichtbaar |
| Activiteitenlogboek | 90 dagen | No | Zichtbaar | Zichtbaar | Zichtbaar | Zichtbaar |  Niet zichtbaar |
| Managed instance Operations API | 24 uur | [Ja](management-operations-cancel.md) | Zichtbaar | Zichtbaar | Zichtbaar | Zichtbaar | Zichtbaar |
|  |  |  |  |  |  |  | |

<sup>1</sup> de implementatie geschiedenis voor een resource groep is beperkt tot 800 implementaties.

<sup>2</sup> implementaties van resource groepen ondersteunen annulerings bewerking. Als gevolg van het annuleren van logica, wordt alleen een bewerking die is gepland voor implementatie na de annulering, geannuleerd. Continue implementatie wordt niet geannuleerd als de implementatie van de resource groep is geannuleerd. Omdat de implementatie van een beheerd exemplaar bestaat uit één langlopende stap (uit het perspectief van Azure Resource Manager), wordt door het annuleren van de implementatie van een resource groep de implementatie van het beheerde exemplaar niet geannuleerd en wordt de bewerking voltooid. 

## <a name="managed-instance-operations-api"></a>Managed instance Operations API

Beheer bewerkingen-Api's zijn speciaal ontworpen voor het bewaken van bewerkingen. Bewerkingen van beheerde exemplaren bewaken kunnen inzicht geven in bewerkings parameters en bewerkings stappen, evenals het [annuleren van specifieke bewerkingen](management-operations-cancel.md). Naast de bewerkings Details en de Annuleer opdracht, kan deze API worden gebruikt in Automation-scripts met implementaties met meerdere resources. op basis van de stap van de voortgang kunt u de implementatie van een afhankelijke resource starten.

Dit zijn de Api's: 

| Opdracht | Beschrijving |
| --- | --- |
|[Bewerkingen voor beheerd exemplaar-ophalen](/rest/api/sql/managedinstanceoperations/get)|Hiermee wordt een beheer bewerking opgehaald voor een beheerd exemplaar.|
|[Bewerkingen voor beheerd exemplaar-annuleren](/rest/api/sql/managedinstanceoperations/cancel)|Hiermee wordt de asynchrone bewerking op het beheerde exemplaar geannuleerd.|
|[Beheerde exemplaar bewerkingen-lijst op beheerde instantie](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Hiermee haalt u een lijst met bewerkingen op die zijn uitgevoerd op het beheerde exemplaar.|

> [!NOTE]
> Gebruik API-versie 2020-02-02 om de bewerking voor het maken van een beheerd exemplaar te bekijken in de lijst met bewerkingen. Dit is de standaard versie die wordt gebruikt in het Azure Portal en de meest recente Power shell-en Azure CLI-pakketten.

## <a name="monitor-operations"></a>Bewerkingen bewaken

# <a name="portal"></a>[Portal](#tab/azure-portal)

Gebruik in de Azure Portal de pagina **overzicht** beheerde exemplaren om bewerkingen van beheerde exemplaren te controleren. 

De **bewerking maken** wordt bijvoorbeeld weer gegeven aan het begin van het aanmaak proces op de pagina **overzicht** : 

![Voortgang van beheerde instantie maken](./media/management-operations-monitor/monitoring-create-operation.png)

Selecteer **doorlopende bewerking** om de pagina **actieve bewerking** te openen en om bewerkingen voor **maken** of **bijwerken** weer te geven. U kunt ook bewerkingen op deze pagina [Annuleren](management-operations-cancel.md) .  

![Details van bewerkingen voor beheerde exemplaren](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Het maken van bewerkingen die via Azure Portal, Power shell, Azure CLI of andere hulp middelen zijn verzonden met REST API versie 2020-02-02 [kunnen worden geannuleerd](management-operations-cancel.md). REST API versies die ouder zijn dan 2020-02-02 voor het verzenden van een aanmaak bewerking, wordt de implementatie van het exemplaar gestart, maar de implementatie wordt niet weer gegeven in de operations API en kan niet worden geannuleerd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De cmdlet Get-AzSqlInstanceOperation haalt informatie op over de bewerkingen op een beheerd exemplaar. U kunt alle bewerkingen voor een beheerd exemplaar weer geven of een specifieke bewerking weer geven door de naam van de bewerking op te gegeven.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Zie [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)voor gedetailleerde uitleg over opdrachten.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De AZ SQL mi a-lijst haalt een lijst met bewerkingen op die zijn uitgevoerd op het beheerde exemplaar. Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Zie [AZ SQL mi op](/cli/azure/sql/mi/op)voor gedetailleerde opdracht uitleg.

---

## <a name="next-steps"></a>Volgende stappen

- Zie de [Quickstart-gids](instance-create-quickstart.md) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [algemene SQL-functies](../database/features-comparison.md)voor een lijst met functies en vergelijkingen.
- Zie [VNet-configuratie van SQL Managed Instance](connectivity-architecture-overview.md) voor meer informatie over VNet-configuratie.
- Zie [Beheerd exemplaar maken](instance-create-quickstart.md) voor een quickstart waarmee u een beheerd exemplaar kunt maken en een database vanuit een back-upbestand kunt herstellen.
- Zie [Migratie van SQL Managed Instance met behulp van Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) voor een zelfstudie over Azure Database Migration Service voor migratie.