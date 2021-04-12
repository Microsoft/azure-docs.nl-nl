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
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491071"
---
<!-- All needs updating -->
Als operator in uw Azure IoT Central-toepassing kunt u:

* Bekijk de telemetrie die wordt verzonden door de twee Thermo staat-onderdelen op de pagina **overzicht** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Telemetrie van apparaten weergeven":::

* Bekijk de apparaateigenschappen op de pagina **over** . Op deze pagina ziet u de eigenschappen van het onderdeel apparaatgegevens en de twee Thermo staat-onderdelen:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Apparaateigenschappen weergeven":::

## <a name="customize-the-device-template"></a>Het apparaatsjabloon aanpassen

Als oplossings ontwikkelaar kunt u de sjabloon voor het apparaat aanpassen dat automatisch IoT Central gemaakt wanneer het apparaat met de temperatuur controller is verbonden.

Als u een cloudeigenschap wilt toevoegen om de naam van de klant op te slaan die aan het apparaat is gekoppeld:

1. Navigeer in uw IoT Central-toepassing naar de sjabloon **temperatuur controller** apparaat op de pagina met **Apparaatinstellingen** .

1. Selecteer in de sjabloon **temperatuur controller** -apparaat de optie **Cloud eigenschappen**.

1. Selecteer **Een cloudeigenschap toevoegen**. Voer *Klantnaam* in als de **weergavenaam** en kies **Tekenreeks** als het **schema**. Selecteer vervolgens **Opslaan**.

U kunt als volgt aanpassen hoe de **Get Max-Min-rapport** opdrachten worden weer gegeven in uw IOT Central-toepassing:

1. Selecteer **aanpassen** in de sjabloon voor het apparaat.

1. Voor **getMaxMinReport (thermostat1)**, vervangt u *Max-Min rapport ophalen.* met het *thermostat1-status rapport ophalen*.

1. Voor **getMaxMinReport (thermostat2)**, vervangt u *Max-Min rapport ophalen.* met het *thermostat2-status rapport ophalen*.

1. Selecteer **Opslaan**.

Aanpassen hoe de Beschrijf bare eigenschappen van de **doel temperatuur** worden weer gegeven in uw IOT Central-toepassing:

1. Selecteer **aanpassen** in de sjabloon voor het apparaat.

1. Vervang voor **targetTemperature (thermostat1)** de *doel temperatuur* door de *doel temperatuur (1)*.

1. Vervang voor **targetTemperature (thermostat2)** de *doel temperatuur* door de *doel temperatuur (2)*.

1. Selecteer **Opslaan**.

De Thermo staat-onderdelen in het **temperatuur controller** model bevatten de eigenschap Beschrijf bare **doel temperatuur** . de sjabloon voor het apparaat bevat de eigenschap **klant naam** Cloud. Een weergave maken die een operator kan gebruiken om deze eigenschappen te bewerken:

1. Selecteer **Weergaven** en vervolgens de tegel **Apparaat- en cloudgegevens bewerken**.

1. Voer _Eigenschappen_ in als de formuliernaam.

1. Selecteer de **doel temperatuur (1)**, de  **doel temperatuur (2)** en de naam van de **klant** . Selecteer vervolgens **Sectie toevoegen**.

1. Sla uw wijzigingen op.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Weergave voor het bijwerken van eigenschapswaarden":::

## <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat een operator de aanpassingen kan zien en gebruiken die u hebt gemaakt, moet u de apparaatsjabloon publiceren.

Selecteer In de apparaatsjabloon **Thermostaat** de optie **Publiceren**. Selecteer **Publiceren** in het deelvenster **Dit apparaatsjabloon op de toepassing publiceren**.

Een operator kan nu de weer gave **Eigenschappen** gebruiken om de eigenschaps waarden bij te werken en opdrachten met de naam **Thermostat1 status rapport ophalen** en **thermostat2 status rapport ophalen** op de pagina met Apparaatinstellingen:

* Schrijfbare eigenschapswaarden bijwerken op de pagina **Eigenschappen**:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="De apparaateigenschappen bijwerken":::

* Opdrachten aanroepen via de pagina **Opdrachten**:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="De opdracht aanroepen":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="De reactie op de opdracht weergeven":::
