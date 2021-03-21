---
title: Schema's voor de Azure Security Center waarschuwingen
description: In dit artikel worden de verschillende schema's beschreven die worden gebruikt door Azure Security Center voor beveiligings waarschuwingen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 55f8d37d435aa8adeb4d97246ce7b2c7811140be
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557995"
---
# <a name="security-alerts-schemas"></a>Schema's voor beveiligings waarschuwingen

Als Azure Defender is ingeschakeld voor uw abonnement, ontvangt u beveiligings waarschuwingen wanneer Security Center bedreigingen voor hun resources detecteert.

U kunt deze beveiligings waarschuwingen bekijken op de pagina's van Azure Security Center **Threat Protection** of via externe hulpprogram ma's, zoals:

- [Azure Sentinel](../sentinel/index.yml) : de Cloud-native Siem van micro soft. De Sentinel-connector ontvangt waarschuwingen van Azure Security Center en verzendt deze naar de [log Analytics-werk ruimte](../azure-monitor/logs/quick-create-workspace.md) voor Azure Sentinel.
- Siem's van derden: gegevens verzenden naar [Azure Event hubs](../event-hubs/index.yml). Integreer vervolgens uw event hub-gegevens met een SIEM van derden. Zie voor meer informatie [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md).
- [De rest API](/rest/api/securitycenter/) : als u de rest API gebruikt voor toegang tot waarschuwingen, raadpleegt u de [online Alerts API-documentatie](/rest/api/securitycenter/alerts).

Als u een programmatische methode gebruikt om de waarschuwingen te verbruiken, hebt u het juiste schema nodig om de velden te vinden die relevant zijn voor u. Als u exporteert naar een event hub of als u werk stroom automatisering wilt activeren met algemene HTTP-connectors, gebruikt u de schema's om de JSON-objecten correct te parseren.

>[!IMPORTANT]
> Het schema wijkt enigszins af voor elk van deze scenario's. Zorg er dus voor dat u het relevante tabblad hieronder selecteert.


## <a name="the-schemas"></a>De schema's 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Werk stroom automatisering en doorlopend exporteren naar Event hub](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Voor beeld-JSON voor waarschuwingen die worden verzonden naar Logic Apps, Event hub en Siem's van derden

Hieronder vindt u het schema van de waarschuwings gebeurtenissen die worden door gegeven aan:

- Azure Logic app-exemplaren die in de werk stroom automatisering van Security Center zijn geconfigureerd
- Azure Event hub met behulp van de continue export functie van Security Center

Zie voor meer informatie over de functie werk stroom automatisering de optie [reacties automatiseren op Security Center triggers](workflow-automation.md).

