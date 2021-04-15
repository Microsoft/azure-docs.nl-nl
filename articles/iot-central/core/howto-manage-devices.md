---
title: De apparaten in uw Azure IoT Central-| Microsoft Docs
description: Als operator leert u hoe u apparaten in uw Azure IoT Central beheert. Meer informatie over het beheren van afzonderlijke apparaten en het bulksgewijs importeren en exporteren van de apparaten in uw toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5bab4a7a90101d3749571e0f2d4179f0fce14296
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378632"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Apparaten in uw Azure IoT Central beheren

In dit artikel wordt beschreven hoe u als operator apparaten in uw Azure IoT Central beheert. Als operator kunt u het volgende doen:

- Gebruik de **pagina Apparaten** om apparaten weer te geven, toe te voegen en te verwijderen die zijn verbonden met uw Azure IoT Central toepassing.
- Apparaten bulksgewijs importeren en exporteren.
- Een up-to-date inventaris van uw apparaten onderhouden.
- Houd de metagegevens van uw apparaat up-to-date door de waarden die zijn opgeslagen in de apparaateigenschappen te wijzigen vanuit uw weergaven.
- U kunt het gedrag van uw apparaten beheren door een instelling op een specifiek apparaat bij te werken vanuit uw weergaven.

Zie Zelfstudie: Apparaatgroepen gebruiken om [telemetrie](tutorial-use-device-groups.md)van apparaten te analyseren voor meer informatie over het beheren van aangepaste groepen apparaten.

## <a name="view-your-devices"></a>Uw apparaten weergeven

Een afzonderlijk apparaat weergeven:

1. Kies **Apparaten** in het linkerdeelvenster. Hier ziet u een lijst met alle apparaten en van uw apparaatsjablonen.

1. Kies een apparaatsjabloon.

