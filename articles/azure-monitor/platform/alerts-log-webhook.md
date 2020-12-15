---
title: Webhook-acties voor logboek waarschuwingen in azure-waarschuwingen
description: Hierin wordt beschreven hoe u een logboek waarschuwings pushes configureert met een webhook-actie en beschik bare aanpassingen
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 97c3ead4dbdf0608c6b7cd1be6a9edf242ecd2b5
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510760"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-acties voor waarschuwingsregels voor logboeken

[Logboek waarschuwing](alerts-log.md) ondersteunt het [configureren van webhook-actie groepen](action-groups.md#webhook). In dit artikel wordt beschreven welke eigenschappen beschikbaar zijn en hoe u een aangepaste JSON-webhook configureert.

> [!NOTE]
> Aangepaste op JSON gebaseerde webhook wordt momenteel niet ondersteund in de API-versie `2020-05-01-preview`

> [!NOTE]
> Het is raadzaam om het [algemene waarschuwings schema](alerts-common-schema.md) voor uw webhook-integraties te gebruiken. Het algemene waarschuwings schema biedt het voor deel dat er één uitbreid bare en Unified alert Payload is voor alle waarschuwings Services in Azure Monitor. Voor waarschuwings regels voor logboeken waarvoor een aangepaste JSON-nettolading is gedefinieerd [, wordt het](alerts-common-schema-definitions.md#log-alerts)Payload-schema voor het algemene schema opnieuw ingesteld op de payload. Waarschuwingen waarvoor het algemene schema is ingeschakeld, hebben een maximale grootte van 256 KB per waarschuwing. een grotere waarschuwing bevat geen Zoek resultaten. Wanneer de zoek resultaten niet zijn opgenomen, moet u de `LinkToFilteredSearchResultsAPI` of gebruiken `LinkToSearchResultsAPI` om de query resultaten te openen via de log Analytics-API.

## <a name="webhook-payload-properties"></a>Eigenschappen van de webhook-nettolading

Met webhook-acties kunt u één HTTP POST-aanvraag aanroepen. De aangeroepen service moet webhooks ondersteunen en weet hoe u de ontvangen nettolading kunt gebruiken.

Standaard eigenschappen van de webhook-actie en hun aangepaste JSON-parameter namen:

| Parameter | Variabele | Beschrijving |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |De naam van de waarschuwings regel. |
| *Ernst* |#severity |Ernst ingesteld voor de waarschuwing voor het geactiveerde logboek. |
| *AlertThresholdOperator* |#thresholdoperator |De drempel operator voor de waarschuwings regel. |
| *AlertThresholdValue* |#thresholdvalue |Drempel waarde voor de waarschuwings regel. |
| *LinkToSearchResults* |#linktosearchresults |Koppeling naar de analyse portal die de records retourneert uit de query die de waarschuwing heeft gemaakt. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Koppeling naar de analyse-API die de records retourneert uit de query die de waarschuwing heeft gemaakt. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Koppeling naar de analyse portal die de records retourneert uit de query die is gefilterd op combi Naties van dimensie waarden die de waarschuwing hebben gemaakt. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Koppeling naar de analyse-API die de records retourneert uit de query die is gefilterd op combi Naties van dimensie waarden waarmee de waarschuwing is gemaakt. |
| *ResultCount* |#searchresultcount |Het aantal records in de zoek resultaten. |
| *Eind tijd van zoek interval* |#searchintervalendtimeutc |De eind tijd voor de query in UTC, met de notatie mm/dd/jjjj uu: mm: SS AM/PM. |
| *Zoek interval* |#searchinterval |Tijd venster voor de waarschuwings regel, met de indeling uu: mm: SS. |
| *Tijd in zoek interval* |#searchintervalstarttimeutc |Begin tijd voor de query in UTC met de notatie mm/dd/jjjj uu: mm: SS AM/PM. 
| *Search query* |#searchquery |Zoek query in logboek die wordt gebruikt door de waarschuwings regel. |
| *SearchResults* |"IncludeSearchResults": True|Records die worden geretourneerd door de query als een JSON-tabel, beperkt tot de eerste 1.000 records. "IncludeSearchResults": True wordt toegevoegd aan een aangepaste JSON-webhook-definitie als eigenschap op het hoogste niveau. |
| *Afmetingen* |"IncludeDimensions": True|Combi Naties van dimensie waarden die de waarschuwing hebben geactiveerd als JSON-gedeelte. "IncludeDimensions": True wordt toegevoegd aan een aangepaste JSON-webhook-definitie als eigenschap op het hoogste niveau. |
| *Waarschuwings type*| #alerttype | Het type waarschuwings regel voor het logboek dat is geconfigureerd als [metrische meet waarde of aantal resultaten](alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID van uw Log Analytics-werk ruimte. |
| *Toepassings-id* |#applicationid |ID van uw Application Insights-app. |
| *Abonnements-id* |#subscriptionid |De ID van uw Azure-abonnement dat wordt gebruikt. |

## <a name="custom-webhook-payload-definition"></a>Definitie van de aangepaste webhook-nettolading

U kunt de **aangepaste JSON-Payload voor webhook insluiten** gebruiken om een aangepaste JSON-nettolading op te halen met behulp van de bovenstaande para meters. U kunt ook extra eigenschappen genereren.
U kunt bijvoorbeeld de volgende aangepaste nettolading opgeven die een enkele para meter met de naam *Text* bevat. Deze para meter wordt verwacht door de service die deze webhook aanroept:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
In dit voor beeld wordt het volgende omgezet naar de webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Variabelen in een aangepaste webhook moeten worden opgegeven binnen een JSON-behuizing. Als u bijvoorbeeld verwijst naar ' #searchresultcount ' in het bovenstaande voor beeld van de webhook, wordt de uitvoer uitgevoerd op basis van de waarschuwings resultaten.

Als u zoek resultaten wilt toevoegen, voegt u **IncludeSearchResults** toe als eigenschap op het hoogste niveau in de aangepaste JSON. Zoek resultaten zijn opgenomen als een JSON-structuur, waardoor er in aangepaste gedefinieerde velden niet naar de resultaten kan worden verwezen. 

> [!NOTE]
> Met de knop **webhook weer geven** naast de optie voor het **toevoegen van aangepaste JSON-nettolading voor webhook** wordt een voor beeld weer gegeven van wat er is geleverd. Het bevat geen werkelijke gegevens, maar is representatief voor het JSON-schema dat wordt gebruikt. 

## <a name="sample-payloads"></a>Voor beeld van nettoladingen
In deze sectie vindt u voor beelden van nettoladingen voor webhooks voor logboek waarschuwingen. De nettolading van de steek proeven bevatten voor beelden wanneer de payload standaard is en wanneer deze aangepast is.

### <a name="log-alert-for-log-analytics"></a>Logboek waarschuwing voor Log Analytics
De volgende voor beeld-nettolading is voor een standaard webhook-actie die wordt gebruikt voor waarschuwingen op basis van Log Analytics:

> [!NOTE]
> De waarde van het veld ernst wordt gewijzigd als u bent [overgeschakeld naar de huidige scheduledQueryRules-API](alerts-log-api-switch.md) van de [verouderde log Analytics alert-API](api-alerts.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Logboek waarschuwing voor Application Insights
De volgende voor beeld-nettolading is voor een standaard webhook wanneer deze wordt gebruikt voor logboek waarschuwingen op basis van Application Insights resources:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Logboek waarschuwing voor andere bronnen Logboeken (van API `2020-05-01-preview` -versie)

> [!NOTE]
> Er zijn momenteel geen extra kosten verbonden aan de API-versie en de waarschuwingen over de `2020-05-01-preview` resource-georiënteerde Logboeken.  Prijzen voor functies die in de preview-versie zijn opgenomen, worden in de toekomst aangekondigd en er is een kennisgeving gegeven vóór het begin van de facturering. Als u ervoor kiest om door te gaan met het gebruik van de nieuwe API-versie en de waarschuwingen van resource gerichte logboeken na de kennisgevings periode, wordt u gefactureerd tegen het toepasselijke rente bedrag.

De volgende voor beeld-nettolading is voor een standaard webhook wanneer deze wordt gebruikt voor logboek waarschuwingen op basis van andere bronnen Logboeken (met uitzonde ring van werk ruimten en Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Waarschuwing vastleggen met een aangepaste JSON-nettolading
Als u bijvoorbeeld een aangepaste nettolading wilt maken die alleen de naam van de waarschuwing en de zoek resultaten bevat, gebruikt u deze configuratie: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

De volgende voor beeld-nettolading is voor een aangepaste webhook-actie voor een logboek waarschuwing:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [logboek waarschuwingen in azure-waarschuwingen](alerts-unified-log.md).
- Meer informatie over het [beheren van logboek waarschuwingen in azure](alerts-log.md).
- Actie groepen maken en beheren [in azure](action-groups.md).
- Meer informatie over [Application Insights](../log-query/log-query-overview.md).
- Meer informatie over [logboek query's](../log-query/log-query-overview.md). 
