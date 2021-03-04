---
title: Upgrade uitvoeren naar de huidige API voor logboek waarschuwingen van Azure Monitor
description: Meer informatie over het overschakelen naar de ScheduledQueryRules-API voor logboek waarschuwingen
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b91560f09b30cc5d3089e5beca4d670f94f33402
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031307"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Upgrade uitvoeren naar de huidige API voor logboek waarschuwingen van de verouderde API voor Log Analytics waarschuwingen

> [!NOTE]
> Dit artikel is alleen relevant voor Azure Public (**niet** Azure Government of Azure China-Cloud).

> [!NOTE]
> Wanneer een gebruiker ervoor kiest om de voor keur naar de huidige [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules) te scha kelen, is het niet mogelijk om terug te keren naar de oudere [verouderde log Analytics-waarschuwings-API](./api-alerts.md).

In het verleden hebben gebruikers de [API voor verouderde log Analytics waarschuwingen](./api-alerts.md) gebruikt voor het beheren van waarschuwings regels voor Logboeken. Huidige werk ruimten gebruiken [ScheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules). In dit artikel worden de voor delen en het proces van het overschakelen van de oudere API naar de huidige API beschreven.

## <a name="benefits"></a>Voordelen

- Eén sjabloon voor het maken van waarschuwings regels (eerder nodig drie afzonderlijke sjablonen).
- Eén API voor zowel Log Analytics werk ruimten als Application Insights resources.
- [Ondersteuning voor Power shell-cmdlets](./alerts-log.md#managing-log-alerts-using-powershell).
- Uitlijning van Ernst met alle andere typen waarschuwingen.
- De mogelijkheid om een [logboek waarschuwing voor meerdere werk ruimten](../logs/cross-workspace-query.md) te maken dat meerdere externe resources omvat, zoals log Analytics werk ruimten of Application Insights resources.
- Gebruikers kunnen dimensies opgeven om de waarschuwingen te splitsen door de para meter ' aggregate on ' te gebruiken.
- Logboek waarschuwingen hebben een lange periode van Maxi maal twee dagen aan gegevens (eerder beperkt tot één dag).

## <a name="impact"></a>Impact

- Alle nieuwe regels moeten worden gemaakt/bewerkt met de huidige API. Zie voor [beeld van gebruik via een Azure-resource sjabloon](alerts-log-create-templates.md) en voor [beeld gebruik via Power shell](./alerts-log.md#managing-log-alerts-using-powershell).
- Wanneer regels worden Azure Resource Manager bijgehouden resources in de huidige API en moeten uniek zijn, wordt de resource-ID van de regel gewijzigd in deze structuur: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . De weergave namen van de waarschuwings regel blijven ongewijzigd.

## <a name="process"></a>Proces

Het wisselings proces is niet interactief en vereist in de meeste gevallen geen hand matige stappen. Uw waarschuwings regels worden tijdens of na de switch niet gestopt of geblokkeerd.
Deze aanroep om te scha kelen van alle waarschuwings regels die zijn gekoppeld aan de specifieke Log Analytics-werk ruimte:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Met de aanvraag tekst die de onderstaande JSON bevat:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Hier volgt een voor beeld van het gebruik van [ARMClient](https://github.com/projectkudu/ARMClient), een open-source opdracht regel programma, waarmee de bovenstaande API-aanroep wordt vereenvoudigd:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Als de schakel optie is geslaagd, is de reactie:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Scha kelen tussen status van werk ruimte controleren

U kunt deze API-aanroep ook gebruiken om de status van de switch te controleren:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

U kunt ook [ARMClient](https://github.com/projectkudu/ARMClient) -hulp programma gebruiken:

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Als de Log Analytics-werk ruimte is overgeschakeld naar de [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules), is de reactie:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Als de Log Analytics werk ruimte niet is geswitcheerd, is de reactie:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [waarschuwingen voor Azure monitor logboeken](./alerts-unified-log.md).
- Meer informatie over het [beheren van uw logboek waarschuwingen met behulp van de API](alerts-log-create-templates.md).
- Meer informatie over het [beheren van logboek waarschuwingen met behulp van Power shell](./alerts-log.md#managing-log-alerts-using-powershell).
- Meer informatie over de [Azure Alerts-ervaring](./alerts-overview.md).