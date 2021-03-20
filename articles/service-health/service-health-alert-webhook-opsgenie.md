---
title: Azure service Health-waarschuwingen verzenden met OpsGenie met behulp van webhooks
description: Krijg persoonlijke meldingen over service status gebeurtenissen aan uw OpsGenie-exemplaar.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594596"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Azure service Health-waarschuwingen verzenden met OpsGenie met behulp van webhooks

Dit artikel laat u zien hoe u Azure service Health-waarschuwingen kunt instellen met OpsGenie met behulp van een webhook. Door de Azure Service Health-integratie van [OpsGenie](https://www.opsgenie.com/)te gebruiken, kunt u Azure service Health-waarschuwingen door sturen naar OpsGenie. Met OpsGenie kunt u de juiste mensen op de hoogte stellen op basis van planningen op aanroepen, met behulp van e-mail, tekst berichten (SMS), telefoon gesprekken, iOS & Android-push meldingen en het escaleren van waarschuwingen totdat de waarschuwing wordt bevestigd of gesloten.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Een service Health Integration-URL maken in OpsGenie
1.  Zorg ervoor dat u zich hebt aangemeld voor en bent aangemeld bij uw [OpsGenie](https://www.opsgenie.com/) -account.

1.  Navigeer naar de sectie **integraties** in OpsGenie.

    ![De sectie integraties in OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Selecteer de knop integratie **Azure service Health** .

    ![De knop Azure Service Health in OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  Geef een **naam** op voor de waarschuwing en stel het veld **toegewezen aan team in** .

1.  Vul de andere velden in, zoals **ontvangers**, **ingeschakeld** en **waarschuwingen onderdrukken**.

1.  Kopieer de integratie- **URL** en sla deze op. deze moet al zijn `apiKey` toegevoegd aan het einde.

    ![De integratie-URL in OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  **Integratie voor opslaan** selecteren

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Een waarschuwing maken met behulp van OpsGenie in de Azure Portal
### <a name="for-a-new-action-group"></a>Voor een nieuwe actie groep:
1. Volg de stappen 1 tot en met 8 in een [waarschuwing maken voor een service status melding voor een nieuwe actie groep met behulp van de Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. Definieer in de lijst met **acties**:

    a. **Actie Type:** *webhook*

    b. **Details:** De URL van de OpsGenie- **integratie** die u eerder hebt opgeslagen.

    c. **Naam:** Naam, alias of id van de webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de waarschuwing te maken.

### <a name="for-an-existing-action-group"></a>Voor een bestaande actie groep:
1. Selecteer in de [Azure Portal](https://portal.azure.com/) **monitor**.

1. Selecteer in de sectie **instellingen** de optie **actie groepen**.

1. Zoek en selecteer de actie groep die u wilt bewerken.

1. Toevoegen aan de lijst met **acties**:

    a. **Actie Type:** *webhook*

    b. **Details:** De URL van de OpsGenie- **integratie** die u eerder hebt opgeslagen.

    c. **Naam:** Naam, alias of id van de webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de actie groep bij te werken.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Uw integratie van webhooks testen via een HTTP POST-aanvraag
1. Maak de service status Payload die u wilt verzenden. U kunt een voor beeld van een service Health-webhook Payload vinden op [webhooks voor Azure-activiteiten logboek waarschuwingen](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. U ontvangt een `200 OK` antwoord met het bericht status geslaagd.

1. Ga naar [OpsGenie](https://www.opsgenie.com/) om te bevestigen dat de integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van webhookmeldingen voor bestaande problematische beheersystemen](service-health-alert-webhook-guide.md).
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Meer informatie over [servicestatusmeldingen](./service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
