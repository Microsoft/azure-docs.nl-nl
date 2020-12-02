---
title: Activiteiten logboeken voor Azure RBAC-wijzigingen weer geven
description: Activiteiten logboeken weer geven voor Azure RBAC-wijzigingen (Azure Role-based Access Control) in azure-resources voor de afgelopen 90 dagen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 3e4d75c59d0e3c97b0900dd72a27d4afbf4015c9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492315"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Activiteiten logboeken voor Azure RBAC-wijzigingen weer geven

Soms hebt u informatie nodig over de wijzigingen van Azure op rollen gebaseerd toegangs beheer (Azure RBAC), zoals voor het controleren of oplossen van problemen. Wanneer iemand wijzigingen aanbrengt in roltoewijzingen of roldefinities binnen uw abonnementen, worden de wijzigingen vastgelegd in het [Azure-activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md). U kunt de activiteiten logboeken weer geven om alle Azure RBAC-wijzigingen voor de afgelopen 90 dagen te bekijken.

## <a name="operations-that-are-logged"></a>Bewerkingen die zijn geregistreerd

Hier volgen de Azure RBAC-gerelateerde bewerkingen die zijn vastgelegd in het activiteiten logboek:

- Roltoewijzing maken
- Roltoewijzing verwijderen
- Aangepaste roldefinitie maken of bijwerken
- Aangepaste roldefinitie verwijderen

## <a name="azure-portal"></a>Azure Portal

De eenvoudigste manier om hieraan te beginnen is door de activiteitenlogboeken in de Azure Portal te bekijken. De volgende scherm afbeelding toont een voor beeld van roltoewijzings bewerkingen in het activiteiten logboek. Het bevat ook een optie om de logboeken te downloaden als een CSV-bestand.

![Activiteiten logboeken met behulp van de portal-scherm opname](./media/change-history-report/activity-log-portal.png)

Het activiteiten logboek in de portal heeft verschillende filters. Dit zijn de Azure RBAC-filters:

| Filter | Waarde |
| --------- | --------- |
| Gebeurteniscategorie | <ul><li>Administratief</li></ul> |
| Bewerking | <ul><li>Roltoewijzing maken</li><li>Roltoewijzing verwijderen</li><li>Aangepaste roldefinitie maken of bijwerken</li><li>Aangepaste roldefinitie verwijderen</li></ul> |

Zie [activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)voor meer informatie over activiteiten Logboeken.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Als u activiteiten logboeken met Azure PowerShell wilt weer geven, gebruikt u de opdracht [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Met deze opdracht worden alle roltoewijzings wijzigingen in een abonnement voor de afgelopen zeven dagen weer gegeven:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Met deze opdracht worden alle roldefinitie wijzigingen in een resource groep voor de afgelopen zeven dagen weer gegeven:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Met deze opdracht worden alle roltoewijzings-en roldefinitie wijzigingen in een abonnement voor de afgelopen zeven dagen weer gegeven en worden de resultaten in een lijst getoond:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Als u een Service-Principal gebruikt om roltoewijzingen te maken, is de eigenschap beller een object-ID. U kunt [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) gebruiken om informatie over de Service-Principal op te halen.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Als u activiteiten logboeken wilt weer geven met de Azure CLI, gebruikt u de opdracht [AZ monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

Met deze opdracht wordt een lijst weer gegeven met de activiteiten Logboeken in een resource groep van 27 februari, op een termijn van zeven dagen:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Met deze opdracht worden de activiteiten logboeken voor de provider van de autorisatie resource van 27 februari weer gegeven, die zeven dagen kijken:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor-logboeken](../azure-monitor/log-query/log-query-overview.md) is een ander hulp programma dat u kunt gebruiken voor het verzamelen en analyseren van Azure RBAC-wijzigingen voor al uw Azure-resources. Azure Monitor logboeken heeft de volgende voor delen:

- Complexe query's en logica schrijven
- Integreren met waarschuwingen, Power BI en andere hulpprogram ma's
- Gegevens opslaan voor langere Bewaar perioden
- Kruis verwijzing met andere logboeken, zoals beveiliging, virtuele machine en aangepast

Hier volgen de basis stappen om aan de slag te gaan:

1. [Maak een log Analytics-werk ruimte](../azure-monitor/learn/quick-create-workspace.md).

1. [Configureer de analyse van activiteitenlogboek-oplossing](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution) voor uw werk ruimte.

1. [Bekijk de activiteiten logboeken](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution). Een snelle manier om naar de overzichts pagina van de Analyse van activiteitenlogboek oplossing te gaan, is door op de optie **Logboeken** te klikken.

   ![Optie Azure Monitor Logboeken in Portal](./media/change-history-report/azure-log-analytics-option.png)

1. Gebruik eventueel de [Azure Monitor Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) om de logboeken te zoeken en weer te geven. Zie [aan de slag met Azure monitor-logboek query's](../azure-monitor/log-query/get-started-queries.md)voor meer informatie.

Hier volgt een query waarmee nieuwe roltoewijzingen worden geretourneerd die zijn ingedeeld op de doel resource provider:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Hier volgt een query die de gewijzigde roltoewijzingen in een grafiek retourneert:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Activiteiten logboeken met behulp van de geavanceerde analyse portal-scherm opname](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Volgende stappen
* [Gebeurtenissen in het activiteitenlogboek bekijken](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Abonnementactiviteit controleren met Azure-activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)