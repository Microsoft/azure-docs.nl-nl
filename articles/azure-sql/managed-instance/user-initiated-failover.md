---
title: Hand matig een failover initiëren op een SQL-beheerd exemplaar
description: Meer informatie over het hand matig failover van primaire en secundaire replica's op Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 08/31/2020
ms.openlocfilehash: 7bcdabdb4e49d198b9acb7a1c57312b31a5b4fff
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842223"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Door de gebruiker geïnitieerde hand matige failover op een SQL-beheerd exemplaar

In dit artikel wordt uitgelegd hoe u hand matig een primair knoop punt doorstuurt op de service lagen van SQL Managed instance Algemeen (GP) en Bedrijfskritiek (BC) en hoe u een secundair alleen-lezen replica knooppunt hand matig doorstuurt op de service tier BC.

## <a name="when-to-use-manual-failover"></a>Wanneer moet u hand matige failover gebruiken?

[Hoge Beschik baarheid](../database/high-availability-sla.md) is een fundamenteel onderdeel van het SQL Managed instance-platform dat transparant werkt voor uw database toepassingen. Failovers van het primaire naar het secundaire knoop punt in het geval van een knooppunt vermindering of-fout detectie of tijdens reguliere maandelijkse software-updates is een verwacht exemplaar voor alle toepassingen die gebruikmaken van SQL Managed instance in Azure.

