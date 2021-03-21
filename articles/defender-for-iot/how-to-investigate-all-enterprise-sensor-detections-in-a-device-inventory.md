---
title: Meer informatie over apparaten die door alle Enter prise Sens oren zijn gedetecteerd
description: Gebruik de apparaat-inventarisatie in de on-premises beheer console om een uitgebreid overzicht te krijgen van de apparaatgegevens van verbonden Sens oren. Gebruik de hulpprogram ma's voor importeren, exporteren en filteren om deze gegevens te beheren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524022"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Onderzoek alle Enter prise sensor-detecties in de inventaris van de apparaten

U kunt apparaatgegevens van verbonden Sens oren weer geven met behulp van de *inventarisatie van apparaten* in de on-premises beheer console. Deze functie biedt een uitgebreid overzicht van alle netwerk gegevens. Gebruik de hulpprogram ma's voor importeren, exporteren en filteren om deze gegevens te beheren. De status informatie over de verbonden sensor versies wordt ook weer gegeven.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Scherm opname van de gegevens tabel van de inventaris van het apparaat.":::

In de volgende tabel worden de tabel kolommen in de inventaris van apparaten beschreven.

| Parameter | Beschrijving |
|--|--|
| **Niet-bevestigde waarschuwingen** | Het aantal niet-verwerkte waarschuwingen dat is gekoppeld aan dit apparaat. |
| **Bedrijfs eenheid** | De bedrijfs eenheid die dit apparaat bevat. |
| **Regio** | De regio die dit apparaat bevat. |
| **Site** | De site die dit apparaat bevat. |
| **Zone** | De zone die dit apparaat bevat. |
| **Apparaat** | De Azure Defender voor IoT-sensor die dit apparaat beveiligt. |
| **Naam** | De naam van dit apparaat als Defender voor IoT gedetecteerd. |
| **Type** | Het type apparaat, zoals PLC of HMI. |
| **Leverancier** | De naam van de leverancier van het apparaat, zoals gedefinieerd in het MAC-adres. |
| **Besturingssysteem** | Het besturings systeem van het apparaat. |
| **Firmware** | De firmware van het apparaat. |
| **IP-adres** | Het IP-adres van het apparaat. |
| **VLAN** | Het VLAN van het apparaat. |
| **MAC-adres** | Het MAC-adres van het apparaat. |
| **Protocollen** | De protocollen die door het apparaat worden gebruikt. |
| **Niet-bevestigde waarschuwingen** | Het aantal niet-verwerkte waarschuwingen dat is gekoppeld aan dit apparaat. |
| **Is geautoriseerd** | De autorisatie status van het apparaat:<br />- **Waar**: het apparaat is geautoriseerd.<br />- **Onwaar**: het apparaat is niet geautoriseerd. |
| **Staat bekend als scanner** | Hiermee wordt aangegeven of dit apparaat scan achtige activiteiten in het netwerk uitvoert. |
| **Is programmerings apparaat** | Of dit een programmerings apparaat is:<br />- **Waar**: het apparaat voert programmeer activiteiten uit voor PLCs, RTUs en controllers, die relevant zijn voor de technische stations.<br />- **Onwaar**: het apparaat is geen programmeer apparaat. |
| **Groepen** | Groepen waarvan dit apparaat deel uitmaakt. |
| **Laatste activiteit** | De laatste activiteit die door het apparaat is uitgevoerd. |
| **Discovered** | Wanneer dit apparaat voor het eerst in het netwerk werd weer gegeven. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Gegevens integreren in de inventaris van het bedrijf

Dankzij de mogelijkheden voor gegevens integratie kunt u de gegevens in de inventaris van apparaten verbeteren met informatie van andere bedrijfs resources. Deze bronnen omvatten CMDB, DNS, firewalls en Web-Api's.

U kunt deze informatie gebruiken voor meer informatie. Bijvoorbeeld:

- Aankoop datums van apparaten en datum einde van garantie

- Gebruikers die verantwoordelijk zijn voor elk apparaat

- Geopende tickets voor apparaten

- De laatste datum waarop de firmware is bijgewerkt

- Apparaten die toegang tot internet hebben

- Apparaten met actieve antivirus toepassingen

- Gebruikers die zijn aangemeld bij apparaten

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Gegevens tabel in het scherm apparaat inventarisatie.":::

U kunt gegevens integreren met behulp van:

- Deze hand matig toevoegen

- Aangepaste scripts uitvoeren die worden uitgevoerd door Defender voor IoT

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagram van de Enter prise data integrator.":::

U kunt samen werken met Defender voor IoT-technische ondersteuning om uw systeem in te stellen voor het ontvangen van Web-API-query's.

