---
title: Technische specificaties voor StorSimple | Microsoft Docs
description: Hierin worden de technische specificaties en nalevings gegevens voor reglementaire standaarden voor de StorSimple-hardwareonderdelen beschreven.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68965293"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Technische specificaties en naleving voor het StorSimple-apparaat

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

De hardwareonderdelen van uw Microsoft Azure StorSimple-apparaat voldoen aan de technische specificaties en regelgevings normen die in dit artikel worden beschreven. In de technische specificaties worden de voedings-en koel modules (PCMs), schijf stations, opslag capaciteit en behuizingen beschreven. De compatibiliteits informatie behandelt zaken als internationale normen, veiligheid en emissies, en bekabeling.

## <a name="power-and-cooling-module-specifications"></a>Specificaties voor voedings-en koel module

Het StorSimple-apparaat heeft twee 100-240 V-ventilatoren met SBB-compatibel (PCMs). Dit biedt een redundante energie configuratie. Als een PCM mislukt, blijft het apparaat normaal actief op de andere PCM totdat de module defect wordt vervangen.

De EBOD Enclosure maakt gebruik van een 580 W PCM en de primaire behuizing maakt gebruik van een 764 W PCM. De volgende tabellen geven een lijst van de technische specificaties die zijn gekoppeld aan de PCMs.

| Specificatie | 580 W PCM (EBOD) | 764 W PCM (primair) |
| --- | --- | --- |
| Maximale uitvoer kracht |580 W |764 |
| Frequentie |50/60 Hz |50/60 Hz |
| Selectie van voltage bereik |Automatisch variërend: 90 – 264 V AC, 47/63 Hz |Automatisch variërend: 90-264 V AC, 47/63 Hz |
| Maximum inactieve stroom |20 A |20 A |
| Correctie van de energie factor |>95% nominale invoer spanning |>95% nominale invoer spanning |
| Harmonischen |Voldoet aan EN61000-3-2 |Voldoet aan EN61000-3-2 |
| Uitvoer |5 v stand-by-spanning \@ 2,0 A |5 v stand-by-spanning \@ 2,7 A |
| + 5V \@ 42 A |+ 5V \@ 40 A | |
| + 12V \@ 38 A |+ 12V \@ 38 A | |
| Hot pluggable |Ja |Ja |
| Switches en Led's |WISSELSTROOM schakelaar en vier Led's voor de status indicator |Schakelaar voor AC aan/uit en zes Led's voor de status indicator |
| Behuizing-koeling |Axiale ventilatoren met variabele ventilator snelheid Control |Axiale ventilatoren met variabele ventilator snelheid Control |

## <a name="power-consumption-statistics"></a>Statistieken voor energie verbruik

De volgende tabel bevat de typische gegevens voor het energie verbruik (werkelijke waarden kunnen afwijken van de publicatie) voor de verschillende modellen van het StorSimple-apparaat.

| Voorwaarden | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventilatoren langzaam, stations niet-actief |1,45 A |0,31 kW |1057,76 BTU/uur |3,19 A |0,34 kW |1160,13 BTU/uur |
|  Ventilatoren langzaam, stations toegang |1,54 A |0,33 kW |1126,01 BTU/uur |3,27 A |0,36 kW |1228,37 BTU/uur |
|  Snelle ventilatoren, niet-actieve schijven, twee PSUs-voeding |2,14 A |0,49 kW |1671,95 BTU/uur |4,99 A |0,54 kW |1842,56 BTU/uur |
|  Snelle ventilatoren, stations niet-actief, één PSU is één maal actief |2,05 A |0,48 kW |1637,83 BTU/uur |4,58 A |0,50 kW |1706,07 BTU/uur |
|  Snelle ventilatoren, drives toegang, twee PSUs-voeding |2,26 A |0,51 kW |1740,19 BTU/uur |4,95 A |0,54 kW |1842,56 BTU/uur |
|  Snelle ventilatoren, toegang tot stations, één PSU-inactief |2,14 A |0,49 kW |1671,95 BTU/uur |4,81 A |0,53 kW |1808,44 BTU/uur |

## <a name="disk-drive-specifications"></a>Specificaties van schijf station

