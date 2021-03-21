---
title: 'Zelfstudie: Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing | Microsoft Docs'
description: 'Zelfstudie: Als operator leert u hoe u apparaatgroepen kunt gebruiken om telemetrie te analyseren vanaf apparaten in uw Azure IoT Central-toepassing.'
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a7d26eebb24662a448d8ccb44d037e7706fe776b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832841"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Zelfstudie: Apparaatgroepen gebruiken om apparaattelemetrie te analyseren

In dit artikel wordt beschreven hoe u, als operator, apparaatgroepen kunt gebruiken voor het analyseren van apparaattelemetrie in uw Azure IoT Central-toepassing.

Een apparaatgroep is een lijst met apparaten die zijn gegroepeerd omdat ze voldoen aan bepaalde criteria. Met apparaatgroepen kunt u apparaten op schaal beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U kunt bijvoorbeeld een apparaatgroep maken om alle airco's in Seattle weer te geven, zodat een technicus kan zoeken naar de apparaten waarvoor hij verantwoordelijk is.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatgroep maken
> * Een apparaatgroep gebruiken om apparaattelemetrie te analyseren

## <a name="prerequisites"></a>Vereisten

Voordat u begint, voltooit u de quickstarts [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) en [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md) om de apparaatsjabloon **Sensor Controller** te maken waarmee u kunt werken.

## <a name="create-simulated-devices"></a>Gesimuleerde apparaten maken

Voordat u een apparaatgroep kunt maken, voegt u ten minste vijf gesimuleerde apparaten toe op basis van de apparaatsjabloon **Sensor Controller** om in deze zelfstudie te gebruiken:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Schermopname met vijf gesimuleerde sensorcontrollerapparaten":::

Voor vier van de gesimuleerde sensorapparaten gebruikt u de weergave **Apparaat beheren** om de naam van de klant in te stellen op *Contoso*:

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Schermafbeelding die laat zien hoe u de cloudeigenschap Klantnaam instelt":::

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Een apparaatgroep maken:

1. Kies **Apparaatgroepen** in het linkerdeelvenster.

1. Selecteer **+ Nieuw**.

1. Noem uw apparaatgroep *Contoso-apparaten*. U kunt ook een beschrijving toevoegen. Een apparaatgroep kan alleen apparaten uit één apparaatsjabloon bevatten. Kies de apparaatsjabloon **Sensor Controller** om te gebruiken voor deze groep.

1. Als u de apparaatgroep wilt aanpassen zodat deze alleen de apparaten bevat die horen bij **Contoso**, selecteert u **+ Filter**. Selecteer de eigenschap **Klantnaam**, de vergelijkingsoperator **Is gelijk aan** en **Contoso** als waarde. U kunt meerdere filters en apparaten toevoegen die voldoen aan **alle** filtercriteria die in de apparaatgroep zijn geplaatst. De apparaatgroep die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatgroep kan bekijken, wijzigen of verwijderen.

    > [!TIP]
    > De apparaatgroep is een dynamische query. Telkens wanneer u de lijst met apparaten bekijkt, kunnen er andere apparaten in de lijst staan. De lijst is afhankelijk van de apparaten die op dat moment voldoen aan de criteria van de query.

1. Kies **Opslaan**.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Schermopname die de queryconfiguratie van de apparaatgroep weergeeft":::

> [!NOTE]
> Selecteer Azure IoT Edge-sjablonen voor Azure IoT Edge-apparaten om een apparaatgroep te maken.

## <a name="analytics"></a>Analyse

U kunt **Analytische gegevens** gebruiken met een apparaatgroep om de telemetrie van de apparaten in de groep te analyseren. U kunt bijvoorbeeld de gemiddelde temperatuur uitzetten die door alle Contoso-omgevingssensoren wordt gerapporteerd.

De telemetrie voor een apparaatgroep analyseren:

1. Kies **Analyse** in het linkerdeelvenster.

1. Selecteer de apparaatgroep **Contoso-apparaten** die u hebt gemaakt. Voeg vervolgens de telemetrietypen **Temperatuur** en **Vochtigheid** toe:

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Schermopname die de telemetrietypen weergeeft die zijn geselecteerd voor analyse":::

    Gebruik de tandwielpictogrammen naast de telemetrietypen om een aggregatietype te selecteren. De standaardwaarde is **Gemiddeld**. Gebruik **Groeperen op** om te wijzigen hoe de statistische gegevens worden weergegeven. Als u bijvoorbeeld hebt gesplitst op apparaat-id, ziet u een plot voor elk apparaat wanneer u **Analyse** selecteert.

1. Selecteer **Analyseren** om de gemiddelde telemetriegegevens weer te geven:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Schermopname waarin de gemiddelde waarden voor alle Contoso-apparaten worden weergegeven":::

    U kunt de weergave aanpassen, de weergegeven tijdsperiode wijzigen en de gegevens exporteren.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatgroepen gebruikt in uw Azure IoT Central-toepassing, volgt hier de aanbevolen volgende stap:

> [!div class="nextstepaction"]
> [Telemetrieregels maken](tutorial-create-telemetry-rules.md)
