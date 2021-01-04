---
title: Metrische waarschuwingen maken voor logboeken in Azure Monitor
description: Zelf studie over het maken van waarschuwingen voor bijna realtime metrische gegevens op de populaire log Analytics-Data.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 06/17/2020
ms.subservice: alerts
ms.openlocfilehash: f9005e95ac902a2d09b792c5f64520d0175c7707
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694977"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Metrische waarschuwingen maken voor logboeken in Azure Monitor

## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor ondersteunt het [type metrische waarschuwing](./alerts-metric-near-real-time.md) dat voor delen heeft ten opzichte van de [klassieke waarschuwingen](./alerts-classic-portal.md). Metrische gegevens zijn beschikbaar voor een [grote lijst met Azure-Services](./metrics-supported.md). In dit artikel wordt het gebruik van een subset (dat wil zeggen) voor resource- `Microsoft.OperationalInsights/workspaces` .

U kunt metrische waarschuwingen gebruiken in populaire Log Analytics logboeken die als meet waarden worden geëxtraheerd als onderdeel van de gegevens in Logboeken, inclusief resources in azure of on-premises. De ondersteunde Log Analytics oplossingen worden hieronder weer gegeven:

- [Prestatie meter items](./data-sources-performance-counters.md) voor Windows & Linux-machines
- [Heartbeat-records voor Status van agent](../insights/solution-agenthealth.md)
- [Update beheer](../../automation/update-management/overview.md) records
- Logboeken met [gebeurtenis gegevens](./data-sources-windows-events.md)

Er zijn veel voor delen voor het gebruik van **metrische waarschuwingen voor logboeken** die zijn gebaseerd op query [waarschuwingen](./alerts-log.md) in Azure. enkele hiervan worden hieronder weer gegeven:

- Metrische waarschuwingen bieden bijna realtime bewakings mogelijkheden en metrische waarschuwingen voor logboeken worden gegevens uit de logboek bron gevorken om ervoor te zorgen.
- Metrische waarschuwingen zijn stateful-slechts eenmaal meldingen wanneer een waarschuwing wordt geactiveerd en eenmaal wanneer de waarschuwing is opgelost; in tegens telling tot logboek waarschuwingen, die stateless zijn en tijdens elk interval worden geactiveerd als aan de voor waarde van de waarschuwing wordt voldaan.
- Metrische waarschuwingen voor het logboek bieden meerdere dimensies, waardoor filteren kan worden toegepast op specifieke waarden, zoals computers, type besturings systeem, enzovoort. zonder de behoefte aan penning-query in Analytics.

