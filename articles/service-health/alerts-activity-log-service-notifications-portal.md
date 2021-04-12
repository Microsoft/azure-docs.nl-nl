---
title: Waarschuwingen voor activiteiten logboeken ontvangen op Azure-service meldingen met behulp van Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het instellen van waarschuwingen voor activiteiten logboeken voor service status meldingen met behulp van de Azure Portal.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48126d923cb0baa33058c6fd55e48f31d793fade
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570185"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Waarschuwingen voor activiteiten logboeken maken voor service meldingen met behulp van de Azure Portal
## <a name="overview"></a>Overzicht

In dit artikel leest u hoe u met behulp van de Azure Portal waarschuwingen voor activiteiten Logboeken kunt instellen voor service status meldingen via de Azure Portal.  

Servicestatusmeldingen worden opgeslagen in het [Activiteitenlogboek van Azure](../azure-monitor/essentials/platform-logs-overview.md). Gezien het grote aantal informatie dat in het activiteiten logboek is opgeslagen, is er een afzonderlijke gebruikers interface waarmee u waarschuwingen voor service status meldingen gemakkelijker kunt bekijken en instellen. 

U kunt een waarschuwing ontvangen wanneer Azure servicestatusmeldingen verzendt naar uw Azure-abonnement. U kunt de waarschuwing configureren op basis van:

- De klasse van service status meldingen (Service problemen, gepland onderhoud, status adviezen, beveiligings adviezen).
- Het betrokken abonnement.
- De betrokken service(s).
- De betrokken regio('s).

> [!NOTE]
> Service status meldingen verzenden geen waarschuwingen voor resource Health-gebeurtenissen.

U kunt ook configureren naar wie de waarschuwing moet worden verzonden:

- Selecteer een bestaande actiegroep.
- Maak een nieuwe actiegroep maken (die kan worden gebruikt voor toekomstige waarschuwingen).

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

Zie [Resource Manager-sjablonen](../azure-monitor/alerts/alerts-activity-log.md)voor meer informatie over het configureren van waarschuwingen voor service status meldingen met behulp van Azure Resource Manager sjablonen.

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Bekijk een video over het instellen van uw eerste Azure Service Health waarschuwing

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Service Health waarschuwing maken met behulp van Azure Portal
1. Selecteer in de [portal](https://portal.azure.com) **service Health**.

    ![De ' Service Health-Service](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Selecteer in de sectie **waarschuwingen** **Health Alerts**.

    ![Het tabblad status meldingen](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecteer **service status waarschuwing toevoegen** en vul de velden in.

    ![De opdracht ' service Health alert maken '](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecteer het **abonnement**, de **Services** en de **regio's** waarvoor u een waarschuwing wilt ontvangen.

    [![Het dialoog venster waarschuwing voor activiteiten logboek toevoegen](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Dit abonnement wordt gebruikt om de waarschuwing voor activiteiten logboek op te slaan. De resource van de waarschuwing wordt geïmplementeerd voor dit abonnement en controleert gebeurtenissen in het activiteiten logboek.

5. Kies de **gebeurtenis typen** waarvoor u een waarschuwing wilt ontvangen: *service probleem*, *gepland onderhoud*, *status adviezen* en *beveiligings advies*.

6. Klik op **actie groep selecteren** als u een bestaande actie groep wilt kiezen of een nieuwe actie groep wilt maken. Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](../azure-monitor/alerts/action-groups.md).


7. Definieer uw waarschuwings Details door een naam en **Beschrijving** voor de **waarschuwings regel** in te voeren.

8. Selecteer de **resource groep** waar u de waarschuwing wilt opslaan.



Binnen een paar minuten is de waarschuwing actief en begint deze te activeren op basis van de voor waarden die u hebt opgegeven tijdens het maken.

Meer informatie over het [configureren van webhook-meldingen voor bestaande probleem beheersystemen](service-health-alert-webhook-guide.md). Zie [webhooks voor Azure-activiteiten logboek waarschuwingen](../azure-monitor/alerts/activity-log-alerts-webhook.md)voor informatie over het webhook-schema voor waarschuwingen voor activiteiten Logboeken.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [best practices voor het instellen van Azure Service Health-waarschuwingen](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Meer informatie over het [instellen van mobiele pushmeldingen voor Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Meer informatie over het [configureren van webhookmeldingen voor bestaande problematische beheersystemen](service-health-alert-webhook-guide.md).
- Meer informatie over [servicestatusmeldingen](service-notifications.md).
- Meer informatie over [meldingssnelheidsbeperking](../azure-monitor/alerts/alerts-rate-limiting.md).
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Een [overzicht van waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
