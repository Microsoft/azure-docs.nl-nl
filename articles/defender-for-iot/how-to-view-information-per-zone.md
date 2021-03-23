---
title: Meer informatie over apparaten in specifieke zones
description: De on-premises beheer console gebruiken om een uitgebreide weer gave-informatie te verkrijgen per specifieke zone
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776227"
---
# <a name="view-information-per-zone"></a>Informatie per zone weer geven


## <a name="view-a-device-map-for-a-zone"></a>Een apparaattoewijzing voor een zone weer geven

Een apparaattoewijzing voor een geselecteerde zone weer geven op een sensor. In deze weer gave worden alle netwerk elementen weer gegeven die betrekking hebben op de geselecteerde zone, inclusief de Sens oren, de apparaten die zijn verbonden met de zones en andere informatie.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Scherm opname van de zone kaart.":::


- Selecteer in het venster **site beheer** de optie **zone toewijzing weer geven** op de balk met de zone naam.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Standaard regio voor de standaard bedrijfs eenheid.":::

Het venster **apparaattoewijzing** wordt weer gegeven. De volgende hulpprogram ma's zijn beschikbaar voor het weer geven van apparaten en apparaatgegevens van de kaart. Zie de *Gebruikers handleiding voor Defender voor IOT-platform* voor meer informatie over elk van deze functies.

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

## <a name="view-alerts-associated-with-a-zone"></a>Waarschuwingen weer geven die zijn gekoppeld aan een zone

Waarschuwingen weer geven die zijn gekoppeld aan een specifieke zone:

- Selecteer het waarschuwings pictogram in het venster van de **zone** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="De standaard weergave voor bedrijfs eenheden met voor beelden.":::

Zie [overzicht: werken met waarschuwingen](how-to-work-with-alerts-on-premises-management-console.md)voor meer informatie.

### <a name="view-the-device-inventory-of-a-zone"></a>De apparaat-inventaris van een zone weer geven

De inventaris van apparaten weer geven die zijn gekoppeld aan een specifieke zone:

- Selecteer **apparaat-inventaris weer geven** in het venster van de **zone** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Het scherm inventarisatie van apparaten wordt weer gegeven.":::

Zie voor meer informatie [onderzoeken van alle Enter prise sensor detecties in een inventaris van apparaten](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Aanvullende zone gegevens weer geven

De volgende aanvullende zone gegevens zijn beschikbaar:

- **Zone Details**: hier ziet u het aantal apparaten, waarschuwingen en Sens oren dat is gekoppeld aan de zone.

- **Sensor Details**: de naam, het IP-adres en de versie weer geven van elke sensor die aan de zone is toegewezen.

- **Verbindings status**: als de verbinding met een sensor is verbroken, kunt u verbinding maken vanaf de sensor. Zie [Sens oren verbinden met de on-premises beheer console](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Voortgang** van de update: als de verbonden sensor wordt bijgewerkt, worden de upgrade statussen weer gegeven. Tijdens de upgrade ontvangt de on-premises beheer console geen apparaatgegevens van de sensor.

## <a name="next-steps"></a>Volgende stappen

[Inzicht krijgen in globale, regionale en lokale bedreigingen](how-to-gain-insight-into-global-regional-and-local-threats.md)