U kunt overwegen om een van de volgende redenen een [hand matige failover](../database/high-availability-sla.md#testing-application-fault-resiliency) uit te voeren op een SQL-beheerd exemplaar:
- Test toepassing voor failover-tolerantie voordat deze naar productie wordt geïmplementeerd
- End-to-end-systemen testen op fout tolerantie bij automatische failovers
- Testen hoe failover invloed heeft op bestaande database sessies
- Controleren of de end-to-end-prestaties van een failover zijn gewijzigd vanwege wijzigingen in de netwerk latentie
- In sommige gevallen van prestatie vermindering van query's kan hand matige failover het prestatie probleem oplossen.

> [!NOTE]
> Om ervoor te zorgen dat uw toepassingen flexibeler zijn dan de implementatie van de productie, kunt u het risico op toepassings fouten in de productie verminderen en bijdragen aan de beschik baarheid van toepassingen voor uw klanten.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Hand matige failover initiëren op een SQL-beheerd exemplaar

### <a name="rbac-permissions-required"></a>RBAC-machtigingen vereist

De gebruiker die een failover initieert, moet een van de volgende RBAC-rollen hebben:

- De rol van abonnements eigenaar of
- Rol van beheerde instantie Inzender of
- Aangepaste rol met de volgende machtiging:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>PowerShell gebruiken

De minimale versie van AZ. SQL moet [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)zijn. Overweeg het gebruik van [Azure Cloud shell](../../cloud-shell/overview.md) van de Azure portal waarop altijd de nieuwste Power shell-versie beschikbaar is. 

Gebruik het volgende Power shell-script om vereiste Azure-modules te installeren als een voor waarde. Daarnaast selecteert u het abonnement waarin het beheerde exemplaar dat u wilt failover, zich bevindt.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Gebruik de Power shell [-opdracht invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) met het volgende voor beeld om failover van het primaire knoop punt te initiëren, die van toepassing is op de service tier BC en GP.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Gebruik de volgende PS-opdracht voor het lezen van een secundair knoop punt voor failover, alleen van toepassing op de service tier BC.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>CLI gebruiken

Zorg ervoor dat de nieuwste CLI-scripts zijn geïnstalleerd.

Gebruik AZ SQL mi failover CLI-opdracht met het volgende voor beeld om failover van het primaire knoop punt te initiëren, die van toepassing is op de service tier BC en GP.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Gebruik de volgende CLI-opdracht voor het lezen van een secundair knoop punt voor failover, alleen van toepassing op de service tier BC.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>REST API gebruiken

Voor geavanceerde gebruikers die mogelijk failovers van hun SQL Managed instances moeten automatiseren voor het implementeren van doorlopende test pijplijn of geautomatiseerde prestaties, kan deze functie worden uitgevoerd via het initiëren van een failover via een API-aanroep. Zie [beheerde instanties-failover-rest API](/rest/api/sql/managed%20instances%20-%20failover/failover) voor meer informatie.

Als u failover wilt initiëren met REST API aanroep, genereert u eerst het verificatie token met behulp van de API-client van uw keuze. Het gegenereerde verificatie token wordt gebruikt als autorisatie-eigenschap in de header van de API-aanvraag en is verplicht.

De volgende code is een voor beeld van de API-URI die moet worden aangeroepen:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

De volgende eigenschappen moeten worden door gegeven in de API-aanroep:

| **API-eigenschap** | **Parameter** |
| --- | --- |
| subscriptionId | Abonnements-ID waarop het beheerde exemplaar is geïmplementeerd |
| resourceGroupName | Resource groep die een beheerd exemplaar bevat |
| managedInstanceName | Naam van het beheerde exemplaar |
| Type | Beschrijving (Primair of ReadableSecondary). Deze para meters vertegenwoordigen het type replica waarvoor een failover moet worden uitgevoerd: primair of leesbaar secundair. Als deze niet wordt opgegeven, wordt standaard failover gestart op de primaire replica. |
| api-versie | Statische waarde en momenteel moet ' 2019-06-01-preview ' zijn |

API-antwoord is een van de volgende twee:

- 202 geaccepteerd
- Een van de 400-aanvraag fouten.

De bewerkings status kan worden gevolgd door de API-antwoorden in de reactie headers te controleren. Zie [status van asynchrone Azure-bewerkingen](../../azure-resource-manager/management/async-operations.md)voor meer informatie.

## <a name="monitor-the-failover"></a>De failover bewaken

Als u de voortgang van de door de gebruiker gestarte hand matige failover wilt bewaken, voert u de volgende T-SQL-query uit in uw favoriete client (bijvoorbeeld SSMS) op SQL Managed instance. De weer gave systeem sys.dm_hadr_fabric_replica_states en rapport replica's die beschikbaar zijn op het exemplaar worden gelezen. Vernieuw dezelfde query na het initiëren van de hand matige failover.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Voordat de failover wordt gestart, wordt in de uitvoer de huidige primaire replica van de service tier aangegeven met één primaire en drie secundaire zones in de AlwaysOn-beschikbaarheids groep. Wanneer de uitvoering van een failover wordt uitgevoerd, moet u deze query opnieuw uitvoeren om een wijziging van het primaire knoop punt aan te geven.

Het is niet mogelijk om dezelfde uitvoer te zien met de categorie GP als hierboven wordt weer gegeven voor BC. Dit komt omdat de servicelaag van de groeps BELEIDS eenheid alleen is gebaseerd op één knoop punt. T-SQL-query uitvoer voor de GP-servicelaag toont één knoop punt alleen voor en na de failover. Het verlies van de connectiviteit van uw client tijdens de failover, doorgaans gedurende een minuut, is de indicatie van de uitvoering van de failover.

> [!NOTE]
> Het volt ooien van het failoverproces (niet de werkelijke korte niet-beschik baarheid) kan enkele minuten in beslag nemen in het geval van werk belastingen met een **hoge intensiteit** . Dit komt doordat de exemplaar-engine alle huidige trans acties op de primaire en op het secundaire knoop punt wordt uitgevoerd voordat failover kan worden uitgevoerd.

> [!IMPORTANT]
> Functionele beperkingen van door de gebruiker gestarte hand matige failover zijn:
> - Er kan één (1) failover worden gestart op hetzelfde beheerde exemplaar om de **30 minuten**.
> - Voor BC-instanties moet er een quorum van replica's bestaan voor het accepteren van de failover-aanvraag.
> - Voor BC-instanties is het niet mogelijk om op te geven welke secundaire replica moet worden gelezen om de failover te initiëren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoge Beschik baarheid van hoge Beschik baarheid van beheerde exemplaren [voor Azure SQL Managed instance](../database/high-availability-sla.md).
- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
