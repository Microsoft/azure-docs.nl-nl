---
title: Resource Health-waarschuwingen maken met behulp van Azure Portal
description: Maak een waarschuwing met behulp van Azure Portal die u op de hoogte stelt wanneer uw Azure-resources niet meer beschikbaar zijn.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594638"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Resourcestatuswaarschuwingen configureren met de Azure-portal

Dit artikel laat u zien hoe u waarschuwingen voor activiteiten logboeken voor resource Health Notifications kunt instellen met behulp van de Azure Portal.

Azure Resource Health houdt u op de hoogte van de huidige en historische status van uw Azure-resources. Met Azure Resource Health-waarschuwingen ontvangt u vrijwel in realtime een melding als de status van deze resources wordt gewijzigd. Door Resource Health waarschuwingen via een programma te maken, kunnen gebruikers waarschuwingen bulksgewijs maken en aanpassen.

Resource Health-meldingen worden opgeslagen in het [Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md) , gezien het mogelijk grote hoeveelheid gegevens die in het activiteiten logboek is opgeslagen, is er een afzonderlijke gebruikers interface waarmee u waarschuwingen voor resource status meldingen gemakkelijker kunt weer geven en instellen.
U kunt een waarschuwing ontvangen wanneer Azure resource status meldingen verzendt naar uw Azure-abonnement. U kunt de waarschuwing configureren op basis van:

* Het betrokken abonnement.
* De betrokken bron/typen (en).
* De betrokken resource groep (en).
* De betrokken resource (s).
* De gebeurtenis status (n) van de betrokken resource (s).
* De resource (s) die de status hebben beïnvloed.
* De reden/typen (n) van de betrokken resources.

U kunt ook configureren naar wie de waarschuwing moet worden verzonden:

* Selecteer een bestaande actiegroep.
* Maak een nieuwe actiegroep maken (die kan worden gebruikt voor toekomstige waarschuwingen).

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

Zie [Resource Manager-sjablonen](./resource-health-alert-arm-template-guide.md)voor meer informatie over het configureren van waarschuwingen voor resource status meldingen met behulp van Azure Resource Manager sjablonen.
Resource Health waarschuwing met behulp van Azure Portal

## <a name="resource-health-alert-using-azure-portal"></a>Resource Health waarschuwing met behulp van Azure Portal

1. Selecteer **service Health** in azure [Portal](https://portal.azure.com/).

    ![Service Health selectie](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. Selecteer **service Health** in het gedeelte **resource Health** .
3. Selecteer **resource Health alert toevoegen** en vul de velden in.
4. Onder waarschuwings doel selecteert u het **abonnement**, de **resource typen**, **resource groepen** en de **resource** waarvoor u een waarschuwing wilt ontvangen.

    ![Doel selectie selecteren](./media/resource-health-alert-monitor-guide/alert-target.png)

5. Selecteer onder waarschuwings voorwaarde:
    1. De **gebeurtenis status** waarvoor u een waarschuwing wilt ontvangen. Het Ernst niveau van de gebeurtenis: actief, opgelost, in uitvoering, bijgewerkt
    2. De **resource status** waarvoor u een waarschuwing wilt ontvangen. De resource status van de gebeurtenis: beschikbaar, niet beschikbaar, onbekend, gedegradeerd
    3. Het **type reden** waarvoor u een waarschuwing wilt ontvangen. De oorzaak van de gebeurtenis: platform gestart, door de gebruiker geïnitieerde ![ waarschuwings voorwaarde selectie status selectie](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. Geef onder waarschuwings Details definiëren de volgende details op:
    1. **Naam van waarschuwings regel**: de naam voor de nieuwe waarschuwings regel.
    2. **Beschrijving**: de beschrijving van de nieuwe waarschuwings regel.
    3. **Waarschuwing opslaan in resource groep**: Selecteer de resource groep waar u deze nieuwe regel wilt opslaan.
7. Geef onder **actie groep** in de vervolg keuzelijst de actie groep op die u wilt toewijzen aan deze nieuwe waarschuwings regel. Of [Maak een nieuwe actie groep](../azure-monitor/alerts/action-groups.md) en wijs deze toe aan de nieuwe regel. Als u een nieuwe groep wilt maken, selecteert u + **nieuwe groep**.
8. Als u de regels wilt inschakelen nadat u ze hebt gemaakt, selecteert u **Ja** voor de optie **regel inschakelen bij het maken** .
9. Selecteer **Waarschuwingsregel maken**.

De nieuwe waarschuwings regel voor het activiteiten logboek wordt gemaakt en er wordt een bevestigings bericht weer gegeven in de rechter bovenhoek van het venster.
U kunt een regel inschakelen, uitschakelen, bewerken of verwijderen. Meer informatie over [het beheren van regels voor het activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Resource Health:

* [Overzicht van Azure Resource Health](Resource-health-overview.md)
* [Resourcetypen en statuscontroles die beschikbaar zijn via Azure Resource Health](resource-health-checks-resource-types.md)

Service Health-waarschuwingen maken:

* [Waarschuwingen voor Service Health configureren](./alerts-activity-log-service-notifications-portal.md) 
* [Azure-gebeurtenisschema in het activiteitenlogboek](../azure-monitor/essentials/activity-log-schema.md)
* [Resourcestatuswaarschuwingen configureren met Resource Manager-sjablonen](./resource-health-alert-arm-template-guide.md)
