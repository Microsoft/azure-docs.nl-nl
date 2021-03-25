---
title: Technische specificaties en naleving van Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Meer informatie over de technische specificaties en naleving voor uw Azure Stack Edge Pro R-apparaat
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: aa1b861555cff65c9e432ea711af3f7c6e410625
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109162"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Technische specificaties van Azure Stack Edge Pro R

De hardwareonderdelen van uw Azure Stack Edge Pro R-apparaat voldoen aan de technische specificaties die in dit artikel worden beschreven. De technische specificaties beschrijven de voedings eenheden (PSUs), opslag capaciteit, behuizingen en omgevings standaarden.


## <a name="compute-memory-specifications"></a>Compute, geheugen specificaties

Het Azure Stack Edge Pro R-apparaat heeft de volgende specificaties voor Compute en geheugen:

| Specificatie       | Waarde                  |
|---------------------|------------------------|
| CPU    | 2 X Intel Xeon Silver 4114 CPU<br>20 phsyical-kernen (10 per CPU)<br>40 logische kernen (Vcpu's) (20 per CPU)  |
| Geheugen              | 256 GB RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Specificaties van Compute Acceleration

Er is een GPU (graphics processing unit) opgenomen op elk apparaat dat Kubernetes, dieper leren en machine learning scenario's mogelijk maakt.

| Specificatie           | Waarde                  |
|-------------------------|----------------------------|
| GPU   | Eén nVidia T4 GPU <br> Zie [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)voor meer informatie.| 

## <a name="power-supply-unit-specifications"></a>Specificaties van voedings eenheid voor voeding

Het Azure Stack Edge Pro R-apparaat heeft twee 100-240 V-energievoedings eenheden (PSUs) met hoge prestaties. De twee PSUs bieden een redundante energie configuratie. Als een PSU mislukt, blijft het apparaat normaal op de andere PSU functioneren totdat de module failed wordt vervangen. De volgende tabel geeft een lijst van de technische specificaties van de PSUs.

| Specificatie           | 550 W PSU                  |
|-------------------------|----------------------------|
| Maximale uitvoer kracht    | 550 W                      |
| Warmte-afbraak (maximum)                   | 2891 BTU/uur                |
| Frequentie               | 50/60 Hz                   |
| Selectie van voltage bereik | Automatisch variërend: 115-230 V AC |
| Hot pluggable           | Ja                        |

## <a name="network-specifications"></a>Netwerk specificaties

Het Azure Stack Edge Pro R-apparaat heeft vier netwerk interfaces, PORT1-PORT4. 


|Specificatie  |Beschrijving                              |
|----------------------|----------------------------------|
|Netwerkinterfaces    |**2 x 1 GbE RJ45** <br> POORT 1 wordt gebruikt als beheer interface voor initiële installatie en is standaard statisch. Nadat de eerste installatie is voltooid, kunt u de interface gebruiken voor gegevens met elk IP-adres. Bij het opnieuw instellen wordt de interface echter teruggezet naar het statische IP-adres. <br>De andere interface poort 2 kan door de gebruiker worden geconfigureerd, kan worden gebruikt voor gegevens overdracht en is standaard DHCP.     |
|Netwerkinterfaces    |**2 x 25 GbE SFP28** <br> Deze gegevens interfaces poort 3 en poort 4 kunnen worden geconfigureerd als DHCP (standaard) of statisch.            |

Uw Azure Stack Edge Pro R-apparaat heeft de volgende netwerkhardware:

* **Mellanox Dual Port 25G connectx-4-kanaal netwerk adapter** -poort 3 en poort 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Voor een volledige lijst met ondersteunde kabels, switches en transceivers voor deze netwerk kaarten gaat u naar: [Mellanox Dual Port 25G connectx-4 kanaal netwerk adapter compatibele producten](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Opslag specificaties

De Azure Stack Edge Pro R-apparaten hebben 8 gegevens schijven en 2 M. 2 SATA-schijven die fungeren als besturingssysteem schijven. Ga voor meer informatie naar [M. 2 SATA-schijven](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Opslag voor een apparaat met één knoop punt

De volgende tabel bevat de Details voor de opslag capaciteit van het apparaat met één knoop punt.

|     Specificatie                          |     Waarde             |
|--------------------------------------------|-----------------------|
|    Aantal Solid-state drives (Ssd's)     |    8                  |
|    Capaciteit van één SSD                     |    8 TB               |
|    Totale capaciteit                          |    64 TB              |
|    Totale bruikbare capaciteit *                  |    ~ 42 TB          |

**Er is ruimte gereserveerd voor intern gebruik.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Afmetingen van behuizing en gewichts specificaties

In de volgende tabellen staan de verschillende specificaties van de behuizing voor dimensies en gewicht.

### <a name="enclosure-dimensions"></a>Afmetingen van behuizing 

De volgende tabel bevat de afmetingen van het apparaat en de nood voeding met het robuuste geval in millimeters en inches.

|     Sluit     |     Millimeters     |     Mm     |
|-------------------|---------------------|----------------|
|    Hoogte         |    301,2            |    11,86       |
|    Breedte          |    604,5            |    23,80       |
|    Lengte         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Gewicht van behuizing 

Het gewicht van het apparaat is afhankelijk van de configuratie van de behuizing.

|     Sluit                                 |     Gewicht          |
|-----------------------------------------------|---------------------|
|    Totaal gewicht van het apparaat met 1 knoop punt + robuuste behuizing met eind kapitalen     |    ~ 114 lbs.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Specificaties van behuizing-omgeving

In deze sectie vindt u de specificaties met betrekking tot de behuizing-omgeving, zoals de Tempe ratuur, trilling, schok en hoogte.


|     Specificatie              |     Waarde    |
|--------------------------------|-------------------------------------------------------------------|
|     Temperatuur bereik          |     0:43 ° C (operationeel)    |
|     Trill                  |     MIL-STD-810 methode 514,7 *<br>Procedure ik kat 4, 20                  |
|     Puls                      |     MIL-STD-810 methode 516,7 *<br>Procedure IV, logistiek                 |
|     Hoogte                   |     Operationeel: 10.000 meter<br>Niet-operationeel: 40.000 meter          |

**Alle verwijzingen naar MIL-STD-810G wijzigen 1 (2014)*

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Stack Edge implementeren](azure-stack-edge-placeholder.md)
