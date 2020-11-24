---
title: Het webhook-schema dat wordt gebruikt in waarschuwingen voor activiteiten logboeken begrijpen
description: Meer informatie over het schema van de JSON dat wordt gepost naar een webhook-URL wanneer een activiteiten logboek waarschuwing wordt geactiveerd.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: 026613c3f5710137fb110153b34f9ed74bbf8a7b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522784"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks voor Azure-activiteiten logboek waarschuwingen
Als onderdeel van de definitie van een actie groep kunt u webhook-eind punten configureren voor het ontvangen van waarschuwings meldingen voor activiteiten Logboeken. Met webhooks kunt u deze meldingen naar andere systemen sturen voor nabewerkingen of aangepaste acties. In dit artikel ziet u hoe de payload voor het HTTP POST-bericht naar een webhook eruit ziet.

Zie [waarschuwingen voor Azure-activiteiten logboeken maken](activity-log-alerts.md)voor meer informatie over waarschuwingen voor activiteiten Logboeken.

Zie voor meer informatie over actie groepen [actie groepen maken](./action-groups.md).

> [!NOTE]
> U kunt ook het [schema common alert](./alerts-common-schema.md)gebruiken. Dit biedt het voor deel van het gebruik van een enkele uitbreid bare en Unified payload van waarschuwingen voor alle waarschuwings services in azure monitor voor uw webhook-integraties. [Meer informatie over de algemene schema definities voor waarschuwingen.](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>De webhook verifiëren
De webhook kan desgewenst autorisatie op basis van tokens gebruiken voor verificatie. De webhook-URI wordt opgeslagen met een token-ID, bijvoorbeeld `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue` .

## <a name="payload-schema"></a>Payload-schema
De JSON-nettolading die deel uitmaakt van de POST-bewerking verschilt op basis van het veld data. context. activityLog. Event source van de payload.

### <a name="common"></a>Algemeen

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administratief

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Beveiliging

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Aanbeveling

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Zie [service Health Notifications](../../service-health/service-notifications.md)(Engelstalig) voor specifieke schema Details over waarschuwingen voor het activiteiten logboek voor service status meldingen. Daarnaast leert u hoe u [service Health-webhook-meldingen kunt configureren met uw bestaande oplossingen voor probleem beheer](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Elementnaam | Beschrijving |
| --- | --- |
| status |Wordt gebruikt voor metrische waarschuwingen. Altijd ingesteld op geactiveerd voor waarschuwingen voor activiteiten Logboeken. |
| context |De context van de gebeurtenis. |
| resourceProviderName |De resource provider van de betrokken resource. |
| conditionType |Altijd ' gebeurtenis '. |
| naam |De naam van de waarschuwings regel. |
| id |De resource-ID van de waarschuwing. |
| beschrijving |De beschrijving van de waarschuwing wordt ingesteld wanneer de waarschuwing wordt gemaakt. |
| subscriptionId |Azure-abonnements-ID. |
| tijdstempel |Tijdstip waarop de gebeurtenis is gegenereerd door de Azure-service die de aanvraag heeft verwerkt. |
| resourceId |De resource-ID van de betrokken resource. |
| resourceGroupName |De naam van de resource groep voor de betrokken resource. |
| properties |Set `<Key, Value>` paren (dat wil zeggen `Dictionary<String, String>` ) met details over de gebeurtenis. |
| gebeurtenislog |Element dat meta gegevens bevat over de gebeurtenis. |
| autorisatie |De op rollen gebaseerde toegangs beheer eigenschappen van de gebeurtenis van Azure. Deze eigenschappen omvatten doorgaans de actie, de rol en het bereik. |
| category |De categorie van de gebeurtenis. Ondersteunde waarden zijn onder andere: beheer, waarschuwing, beveiliging, ServiceHealth en aanbeveling. |
| aanroeper |Het e-mail adres van de gebruiker die de bewerking, UPN-claim of SPN-claim heeft uitgevoerd op basis van Beschik baarheid. Kan null zijn voor bepaalde systeem aanroepen. |
| correlationId |Meestal een GUID in een teken reeks indeling. Gebeurtenissen met correlationId horen bij dezelfde grotere actie en delen meestal een correlationId. |
| eventDescription |Statische tekst beschrijving van de gebeurtenis. |
| eventDataId |De unieke id voor de gebeurtenis. |
| Source |De naam van de Azure-service of-infra structuur die de gebeurtenis heeft gegenereerd. |
| httpRequest |De aanvraag omvat meestal de clientRequestId-, clientIpAddress-en HTTP-methode (bijvoorbeeld PUT). |
| niveau |Een van de volgende waarden: kritiek, fout, waarschuwing en informatief. |
| operationId |Meestal een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |De naam van de bewerking. |
| properties |De eigenschappen van de gebeurtenis. |
| status |Tekenreeks. De status van de bewerking. Veelvoorkomende waarden zijn gestart, in uitvoering, geslaagd, mislukt, actief en opgelost. |
| subStatus |Bevat meestal de HTTP-status code van de bijbehorende REST-aanroep. Het kan ook andere teken reeksen bevatten die een substatus beschrijven. Algemene substatus waarden zijn onder andere OK (HTTP-status code: 200), gemaakt (HTTP-status code: 201), geaccepteerd (HTTP-status code: 202), geen inhoud (HTTP-status code: 204), ongeldige aanvraag (HTTP-status code: 400), niet gevonden (HTTP-status code: 404), conflict (HTTP-status code: 409), interne server fout (HTTP-status code: 500), service niet beschikbaar (HTTP-status code: 503) : 504). |

Zie [overzicht van het Azure-activiteiten logboek](./platform-logs-overview.md)voor specifieke schema Details over alle andere waarschuwingen voor activiteiten Logboeken.

## <a name="next-steps"></a>Volgende stappen
* Meer [informatie over het activiteiten logboek](./platform-logs-overview.md).
* [Voer Azure Automation-scripts (Runbooks) uit in azure-waarschuwingen](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Gebruik een logische app voor het verzenden van een SMS via Twilio vanuit een Azure-waarschuwing](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Dit voor beeld is voor metrische waarschuwingen, maar kan worden gewijzigd om te werken met een waarschuwing voor een activiteiten logboek.
* [Gebruik een logische app voor het verzenden van een bericht met een toegestane vertraging van een Azure-waarschuwing](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Dit voor beeld is voor metrische waarschuwingen, maar kan worden gewijzigd om te werken met een waarschuwing voor een activiteiten logboek.
* [Een logische app gebruiken om een bericht te verzenden naar een Azure-wachtrij vanuit een Azure-waarschuwing](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Dit voor beeld is voor metrische waarschuwingen, maar kan worden gewijzigd om te werken met een waarschuwing voor een activiteiten logboek.