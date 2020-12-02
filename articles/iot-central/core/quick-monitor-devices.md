---
title: 'Quickstart: Uw apparaten bewaken in Azure IoT Central'
description: 'Quickstart: Leer hoe u als operator uw Azure IoT Central-toepassing kunt gebruiken om uw apparaten te bewaken.'
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e4485e4f8e873b1b49dc3d6df72eb04a1cbe8c17
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992751"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Quickstart: Azure IoT Central gebruiken om uw apparaten te bewaken

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

In deze quickstart leert u hoe u als operator uw Microsoft Azure IoT Central toepassing kunt gebruiken om uw apparaten te bewaken en instellingen te wijzigen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, voltooit u de drie voorgaande quickstarts [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) en [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md) en [Regels en acties voor uw apparaat configureren](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Een melding ontvangen

Azure IoT Central verzendt meldingen over apparaten als e-mailberichten. Als bouwer heeft u een regel toegevoegd om een ​​melding te verzenden naar een operator als de vochtigheid in een aangesloten apparaatsensor een drempel overschrijdt. Als operator kunt u uw e-mailberichten controleren op meldingen.

Open het e-mailbericht dat u aan het einde van de quickstart [Regels en acties voor uw apparaat configureren](quick-configure-rules.md) hebt ontvangen. Selecteer de koppeling naar het apparaat in het e-mailbericht:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Schermopname van e-mailmelding":::

De weergave **Overzicht** voor het gesimuleerde apparaat dat u in de vorige quickstarts hebt gemaakt, wordt geopend in uw browser:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Schermopname van een overzicht van het apparaat dat de melding heeft geactiveerd":::

## <a name="investigate-an-issue"></a>Een probleem onderzoeken

Als operator kunt u informatie weergeven over het apparaat in de weergaven **Overzicht**, **Over** en **Opdrachten**. De bouwer heeft een weergave met de naam **Apparaat beheren** gemaakt waarin u apparaatgegevens kunt bewerken en apparaateigenschappen kunt instellen.

De grafiek op het dashboard toont een grafische voorstelling van de vochtigheid van het apparaat. U besluit dat de vochtigheid van het apparaat te hoog is.

## <a name="remediate-an-issue"></a>Een probleem oplossen

Als u een wijziging wilt aanbrengen op het apparaat, gebruikt u de pagina **Apparaat beheren**.

Wijzig **Doeltemperatuur** naar 80 om het apparaat op te warmen en de vochtigheid te verminderen. Kies **Opslaan** om het apparaat bij te werken. Wanneer het apparaat de instellingswijziging bevestigt, verandert de status van de eigenschap in **Gesynchroniseerd**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Schermopname van de bijgewerkte doeltemperatuur voor het apparaat":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

* Een melding ontvangen
* Een probleem onderzoeken
* Een probleem oplossen

Nu u weet hoe u uw apparaat controleert, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Een apparaatsjabloon bouwen en beheren](howto-set-up-template.md).