> [!NOTE]
> Specifieke metriek en/of dimensie worden alleen weer gegeven als de gegevens in de gekozen periode aanwezig zijn. Deze metrische gegevens zijn beschikbaar voor klanten met Azure Log Analytics-werk ruimten.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metrische gegevens en dimensies die worden ondersteund voor logboeken

 Metrische waarschuwingen ondersteunen waarschuwingen voor metrische gegevens die gebruikmaken van dimensies. U kunt dimensies gebruiken om uw metrische gegevens te filteren op het juiste niveau. De volledige lijst met metrische gegevens die worden ondersteund voor logboeken vanuit [log Analytics werk ruimten](./metrics-supported.md#microsoftoperationalinsightsworkspaces) wordt vermeld. over ondersteunde oplossingen.

> [!NOTE]
> Als u een ondersteunde metrische gegevens uit een Log Analytics-werk ruimte wilt weer geven via [Azure monitor metrieken](./metrics-charts.md), moet u een metrische waarschuwing voor het logboek maken op basis van die specifieke metriek. De dimensies die u hebt gekozen in de metrische waarschuwing voor logboeken, worden alleen weer gegeven voor verkennen via Azure Monitor metrieke waarden.

## <a name="creating-metric-alert-for-log-analytics"></a>Waarschuwing voor metrische gegevens maken voor Log Analytics

Metrische gegevens uit populaire logboeken worden gepiped voordat deze in Log Analytics wordt verwerkt, in Azure Monitor metrieke waarden. Op deze manier kunnen gebruikers gebruikmaken van de mogelijkheden van het metrische platform en de metrische waarschuwing, met inbegrip van waarschuwingen met een frequentie van minder dan 1 minuut.
Hieronder ziet u de mogelijkheid om een metrische waarschuwing voor logboeken te maken.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Vereisten voor de metrische waarschuwing voor logboeken

Voordat de metriek wordt vastgelegd voor logboeken die zijn verzameld op Log Analytics gegevens, moet het volgende worden ingesteld en beschikbaar zijn:

1. **Actieve log Analytics-werk ruimte**: er moet een geldige en actieve log Analytics-werk ruimte aanwezig zijn. Zie [een log Analytics-werk ruimte maken in azure Portal](../learn/quick-create-workspace.md)voor meer informatie.
2. De **agent is geconfigureerd voor log Analytics-werk ruimte**: de agent moet worden geconfigureerd voor virtuele machines van Azure (en/of) om gegevens te verzenden naar de log Analytics werk ruimte die in de vorige stap wordt gebruikt. Zie [log Analytics-agent Overview (](./agents-overview.md)Engelstalig) voor meer informatie.
3. **Ondersteunde log Analytics oplossingen is geïnstalleerd**: log Analytics oplossing moet worden geconfigureerd en verzenden van gegevens in log Analytics oplossingen die door de werk ruimte worden ondersteund, zijn [prestatie meter items voor Windows & Linux](./data-sources-performance-counters.md), [heartbeat-records voor status van agent](../insights/solution-agenthealth.md), [Update beheer](../../automation/update-management/overview.md)en [gebeurtenis gegevens](./data-sources-windows-events.md).
4. **Log Analytics oplossingen die zijn geconfigureerd voor het verzenden van Logboeken**: voor de log Analytics-oplossing moeten de vereiste Logboeken/gegevens zijn opgegeven die overeenkomen met de [metrieken voor log Analytics-werk ruimten](./metrics-supported.md#microsoftoperationalinsightsworkspaces) zijn ingeschakeld. Bijvoorbeeld: voor het aantal *% beschik bare geheugen* van de functie moet eerst worden geconfigureerd in de oplossing voor [prestatie meter items](./data-sources-performance-counters.md) .

## <a name="configuring-metric-alert-for-logs"></a>Waarschuwing voor metrische gegevens voor logboeken configureren

 Metrische waarschuwingen kunnen worden gemaakt en beheerd met behulp van de Azure Portal, Resource Manager-sjablonen, REST API, Power shell en Azure CLI. Aangezien metrische waarschuwingen voor logboeken een variant van metrische waarschuwingen zijn: zodra de vereisten zijn uitgevoerd, kunnen er metrische waarschuwingen voor logboeken worden gemaakt voor de opgegeven Log Analytics-werk ruimte. Alle kenmerken en functies van [metrische waarschuwingen](./alerts-metric-near-real-time.md) zijn ook van toepassing op metrische waarschuwingen voor Logboeken. inclusief Payload-schema, toepasselijke quotum limieten en gefactureerde prijzen.

Zie [metrische waarschuwingen maken en beheren](./alerts-metric.md)voor stapsgewijze Details en voor beelden. In het bijzonder, voor metrische waarschuwingen voor logboeken, volgt u de instructies voor het beheren van metrische waarschuwingen en zorgt u voor het volgende:

- Doel voor metrische waarschuwing is een geldige *log Analytics-werk ruimte*
- Het signaal dat is gekozen voor de metrische waarschuwing voor de geselecteerde *log Analytics werk ruimte* is van het type **metric**
- Filteren op specifieke voor waarden of resources met behulp van dimensie filters; metrische gegevens voor logboeken zijn meerdere dimensies
- Bij het configureren van de *signaal logica* kan één waarschuwing worden gemaakt om meerdere dimensie waarden (zoals computer) te omvatten
- Als er **geen** Azure portal wordt gebruikt voor het maken van een metrische waarschuwing voor de geselecteerde *log Analytics werk ruimte*; vervolgens moet de gebruiker hand matig een expliciete regel maken voor het converteren van logboek gegevens naar een metriek met behulp van [Azure monitor geplande query regels](/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Bij het maken van een metrische waarschuwing voor Log Analytics werk ruimte via Azure Portal-overeenkomende regel voor het converteren van logboek gegevens naar metriek via [Azure monitor-geplande query regels](/rest/api/monitor/scheduledqueryrules) wordt automatisch op de achtergrond gemaakt, *zonder dat dit tussen komst van een gebruiker of actie nodig* is. Voor de metrische waarschuwing voor het maken van logboeken met behulp van een andere methode dan Azure Portal, Zie [resource sjabloon voor metrische waarschuwingen voor logboeken](#resource-template-for-metric-alerts-for-logs) in het voor beeld van een ScheduledQueryRule op basis van metrische conversie regel voordat de metrische waarschuwing wordt gemaakt. anders worden er geen gegevens weer gegeven voor de metrische waarschuwing op Logboeken die zijn gemaakt.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Resource sjabloon voor metrische waarschuwingen voor logboeken

Zoals eerder vermeld, is het proces voor het maken van metrische waarschuwingen uit logboeken twee ledige:

1. Een regel maken voor het extra heren van metrische gegevens uit de ondersteunde logboeken met behulp van de scheduledQueryRule-API
2. Een waarschuwing voor metrische gegevens maken voor metrische gegevens die zijn geëxtraheerd uit het logboek (in stap 1-4) en Log Analytics werk ruimte als doel resource

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Metrische waarschuwingen voor logboeken met statische drempel waarde

Om hetzelfde te krijgen, kunt u de voor beeld-Azure Resource Manager onderstaande sjabloon gebruiken, waarbij het maken van een waarschuwing voor een statische drempel waarde is afhankelijk van het maken van de regel voor het extra heren van metrische gegevens uit logboeken via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Stel dat de bovenstaande JSON is opgeslagen als metricfromLogsAlertStatic.jsen kan worden gekoppeld aan een JSON-parameter bestand voor het maken van een resource sjabloon. Hieronder vindt u een voor beeld van een JSON-bestand voor de para meter:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Ervan uitgaande dat het bovenstaande parameter bestand wordt opgeslagen als metricfromLogsAlertStatic.parameters.jsop; vervolgens kan een metrische waarschuwing voor logboeken worden gemaakt met behulp van een [resource sjabloon om te maken in azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

U kunt ook de Azure Power shell-opdracht hieronder ook gebruiken:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Of gebruik een resource sjabloon implementeren met behulp van Azure CLI:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Metrische waarschuwingen voor logboeken met dynamische drempel waarden

Om hetzelfde te halen, kunt u de voor beeld-Azure Resource Manager sjabloon hieronder gebruiken, waarbij een waarschuwing voor een dynamische drempel waarde wordt gemaakt, afhankelijk is van het maken van de regel voor het extra heren van metrische gegevens uit logboeken via scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Stel dat de bovenstaande JSON is opgeslagen als metricfromLogsAlertDynamic.jsen kan worden gekoppeld aan een JSON-parameter bestand voor het maken van een resource sjabloon. Hieronder vindt u een voor beeld van een JSON-bestand voor de para meter:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Ervan uitgaande dat het bovenstaande parameter bestand wordt opgeslagen als metricfromLogsAlertDynamic.parameters.jsop; vervolgens kan een metrische waarschuwing voor logboeken worden gemaakt met behulp van een [resource sjabloon om te maken in azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

U kunt ook de Azure Power shell-opdracht hieronder ook gebruiken:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Of gebruik een resource sjabloon implementeren met behulp van Azure CLI:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [metrische waarschuwingen](alerts-metric.md).
- Meer informatie over [logboek waarschuwingen in azure](./alerts-unified-log.md).
- Meer informatie over [waarschuwingen in azure](alerts-overview.md).