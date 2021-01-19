---
title: 'Zelfstudie: regels maken en beheren in uw Azure IoT Central-toepassing'
description: Deze zelfstudie laat zien hoe u met Azure IoT Central-regels uw apparaten bijna in realtime kunt bewaken en automatisch acties kunt aanroepen, zoals het verzenden van een e-mailbericht wanneer de regel wordt geactiveerd.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6be49ec3777b4bcaa033a60546e95711090662a4
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065283"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Zelfstudie: Een regel maken en meldingen instellen in uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

U kunt Azure IoT Central gebruiken om uw verbonden apparaten op afstand te bewaken. Met Azure IoT Central-regels kunt u uw apparaten bijna in realtime bewaken en automatisch acties aanroepen, zoals het verzenden van een e-mailbericht. In dit artikel wordt uitgelegd hoe u regels kunt maken voor het bewaken van de telemetrie die uw apparaten verzenden.

Apparaten gebruiken telemetrie om numerieke gegevens van het apparaat te verzenden. Een regel wordt geactiveerd wanneer de geselecteerde telemetrie een opgegeven drempel overschrijdt.

In deze zelfstudie maakt u een regel voor het verzenden van een e-mailbericht wanneer de temperatuur in een gesimuleerd sensorapparaat hoger is dan 70 &deg;C.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een regel maken
> * Een e-mailactie maken

## <a name="prerequisites"></a>Vereisten

Voordat u begint, voltooi de quickstarts [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) en [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md) om de apparaatsjabloon **Sensor Controller** te maken waarmee u kunt werken.

## <a name="create-a-rule"></a>Een regel maken

Voor het maken van een telemetrieregel moet de apparaatsjabloon ten minste één telemetriewaarde bevatten. Deze zelfstudie maakt gebruik van een gesimuleerd **Sensor Controller**-apparaat dat de telemetrie van de temperatuur en de vochtigheid verzendt. U hebt deze apparaatsjabloon toegevoegd en een gesimuleerd apparaat gemaakt in de quickstart [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md). De regel controleert de door het apparaat gemelde temperatuur en verzendt een e-mailbericht wanneer deze hoger wordt dan 70 graden.

> [!NOTE]
> Er geldt een limiet van 50 regels per toepassing.

1. Selecteer in het linkerdeelvenster de optie **Regels**.

1. Als u nog geen regels hebt gemaakt, ziet u het volgende scherm:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Schermopname waarin de lege lijst met regels wordt weergegeven":::

1. Selecteer **+ Nieuw** om een nieuwe regel toe te voegen.

1. Voer _Temperatuurmonitor_ in als naam voor de regel en druk op Enter.

1. Selecteer het apparaatsjabloon **Sensor Controller**. De regel wordt standaard automatisch toegepast op alle apparaten die aan de apparaatsjabloon zijn gekoppeld. Als u wilt filteren op een subset van apparaten, selecteert u **+ Filter** en gebruikt u apparaateigenschappen om de apparaten te identificeren. U kunt de regel uitschakelen met de knop **In-/uitschakelen**:

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Schermopname van de selectie van het apparaatsjabloon in de regeldefinitie":::

### <a name="configure-the-rule-conditions"></a>De regelvoorwaarden configureren

Voorwaarden vormen de criteria die met de regel worden gecontroleerd. In deze zelfstudie configureert u de regel die moet worden geactiveerd wanneer de temperatuur hoger is dan 70 &deg;C.

1. Selecteer **Temperatuur** in de vervolgkeuzelijst **Telemetrie**.

1. Kies vervolgens **Is groter dan** als **operator** en voer _70_ in als **waarde**.

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Schermopname van de temperatuurvoorwaarde voor de regel":::

