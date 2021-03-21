---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017475"
---
Als operator in uw Azure IoT Central-toepassing kunt u:

* De telemetriegegevens die door het apparaat zijn verzonden, bekijken op de pagina **Overzicht**:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Telemetrie van apparaten weergeven":::

* De eigenschappen van het apparaat bekijken op de pagina **Over**:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Apparaateigenschappen weergeven":::

## <a name="customize-the-device-template"></a>Het apparaatsjabloon aanpassen

Als oplossingsontwikkelaar kunt u de apparaatsjabloon aanpassen die automatisch door IoT Central is gemaakt toen het thermostaatapparaat werd verbonden.

Als u een cloudeigenschap wilt toevoegen om de naam van de klant op te slaan die aan het apparaat is gekoppeld:

1. Navigeer in de IoT Central-toepassing naar de apparaatsjabloon **Thermostaat** op de pagina **Apparaatsjablonen**.

1. Selecteer In de apparaatsjabloon **Thermostaat** de optie **Cloudeigenschappen**.

1. Selecteer **Een cloudeigenschap toevoegen**. Voer *Klantnaam* in als de **weergavenaam** en kies **Tekenreeks** als het **schema**. Selecteer vervolgens **Opslaan**.

Als u wilt aanpassen hoe de eigenschap **Max-min-rapport ophalen** wordt weergegeven in uw IoT Central-toepassing, selecteert u **Aanpassen** in de apparaatsjabloon. Vervang **Max-min-rapport ophalen** door *Statusrapport*. Selecteer vervolgens **Opslaan**.

Het model **Thermostaat** bevat de schrijfbare eigenschap **Doeltemperatuur**, de apparaatsjabloon bevat de cloudeigenschap **klantnaam**. Een weergave maken die een operator kan gebruiken om deze eigenschappen te bewerken:

1. Selecteer **Weergaven** en vervolgens de tegel **Apparaat- en cloudgegevens bewerken**.

1. Voer _Eigenschappen_ in als de formuliernaam.

1. Selecteer de eigenschappen **Doeltemperatuur** en **Klantnaam**. Selecteer vervolgens **Sectie toevoegen**.

1. Sla uw wijzigingen op.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Weergave voor het bijwerken van eigenschapswaarden":::

## <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat een operator de aanpassingen kan zien en gebruiken die u hebt gemaakt, moet u de apparaatsjabloon publiceren.

Selecteer In de apparaatsjabloon **Thermostaat** de optie **Publiceren**. Selecteer **Publiceren** in het deelvenster **Dit apparaatsjabloon op de toepassing publiceren**.

Een operator kan nu de weergave **Eigenschappen** gebruiken om de eigenschapswaarden bij te werken en een opdracht met de naam **Statusrapport ophalen** op te halen van de pagina met apparaatopdrachten:

* Schrijfbare eigenschapswaarden bijwerken op de pagina **Eigenschappen**:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="De apparaateigenschappen bijwerken":::

* Opdrachten aanroepen via de pagina **Opdrachten**:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="De opdracht aanroepen":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="De reactie op de opdracht weergeven":::
