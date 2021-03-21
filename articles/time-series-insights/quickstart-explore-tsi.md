---
title: 'Quickstart: De Gen2 demo-omgeving van Azure Time Series Insights Gen2 verkennen | Microsoft Docs'
description: Verken de belangrijkste functies van de Gen2 demo-omgeving van Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 03/01/2021
ms.openlocfilehash: 25340af243217313ce6d3f7205c102d4bcd350b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698107"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Quickstart: De demo-omgeving van Azure Time Series Insights Gen2 verkennen

Met deze quickstart kunt u aan de slag met een Azure Time Series Insights Gen2-omgeving. In de gratis demo bekijkt u de belangrijkste functies die zijn toegevoegd aan Azure Time Series Insights Gen2.

De Azure Time Series Insights Gen2-demo omgeving bevat een fictief bedrijf, Contoso, dat twee windturbineparken exploiteert. Elk park bevat 10 turbines. Elke turbine beschikt over 20 sensoren die elke minuut gegevens rapporteren aan Azure IoT Hub. De sensoren verzamelen informatie over de weersomstandigheden, de bladhellingshoek en de gierpositie. Informatie over de prestaties van de generator, het gedrag van de versnellingsbak en veiligheidsmonitors wordt ook vastgelegd.

In deze quickstart leert u hoe u Azure Time Series Insights Gen2 kunt gebruiken om inzicht te krijgen in de gegevens van Contoso. U voert ook een korte hoofdoorzakanalyse van kritieke fouten uit om ze beter te voorspellen en onderhoud beter uit te voeren.

