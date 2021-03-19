---
title: Hand leiding voor Azure IoT Central-Opera tors
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. Dit artikel bevat een overzicht van de operator rol in IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/17/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: a5c307b8c3c186fd7dff2084c728f02b8165b861
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609235"
---
# <a name="iot-central-operator-guide"></a>IoT Central-operator handleiding

Dit artikel bevat een overzicht van de operator rol in IoT Central. Een operator beheert de apparaten die met de toepassing zijn verbonden.

Een _operator_ beheert de apparaten die met de toepassing zijn verbonden.

Als operator kunt u het volgende doen:

* Gebruik de pagina **apparaten** om apparaten weer te geven, toe te voegen en te verwijderen die zijn verbonden met uw Azure IOT Central-toepassing.
* Apparaten bulksgewijs importeren en exporteren.
* Onderhoud van een actuele inventaris van uw apparaten.
* De meta gegevens van uw apparaat up-to-date houden door de waarden die zijn opgeslagen in de eigenschappen van het apparaat in uw weer gaven te wijzigen.
* U bepaalt het gedrag van uw apparaten door een instelling op een specifiek apparaat in uw weer gaven bij te werken.

## <a name="iot-central-homepage"></a>Startpagina IoT Central

Op de startpagina van [IoT Central](https://aka.ms/iotcentral-get-started) vindt u meer informatie over het laatste nieuws en de beschikbare functies op IoT Central, kunt u nieuwe toepassingen maken en uw bestaande toepassing weergeven en starten.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Scherm afbeelding van IoT Central Home page":::

## <a name="view-your-devices"></a>Uw apparaten weergeven

Een afzonderlijk apparaat weer geven:

1. Kies **apparaten** in het linkerdeel venster. Hier ziet u een lijst met alle apparaten en de sjablonen van uw apparaten.

1. Kies een sjabloon voor het apparaat.

1. In het rechterdeel venster van de pagina **apparaten** ziet u een lijst met apparaten die zijn gemaakt op basis van die apparaataccount. Kies een afzonderlijk apparaat om de pagina met details van het apparaat voor dat apparaat weer te geven:

    ![Scherm afbeelding van de pagina met details van het apparaat](./media/overview-iot-central-operator/device-list.png)

## <a name="add-a-device"></a>Een apparaat toevoegen

Een apparaat toevoegen aan uw Azure IoT Central-toepassing:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de sjabloon van het apparaat waaruit u een apparaat wilt maken.

1. Kies + **Nieuw**.

1. Schakel de **gesimuleerde** wissel knop **in of** **uit**. Een echt apparaat is voor een fysiek apparaat waarmee u verbinding maakt met uw Azure IoT Central-toepassing. Voor een gesimuleerd apparaat zijn voorbeeld gegevens gegenereerd door Azure IoT Central.

1. Selecteer **Maken**.

1. Dit apparaat wordt nu weer gegeven in de lijst met apparaten voor deze sjabloon. Selecteer het apparaat om de detail pagina van het apparaat weer te geven die alle weer gaven voor het apparaat bevat.

## <a name="import-devices"></a>Apparaten importeren

Als u een groot aantal apparaten wilt verbinden met uw toepassing, kunt u apparaten bulksgewijs importeren uit een CSV-bestand. Het CSV-bestand moet de volgende kolommen en kopteksten hebben:

* **IOTC_DeviceID** -de apparaat-id mag letters, cijfers en het `-` teken bevatten.
* **IOTC_DeviceName** : deze kolom is optioneel.

Apparaten in uw toepassing bulksgewijs registreren:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies in het linkerdeel venster de sjabloon voor het apparaat waarvoor u de apparaten bulksgewijs wilt maken.

    > [!NOTE]
    > Als u nog geen sjabloon voor het apparaat hebt, kunt u apparaten onder **alle apparaten** importeren en ze zonder een sjabloon registreren. Nadat de apparaten zijn geïmporteerd, kunt u deze migreren naar een sjabloon.

1. Selecteer **Importeren**.

    ![Scherm afbeelding van import actie](./media/overview-iot-central-operator/bulk-import-1-a.png)


1. Selecteer het CSV-bestand met de lijst met apparaat-Id's die moeten worden geïmporteerd.

1. Het importeren van apparaten wordt gestart zodra het bestand is geüpload. U kunt de import status volgen in het deel venster apparaten. Dit paneel verschijnt automatisch nadat het importeren is gestart of u kunt het openen via het klok pictogram in de rechter bovenhoek.

1. Zodra het importeren is voltooid, wordt een bericht weer gegeven in het deel venster apparaten.

    ![Scherm opname van importeren geslaagd](./media/overview-iot-central-operator/bulk-import-3-a.png)

Als de Importeer bewerking van het apparaat mislukt, wordt er een fout bericht weer gegeven in het paneel voor het apparaat. Een logboek bestand waarin alle fouten worden vastgelegd, wordt gegenereerd dat u kunt downloaden.

## <a name="migrate-devices-to-a-template"></a>Apparaten naar een sjabloon migreren

Als u apparaten registreert door het importeren te starten onder **alle apparaten**, worden de apparaten gemaakt zonder een sjabloon koppeling van het apparaat. Apparaten moeten worden gekoppeld aan een sjabloon om de gegevens en andere details over het apparaat te kunnen verkennen. Volg deze stappen om apparaten te koppelen aan een sjabloon:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies in het linkerdeel venster **alle apparaten**:

    ![Scherm opname van niet-gekoppelde apparaten](./media/overview-iot-central-operator/unassociated-devices-2-a.png)

1. Gebruik het filter op het raster om te bepalen of de waarde in de kolom **device Temp late** is **gekoppeld** aan een van uw apparaten.

1. Selecteer de apparaten die u wilt koppelen aan een sjabloon:

1. Selecteer **migreren**:

    ![Scherm opname van apparaten koppelen](./media/overview-iot-central-operator/unassociated-devices-1-a.png)

1. Kies de sjabloon in de lijst met beschik bare sjablonen en selecteer **migreren**.

1. De geselecteerde apparaten zijn gekoppeld aan de apparaatprofiel die u hebt gekozen.

## <a name="export-devices"></a>Apparaten exporteren

Als u een echt apparaat wilt verbinden met IoT Central, hebt u het connection string. U kunt de details van het apparaat in bulk exporteren om de informatie te verkrijgen die u nodig hebt om verbindings reeksen voor apparaten te maken. Het export proces maakt een CSV-bestand met de apparaat-id, de apparaatnaam en de sleutels voor alle geselecteerde apparaten.

Apparaten bulksgewijs exporteren vanuit uw toepassing:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies in het linkerdeel venster de sjabloon voor het apparaat waaruit u de apparaten wilt exporteren.

1. Selecteer de apparaten die u wilt exporteren en selecteer vervolgens de **export** actie.

    ![Scherm afbeelding van exporteren](./media/overview-iot-central-operator/export-1-a.png)

1. Het export proces wordt gestart. U kunt de status bijhouden met behulp van het deel venster apparaten.

1. Wanneer het exporteren is voltooid, wordt een bericht weer gegeven samen met een koppeling om het gegenereerde bestand te downloaden.

1. Selecteer de koppeling **bestand downloaden** om het bestand te downloaden naar een lokale map op de schijf.

    ![Scherm opname van het exporteren is voltooid](./media/overview-iot-central-operator/export-2-a.png)

1. Het geëxporteerde CSV-bestand bevat de volgende kolommen: apparaat-ID, apparaatnaam, Apparaatsets en x509-certificaat vingerafdrukken:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Zie [connectiviteit van apparaten in Azure IOT Central](concepts-get-connected.md)voor meer informatie over verbindings reeksen en het verbinden van echte apparaten met uw IOT Central-toepassing.

## <a name="delete-a-device"></a>Een apparaat verwijderen

Een echt of gesimuleerd apparaat verwijderen uit uw Azure IoT Central-toepassing:

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de sjabloon van het apparaat dat u wilt verwijderen.

1. Gebruik de filter hulpprogramma's om uw apparaten te filteren en te zoeken. Schakel het selectie vakje in naast de apparaten die u wilt verwijderen.

1. Kies **Verwijderen**. U kunt de status van deze verwijdering volgen in het deel venster van het apparaat.

## <a name="change-a-property"></a>Een eigenschap wijzigen

Cloud eigenschappen zijn de meta gegevens van het apparaat die zijn gekoppeld aan het apparaat, zoals City en serie nummer. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden niet gesynchroniseerd met uw apparaten. Beschrijf bare eigenschappen bepalen het gedrag van een apparaat en stelt u in staat om de status van een apparaat op afstand in te stellen, bijvoorbeeld door de doel temperatuur van een thermo staat-apparaat in te stellen.  Apparaateigenschappen worden ingesteld door het apparaat en zijn alleen-lezen in IoT Central. U kunt eigenschappen weer geven en bijwerken op de details van het **apparaat** voor uw apparaat.

1. Kies **apparaten** in het linkerdeel venster.

1. Kies de Device-sjabloon van het apparaat waarvan u de eigenschappen wilt wijzigen en selecteer het doel apparaat.

1. Kies de weer gave met de eigenschappen van het apparaat. in deze weer gave kunt u waarden invoeren en boven aan de pagina **Opslaan** selecteren. Hier ziet u de eigenschappen van uw apparaat en de huidige waarden. Eigenschappen van de Cloud en de Beschrijf bare eigenschappen hebben bewerkte velden, terwijl de apparaateigenschappen alleen-lezen zijn. Voor schrijf bare eigenschappen ziet u de synchronisatie status onder aan het veld. 

1. Wijzig de eigenschappen in de gewenste waarden. U kunt meerdere eigenschappen tegelijk wijzigen en deze allemaal tegelijk bijwerken.

1. Kies **Opslaan**. Als u schrijf bare eigenschappen hebt opgeslagen, worden de waarden naar uw apparaat verzonden. Wanneer het apparaat de wijziging voor de Beschrijf bare eigenschap bevestigt, keert de status terug naar **gesynchroniseerd**. Als u een Cloud eigenschap hebt opgeslagen, wordt de waarde bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaten in uw Azure IoT Central-toepassing beheert, is de voorgestelde volgende stap informatie over het [configureren van regels](howto-configure-rules.md) voor uw apparaten.