1. In het rechterdeelvenster van de pagina **Apparaten** ziet u een lijst met apparaten die zijn gemaakt op basis van die apparaatsjabloon. Kies een afzonderlijk apparaat om de pagina met apparaatdetails voor dat apparaat weer te geven:

    ![Pagina apparaatdetails](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Een apparaat toevoegen

Een apparaat toevoegen aan uw Azure IoT Central toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies de apparaatsjabloon van waaruit u een apparaat wilt maken.

1. Kies + **Nieuw.**

1. Zet de **schakelknop Gesimuleerd** op **Aan of** **Uit.** Een echt apparaat is voor een fysiek apparaat dat u verbindt met uw Azure IoT Central toepassing. Een gesimuleerd apparaat heeft voorbeeldgegevens die voor u zijn gegenereerd door Azure IoT Central.

1. Selecteer **Maken**.

1. Dit apparaat wordt nu weergegeven in de lijst met apparaten voor deze sjabloon. Selecteer het apparaat om de pagina met apparaatdetails te zien die alle weergaven voor het apparaat bevat.

## <a name="import-devices"></a>Apparaten importeren

Als u een groot aantal apparaten wilt verbinden met uw toepassing, kunt u apparaten bulksgewijs importeren uit een CSV-bestand. Het CSV-bestand moet de volgende kolomkoppen hebben:

| Kolom | Beschrijving 
| - | - | 
| IOTC_DEVICEID | De apparaat-id is een unieke id die door dit apparaat wordt gebruikt om verbinding te maken. De apparaat-id kan letters, cijfers en het `-` teken bevatten zonder spaties. |
| IOTC_DEVICENAME | Optioneel. De apparaatnaam is een gebruiksvriendelijke naam die in de hele toepassing wordt weergegeven. Als dit niet wordt opgegeven, is dit hetzelfde als de apparaat-id.   |

Apparaten bulksgewijs registreren in uw toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies in het linkerdeelvenster de apparaatsjabloon waarvoor u de apparaten bulksgewijs wilt maken.

    > [!NOTE]
    > Als u nog geen apparaatsjabloon hebt, kunt u apparaten importeren onder **Alle** apparaten en deze zonder sjabloon registreren. Nadat apparaten zijn geïmporteerd, kunt u ze migreren naar een sjabloon.

1. Selecteer **Importeren**.

    ![Importactie](./media/howto-manage-devices/bulkimport1a.png)


1. Selecteer het CSV-bestand met de lijst met apparaat-ID's die moeten worden geïmporteerd.

1. Het importeren van het apparaat wordt gestart zodra het bestand is geüpload. U kunt de importstatus volgen in het deelvenster Apparaatbewerkingen. Dit deelvenster wordt automatisch weergegeven nadat het importeren is gestart of u kunt het openen via het belpictogram in de rechterbovenhoek.

1. Zodra het importeren is voltooid, wordt een bericht weergegeven in het deelvenster Apparaatbewerkingen.

    ![Importeren geslaagd](./media/howto-manage-devices/bulkimport3a.png)

Als de importbewerking van het apparaat mislukt, wordt er een foutbericht weergegeven in het deelvenster Apparaatbewerkingen. Er wordt een logboekbestand gegenereerd met alle fouten dat u kunt downloaden.

## <a name="migrate-devices-to-a-template"></a>Apparaten migreren naar een sjabloon

Als u apparaten registreert door het importeren te starten onder **Alle apparaten,** worden de apparaten gemaakt zonder apparaatsjabloon-associatie. Apparaten moeten worden gekoppeld aan een sjabloon om de gegevens en andere gegevens over het apparaat te verkennen. Volg deze stappen om apparaten te koppelen aan een sjabloon:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies in het linkerpaneel Alle **apparaten:**

    ![Niet-verbonden apparaten](./media/howto-manage-devices/unassociateddevices1a.png)

1. Gebruik het filter op het raster om  te bepalen of de waarde in de kolom **Apparaatsjabloon Niet-verbonden** is voor een van uw apparaten.

1. Selecteer de apparaten die u aan een sjabloon wilt koppelen:

1. Selecteer **Migreren:**

    ![Apparaten koppelen](./media/howto-manage-devices/unassociateddevices2a.png)

1. Kies de sjabloon in de lijst met beschikbare sjablonen en selecteer **Migreren.**

1. De geselecteerde apparaten zijn gekoppeld aan de apparaatsjabloon die u hebt gekozen.

## <a name="export-devices"></a>Apparaten exporteren

Als u een echt apparaat wilt verbinden met IoT Central, hebt u de connection string. U kunt apparaatdetails bulksgewijs exporteren om de informatie op te halen die u nodig hebt om apparaatverbindingsreeksen te maken. Tijdens het exportproces wordt een CSV-bestand gemaakt met de apparaat-id, apparaatnaam en sleutels voor alle geselecteerde apparaten.

Apparaten bulksgewijs exporteren vanuit uw toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies in het linkerdeelvenster de apparaatsjabloon waaruit u de apparaten wilt exporteren.

1. Selecteer de apparaten die u wilt exporteren en selecteer vervolgens de **actie** Exporteren.

    ![Exporteren](./media/howto-manage-devices/export1a.png)

1. Het exportproces wordt gestart. U kunt de status volgen via het deelvenster Apparaatbewerkingen.

1. Wanneer de export is voltooid, wordt er een bericht weergegeven met een koppeling om het gegenereerde bestand te downloaden.

1. Selecteer de **koppeling Bestand** downloaden om het bestand te downloaden naar een lokale map op de schijf.

    ![Exporteren is geslaagd](./media/howto-manage-devices/export2a.png)

1. Het geëxporteerde CSV-bestand bevat de volgende kolommen: apparaat-id, apparaatnaam, apparaatsleutels en X509-certificaatvingerafdrukken:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Zie Apparaatconnectiviteit in Azure IoT Central voor meer informatie over verbindingsreeksen en het verbinden van echte apparaten met IoT Central [toepassing.](concepts-get-connected.md)

## <a name="delete-a-device"></a>Een apparaat verwijderen

Een echt of gesimuleerd apparaat verwijderen uit uw Azure IoT Central toepassing:

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies de apparaatsjabloon van het apparaat dat u wilt verwijderen.

1. Gebruik de filterhulpprogramma's om uw apparaten te filteren en te zoeken. Vink het selectievakje aan naast de apparaten die u wilt verwijderen.

1. Kies **Verwijderen**. U kunt de status van deze verwijdering bijhouden in het deelvenster Apparaatbewerkingen.

## <a name="change-a-property"></a>Een eigenschap wijzigen

Cloudeigenschappen zijn de metagegevens van het apparaat die aan het apparaat zijn gekoppeld, zoals plaats en serienummer. Cloudeigenschappen bestaan alleen in IoT Central toepassing en worden niet gesynchroniseerd met uw apparaten. Beschrijfbare eigenschappen bepalen het gedrag van een apparaat en stellen u in staat om de status van een apparaat op afstand in te stellen, bijvoorbeeld door de doeltemperatuur van een thermostaatapparaat in te stellen.  Apparaateigenschappen worden ingesteld door het apparaat en zijn alleen-lezen binnen IoT Central. U kunt eigenschappen weergeven en bijwerken in de **weergaven Apparaatdetails** voor uw apparaat.

1. Kies **Apparaten** in het linkerdeelvenster.

1. Kies de apparaatsjabloon van het apparaat waarvan u de eigenschappen wilt wijzigen en selecteer het doelapparaat.

1. Kies de weergave die eigenschappen voor uw apparaat bevat.  Met deze weergave kunt u waarden invoeren en Opslaan bovenaan de pagina selecteren. Hier ziet u de eigenschappen die uw apparaat heeft en de huidige waarden. Cloudeigenschappen en beschrijfbare eigenschappen hebben bewerkbare velden, terwijl apparaateigenschappen alleen-lezen zijn. Voor beschrijfbare eigenschappen ziet u de synchronisatiestatus onderaan het veld. 

1. Wijzig de eigenschappen in de waarden die u nodig hebt. U kunt meerdere eigenschappen tegelijk wijzigen en ze allemaal tegelijk bijwerken.

1. Kies **Opslaan**. Als u beschrijfbare eigenschappen hebt opgeslagen, worden de waarden naar uw apparaat verzonden. Wanneer het apparaat de wijziging voor de beschrijfbare eigenschap bevestigt, wordt de status weer **gesynchroniseerd.** Als u een cloud-eigenschap hebt opgeslagen, wordt de waarde bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaten beheert in uw Azure IoT Central toepassing, is de voorgestelde volgende stap het configureren van regels[voor](howto-configure-rules.md) uw apparaten.