> [!IMPORTANT]
> Maak een  [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  als u er nog geen hebt.

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>De Explorer van Azure Time Series Insights Gen2 verkennen in een demo-omgeving

De Explorer van Azure Time Series Insights Gen2 geeft historische gegevens en hoofdoorzakanalyses weer. Aan de slag:

1. Ga in uw browser naar de omgeving  [Contoso Wind Farm demo](https://insights.timeseries.azure.com/preview/samples).  

1. Meld u indien nodig aan bij Azure Time Series Insights Gen2 Explorer. Gebruik hiervoor de referenties van uw Azure-account.

## <a name="work-with-historical-data"></a>Werken met historische gegevens

1. Selecteer **Contoso WindFarm-hiërarchie**.

   [![Contoso WindFarm-hiërarchie](media/quickstart-explore/quick-start-contoso-1.png)](media/quickstart-explore/quick-start-contoso-1.png#lightbox)

1. Bekijk in **Contoso Plant 1** windturbine **W7**.

   [![W7 in Contoso Plant 1](media/quickstart-explore/quick-start-contoso-2.png)](media/quickstart-explore/quick-start-contoso-2.png#lightbox)

   1. Wijzig het weergavebereik naar **1-1-2017 20:00:00.00 tot 10-3-2017 20:00:00.00 (UTC)** .

      [![Bereikweergave](media/quickstart-explore/range-setting-1.png)](media/quickstart-explore/range-setting-1.png#lightbox)

      [![Instelling bereikweergave](media/quickstart-explore/range-setting-2.png)](media/quickstart-explore/range-setting-2.png#lightbox)

   1. U kunt de bereikweergave aanpassen met behulp van de inzoomfunctie **(+)** en de uitzoomfunctie **(-)** en door de schuifregelaar te bewegen.

      [![Bereikweergave aanpassen](media/quickstart-explore/view-range-setting.png)](media/quickstart-explore/view-range-setting.png#lightbox)

   1. Als u een sensor wilt selecteren, selecteert u **Contoso Plant 1** > **W7** > **GeneratorSystem** > **GeneratorSpeed**. Controleer vervolgens de waarden die worden weergegeven.

      [![Snelheid van generator](media/quickstart-explore/quick-start-generator-speed-1.png)](media/quickstart-explore/quick-start-generator-speed-1.png#lightbox)

1. Onlangs heeft Contoso brand geconstateerd in windturbine **W7**. Men is verdeeld over de oorzaak van de brand. In Azure Time Series Insights Gen2 wordt weergegeven welke brandmeldingssensor tijdens de brand is geactiveerd.

   1. Wijzig het weergavebereik naar **9-3-2017 20:00:00.00 tot 10-3-2017 20:00:00.00 (UTC)** .
   1. Selecteer **Safety System** > **FireAlert**.

      [![Contoso heeft een brand geconstateerd in windturbine W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Bekijk andere gebeurtenissen rond het tijdstip van de brand om te begrijpen wat er is gebeurd. De oliedruk en het aantal actieve waarschuwingen waren net voor de brand erg hoog.

   1. Selecteer **Pitch System** > **HydraulicOilPressure**.
   1. Selecteer **Pitch System** > **ActiveWarning**.

      [![Controleer andere gebeurtenissen rond hetzelfde tijdstip](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. De sensoren voor de oliedruk en actieve waarschuwingen vertonen net voor de brand een piek. Vouw de weergegeven tijdreeks uit om andere duidelijke tekens te bekijken die voor de brand zichtbaar waren. Beide sensoren schommelen continu in de loop van de tijd. De schommelingen geven een persistent en onrustbarend patroon aan.

    * Wijzig het weergavebereik naar **24-2-2017 20:00:00.00 tot 10-3-2017 20:00:00.00 (UTC)** .

      [![De sensoren voor de oliedruk en actieve waarschuwingen vertoonden ook een piek](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Wanneer er twee jaar aan historische gegevens worden onderzocht, blijkt dat dezelfde sensorschommelingen optraden bij een andere brand.

    * Wijzig het weergavebereik naar **1-1-2016 tot 31-12-2017** (alle gegevens).

      [![Zoek naar historische patronen](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Door Azure Time Series Insights Gen2 en sensortelemetrie te gebruiken, hebben we een langetermijntrend in de historische gegevens gevonden. Met deze nieuwe inzichten kunnen we het volgende doen:

* Uitleggen wat er daadwerkelijk is gebeurd.
* Het probleem oplossen.
* Een beter waarschuwingssysteem installeren.

## <a name="root-cause-analysis"></a>Hoofdoorzaakanalyse

1. In sommige scenario's is geavanceerde analyse vereist om aanwijzingen in gegevens te ontdekken. Selecteer turbine **W6** op datum **25-6**.

    1. Wijzig het weergavebereik naar **1-6-2017 20:00:00.00 tot 1-7-2017 20:00:00.00 (UTC)** .
    1. Selecteer **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

       [![Wijzig het weergavebereik en selecteer W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. De waarschuwing geeft aan dat er een probleem is met de spanning van de generator. De algemene energie-uitvoer van de generator bevindt zich binnen de normale parameters in het huidige interval. Als we het interval vergroten, komt een ander patroon aan het licht. Er is duidelijk sprake van een vermindering.

    1. Verwijder de sensor **VoltageActuatorSwitchWarning**.
    1. Selecteer **Generator System** > **ActivePower**.
    1. Wijzig het interval naar **3d**.

       [![Wijzig het interval naar 3d](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Door het tijdsbereik uit te breiden, kunnen we bepalen of het probleem is gestopt of dat het wordt voortgezet.

    * Breid de tijdsduur uit naar 60 dagen.

      [![Breid de tijdsduur uit naar 60 dagen](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. U kunt andere gegevenspunten van sensoren toevoegen voor meer context. Hoe meer sensoren er worden weergegeven, hoe beter we het probleem begrijpen. We verwijderen een markering om de werkelijke waarden weer te geven.

    1. Selecteer **Generator Systeem** en selecteer vervolgens drie sensoren: **GridVoltagePhase1**, **GridVoltagePhase2** en **GridVoltagePhase3** toe.
    1. Verwijder een markering van het laatste gegevenspunt in het zichtbare gedeelte.

       [![Plaats een markering](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Twee van de spanningssensoren werken op een vergelijkbare manier en binnen de normale parameters. Het lijkt erop dat de sensor **GridVoltagePhase3** van de dader is.

1. Wanneer zeer contextuele gegevens worden toegevoegd, lijkt het nog duidelijker dat de vermindering in fase 3 het probleem is. Nu hebben we een goed idee van de oorzaak van de waarschuwing. We zijn klaar om het probleem door te spelen aan ons onderhoudsteam.  

    * Wijzig de weergave bij zodat alle sensoren in het **Generator System** als overlay op dezelfde grafiekschaal worden weergegeven.

      [![Wijzig de weergave zodat deze alle gegevens bevat](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

U kunt nu uw eigen Azure Time Series Insights Gen2-omgeving gaan maken. Aan de slag:

> [!div class="nextstepaction"]
> [Uw Azure Time Series Insights Gen2-omgeving plannen](./how-to-plan-your-environment.md)

Meer informatie over het gebruik van de demo en de bijbehorende functies:

> [!div class="nextstepaction"]
> [De Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)
