---
title: Technische specificaties en naleving van Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Meer informatie over de technische specificaties en naleving voor uw Azure Stack Edge Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 8b36bb34f4c9081d807998cb8287797443625a6b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460300"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Technische specificaties van Azure Stack Edge Pro

De hardwareonderdelen van uw Microsoft Azure Stack Edge Pro-apparaat voldoen aan de technische specificaties en regelgevings normen als beschreven in dit artikel. De technische specificaties beschrijven de voedings eenheden (PSUs), opslag capaciteit, behuizingen en omgevings standaarden.

## <a name="compute-memory-specifications"></a>Compute, geheugen specificaties

Het Azure Stack Edge Pro-apparaat heeft de volgende specificaties voor Compute en geheugen:

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 core CPU Intel Xeon Silver 4114 2,2 G                    |
| Geheugen              | 128 GB RAM (8x 16 GB RDIMM)                 |

## <a name="fpga-specifications"></a>FPGA-specificaties

Een veld Programmeer bare gate array (FPGA) is opgenomen op elk Azure Stack Edge Pro-apparaat dat Machine Learning (ML) scenario's mogelijk maakt.

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Beschik bare DNN-modellen (diepe Neural Network) zijn hetzelfde als die [van Cloud FPGA-exemplaren](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Specificaties van voedings eenheid voor voeding

Het Azure Stack Edge Pro-apparaat heeft twee 100-240 V-energievoedings eenheden (PSUs) met hoge prestaties. De twee PSUs bieden een redundante energie configuratie. Als een PSU mislukt, blijft het apparaat normaal op de andere PSU functioneren totdat de module failed wordt vervangen. De volgende tabel geeft een lijst van de technische specificaties van de PSUs.

| Specificatie           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximale uitvoer kracht    | 750 W                     |
| Frequentie               | 50/60 Hz                   |
| Selectie van voltage bereik | Automatisch variërend: 100-240 V AC |
| Hot pluggable           | Ja                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Specificaties van Azure Stack Edge Pro-stroom kabel per regio

Uw Azure Stack Edge Pro-apparaat moet een stroom kabel hebben dat afhankelijk is van uw Azure-regio.
Zie [Azure stack Edge Pro Power koord-specificaties per regio](azure-stack-edge-technical-specifications-power-cords-regional.md)voor technische specificaties van alle ondersteunde stroom kabels.

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Specificaties van netwerk interface

Uw Azure Stack Edge Pro-apparaat heeft zes netwerk interfaces, PORT1-PORT6.

| Specificatie           | Beschrijving                 |
|-------------------------|----------------------------|
|  Netwerkinterfaces    | 2 X 1 GbE-interfaces: Eén beheerinterface, niet configureerbaar door gebruikers, gebruikt voor eerste installatie. De andere gegevensinterface kan door gebruikers worden geconfigureerd, kan worden gebruikt voor gegevensoverdracht en is standaard DHCP. <br>2 X 25 GbE-interfaces: Deze kunnen ook als 10 GbE-interfaces werken. Deze gegevensinterfaces kunnen door gebruikers worden geconfigureerd als DHCP (standaard) of statisch. <br> 2 X 25 GbE-interfaces: Deze gegevensinterfaces kunnen door gebruikers worden geconfigureerd als DHCP (standaard) of statisch.                  |

De gebruikte netwerk adapters zijn: 

| Specificatie           | Beschrijving                 |
|-------------------------|----------------------------|
|Kaart voor netwerk dochter (rNDC) |QLogic FastLinQ 41264 Dual Port 25GbE SFP +, Dual Port 1GbE, rNDC|
|PCI-netwerk adapter |QLogic FastLinQ 41262 zwei poorten 25Gbit/s SFP28-adapter|

Raadpleeg de HCL-lijst van Intel QLogic voor compatibele Gigabit-Interface Converter (GBIC). Gigabit Interface Converter (GBIC) is niet inbegrepen bij de levering van Azure Stack Edge. 

## <a name="storage-specifications"></a>Opslag specificaties

De Azure Stack Edge Pro-apparaten hebben 9 X 2,5 ' NVMe Ssd's, elk met een capaciteit van 1,6 TB. Van deze Ssd's, 1 is een schijf van het besturings systeem en de andere 8 zijn gegevens schijven. De totale bruikbare capaciteit van het apparaat is ongeveer 12,5 TB. De volgende tabel bevat de Details voor de opslag capaciteit van het apparaat.

|     Specificatie                          |     Waarde             |
|--------------------------------------------|-----------------------|
|    Aantal Solid-state drives (Ssd's)     |    8                  |
|    Capaciteit van één SSD                     |    1,6 TB             |
|    Totale capaciteit                          |    12,8 TB            |
|    Totale bruikbare capaciteit *                  |    ~ 12,5 TB            |

**Er is ruimte gereserveerd voor intern gebruik.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen van behuizing en gewichts specificaties

In de volgende tabellen staan de verschillende specificaties van de behuizing voor dimensies en gewicht.

### <a name="enclosure-dimensions"></a>Afmetingen van behuizing

De volgende tabel bevat de afmetingen van de behuizing in millimeters en inches.

|     Sluit     |     Millimeters     |     Mm     |
|-------------------|---------------------|----------------|
|    Hoogte         |    44,45            |    1,75 '          |
|    Breedte          |    434,1           |    17,09 '          |
|    Lengte          |    740,4           |    29,15 '          |

De volgende tabel bevat de afmetingen van het verzend pakket in millimeters en inches.

|     Pakket     |     Millimeters     |     Mm     |
|-------------------|---------------------|----------------|
|    Hoogte         |    311,2            |    12,25 '          |
|    Breedte          |    642,8          |    25,31 '          |
|    Lengte          |   1.051,1          |    41,38 '          |

### <a name="enclosure-weight"></a>Gewicht van behuizing

Het apparaatstuurprogrammapakket weegt 61 lbs. en er zijn twee personen nodig om deze af te handelen. Het gewicht van het apparaat is afhankelijk van de configuratie van de behuizing.

|     Sluit                                 |     Gewicht          |
|-----------------------------------------------|---------------------|
|    Totaal gewicht inclusief de verpakking       |    61 lbs.          |
|    Gewicht van het apparaat                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Specificaties van behuizing-omgeving

In deze sectie vindt u de specificaties met betrekking tot de behuizing-omgeving, zoals Tempe ratuur, vochtigheid en hoogte.

### <a name="temperature-and-humidity"></a>Tempe ratuur en vochtigheid

|     Sluit         |     Omgevings temperatuur bereik     |     Relatieve lucht vochtigheid     |     Maximum Dew-punt     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operationeel        |    10 °C-35 °C (50 °F-86 °F)         |    10%-80% niet-condenserend.         |    29 °C (84 °F)            |
|    Niet-operationeel    |    -40 °C tot 65 °C (-40 °F-149 °F)     |    5%-95% niet-condenserend.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Lucht stroom, hoogte, schokken, trillingen, oriëntatie, veiligheid en EMC

|     Sluit                           |     Operationele specificaties                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Lucht stroom                              |    De stroom van het systeem is aan de achterzijde. Het systeem moet worden bediend met een installatie met een lage druk, een achterlopende uitlaat. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximum hoogte, operationeel        |    3048 meters (10.000 meter) met een maximale gebruiks temperatuur van de nominale waarde bepaald door [de specificaties van de gebruiks temperatuur van de beoordeling](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximale hoogte, niet-operationeel    |    12.000 meters (39.370 meter)                                                                                                                                                                                         |
|    Schokken, operationeel                   |    6 G voor 11 milliseconden in 6 standen                                                                                                                                                                         |
|    Schokken, niet-operationeel               |    71 G voor 2 milliseconden in 6 standen                                                                                                                                                                           |
|    Trillingen, operationeel               |    0,26 G<sub>RMS</sub> 5 hz tot 350 Hz wille keurig                                                                                                                                                                                     |
|    Trillingen, niet-operationeel           |    1,88 G<sub>RMS</sub> 10 hz tot 500 Hz gedurende 15 minuten (alle zes zijden zijn getest)                                                                                                                                                  |
|    Afdruk stand en koppelen             |    19-rack koppeling                                                                                                                                                                                        |
|    Veiligheid en goed keuringen                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    |                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (klasse D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energie             |    Verordening (EU) van de Commissie. 617/2013                                                                                                                                                                                        |
|    BvGS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specificaties voor de gebruiks temperatuur van de beoordeling

|     De beoordeling van de gebruiks temperatuur     |     Omgevings temperatuur bereik                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Maxi maal 35 °C (95 °F)                       |    De maximale Tempe ratuur wordt verlaagd met 1 °C/300 m (1 °F/547 ft) boven 950 m (3.117 ft).    |
|    35 °C tot 40 °C (95 °F tot 104 °F)            |    De maximale Tempe ratuur wordt verlaagd met 1 °C/175 m (1 °F/319 ft) van meer dan 950 m (3.117 ft).    |
|    40 °C tot 45 °C (104 °F tot 113 °F)           |    De maximale Tempe ratuur wordt verlaagd met 1 °C/125 m (1 °F/228 ft) van meer dan 950 m (3.117 ft).    |

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Stack Edge Pro implementeren](azure-stack-edge-deploy-prep.md)