Gegevens hand matig toevoegen:

1. Selecteer **apparaat-inventaris** in het menu aan de zijkant en selecteer :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Bewerk de inventaris instellingen van uw apparaat.":::

2. Selecteer in het dialoog venster **instellingen voor Apparaatbeheer** de optie **aangepaste kolom toevoegen**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Voeg een aangepaste kolom toe aan uw inventaris.":::

3. In het dialoog venster **aangepaste kolom toevoegen** voegt u de nieuwe kolom naam (maxi maal 250 tekens UTF) toe, selecteert u **hand matig** en selecteert u **Opslaan**. Het nieuwe item wordt weer gegeven in het dialoog venster **instellingen voor apparaat-inventarisatie** .

4. Selecteer in de rechter bovenhoek van het venster **inventarisatie van apparaten** de optie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **alle apparaat-inventaris exporteren**. Het CSV-bestand wordt gegenereerd.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Het geëxporteerde CSV-bestand.":::

5. Voeg de informatie hand matig toe aan de nieuwe kolom en sla het bestand op.

6. Selecteer in de rechter bovenhoek van het venster **inventarisatie van apparaten** de optie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **hand matige invoer kolommen importeren** en blader naar het CSV-bestand. De nieuwe gegevens worden weer gegeven in de **inventarisatie** tabel van het apparaat.

Gegevens van andere bedrijfs entiteiten integreren:

1. Selecteer in de rechter bovenhoek van het venster **inventarisatie van apparaten** de optie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: **alle apparaat-inventaris exporteren**.

2. Selecteer in het dialoog venster **instellingen voor Apparaatbeheer** de optie **aangepaste kolom toevoegen**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Voeg een aangepaste kolom toe aan uw inventaris.":::

3. Voeg in het dialoog venster **aangepaste kolom toevoegen** de nieuwe kolom naam (maxi maal 250 tekens UTF) toe en selecteer vervolgens **automatisch**. De opties voor het **Upload script** en de **test script** worden weer gegeven.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Automatisch aangepaste kolommen toevoegen.":::

4. Upload en test het script dat u van [Microsoft ondersteuning](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)hebt ontvangen.

## <a name="retrieve-information-from-the-device-inventory"></a>Informatie ophalen uit de inventaris van het apparaat

U kunt een uitgebreid scala aan apparaatgegevens ophalen dat is gedetecteerd door Managed Sens oren en die informatie integreren met partner systemen. U kunt bijvoorbeeld sensor, zone, site-ID, IP-adres, MAC-adres, firmware, protocol en informatie over de leverancier ophalen. Filter gegevens die u ophaalt op basis van:

- Geautoriseerde en niet-geautoriseerde apparaten.

- Apparaten die aan specifieke sites zijn gekoppeld.

- Apparaten die zijn gekoppeld aan specifieke zones.

- Apparaten die zijn gekoppeld aan bepaalde Sens oren.

