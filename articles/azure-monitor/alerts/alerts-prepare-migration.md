---
title: Logic apps bijwerken & runbooks voor de migratie van waarschuwingen
description: Meer informatie over het wijzigen van uw webhooks, Logic apps en runbooks om een vrijwillige migratie voor te bereiden.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: c88d0b8595434298eb564034a44665c5375457c4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701039"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Uw logische apps en runbooks voorbereiden voor de migratie van klassieke waarschuwingsregels

> [!NOTE]
> Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in azure monitor buiten gebruik gesteld voor open bare Cloud gebruikers, maar nog maar in beperkte mate, tot **31 mei 2021**. Klassieke waarschuwingen voor Azure Government Cloud en Azure China 21Vianet worden op **29 februari 2024** buiten gebruik gesteld.
>

Als u ervoor kiest om uw klassieke waarschuwings regels vrijwillig te migreren naar nieuwe waarschuwings regels, zijn er enkele verschillen tussen de twee systemen. In dit artikel worden deze verschillen beschreven en wordt uitgelegd hoe u de wijziging kunt voor bereiden.

## <a name="api-changes"></a>API-wijzigingen

De Api's die klassieke waarschuwings regels maken en beheren ( `microsoft.insights/alertrules` ) verschillen van de api's die nieuwe metrische waarschuwingen maken en beheren ( `microsoft.insights/metricalerts` ). Als u nu een regel matig klassieke waarschuwings regels maakt en beheert, moet u de implementatie scripts bijwerken zodat deze met de nieuwe Api's kan worden gebruikt.

De volgende tabel bevat een verwijzing naar de programmatische interfaces voor zowel klassieke als nieuwe waarschuwingen:

| Implementatie script type | Klassieke waarschuwingen | Nieuwe metrische waarschuwingen |
| ---------------------- | -------------- | ----------------- |
|REST-API     | [micro soft. Insights/alertrules](/rest/api/monitor/alertrules)         | [micro soft. Insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Azure CLI     | [AZ-monitor waarschuwing](/cli/azure/monitor/alert)        | [waarschuwing AZ monitor Metrics](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [Verwijzing](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Verwijzing](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-sjabloon | [Voor klassieke waarschuwingen](./alerts-enable-template.md)|[Voor nieuwe metrische waarschuwingen](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Wijzigingen aan de meldings lading

De indeling van de meldings lading verschilt enigszins van de [klassieke waarschuwings regels](alerts-webhooks.md) en de [nieuwe metrische waarschuwingen](alerts-metric-near-real-time.md#payload-schema). Als u klassieke waarschuwings regels met webhook, logische app of runbook-acties hebt, moet u de doelen bijwerken om de nieuwe indeling van de nettolading te accepteren.

Gebruik de volgende tabel om de velden voor de nettolading van webhooks van de klassieke indeling toe te wijzen aan de nieuwe indeling:

| Type meldings eindpunt | Klassieke waarschuwingen | Nieuwe metrische waarschuwingen |
| -------------------------- | -------------- | ----------------- |
|Is de waarschuwing geactiveerd of opgelost?    | **status**       | **data. status** |
|Contextuele informatie over de waarschuwing     | **context**        | **data. context**        |
|Tijds tempel waarop de waarschuwing is geactiveerd of opgelost     | **context. time stamp**       | **data. context. time stamp**        |
| ID van waarschuwings regel | **context.id** | **data.context.id** |
| Naam van waarschuwingsregel | **context.name** | **data.context.name** |
| Beschrijving van de waarschuwings regel | **context. Description** | **data. context. Description** |
| Voor waarde waarschuwings regel | **context. condition** | **data. context. condition** |
| Naam van metrische gegevens | **context. condition. metrische** | **data. context. condition. overzet [0]. metrische** |
| Tijd aggregatie (hoe de metrische gegevens worden geaggregeerd over het evaluatie venster)| **context. condition. timeAggregation** | **context. condition. timeAggregation** |
| Evaluatie periode | **context. condition. windowSize** | **data. context. condition. windowSize** |
| Operator (hoe de cumulatieve metrische waarde wordt vergeleken met de drempel) | **context. condition. operator** | **data. context. condition. operator** |
| Drempelwaarde | **context. condition. threshold** | **data. context. condition. overzet [0]. drempel waarde** |
| Metrische waarde | **context. condition. metricValue** | **data. context. condition. overzet [0]. metricValue** |
| Abonnements-id | **context. subscriptionId** | **data. context. subscriptionId** |
| Resource groep van de betrokken resource | **context. resourceGroup** | **data. context. resourceGroup** |
| Naam van de betrokken resource | **context. ResourceName** | **data. context. ResourceName** |
| Type van de betrokken resource | **context. resource type** | **data. context. resource type** |
| Resource-ID van de betrokken resource | **context. resourceId** | **data. context. resourceId** |
| Directe koppeling naar de pagina overzicht van portal-resources | **context. portalLink** | **data. context. portalLink** |
| Aangepaste Payload-velden die moeten worden door gegeven aan de webhook-of logische app | **properties** | **data. Properties** |

De payloads zijn vergelijkbaar, zoals u kunt zien. In de volgende sectie vindt u:

- Meer informatie over het wijzigen van Logic apps voor gebruik van de nieuwe indeling.
- Een voor beeld van een runbook waarmee de meldings lading voor nieuwe waarschuwingen wordt geparseerd.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Een logische app wijzigen om een melding over een metrische waarschuwing te ontvangen

Als u Logic apps gebruikt met klassieke waarschuwingen, moet u de code van uw logische App aanpassen om de nieuwe Payload voor metrische waarschuwingen te parseren. Volg deze stappen:

1. Een nieuwe logische app maken.

1. Gebruik de sjabloon ' Azure Monitor-metrische waarschuwings-handler '. Voor deze sjabloon is een **HTTP-aanvraag** trigger met het juiste schema gedefinieerd.

    ![Scherm opname toont twee knoppen, lege logische app en Azure Monitor – metrische waarschuwings-handler.](media/alerts-prepare-migration/logic-app-template.png "Sjabloon metrische waarschuwing")

1. Voeg een actie toe om uw verwerkings logica te hosten.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Een Automation-runbook gebruiken dat een melding over een metrische waarschuwing ontvangt

Het volgende voor beeld biedt Power shell-code voor gebruik in uw runbook. Deze code kan de nettoladingen voor zowel de klassieke metrische waarschuwings regels als nieuwe metrische waarschuwings regels parseren.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Zie de [Azure Automation-documentatie](../../automation/automation-create-alert-triggered-runbook.md)voor een volledig voor beeld van een runbook dat een virtuele machine stopt wanneer een waarschuwing wordt geactiveerd.

## <a name="partner-integration-via-webhooks"></a>Partner integratie via webhooks

De meeste van [onze partners die integreren met klassieke waarschuwingen](../partners.md) , ondersteunen reeds nieuwere metrische waarschuwingen via hun integraties. Bekende integraties die al werken met nieuwe metrische waarschuwingen zijn:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Als u een partner integratie gebruikt die hier niet wordt vermeld, bevestigt u met de provider die ze met nieuwe metrische waarschuwingen werken.

## <a name="next-steps"></a>Volgende stappen

- [Het hulpprogramma voor migratie gebruiken](alerts-using-migration-tool.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)