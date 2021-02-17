---
title: Azure service Health-waarschuwingen verzenden met PagerDuty
description: Krijg persoonlijke meldingen over service status gebeurtenissen aan uw PagerDuty-exemplaar.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594531"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Azure service Health-waarschuwingen verzenden met PagerDuty met behulp van webhooks

Dit artikel laat u zien hoe u Azure service Health Notifications kunt instellen via PagerDuty met behulp van een webhook. Met het aangepaste Microsoft Azure integratie type van [PagerDuty](https://www.pagerduty.com/)kunt u moeiteloos service Health waarschuwingen toevoegen aan uw nieuwe of bestaande PagerDuty-Services.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Een service Health Integration-URL maken in PagerDuty
1.  Zorg ervoor dat u zich hebt aangemeld voor en bent aangemeld bij uw [PagerDuty](https://www.pagerduty.com/) -account.

1.  Navigeer naar het gedeelte **Services** in PagerDuty.

    ![De sectie ' Services ' in PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selecteer **nieuwe service toevoegen** of open een bestaande service die u hebt ingesteld.

1.  Selecteer in de **integratie-instellingen** het volgende:

    a. **Integratie type**: Microsoft Azure

    b. **Integratie naam**: \<Name\>

    ![De integratie-instellingen in PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Vul alle vereiste velden in en selecteer **toevoegen**.

1.  Open deze nieuwe integratie en kopieer de integratie- **URL** en sla deze op.

    ![De integratie-URL in PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Een waarschuwing maken met behulp van PagerDuty in de Azure Portal
### <a name="for-a-new-action-group"></a>Voor een nieuwe actie groep:
1. Volg de stappen 1 tot en met 8 in een [waarschuwing maken voor een service status melding voor een nieuwe actie groep met behulp van de Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. Definieer in de lijst met **acties**:

    a. **Actie Type:** *webhook*

    b. **Details:** De URL van de PagerDuty- **integratie** die u eerder hebt opgeslagen.

    c. **Naam:** Naam, alias of id van de webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de waarschuwing te maken.

### <a name="for-an-existing-action-group"></a>Voor een bestaande actie groep:
1. Selecteer in de [Azure Portal](https://portal.azure.com/) **monitor**.

1. Selecteer in de sectie **instellingen** de optie **actie groepen**.

1. Zoek en selecteer de actie groep die u wilt bewerken.

1. Toevoegen aan de lijst met **acties**:

    a. **Actie Type:** *webhook*

    b. **Details:** De URL van de PagerDuty- **integratie** die u eerder hebt opgeslagen.

    c. **Naam:** Naam, alias of id van de webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de actie groep bij te werken.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Uw integratie van webhooks testen via een HTTP POST-aanvraag
1. Maak de service status Payload die u wilt verzenden. U kunt een voor beeld van een service Health-webhook Payload vinden op [webhooks voor Azure-activiteiten logboek waarschuwingen](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. U ontvangt een `202 Accepted` met een bericht met de gebeurtenis-id.

1. Ga naar [PagerDuty](https://www.pagerduty.com/) om te bevestigen dat de integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van webhookmeldingen voor bestaande problematische beheersystemen](service-health-alert-webhook-guide.md).
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Meer informatie over [servicestatusmeldingen](./service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