1. U kunt desgewenst een **tijdaggregatie** instellen. Wanneer u een tijdaggregatie selecteert, moet u ook een aggregatietype, zoals gemiddelde of som, selecteren in de vervolgkeuzelijst Aggregatie.

    * Zonder aggregatie wordt de regel geactiveerd voor elk telemetriegegevenspunt dat voldoet aan de voorwaarde. Als u bijvoorbeeld de regel zodanig configureert dat deze wordt geactiveerd wanneer de temperatuur hoger is dan 70 graden, wordt de regel bijna onmiddellijk geactiveerd wanneer de temperatuur van het apparaat deze waarde overschrijdt.
    * Met aggregatie wordt de regel geactiveerd als de aggregatiewaarde van de telemetriegegevenspunten in het tijdvenster voldoet aan de voorwaarde. Als u bijvoorbeeld de regel zodanig configureert dat deze wordt geactiveerd wanneer de temperatuur hoger is dan 70 graden met een gemiddelde tijdaggregatie van 10 minuten, wordt de regel geactiveerd wanneer het apparaat een gemiddelde temperatuur van meer dan 70 graden rapporteert, berekend over een interval van 10 minuten.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Schermopname van de ingevulde aggregatievoorwaarde":::

U kunt meerdere voorwaarden aan een regel toevoegen door **+ Voorwaarde** te selecteren. Wanneer er meerdere voorwaarden worden opgegeven, moet aan alle voorwaarden worden voldaan om de regel te activeren. Alle voorwaarden worden samengevoegd met een impliciete `AND`-component. Als u tijdaggregatie met meerdere voorwaarden gebruikt, moeten alle telemetriewaarden worden geaggregeerd.

### <a name="configure-actions"></a>Acties configureren

Nadat u de voorwaarde hebt gedefinieerd, stelt u de acties in die moeten worden uitgevoerd wanneer de regel wordt geactiveerd. Acties worden aangeroepen wanneer de voorwaarden die in de regel zijn opgegeven, worden geëvalueerd als waar.

1. Selecteer **+ E-mail** in de sectie **Acties**.

1. Voer _Temperatuurwaarschuwing_ in als weergavenaam voor de actie, uw e-mailadres in het veld **Naar** en _Controleer het apparaat!_ als opmerking die moet worden weergegeven in de hoofdtekst van het e-mailbericht.

    > [!NOTE]
    > E-mailberichten worden alleen verzonden naar gebruikers die zijn toegevoegd aan de toepassing en zich ten minste één keer hebben aangemeld. Meer informatie over [gebruikersbeheer](howto-administer.md) in Azure IoT Central.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Schermopname van de e-mailactie voor de regel":::

1. Als u de actie wilt opslaan, kiest u **Gereed**. U kunt meerdere acties toevoegen aan een regel.

1. Als u de regel wilt opslaan, kiest u **Opslaan**. De regel wordt binnen enkele minuten van kracht en begint met de bewaking van de telemetrische gegevens die naar uw toepassing worden verzonden. Wanneer aan de voorwaarde die in de regel is opgegeven, wordt voldaan, wordt de geconfigureerde e-mailactie door de regel geactiveerd.

Enige tijd nadat de regel is geactiveerd, ontvangt u een e-mailbericht:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Schermopname van e-mailmelding":::

## <a name="delete-a-rule"></a>Een regel verwijderen

Als u een regel niet meer nodig hebt, kunt u deze verwijderen door de regel te openen en **Verwijderen** te kiezen.

## <a name="enable-or-disable-a-rule"></a>Een regel in- of uitschakelen

Kies de regel die u wilt in- of uitschakelen. Gebruik de knop **In- of uitschakelen** om de regel in of uit te schakelen voor alle apparaten waarop de regel van toepassing is.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Een regel voor specifieke apparaten in- of uitschakelen

Kies de regel die u wilt aanpassen. Gebruik een of meer filters in de sectie **Doelapparaten** om het bereik van de regel te beperken tot de apparaten die u wilt bewaken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een regel op basis van telemetrie maken
* Een actie toevoegen

Nu u een regel op basis van drempelwaarden hebt gedefinieerd, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Webhooks voor regels maken](./howto-create-webhooks.md).