Zie [continu exporteren Security Center gegevens](continuous-export.md)voor meer informatie over doorlopend exporteren.

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel-en Log Analytics-werk ruimten](#tab/schema-sentinel)

De Sentinel-connector ontvangt waarschuwingen van Azure Security Center en verzendt deze naar de Log Analytics-werk ruimte voor Azure Sentinel. 

Als u een verklikker Case of incident wilt maken met behulp van Security Center waarschuwingen, hebt u het schema nodig voor de hieronder weer gegeven waarschuwingen. 

Raadpleeg [de documentatie](../sentinel/index.yml)voor meer informatie over Azure Sentinel.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-activiteitenlogboek](#tab/schema-activitylog)

Azure Security Center controles gegenereerd beveiligings waarschuwingen als gebeurtenissen in azure-activiteiten logboek.

U kunt de gebeurtenissen voor beveiligings waarschuwingen in het activiteiten logboek weer geven door te zoeken naar de gebeurtenis waarschuwing activeren, zoals wordt weer gegeven:

[![Zoeken in het activiteiten logboek voor de gebeurtenis waarschuwing activeren](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Voor beeld-JSON voor waarschuwingen die zijn verzonden naar Azure-activiteiten logboek

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Het gegevens model van het schema

|Veld|Beschrijving|
|----|----|
|**detailhandelkanalen**|Constante, ' bewerking '|
|**correlationId**|De meldings-ID Azure Security Center|
|**beschrijvingen**|Beschrijving van de waarschuwing|
|**eventDataId**|Zie correlationId|
|**eventName**|De subvelden value en localizedValue bevatten de weergave naam van de waarschuwing|
|**category**|De subvelden value en localizedValue zijn constant-' Security '|
|**eventTimestamp**|UTC-tijds tempel voor het moment waarop de waarschuwing is gegenereerd|
|**id**|De volledig gekwalificeerde waarschuwings-ID|
|**niveau**|Constante, ' informatief '|
|**operationId**|Zie correlationId|
|**operationName**|Het veld waarde is constant: ' micro soft. Security/locations/Alerts/Activate/Action ' en de gelokaliseerde waarde is ' waarschuwing activeren ' (de gebruiker kan mogelijk worden gelokaliseerd pari)|
|**resourceGroupName**|Bevat de naam van de resource groep|
|**resourceProviderName**|De subvelden value en localizedValue zijn constant-"micro soft. Security"|
|**Resource**|De subvelden value en localizedValue zijn constant-"micro soft. Security/locations/Alerts"|
|**resourceId**|De volledig gekwalificeerde Azure-Resource-ID|
|**status**|De subvelden value en localizedValue zijn constant-"actief"|
|**subStatus**|De subvelden value en localizedValue zijn leeg|
|**submissionTimestamp**|De UTC-tijds tempel van het verzenden van gebeurtenissen naar het activiteiten logboek|
|**subscriptionId**|De abonnements-ID van de aangetaste resource|
|**properties**|Een JSON-verzameling extra eigenschappen die betrekking hebben op de waarschuwing. Deze kunnen worden gewijzigd van de ene waarschuwing naar de andere, maar de volgende velden worden in alle waarschuwingen weer gegeven:<br>-Ernst: de ernst van de aanval<br>-compromisedEntity: de naam van de aangetaste bron<br>-remediationSteps: matrix van herstels tappen die moeten worden genomen<br>-intentie: de bedoeling van de Kill-keten van de waarschuwing. Mogelijke intenties worden gedocumenteerd in de [tabel met bedoelingen](alerts-reference.md#intentions)|
|**relatedEvents**|Constante-lege matrix|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph is de gateway naar gegevens en intelligentie in Microsoft 365. Het biedt een uniform programmeerbaar model dat u kunt gebruiken voor toegang tot de enorme hoeveelheid gegevens in Microsoft 365, Windows 10 en Enterprise Mobility + Security. Gebruik de schat aan gegevens in Microsoft Graph om apps te bouwen voor organisaties en consumenten die met miljoenen gebruikers communiceren.

Het schema en een JSON-weer gave voor beveiligings waarschuwingen die naar MS Graph worden verzonden, zijn beschikbaar in [de Microsoft Graph documentatie](/graph/api/resources/alert).

---


## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de schema's beschreven die worden gebruikt bij het verzenden van informatie over beveiligings waarschuwingen Azure Security Center de hulpprogram ma's voor beveiliging tegen bedreigingen.

Zie de volgende pagina's voor meer informatie over de manieren om toegang te krijgen tot beveiligings waarschuwingen van buiten Security Center:

- [Azure-Sentinel](../sentinel/index.yml) : de Cloud-native Siem van micro soft
- [Azure-Event hubs](../event-hubs/index.yml) -de volledig beheerde, realtime Service voor gegevens opname van micro soft
- [Security Center-gegevens continu exporteren](continuous-export.md)
- [Log Analytics werk ruimten](../azure-monitor/logs/quick-create-workspace.md) : Azure monitor slaat logboek gegevens op in een log Analytics-werk ruimte, een container die gegevens en configuratie gegevens bevat