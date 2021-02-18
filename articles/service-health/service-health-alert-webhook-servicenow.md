---
title: Azure service Health-waarschuwingen verzenden met ServiceNow
description: Krijg persoonlijke meldingen over service status gebeurtenissen aan uw ServiceNow-exemplaar.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: df6596dd9853a792b5bfdb333361a2b9cd02a347
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588181"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Azure service Health-waarschuwingen verzenden met ServiceNow met behulp van webhooks

In dit artikel leest u hoe u Azure service Health Alerts integreert met ServiceNow met behulp van een webhook. Nadat u de integratie van webhook met uw ServiceNow-exemplaar hebt ingesteld, ontvangt u waarschuwingen via uw bestaande meldings infrastructuur wanneer de Azure-Service problemen van invloed zijn op u. Telkens wanneer een Azure Service Health waarschuwing wordt geactiveerd, wordt er een webhook aangeroepen via de script REST API van ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Een REST API in een script maken in ServiceNow

1.  Zorg ervoor dat u zich hebt aangemeld voor en bent aangemeld bij uw [ServiceNow](https://www.servicenow.com/) -account.

1.  Ga naar de sectie **systeem webservices** in ServiceNow en selecteer **scripted rest-api's**.

    ![De sectie ' scripted web service ' in ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selecteer **Nieuw** om een nieuwe gescriptde rest-service te maken.
 
    ![De knop Nieuw script REST API in ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Voeg een **naam** toe aan uw rest API en stel de **API-id** in op `azureservicehealth` .

1.  Selecteer **Indienen**.

    ![De instellingen voor ' REST API ' in ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selecteer de REST API die u hebt gemaakt en selecteer op het tabblad **resources** de optie **Nieuw**.

    ![Het tabblad resource in ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Geef** uw nieuwe resource een naam `event` en wijzig de **HTTP-methode** in `POST` .

1.  Voeg in de sectie **script** de volgende Java script-code toe:

    >[!NOTE]
    >U moet de `<secret>` `<group>` waarde, en `<email>` in het onderstaande script bijwerken.
    >* `<secret>` moet een wille keurige teken reeks zijn, zoals een GUID
    >* `<group>` moet de ServiceNow-groep waaraan u het incident wilt toewijzen
    >* `<email>` moet de specifieke persoon waaraan u het incident wilt toewijzen (optioneel)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Schakel op het tabblad Beveiliging het selectie vakje **verificatie vereist** en selecteer **verzenden** in. Met `<secret>` deze instelling wordt deze API beveiligd.

    ![Het selectie vakje ' authenticatie vereist ' in ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Terug op de sectie scripted REST Api's vindt u het basis- **API-pad** voor uw nieuwe rest API:

     ![Het ' basis-API-pad ' in ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Uw volledige integratie-URL ziet er als volgt uit:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Een waarschuwing maken met behulp van ServiceNow in de Azure Portal
### <a name="for-a-new-action-group"></a>Voor een nieuwe actie groep:
1. Volg de stappen 1 tot en met 8 in [dit artikel](./alerts-activity-log-service-notifications-portal.md) om een waarschuwing met een nieuwe actie groep te maken.

1. Definieer in de lijst met **acties**:

    a. **Actie Type:** *webhook*

    b. **Details:** De URL van de ServiceNow- **integratie** die u eerder hebt opgeslagen.

    c. **Naam:** Naam, alias of id van de webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de waarschuwing te maken.

### <a name="for-an-existing-action-group"></a>Voor een bestaande actie groep:
1. Selecteer in de [Azure Portal](https://portal.azure.com/) **monitor**.

1. Selecteer in de sectie **instellingen** de optie **actie groepen**.

1. Zoek en selecteer de actie groep die u wilt bewerken.

1. Toevoegen aan de lijst met **acties**:

    a. **Actie Type:** *webhook*

    b. **Details:** De URL van de ServiceNow- **integratie** die u eerder hebt opgeslagen.

    c. **Naam:** Naam, alias of id van de webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de actie groep bij te werken.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Uw integratie van webhooks testen via een HTTP POST-aanvraag
1. Maak de service status Payload die u wilt verzenden. U kunt een voor beeld van een service Health-webhook Payload vinden op [webhooks voor Azure-activiteiten logboek waarschuwingen](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. U ontvangt een `200 OK` antwoord met het bericht ' incident gemaakt '.

1. Ga naar [ServiceNow](https://www.servicenow.com/) om te bevestigen dat de integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van webhookmeldingen voor bestaande problematische beheersystemen](service-health-alert-webhook-guide.md).
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Meer informatie over [servicestatusmeldingen](./service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