Werk met Defender voor IoT API-opdrachten om deze informatie op te halen en te integreren. Zie voor meer informatie [Defender voor IOT API-sensor-en beheer console-api's](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>De inventarisatie van apparaten filteren

U kunt de inventaris van de apparaten filteren om kolommen met interesses weer te geven. U kunt bijvoorbeeld de gegevens van de PLC-apparaten weer geven.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Scherm opname van de inventaris van het apparaat.":::

Het filter wordt gewist wanneer u het venster verlaat.

Als u hetzelfde filter meerdere keren wilt gebruiken, kunt u een filter of een combi natie van filters die u nodig hebt, opslaan. U kunt een linkerdeel venster openen en de filters bekijken die u hebt opgeslagen:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Scherm inventarisatie van apparaten.":::

De inventarisatie van apparaten filteren:

1. Selecteer in de kolom die u wilt filteren :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. Selecteer in het dialoog venster **filteren** het filter type:

   - **Is gelijk aan**: de exacte waarde op basis waarvan u de kolom wilt filteren. Als u bijvoorbeeld de kolom Protocol filtert op basis van **gelijk** aan en `value=ICMP` , wordt in de kolom alleen apparaten weer gegeven die het ICMP-protocol gebruiken.

   - **Bevat**: de waarde die is opgenomen onder andere waarden in de kolom. Als u bijvoorbeeld de kolom Protocol filtert op basis van en, **bevat** `value=ICMP` de kolom apparaten die het ICMP-protocol gebruiken als onderdeel van de lijst met protocollen die door het apparaat worden gebruikt.

3. Als u de kolom informatie wilt ordenen volgens alfabetische volg orde, selecteert u :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Rang Schik de volg orde door de :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: pijlen en te selecteren :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Als u een nieuw filter wilt opslaan, definieert u het filter en selecteert u **Opslaan als**.

5. Als u de filter definities wilt wijzigen, wijzigt u de definities en selecteert u **wijzigingen opslaan**.

## <a name="view-device-information-per-zone"></a>Apparaatgegevens per zone weer geven

U vindt de volgende informatie over apparaten in een zone.

### <a name="view-a-device-map"></a>Een apparaattoewijzing weer geven

Een kaart voor sensor apparaten weer geven voor een geselecteerde zone:

- Selecteer in het venster **site beheer** de optie **zone toewijzing weer geven** op de balk met de zone naam.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Standaard regio voor de standaard bedrijfs eenheid.":::

Het venster **apparaattoewijzing** wordt weer gegeven. Hierin worden alle netwerk elementen weer gegeven die betrekking hebben op de geselecteerde zone, inclusief de Sens oren, de apparaten die zijn verbonden met de zones en andere informatie.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Scherm opname van de zone kaart.":::

De volgende hulpprogram ma's zijn beschikbaar voor het weer geven van apparaten en apparaatgegevens van de kaart. Zie de *Gebruikers handleiding voor Defender voor IOT-platform* voor meer informatie over elk van deze functies.

- **Zoom weergaven voor kaarten**: vereenvoudigde weer gave, weer gave van verbindingen en gedetailleerde weer gave. De weer gegeven kaart weergave varieert afhankelijk van het zoom niveau van de kaart. U schakelt tussen kaart weergaven door de zoom niveaus aan te passen.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Hulp middelen voor kaarten zoeken en lay-out**: hulpprogram ma's die worden gebruikt om verschillende netwerk segmenten, apparaten, apparaatgroepen of lagen weer te geven.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Scherm opname van de weer gave Zoek-en indelings Hulpprogramma's.":::

- **Labels en indica toren op apparaten:** Bijvoorbeeld het aantal apparaten dat is gegroepeerd in een subnet in een IT-netwerk. In dit voor beeld is dat 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Scherm opname van labels en indica toren.":::

- **Apparaateigenschappen weer geven**: bijvoorbeeld de sensor die het apparaat bewaken en de basis eigenschappen van het apparaat. Klik met de rechter muisknop op het apparaat om de eigenschappen van het apparaat weer te geven.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Scherm opname van de weer gave Apparaateigenschappen.":::

- **Waarschuwing die is gekoppeld aan een apparaat:** Klik met de rechter muisknop op het apparaat om gerelateerde waarschuwingen weer te geven.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Scherm opname van de weer gave waarschuwingen weer geven.":::

### <a name="view-alerts-associated-with-a-zone"></a>Waarschuwingen weer geven die zijn gekoppeld aan een zone

Waarschuwingen weer geven die zijn gekoppeld aan een specifieke zone:

- Selecteer het waarschuwings pictogram in het venster van de **zone** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="De standaard weergave voor bedrijfs eenheden met voor beelden.":::

Zie [overzicht: werken met waarschuwingen](how-to-work-with-alerts-on-premises-management-console.md)voor meer informatie.

### <a name="view-the-device-inventory-of-a-zone"></a>De apparaat-inventaris van een zone weer geven

De inventaris van apparaten weer geven die zijn gekoppeld aan een specifieke zone:

- Selecteer **apparaat-inventaris weer geven** in het venster van de **zone** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Het scherm inventarisatie van apparaten wordt weer gegeven.":::

Zie voor meer informatie [onderzoeken van alle Enter prise sensor detecties in een inventaris van apparaten](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

### <a name="view-additional-zone-information"></a>Aanvullende zone gegevens weer geven

De volgende aanvullende zone gegevens zijn beschikbaar:

- **Zone Details**: hier ziet u het aantal apparaten, waarschuwingen en Sens oren dat is gekoppeld aan de zone.

- **Sensor Details**: de naam, het IP-adres en de versie weer geven van elke sensor die aan de zone is toegewezen.

- **Verbindings status**: als de verbinding met een sensor is verbroken, kunt u verbinding maken vanaf de sensor. Zie [Sens oren verbinden met de on-premises beheer console](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Voortgang** van de update: als de verbonden sensor wordt bijgewerkt, worden de upgrade statussen weer gegeven. Tijdens de upgrade ontvangt de on-premises beheer console geen apparaatgegevens van de sensor.

## <a name="see-also"></a>Zie ook

[Alle sensordetecties in een apparaatinventaris onderzoeken](how-to-investigate-sensor-detections-in-a-device-inventory.md)
