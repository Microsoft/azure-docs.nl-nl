---
title: Activiteiten logboeken voor Azure RBAC-wijzigingen weer geven
description: Bekijk de activiteiten logboeken voor Azure RBAC-wijzigingen (Azure Role-based Access Control) gedurende de afgelopen 90 dagen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042118"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Activiteiten logboeken voor Azure RBAC-wijzigingen weer geven

Soms hebt u informatie nodig over de wijzigingen van Azure op rollen gebaseerd toegangs beheer (Azure RBAC), zoals voor het controleren of oplossen van problemen. Wanneer iemand wijzigingen aanbrengt in roltoewijzingen of roldefinities binnen uw abonnementen, worden de wijzigingen vastgelegd in het [Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md). U kunt de activiteiten logboeken weer geven om alle Azure RBAC-wijzigingen voor de afgelopen 90 dagen te bekijken.

## <a name="operations-that-are-logged"></a>Bewerkingen die zijn geregistreerd

Hier volgen de Azure RBAC-gerelateerde bewerkingen die zijn vastgelegd in het activiteiten logboek:

- Roltoewijzing maken
- Roltoewijzing verwijderen
- Aangepaste roldefinitie maken of bijwerken
- Aangepaste roldefinitie verwijderen

## <a name="azure-portal"></a>Azure Portal

De eenvoudigste manier om hieraan te beginnen is door de activiteitenlogboeken in de Azure Portal te bekijken. De volgende scherm afbeelding toont een voor beeld van roltoewijzings bewerkingen in het activiteiten logboek. Het bevat ook een optie om de logboeken te downloaden als een CSV-bestand.

![Activiteiten logboeken met behulp van de portal-scherm opname](./media/change-history-report/activity-log-portal.png)

Klik op een vermelding om het deel venster samen vatting te openen voor meer informatie. Klik op het tabblad **JSON** om een gedetailleerd logboek op te halen.

![Activiteiten logboeken met behulp van de portal met het deel venster samen vatting open scherm afbeelding](./media/change-history-report/activity-log-summary-portal.png)

Het activiteiten logboek in de portal heeft verschillende filters. Dit zijn de Azure RBAC-filters:

| Filter | Waarde |
| --------- | --------- |
| Gebeurteniscategorie | <ul><li>Administratief</li></ul> |
| Bewerking | <ul><li>Roltoewijzing maken</li><li>Roltoewijzing verwijderen</li><li>Aangepaste roldefinitie maken of bijwerken</li><li>Aangepaste roldefinitie verwijderen</li></ul> |

Zie [activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)voor meer informatie over activiteiten Logboeken.


## <a name="interpret-a-log-entry"></a>Een logboek vermelding interpreteren

De logboek uitvoer van het JSON-tabblad, Azure PowerShell of Azure CLI kan veel informatie bevatten. Hier volgen enkele van de belangrijkste eigenschappen die u moet opzoeken wanneer u een logboek vermelding probeert te interpreteren. Zie de volgende secties voor manieren om de logboek uitvoer te filteren met behulp van Azure PowerShell of Azure CLI.

> [!div class="mx-tableFixed"]
> | Eigenschap | Voorbeeldwaarden | Beschrijving |
> | --- | --- | --- |
> | autorisatie: actie | Microsoft.Authorization/roleAssignments/write | Roltoewijzing maken |
> |  | Micro soft. Authorization/roleAssignments/verwijderen | Roltoewijzing verwijderen |
> |  | Micro soft. Authorization/roleDefinitions/write | Roldefinitie maken of bijwerken |
> |  | Micro soft. Authorization/roleDefinitions/verwijderen | Roldefinitie verwijderen |
> | autorisatie: bereik | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Bereik voor de actie |
> | aanroeper | admin@example.com<br/>Id | Wie de actie heeft gestart |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | Tijdstip waarop de actie heeft plaatsgevonden |
> | status: waarde | Gestart<br/>Geslaagd<br/>Mislukt | Status van de actie |

## <a name="azure-powershell"></a>Azure PowerShell

Als u activiteiten logboeken met Azure PowerShell wilt weer geven, gebruikt u de opdracht [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Met deze opdracht worden alle roltoewijzings wijzigingen in een abonnement voor de afgelopen zeven dagen weer gegeven:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Met deze opdracht worden alle roldefinitie wijzigingen in een resource groep voor de afgelopen zeven dagen weer gegeven:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Logboek uitvoer filteren

De logboek uitvoer kan veel informatie bevatten. Met deze opdracht worden alle roltoewijzings-en roldefinitie wijzigingen in een abonnement voor de afgelopen zeven dagen weer gegeven en wordt de uitvoer gefilterd:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Hieronder ziet u een voor beeld van de gefilterde logboek uitvoer bij het maken van een roltoewijzing:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Als u een Service-Principal gebruikt om roltoewijzingen te maken, is de eigenschap beller een Service-Principal object-ID. U kunt [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) gebruiken om informatie over de Service-Principal op te halen.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Als u activiteiten logboeken wilt weer geven met de Azure CLI, gebruikt u de opdracht [AZ monitor Activity-Log List](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) .

Met deze opdracht wordt een lijst weer gegeven met de activiteiten Logboeken in een resource groep van 1 maart, met een voorwaartse periode van zeven dagen:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Met deze opdracht worden de activiteiten logboeken voor de provider van de autorisatie resource van 1 maart weer gegeven, die zeven dagen kijken:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Logboek uitvoer filteren

De logboek uitvoer kan veel informatie bevatten. Met deze opdracht wordt een lijst weer gegeven met alle functie-en roldefinitie wijzigingen in een abonnement die zeven dagen worden doorzocht en de uitvoer wordt gefilterd:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Hieronder ziet u een voor beeld van de gefilterde logboek uitvoer bij het maken van een roltoewijzing:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md) is een ander hulp programma dat u kunt gebruiken voor het verzamelen en analyseren van Azure RBAC-wijzigingen voor al uw Azure-resources. Azure Monitor logboeken heeft de volgende voor delen:

- Complexe query's en logica schrijven
- Integreren met waarschuwingen, Power BI en andere hulpprogram ma's
- Gegevens opslaan voor langere Bewaar perioden
- Kruis verwijzing met andere logboeken, zoals beveiliging, virtuele machine en aangepast

Hier volgen de basis stappen om aan de slag te gaan:

1. [Maak een log Analytics-werk ruimte](../azure-monitor/logs/quick-create-workspace.md).

1. [Configureer de analyse van activiteitenlogboek-oplossing](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) voor uw werk ruimte.

1. [Bekijk de activiteiten logboeken](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Een snelle manier om naar de overzichts pagina van de Analyse van activiteitenlogboek oplossing te gaan, is door op de optie **Logboeken** te klikken.

   ![Optie Azure Monitor Logboeken in Portal](./media/change-history-report/azure-log-analytics-option.png)

1. Gebruik eventueel de [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) om de logboeken te zoeken en weer te geven. Zie [aan de slag met logboek query's in azure monitor](../azure-monitor/logs/get-started-queries.md)voor meer informatie.

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
* [Activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Abonnements activiteiten controleren met het Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md)