Uw StorSimple-apparaat ondersteunt Maxi maal 12 3,5-inch SAS-schijf stations (Serial Attached SCSI). De daad werkelijke schijven kunnen een combi natie zijn van Ssd's (Solid-state drives) of harde schijven (Hdd's), afhankelijk van de product configuratie. De 12 schijf stations bevinden zich in een configuratie van 3 bij 4 voor de behuizing. De EBOD Enclosure biedt extra opslag ruimte voor een andere 12 schijf stations. Deze zijn altijd Hdd's.

## <a name="storage-specifications"></a>Opslag specificaties

De StorSimple-apparaten hebben een combi natie van harde schijven en Solid-State schijven voor zowel de 8100 als de 8600. De totale bruikbare capaciteit voor 8100 en 8600 is respectievelijk ongeveer 15 TB en 38 TB. In de volgende tabel worden de details van SSD-, HDD-en Cloud capaciteit gedocumenteerd in de context van de capaciteit van de StorSimple-oplossing.

| Model/capaciteit van apparaat | 8100 | 8600 |
| --- | --- | --- |
| Aantal harde schijven (Hdd's) |8 |19 |
| Aantal Solid-state drives (Ssd's) |4 |5 |
| Capaciteit van één HDD |4 TB |4 TB |
| Capaciteit van één SSD |400 GB |800 GB |
| Reserve capaciteit |4 TB |4 TB |
| Bruikbare capaciteit van de harde schijf |14 TB |36 TB |
| Bruikbare SSD-capaciteit |800 GB |2 TB |
| Totale bruikbare capaciteit * |~ 15 TB |~ 38 TB |
| Maximale oplossings capaciteit (inclusief de Cloud) |200 TB |500 TB |

<sup>* </sup>- *De totale bruikbare capaciteit omvat de capaciteit die beschikbaar is voor gegevens, meta gegevens en buffers. U kunt lokaal vastgemaakte volumes inrichten tot 8,5 TB op het 8100-apparaat of tot 22,5 TB op het grotere 8600-apparaat. Ga naar [lokaal vastgemaakte volumes van StorSimple](storsimple-8000-local-volume-faq.md)voor meer informatie.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen van behuizing en gewichts specificaties

In de volgende tabellen staan de verschillende specificaties van de behuizing voor dimensies en gewicht.

### <a name="enclosure-dimensions"></a>Afmetingen van behuizing

De volgende tabel bevat de afmetingen van de behuizing in millimeters en inches.

| Sluit | Millimeters | Mm |
| --- | --- | --- |
| Hoogte |87,9 |3,46 |
| Breedte van de koppelings flens |483 |19,02 |
| De breedte van de hoofd tekst van de behuizing |443 |17,44 |
| Diepte van de voorste montage-flens tot de lengte van de hoofd tekst van de behuizing |577 |22,72 |
| Diepte van het paneel bewerkingen tot de meest verwerkings ruimte van de behuizing |630,5 |24,82 |
| Diepte van het koppelen van flens aan de achterliggende uiteinde van de behuizing |603 |23,74 |

### <a name="enclosure-weight"></a>Gewicht van behuizing

Afhankelijk van de configuratie kan een volledig geladen primaire behuizing worden afgewogen van 21 tot 33 KGS en moeten twee personen de verwerking afhandelen.

| Sluit | Gewicht |
| --- | --- |
| Maximum gewicht (afhankelijk van de configuratie) |30 kg – 33 kg |
| Leeg (er zijn geen stations geïnstalleerd) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specificaties van behuizing-omgeving

In deze sectie vindt u de specificaties met betrekking tot de behuizing-omgeving. De Tempe ratuur, vochtigheid, hoogte, schokken, trillingen, stand-by-veiligheid en elektromagnetische compatibiliteit (EMC) zijn opgenomen in deze categorie.

### <a name="temperature-and-humidity"></a>Tempe ratuur en vochtigheid

| Sluit | Omgevings temperatuur bereik | Relatieve lucht vochtigheid | Maximum natte lamp |
| --- | --- | --- | --- |
| Operationeel |5 °C-35 °C (41 °F-95 °F) |20%-80% niet-condenserend- |28 °C (82 °F) |
| Niet-operationeel |-40 °C-70 °C (40 °F-158 °F) |5%-100% niet-condenserend |29 °C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Lucht stroom, hoogte, schokken, trillingen, oriëntatie, veiligheid en EMC

| Sluit | Operationele specificaties |
| --- | --- |
| Lucht stroom |De stroom van het systeem is aan de achterzijde. Het systeem moet worden bediend met een installatie met een lage druk, een achterlopende uitlaat. De back-up die is gemaakt door rack deuren en obstakels mag niet groter zijn dan 5 Pascal (0,5 mm vocht meter). |
| Hoogte, operationeel |-30 meter tot 3045 meters (-100 meter tot 10.000 meter) met een maximale gebruiks temperatuur van 5 °C boven de 7000 meter. |
| Hoogte, niet-operationeel |-305 meters tot 12.192 meters (-1.000 meter tot 40.000 meter) |
| Schokken, operationeel |5g 10 MS 1/2-sinus |
| Schokken, niet-operationeel |30g 10 MS 1/2-sinus |
| Trillingen, operationeel |0.21 g RMS 5-500 Hz wille keurig |
| Trillingen, niet-operationeel |1.04 g RMS 2-200 Hz wille keurig |
| Trillingen, herlocatie |sinus van 3G 2-200 Hz |
| Afdruk stand en koppelen |19-rack koppeling (2 EIA-eenheden) |
| Rack Rails |Om aan te passen minimale racks van 700 mm (31,50 inch) die voldoen aan IEC 297 |
| Veiligheid en goed keuringen |CE en UL en 61000-3, IEC 61000-3, UL 61000-3 |
| | |EN55022 (CISPR-A), FCC A |

## <a name="international-standards-compliance"></a>Naleving van International Standards

Uw Microsoft Azure StorSimple-apparaat voldoet aan de volgende internationale normen:  

* CE-EN 60950-1
* CB-rapport naar IEC 60950-1
* UL en cUL tot en met UL 60950-1

## <a name="safety-compliance"></a>Veiligheids naleving

Uw Microsoft Azure StorSimple-apparaat voldoet aan de volgende veiligheids beoordelingen:

* Goed keuring van type systeem product: UL, cUL, CE
* Veiligheids naleving: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-naleving

Uw Microsoft Azure StorSimple-apparaat voldoet aan de volgende EMC-classificaties.

### <a name="emissions"></a>Gassen

Het apparaat voldoet aan de voor waarden voor uitgevoerde en uitgestraalde emissies.

* Uitgevoerde emissie limiet niveaus: CFR 47 Part 15B Class a EN55022 klasse a CISPR Class a
* Limiet niveaus voor uitgestraalde emissies 47: de klasse 15B van de EN55022-klasse a CISPR klasse a

### <a name="harmonics-and-flicker"></a>Harmonischen en Flik keren

Het apparaat voldoet aan EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Grens waarden voor immuniteit

Het apparaat voldoet aan EN55024.

## <a name="ac-power-cord-compliance"></a>Naleving van netstroom kabel

De stekker en de volledige stekker van het netsnoer moeten voldoen aan de normen die van toepassing zijn voor het land/de regio waarin het apparaat wordt gebruikt, en zij moeten veiligheids goedkeuringen hebben die acceptabel zijn in dat land/deze regio. De volgende tabellen geven een lijst van de standaarden voor de Verenigde Staten en Europa.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Netstroom kabels-USA (moet NRTL worden vermeld)

| Onderdeel | Specificatie |
| --- | --- |
| Type kabel |SV of SVT, 18 AWG mini maal, 3 geleider, maximum lengte van 2,0 meters |
| UPnP |NEMA 5-15Ping van het type bijlage-stekker 120 V, 10 A; of IEC 320 C14, 250 V, 10 A |
| 1U |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Netstroom kabels-Europa

| Onderdeel | Specificatie |
| --- | --- |
| Type kabel |Geharmoniseerd, H05-VVF-3G 1.0 |
| 1U |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Ondersteunde netwerk kabels

Raadpleeg de [lijst met ondersteunde netwerk kabels en modules](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)voor de 10 GbE-netwerk interfaces, data 2 en data 3.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om een StorSimple-apparaat te implementeren in uw Data Center. Zie [uw on-premises apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie.

