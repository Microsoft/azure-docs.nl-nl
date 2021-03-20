---
title: Specificaties van Microsoft Azure Edge-FXT | Microsoft Docs
description: Meer informatie over de fysieke en milieutechnische specificaties voor Microsoft Azure FXT edge-hardware.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: b1a172adb54593421ef56e09c131e0ddcaee9f6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92215821"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specificaties van Azure FXT Edge-bestanden

In dit artikel worden de hardwarespecificaties beschreven van de hardware-knoop punten van de Azure FXT Edge-bestanden. In de praktijk worden drie of meer knoop punten tegelijk geconfigureerd om het geclusterde cache systeem te bieden.

## <a name="hardware-specifications"></a>Hardwarespecificaties

| Onderdeel | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| CPU-kernen |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Netwerkpoorten | 6 x 25/10 GB + 2 x 1 GB | 6 x 25/10 GB + 2 x 1 GB |
| NVMe-SSD-capaciteit | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specificaties van station

Het systeem heeft tien drive bays die vanaf de voor grond toegankelijk zijn. Elk gevulde station bevindt zich aan de rechter kant met capaciteits informatie.

Stations worden afgedrukt op de ruimte tussen stations. In de Azure FXT Edge-Bestands server is station 0 het meest linkse station en Station 1 direct onder.

![Foto van een Bay van een harde schijf in het FXT-chassis, met stations en capaciteits labels](media/fxt-drives-photo.png)

| Stationsnummer    |  Gebruik   |  Specificaties |
|------------------|--------|-----------------|
| 0, 1             | Besturingssysteem     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Gegevens   | FXT 6600:3,2 TB NVMe SSD <br> FXT 6400:1,6 TB NVMe SSD |

## <a name="dimensions-and-weight"></a>Afmetingen en gewicht

De Azure FXT Edge-Bestandsr is ontworpen voor gebruik in een Standard 19-apparaat en is één rack eenheid High (1U).

<!-- 10x2.5 inches version -->

| Afmetingen van de bestandsr            | Waarde                    |
|-----------------------------|--------------------------|
| Hoogte                      | 42,8 mm (1,68 inch)    |
| Breedte (inclusief rack oren) | 482,0 mm (18,97 inch)  |
| Breedte: hoofd behuizing      | 434,0 mm (17,08 inch) |
| Diepte: Rack-oren tot de primaire behuizing                   | 733,82 mm (29,61 inch) |
| Diepte: Rack oren tot achterprotrusione achterzijde                 | 772,67 mm (30,42 inch) |
| Diepte: Rack oren tot de meest verprotrusione front-staat, zonder omlijsting | 22,0 mm (0,87 inch)  |
| Diepte: Rack oren tot de meest verprotrusione front-staat, met omlijsting    | 35,84 mm (1,41 inch) |

| Gewicht | Waarde |
|-----------------|----------------------|
| Gewicht van knoop punt (zonder verpakking, zonder accessoires) | 40 lbs (18,1 kg) |
| Nettogewicht (zonder verpakking, inclusief accessoires) | 51 lbs (23,1 kg)|
| Brutogewicht (als verzonden, inclusief alle verpakking) |  64 lbs (29,0 kg) |

### <a name="shipping-dimensions"></a>Verzend dimensies

| Pakket dimensie | Millimeters | Mm |
|-------------------|-------------|--------|
| Hoogte            | 311,2       | 12,25 ' |
| Breedte             | 642,8       | 25,31 ' |
| Lengte            | 1.051,1     | 41,38 ' |

## <a name="power-and-thermal-specifications"></a>Specificaties voor voedings-en thermische apparaten

Deze sectie biedt energie classificaties en metingen voor de Azure FXT Edge-bestands extensie.

### <a name="nameplate-ratings"></a>De kwalificatie classificaties

