---
title: 'Quickstart: een gesimuleerd apparaat toevoegen aan Azure IoT Central'
description: In deze quickstart ziet u hoe u een apparaatsjabloon maakt en een gesimuleerd apparaat toevoegt aan uw IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 142d78cf8b787e04704d26bca0c650944052fd7f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588969"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Quickstart: Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing

Een apparaatsjabloon definieert de mogelijkheden van een apparaat dat verbinding maakt met uw IoT Central-toepassing. Mogelijkheden zijn de telemetrie die het apparaat verzendt, eigenschappen van het apparaat en de opdrachten waarop het apparaat reageert. Met behulp van een apparaatsjabloon kunt u zowel echte als gesimuleerde apparaten toevoegen aan een toepassing. Gesimuleerde apparaten zijn nuttig om het gedrag van uw IoT Central-toepassing te testen voordat u echte apparaten aansluit.

In deze quickstart voegt u een apparaatsjabloon toe voor een ESP32 Azure IoT Kit-ontwikkelkaart en maakt u een gesimuleerd apparaat. U hebt geen echt apparaat nodig om deze quickstart te voltooien, u werkt met een simulatie van het apparaat. Een ESP32-apparaat:

* Verzendt telemetrie zoals temperatuur.
* Rapporteert apparaatspecifieke eigenschappen, zoals de maximumtemperatuur sinds het opnieuw opstarten van het apparaat.
* Reageert op opdrachten zoals opnieuw opstarten.
* Rapporteert apparaateigenschappen zoals de firmwareversie en het serienummer.

## <a name="prerequisites"></a>Vereisten

Voltooi de quickstart [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) om een IoT Central-toepassing te maken met behulp van de toepassing **Aangepaste app > Aangepaste sjabloon**.

## <a name="create-a-device-template"></a>Een apparaatsjabloon maken

Selecteer het tabblad **Apparaatsjablonen** in het linkerdeelvenster om een nieuwe apparaatsjabloon aan uw toepassing toe te voegen.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Schermopname met een lege lijst met apparaatsjablonen":::

Een apparaatsjabloon bevat een model waarin het volgende is gedefinieerd:

* De telemetrie die het apparaat verzendt.
* Apparaateigenschappen.
* De opdrachten waarop het apparaat reageert.

### <a name="add-a-device-template"></a>Een apparaatsjabloon toevoegen

Er zijn verschillende opties voor het toevoegen van een apparaatmodel aan uw IoT Central-toepassing. U kunt een geheel nieuw model maken, een model importeren uit een bestand of een apparaat selecteren uit de apparaatcatalogus. IoT Central biedt ook ondersteuning voor een *apparaat-eerst*-aanpak, waarbij automatisch een model wordt geïmporteerd uit een opslagplaats wanneer een echt apparaat voor het eerst verbinding maakt.

In deze quickstart kiest u een apparaat uit de apparaatcatalogus om een apparaatsjabloon te maken.

De volgende stappen laten zien hoe u de apparaatcatalogus kunt gebruiken om het model voor een **ESP32**-apparaat te importeren. Deze apparaten verzenden telemetrie, zoals temperatuur, naar uw toepassing:

1. Selecteer **+ Nieuw** op de pagina **Apparaatsjablonen** om een nieuwe apparaatsjabloon toe te voegen.

1. Schuif op **de pagina Type** selecteren omlaag totdat u de tegel **ESP32-Azure IoT Kit** vindt in de sectie Een vooraf geconfigureerde **apparaatsjabloon** gebruiken.

1. Selecteer de tegel **ESP32-Azure IoT Kit** en selecteer vervolgens **Volgende: Review**.

1. Selecteer op de pagina **Beoordelen** de optie **Maken**.

1. Na een paar seconden ziet u de nieuwe apparaatsjabloon:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Schermopname van apparaatsjabloon voor ESP32-apparaat":::

    De naam van de sjabloon is **Sensor Controller**. Het model bevat onderdelen als **Sensorcontroller**, **SensorTemp** en **Apparaatgegevens-interface**. Onderdelen definiëren de mogelijkheden van een ESP32-apparaat. Mogelijkheden zijn bijvoorbeeld de telemetrie, eigenschappen en opdrachten.

### <a name="add-cloud-properties"></a>Cloudeigenschappen toevoegen

Een apparaatsjabloon kan cloudeigenschappen bevatten. Cloudeigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat. Ga als volgt te werk om twee cloudeigenschappen toe te voegen:

1. Selecteer **Cloudeigenschappen** en klik vervolgens op **+ Cloudeigenschap toevoegen**. Gebruik de informatie in de volgende tabel om twee cloudeigenschappen toe te voegen aan uw apparaatsjabloon:

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Naam van klant     | Geen          | Tekenreeks |

1. Selecteer **Opslaan** om uw wijzigingen op te slaan:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Schermopname met twee cloudeigenschappen":::

## <a name="views"></a>Weergaven

U kunt de toepassing aanpassen om relevante informatie over het apparaat weer te geven. Met aanpassingen kunt u andere apparaten beheren die zijn verbonden met de toepassing. U kunt twee typen weergaven maken om te communiceren met apparaten:

* Formulieren voor het weergeven en bewerken van apparaat- en cloudeigenschappen.
* Dashboards om apparaten en de telemetrie die ze verzenden te visualiseren.

