---
title: Technische specificaties en naleving van Microsoft Azure Stack Edge-mini-R | Microsoft Docs
description: Meer informatie over de technische specificaties en naleving voor uw Azure Stack Edge mini maal R-apparaat
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727478"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Technische specificaties voor de Azure Stack Edge-mini

De hardwareonderdelen van uw Microsoft Azure Stack Edge mini-R-apparaat voldoen aan de technische specificaties en regelgevings normen als beschreven in dit artikel. In de technische specificaties worden de CPU, het geheugen, de energie-eenheden (PSUs), de opslag capaciteit, de grootte van de behuizing en het gewicht beschreven.


## <a name="compute-memory-specifications"></a>Compute, geheugen specificaties

Het Azure Stack Edge mini-R-apparaat heeft de volgende specificaties voor Compute en geheugen:

| Specificatie           | Waarde                  |
|-------------------------|------------------------|
| CPU    | 16-core CPU, Intel Xeon-D 1577 |
| Geheugen              | 48 GB RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Specificaties van Compute Acceleration

Een Vision processing unit (VPU) is opgenomen op elke Azure Stack Edge mini-R-apparaat waarmee Kubernetes-, diepe Neural-netwerk-en computer vision-toepassingen kunnen worden uitgevoerd.

| Specificatie           | Waarde                  |
|-------------------------|------------------------|
| Kaart voor Compute-versnelling         | Intel Movidius talloze X VPU <br> Zie [Intel Movidius talloze X VPU](https://www.movidius.com/MyriadX) voor meer informatie. |


## <a name="storage-specifications"></a>Opslag specificaties

De Azure Stack Edge mini-R-apparaat heeft 1 gegevens schijf en 1 opstart schijf (die fungeert als opslag voor het besturings systeem). De volgende tabel bevat de Details voor de opslag capaciteit van het apparaat.

|     Specificatie                          |     Waarde             |
|--------------------------------------------|-----------------------|
|    Aantal Solid-state drives (Ssd's)     |    2 X 1 TB schijven <br> Eén gegevens schijf en één opstart schijf                  |
|    Capaciteit van één SSD                     |    1 TB               |
|    Totale capaciteit (alleen gegevens)              |    1 TB              |
|    Totale bruikbare capaciteit *                  |    ~ 750 GB        |

**Er is ruimte gereserveerd voor intern gebruik.*

## <a name="network-specifications"></a>Netwerk specificaties

Het Azure Stack Edge mini-R-apparaat heeft de volgende specificaties voor het netwerk:


|Specificatie  |Waarde  |
|---------|---------|
|Netwerkinterfaces    |2 x 10 GbE SFP + <br> Weer gegeven als poort 3 en poort 4 in de lokale gebruikers interface           |
|Netwerkinterfaces    |2 x 1 GbE RJ45 <br> Weer gegeven als poort 1 en poort 2 in de lokale gebruikers interface          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Specificaties van voedings eenheid voor voeding

Het Azure Stack Edge mini-R-apparaat bevat een externe 85 W wisselstroom adapter om energie te leveren en de onboard batterij te laden.

De volgende tabel bevat de specificaties voor de voedings eenheid van Power Supply:

| Specificatie           | Waarde                      |
|-------------------------|----------------------------|
| Maximale uitvoer kracht    | 85 W                       |
| Frequentie               | 50/60 Hz                   |
| Selectie van voltage bereik | Automatisch variërend: 100-240 V AC |



## <a name="included-battery"></a>Batterij inbegrepen

Het Azure Stack Edge mini-R-apparaat bevat ook een onboard-batterij die door de voeding in rekening wordt gebracht.

Een extra [batterij van het Type 2590](https://www.bren-tronics.com/bt-70791ck.html) kan worden gebruikt in combi natie met de onboard-batterij om het gebruik van het apparaat tussen de kosten uit te breiden. Deze batterij moet voldoen aan alle veiligheids-, vervoers-en milieu voorschriften die van toepassing zijn in het land van gebruik.


| Specificatie           | Waarde                      |
|-------------------------|----------------------------|
| Batterij capaciteit onboarding | 73 w/h                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen van behuizing en gewichts specificaties

In de volgende tabellen staan de verschillende specificaties van de behuizing voor dimensies en gewicht.

### <a name="enclosure-dimensions"></a>Afmetingen van behuizing

De volgende tabel bevat de afmetingen van het apparaat en de USP met het robuuste geval in millimeters en inches.

|     Sluit     |     Millimeters     |     Mm     |
|-------------------|---------------------|----------------|
|    Hoogte         |    68            |    2,68          |
|    Breedte          |    208          |      8,19          |
|    Lengte          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Gewicht van behuizing

In de volgende tabel wordt het gewicht van het apparaat weer gegeven, inclusief de accu.

|     Sluit                                 |     Gewicht          |
|-----------------------------------------------|---------------------|
|    Totaal gewicht van het apparaat     |    7 lbs.          |

## <a name="enclosure-environment-specifications"></a>Specificaties van behuizing-omgeving


In deze sectie vindt u de specificaties met betrekking tot de behuizing-omgeving, zoals Tempe ratuur, vochtigheid en hoogte.


|     Specificaties             |     Beschrijving                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Temperatuur bereik          |     0:43 ° C (operationeel)                                              |
|     Trill                  |     MIL-STD-810 methode 514,7 *<br> Procedure ik kat 4, 20                  |
|     Puls                      |     MIL-STD-810 methode 516,7 *<br> Procedure IV, logistiek                 |
|     Hoogte                   |     Operationeel: 10.000 meter<br> Niet-operationeel: 40.000 meter          |

**Alle verwijzingen naar MIL-STD-810G wijzigen 1 (2014)*


## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Stack Edge implementeren mini maal R](azure-stack-edge-placeholder.md)
