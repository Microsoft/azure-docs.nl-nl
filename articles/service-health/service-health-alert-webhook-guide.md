---
title: Azure Service Health meldingen verzenden via webhooks
description: Verzend gepersonaliseerde meldingen over service Health-gebeurtenissen naar uw bestaande probleem beheersysteem.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594621"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Een webhook gebruiken voor het configureren van status meldingen voor probleem beheersystemen

In dit artikel wordt beschreven hoe u Azure Service Health-waarschuwingen configureert om gegevens te verzenden via webhooks naar uw bestaande meldings systeem.

U kunt Service Health waarschuwingen configureren om u op de hoogte te stellen van SMS-berichten of-e-mail wanneer een Azure-service incident van invloed is.

Maar mogelijk hebt u al een bestaand extern meldings systeem dat u wilt gebruiken. In dit artikel worden de belangrijkste onderdelen van de webhook Payload aangeduid. Daarnaast wordt beschreven hoe u aangepaste waarschuwingen maakt om u te waarschuwen wanneer er relevante service problemen optreden.

Als u een vooraf geconfigureerde integratie wilt gebruiken, raadpleegt u:
* [Waarschuwingen configureren met ServiceNow](service-health-alert-webhook-servicenow.md)
* [Waarschuwingen configureren met PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Waarschuwingen configureren met OpsGenie](service-health-alert-webhook-opsgenie.md)

**Een inleidende video bekijken:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Een aangepaste melding configureren met behulp van de Service Health webhook Payload
Als u uw eigen aangepaste integratie van webhook wilt instellen, moet u de JSON-Payload parseren die via Service Health melding wordt verzonden.

Bekijk [een voor beeld van een nettolading voor](../azure-monitor/alerts/activity-log-alerts-webhook.md) `ServiceHealth` webhooks.

U kunt controleren of het een service status waarschuwing is door te kijken naar `context.eventSource == "ServiceHealth"` . De volgende eigenschappen zijn het meest relevant:
- **data. context. activityLog. status**
- **data. context. activityLog. level**
- **data. context. activityLog. subscriptionId**
- **data. context. activityLog. Properties. title**
- **data. context. activityLog. Properties. impactStartTime**
- **data. context. activityLog. Properties. Communication**
- **data. context. activityLog. Properties. impactedServices**
- **data. context. activityLog. Properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Een koppeling naar het dash board van Service Health maken voor een incident
U kunt een directe koppeling naar uw Service Health dash board maken op een bureau blad of mobiel apparaat door een speciale URL te genereren. Gebruik de *trackingId* en de eerste drie en de laatste drie cijfers van uw *subscriptionId* met de volgende indeling:

https <i></i> ://app.Azure.com/h/*&lt; trackingId &gt;* / *&lt; eerste drie en laatste drie cijfers subscriptionId &gt;*

Als uw *subscriptionId* bijvoorbeeld bba14129-e895-429b-8809-278e836ecdb3 is en uw *TRACKINGID* 0DET-Urb is, is uw service Health URL:

https <i></i> ://app.Azure.com/h/0DET-Urb/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Het niveau gebruiken om de ernst van het probleem te detecteren
Van laag tot hoogste urgentie kan de eigenschap **Level** in de payload *informatie*, *waarschuwing*, *fout* of *kritiek* zijn.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>De betrokken Services parseren om het incident bereik te bepalen
Service Health waarschuwingen kunnen u informeren over problemen in meerdere regio's en services. Als u meer informatie wilt, moet u de waarde van weer parseren `impactedServices` .

De inhoud die zich in bevindt, is een geescapede [JSON](https://json.org/) -teken reeks die, wanneer unesc, een ander JSON-object bevat dat regel matig kan worden geparseerd. Bijvoorbeeld:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

 wordt:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

In dit voor beeld ziet u problemen voor:
- ' Waarschuwingen & metrische gegevens ' in Australië-oost en Australië-zuidoost.
- App Service in Australië-zuidoost.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Uw integratie van webhooks testen via een HTTP POST-aanvraag

Volg deze stappen:

1. Maak de service status Payload die u wilt verzenden. Bekijk een voor beeld van een service Health-webhook Payload op [webhooks voor Azure-activiteiten logboek waarschuwingen](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   U ontvangt een antwoord met de melding ' 2XX geslaagd '.

1. Ga naar [PagerDuty](https://www.pagerduty.com/) om te bevestigen dat de integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Meer informatie over [servicestatusmeldingen](./service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