### <a name="default-views"></a>Standaardweergaven

Standaardweergaven zijn een snelle manier om aan de slag te gaan met het visualiseren van uw belangrijke apparaatgegevens. U kunt drie standaardweergaven genereren voor uw apparaatsjabloon:

* Met **de** weergave Opdrachten kunt u opdrachten naar uw apparaat verzenden.
* In de weergave **Overzicht** worden grafieken en metrische gegevens gebruikt om apparaattelemetrie weer te geven.
* In de weergave **Info** worden apparaateigenschappen weergegeven.

Selecteer het knooppunt **Weergaven** in de apparaatsjabloon. U ziet dat IoT Central een **Overzicht**- en **Info**-weergave heeft gemaakt toen u de sjabloon hebt toegevoegd.

Een nieuw formulier toevoegen om het apparaat te beheren:

1. Selecteer het knooppunt **Weergaven** en vervolgens de tegel **Apparaat- en cloudgegevens bewerken** om een nieuwe weergave toe te voegen.

1. Wijzig de naam van het formulier in **Apparaat beheren**.

1. Selecteer de cloudeigenschappen **Klantnaam** en **Laatste servicedatum** en de eigenschap **Doeltemperatuur**. Selecteer vervolgens **Sectie toevoegen**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Schermopname met het nieuwe formulier dat is toegevoegd aan de apparaatsjabloon":::

1. Selecteer **Opslaan** om uw nieuwe formulier op te slaan.

## <a name="publish-device-template"></a>Apparaatsjabloon publiceren

Voordat u een gesimuleerd apparaat kunt maken of een echt apparaat aansluiten, moet u de sjabloon voor het apparaat publiceren. Hoewel IoT Central de sjabloon heeft gepubliceerd toen u deze hebt gemaakt, moet u de bijgewerkte versie publiceren.

Een apparaatsjabloon publiceren:

1. Ga naar uw apparaatsjabloon **Sensor Controller** op de pagina **Apparaatsjablonen**.

1. Selecteer **Publiceren** in de opdrachtbalk boven aan de pagina.

1. Selecteer Publiceren in het dialoogvenster dat wordt **weergegeven.**

Nadat u een apparaatsjabloon hebt gepubliceerd, wordt deze weergegeven op de pagina **Apparaten**. U kunt in een gepubliceerde apparaatsjabloon geen apparaatmodel bewerken zonder een nieuwe versie te maken. U kunt wel de cloudeigenschappen, aanpassingen en weergaven in een gepubliceerde apparaatsjabloon wijzigen zonder een nieuwe versie te maken. Nadat u wijzigingen heeft aangebracht, selecteert **u Publiceren**  om deze wijzigingen te pushen voor echte en gesimuleerde apparaten die moeten worden gebruikt.

## <a name="add-a-simulated-device"></a>Een gesimuleerd apparaat toevoegen

Als u een gesimuleerd apparaat wilt toevoegen aan uw toepassing, gebruikt u de **ESP32**-apparaatsjabloon die u hebt gemaakt.

1. Als u een nieuw apparaat wilt toevoegen, **kiest u Apparaten** in het linkerdeelvenster. Op het tabblad **Apparaten** worden **Alle apparaten** en de apparaatsjabloon **Sensorcontroller** voor het ESP32-apparaat weergegeven. Selecteer **Sensorcontroller**.

1. Selecteer **+ Nieuw** om een gesimuleerd DevKit-apparaat toe te voegen. Gebruik de voorgestelde **Apparaat-id** of voer uw eigen apparaat-id in. Een apparaat-id mag alleen letters, cijfers en het teken `-` bevatten. U kunt ook een naam voor uw nieuwe apparaat invoeren. Zorg ervoor dat **Dit apparaat simuleren** is ingesteld op **Ja** en selecteer vervolgens **Maken**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Schermopname met het gesimuleerde Sensorcontroller-apparaat":::

U kunt nu communiceren met de weergaven die eerder zijn gemaakt met behulp van gesimuleerde gegevens:

1. Selecteer uw gesimuleerde apparaat op de pagina **Apparaten**

    * In de weergave **Overzicht** ziet u een grafiek van de gesimuleerde telemetrie:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Schermopname met overzichtspagina voor gesimuleerd apparaat":::

    * In de weergave **Info** worden eigenschapswaarden weergegeven.

    * In de weergave **Opdrachten** kunt u opdrachten uitvoeren op het apparaat, zoals **opnieuw opstarten**.

    * De **weergave Apparaten** beheren is het formulier dat u hebt gemaakt om het apparaat te beheren.

    * In de weergave **Onbewerkte gegevens** kunt u de onbewerkte telemetrie en eigenschapswaarden bekijken die door het apparaat worden verzonden. Deze weergave is handig voor het opsporen van problemen met apparaten.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een **Sensorcontroller**-apparaatsjabloon voor een ESP32-apparaat kunt maken en een gesimuleerd apparaat kunt toevoegen aan uw toepassing.

Ga voor meer informatie over het bewaken van met uw toepassing verbonden apparaten verder met de quickstart:

> [!div class="nextstepaction"]
> [Regels en acties configureren](./quick-configure-rules.md)