| Categorie-classificaties voor FXT 6000-serie modellen |
|----------------|
| 100-240V ~    |
| 10 BIS-5 BIS (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Stroom-en thermische metingen

De knoop punten van de Azure FXT Edge-bestanden maken gebruik van variabele snelheid-ventilatoren, dus voeding is afhankelijk van de Tempe ratuur en de belasting. Maximale ventilator snelheden kunnen worden bereikt bij bepaalde combi Naties van hoge belasting en verhoogde omgevings temperaturen.

Deze grafieken bieden energie verbruik en thermische uitvoer metingen voor veelgebruikte combi Naties van spannings frequentie.

| FXT 6600-kracht bij kamer temperatuur <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spanning (V) | 100 | 120 | 208 | 230 | 240 |
| Frequentie (Hz) | 60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 5,02 | 4,16 |2,40 | 2,20 | 2.16 |
| Zichtbaar vermogen (VA) | 502 | 499 | 499 | 506 | 518|
| Energie factor | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Real-Power (W) | 497 |494 | 489 | 496 | 508 |
| Thermische dissipatie (BTU/uur) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 voeding met maximale ventilator snelheid | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spanning (V) | 100 |120 | 208 | 230 | 240|
| Frequentie (Hz) | 60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 5,98 | 5,01 | 2,81 | 2.55 | 2,48 |
| Zichtbaar vermogen (VA) | 598 | 601 | 584 | 587 | 595 |
| Energie factor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Real-Power (W) | 592 | 595 | 573 | 575 | 583 |
| Thermische dissipatie (BTU/uur) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400-kracht bij kamer temperatuur <br />(22 ° C, 71,6 ° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spanning (V) | 100 | 120 | 208 | 230 | 240 |
| Frequentie (Hz) |60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 4,63 | 3,86 | 2.24 | 2,04 | 1,94 |
| Zichtbaar vermogen (VA) | 463 | 463 | 466 | 469 | 466 |
| Energie factor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Real-Power (W) | 458 | 459 | 457 | 460 | 456 |
| Thermische dissipatie (BTU/uur) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 voeding met maximale ventilator snelheid | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Spanning (V) | 100 | 120 | 208 | 230 | 240 |
| Frequentie (Hz) | 60 | 60 | 60 | 50 | 50 |
| Huidige (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2.13 |
| Zichtbaar vermogen (VA) | 515 | 514 | 516 | 524 | 511 |
| Energie factor | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Real-Power (W) | 510 | 508 | 506 | 514 | 501 |
| Thermische dissipatie (BTU/uur) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Milieu vereisten

Deze sectie bevat specificaties voor de omgevings omgeving van de hardware.

### <a name="temperature-and-humidity"></a>Tempe ratuur en vochtigheid

| Milieu kenmerk   | Besturings bereik                   | Niet-werkend bereik         |
|---------------------------|-----------------------------------|-----------------------------|
| Omgevings temperatuur bereik | 10 °C tot 35 °C (50-86 °F)          | -40 °C tot 65 °C (-40-149 °F) |
| Relatieve lucht vochtigheid | 10%-80% niet-condenserend          | 5%-95% niet-condenserend     |
| Maximum Dew-punt         | 29 °C (84 °F)                       | 33 °C (91 °F)                 |
| Hoogte                  | tot 3048 meters (10.000 meter), afhankelijk van de Tempe ratuur die hieronder wordt vermeld | tot 12.000 meters (39.370 meter) |

> [!NOTE]
> **Hoogte temperatuur van de beoordeling:** De maximale Tempe ratuur wordt verlaagd met 1 °C/300 m (1 °F/547 ft) boven 950 m (3.117 ft).

### <a name="airflow-shock-and-vibration"></a>Lucht stroom, schok en trillingen

| Kenmerk         | Specificatie |
|-------------------|---------------|
| Lucht stroom                    | De stroom van het systeem is aan de achterzijde. Het systeem moet worden bediend met een low-druk geluid. |
| Schokken, operationeel         | 6 G voor 11 milliseconden (getest in 6 standen) |
| Schokken, niet-operationeel     | 71 G voor 2 milliseconden (getest in 6 standen) |
| Trillingen, operationeel     | 0,26 G<sub>RMS</sub> 5 hz tot 350 Hz wille keurig         |
| Trillingen, niet-operationeel | 1,88 G<sub>RMS</sub> 10 hz tot 500 Hz gedurende 15 minuten (alle zes zijden zijn getest)  |

## <a name="safety-regulation-compliance"></a>Naleving van veiligheids regelgeving

De Azure FXT Edge-Bestandsr voldoet aan de genoemde voor Schriften.

| Categorie       | Reglementaire specificatie |
|----------------|--------------------------|
| Algemene veiligheid | EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 <br>EN 62311:2008 |
| |            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energie         | Verordening (EU) van de Commissie. 617/2013  |
| BvGS           |    EN 50581:2012   |
