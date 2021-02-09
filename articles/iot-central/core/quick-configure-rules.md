---
title: 'Quickstart: regels en acties configureren in Azure IoT Central'
description: Deze quickstart laat u zien hoe u als bouwer op telemetrie gebaseerde regels en acties in uw Azure IoT Central-toepassing kunt configureren.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 90fc1385afb2ef921828465ba030674281e96ebf
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833844"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Snelstart: Regels en acties voor uw apparaat configureren in Azure IoT Central

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

In deze quickstart maakt u een regel waarmee een e-mailbericht wordt verzonden wanneer de vochtigheidsgraad die wordt gerapporteerd door een apparaatsensor hoger is dan 55%.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, voltooit u de vorige twee quickstarts [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) en [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md) om de **Sensor Controller**-apparaatsjabloon te maken waarmee u kunt werken.

## <a name="create-a-telemetry-based-rule"></a>Een regel op basis van telemetrie maken

1. Als u een nieuwe op telemetrie gebaseerde regel wilt toevoegen aan uw toepassing, selecteert u in het linker deelvenster **Regels**.

1. Selecteer **+ Nieuw** om een nieuwe regel te maken.

1. Voer als regelnaam **Vochtigheidsgraad** in.

1. Selecteer in de sectie **Doelapparaten** **Sensor Controller** als sjabloon voor het apparaat. Deze optie filtert de apparaten waarop de regel van toepassing is op basis van het type apparaat. U kunt meer filtercriteria toevoegen door **+ Filter** te selecteren.

1. In de sectie **Voorwaarden** definieert u wat uw regel activeert. Gebruik de volgende informatie om een voorwaarde op te geven op basis van de temperatuurtelemetrie:

    | Veld        | Waarde            |
    | ------------ | ---------------- |
    | Meting  | SensorHumid      |
    | Operator     | is groter dan  |
    | Waarde        | 55               |

    Als u meer voorwaarden wilt toevoegen, selecteert u **+ Voorwaarde**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Schermopname van de regelvoorwaarde":::

1. Als u een e-mailactie wilt toevoegen die moet worden uitgevoerd wanneer de regel wordt geactiveerd, selecteert u **+ E-mail**.

1. Gebruik de informatie in de volgende tabel om uw actie te definiëren en selecteer vervolgens **Gereed**:

    | Instelling   | Waarde                                             |
    | --------- | ------------------------------------------------- |
    | Weergavenaam | E-mailactie voor operator                          |
    | Tot        | Uw e-mailadres                                |
    | Notities     | Omgevingsvochtigheid heeft de drempelwaarde overschreden. |

    > [!NOTE]
    > Om een e-mailmelding te ontvangen, moet het e-mailadres een [gebruikers-ID in de toepassing](howto-administer.md) zijn, en moet die gebruiker zich minimaal één keer hebben aangemeld bij de toepassing.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Schermopname met een e-mailactie toegevoegd aan de regel":::

1. Selecteer **Opslaan**. De regel wordt weergegeven op de pagina **Regels**.

## <a name="test-the-rule"></a>De regel testen

Kort nadat u de regel hebt opgeslagen, wordt deze actief. Wanneer aan de voorwaarden in de regel wordt voldaan, verzendt uw toepassing een e-mail naar het adres dat u in de actie hebt opgegeven.

> [!NOTE]
> Als u klaar bent met testen, schakelt u de regel uit om geen waarschuwingen meer te ontvangen in uw postvak.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

* Een regel op basis van telemetrie maken
* Een actie toevoegen

Ga voor meer informatie over het bewaken van met uw toepassing verbonden apparaten verder met de quickstart:

> [!div class="nextstepaction"]
> [Azure IoT Central gebruiken om uw apparaten te bewaken](quick-monitor-devices.md).
