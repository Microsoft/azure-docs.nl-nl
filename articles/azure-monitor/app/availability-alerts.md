---
title: Beschikbaarheids waarschuwingen instellen met Azure-toepassing Insights | Microsoft Docs
description: Meer informatie over het instellen van webtests in Application Insights. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: cbafa7997d203cf06a3e91965355258f0088d77e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589884"
---
# <a name="availability-alerts"></a>Beschikbaarheidswaarschuwingen

[Azure Application Insights](./app-insights-overview.md) verzendt regelmatig webaanvragen naar uw toepassing vanaf verschillende punten over de hele wereld. U kunt hiermee een waarschuwing ontvangen als uw toepassing niet reageert of als deze te langzaam reageert.

## <a name="enable-alerts"></a>Waarschuwingen inschakelen

Waarschuwingen worden nu standaard automatisch ingeschakeld, maar om de waarschuwing volledig te configureren, moet u eerst uw beschikbaarheids test maken.

![Ervaring maken](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Met de [nieuwe Unified Alerts](../alerts/alerts-overview.md)moeten de voor keuren voor waarschuwings regels en meldingen met [actie groepen](../alerts/action-groups.md) **worden** geconfigureerd in de-ervaring met waarschuwingen. Zonder de volgende stappen ontvangt u alleen meldingen in de portal.

1. Nadat de beschikbaarheids test is opgeslagen, klikt u op het tabblad Details op het weglatings teken van de test die u zojuist hebt gemaakt. Klik op waarschuwing bewerken.

   ![Scherm afbeelding toont Bewerk waarschuwing geselecteerd in het menu.](./media/availability-alerts/edit-alert.png)

2. Stel het gewenste Ernst niveau, de beschrijving van de regel en het belangrijkste in voor de actie groep met de meldings voorkeuren die u wilt gebruiken voor deze waarschuwings regel.

   ![Scherm afbeelding toont de beheer pagina voor regels waar u de regel kunt bewerken.](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Beschikbaarheids waarschuwingen die zijn gemaakt via deze ervaring zijn op basis van status. Dit betekent dat wanneer aan de waarschuwings criteria wordt voldaan, een enkele waarschuwing wordt gegenereerd wanneer de site als niet-beschikbaar wordt gedetecteerd. Als de site nog steeds niet actief is, wordt de volgende keer dat de waarschuwings criteria worden geëvalueerd, geen nieuwe waarschuwing gegenereerd. Als uw site gedurende een uur niet actief was en u een e-mail waarschuwing hebt ingesteld, zou u alleen een e-mail ontvangen wanneer de site is afgesloten en een daaropvolgende e-mail wanneer er een back-up van de site is gemaakt. U ontvangt geen doorlopende waarschuwingen die u eraan herinneren dat de site nog steeds niet beschikbaar is.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Melding over problemen met de X-en Y-locaties

De waarschuwing regel voor de X-locaties van de Y-locatie is standaard ingeschakeld in de [nieuwe Unified Alerts-ervaring](../alerts/alerts-overview.md)wanneer u een nieuwe beschikbaarheids test maakt. U kunt zich afmelden door de optie ' klassiek ' te selecteren of de waarschuwings regel uit te scha kelen.

> [!NOTE]
> Configureer de actie groepen om meldingen te ontvangen wanneer de waarschuwing wordt geactiveerd door de bovenstaande stappen te volgen. Zonder deze stap ontvangt u alleen meldingen over de Portal wanneer de regel wordt geactiveerd.
>

### <a name="alert-on-availability-metrics"></a>Waarschuwing voor metrische gegevens over beschik baarheid

Met de [nieuwe Unified Alerts](../alerts/alerts-overview.md)kunt u een waarschuwing ontvangen voor metrische gegevens over een gesegmenteerde geaggregeerde Beschik baarheid en voor het testen van de duur.

1. Selecteer een Application Insights bron in de werk ervaring metrische gegevens en selecteer een beschikbaarheids metriek:

    ![Selectie van metrische beschikbaarheids gegevens](./media/availability-alerts/select-metric.png)

2. Met de optie waarschuwingen configureren in het menu gaat u naar de nieuwe ervaring waarin u specifieke tests of locaties kunt selecteren voor het instellen van een waarschuwings regel voor. U kunt ook de actie groepen voor deze waarschuwings regel configureren.

### <a name="alert-on-custom-analytics-queries"></a>Waarschuwing voor aangepaste analyse query's

Met de [nieuwe Unified Alerts](../alerts/alerts-overview.md)kunt u waarschuwingen op [aangepaste logboek query's](../alerts/alerts-unified-log.md)uitvoeren. Met aangepaste query's kunt u een waarschuwing sturen op elke wille keurige voor waarde die u helpt om het meest betrouw bare signaal van beschikbaarheids problemen op te lossen. Dit is ook van toepassing als u aangepaste beschikbaarheids resultaten verzendt met behulp van de TrackAvailability-SDK.

> [!Tip]
> De metrische gegevens over de beschik baarheid bevatten alle aangepaste beschikbaarheids resultaten die u kunt indienen door onze TrackAvailability SDK aan te roepen. U kunt de waarschuwing voor metrische gegevens over metrieken gebruiken om te waarschuwen voor aangepaste beschikbaarheids resultaten.
>

## <a name="automate-alerts"></a>Waarschuwingen automatiseren

Raadpleeg de documentatie [Create a metric Alert with Resource Manager-sjabloon](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) voor het automatiseren van dit proces met Azure Resource Manager sjablonen.

## <a name="troubleshooting"></a>Problemen oplossen

Speciaal [artikel voor probleem oplossing](troubleshoot-availability.md).

## <a name="next-steps"></a>Volgende stappen

* [Webtests met meerdere stappen](availability-multistep.md)
* [Webtests voor URL-ping](monitor-web-app-availability.md)

