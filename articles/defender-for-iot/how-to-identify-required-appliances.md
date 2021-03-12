---
title: Vereiste apparaten identificeren
description: Meer informatie over hardware en virtuele apparaten voor Certified Defender voor IoT Sens oren en de on-premises beheer console.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2ad5bf08542cd98f7acae36827b1a7b284a893b0
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149292"
---
# <a name="identify-required-appliances"></a>Vereiste apparaten identificeren

Dit artikel bevat informatie over gecertificeerde Defender voor IoT-sensor toestellen. Defender Fort IoT kan worden geïmplementeerd op fysieke en virtuele apparaten.

Dit omvat gecertificeerde *vooraf geconfigureerde* apparaten, waarop software al is geïnstalleerd, evenals niet-geconfigureerde gecertificeerde apparaten waarop u de vereiste software kunt downloaden en installeren.

Het artikel bevat ook specificaties voor een on-premises beheer console-apparaat. De on-premises beheer console is niet beschikbaar als een vooraf geconfigureerd apparaat.

- Als u een vooraf geconfigureerde sensor wilt kopen, controleert u de modellen die beschikbaar zijn in de sectie [sensor toestellen](#sensor-appliances) en gaat u verder met de aankoop.

- Als u uw eigen apparaat wilt aanschaffen, bekijkt u de modellen die beschikbaar zijn in de sectie [sensor toestellen](#sensor-appliances) en in de sectie [aanvullende gecertificeerde apparaten](#additional-certified-appliances) . Nadat u het apparaat hebt aangeschaft, kunt u de software downloaden en installeren.

- Als u de on-premises beheer console wilt kopen, raadpleegt u de informatie in de sectie [apparaat van de on-premises beheer console](#on-premises-management-console-appliance) . Nadat u het apparaat hebt aangeschaft, kunt u de software downloaden en installeren.

Nadat u de taken hier hebt voltooid, kunt u de software installeren en uw netwerk instellen.

## <a name="sensor-appliances"></a>Sensor toestellen

Defender voor IoT ondersteunt zowel fysieke als virtuele implementaties.

### <a name="physical-sensors"></a>Fysieke Sens oren

Deze sectie bevat een overzicht van fysieke sensor modellen die beschikbaar zijn. U kunt Sens oren aanschaffen met vooraf geconfigureerde software of Sens oren die niet vooraf zijn geconfigureerd.

| Implementatie type | Bedrijf | Enterprise | SMB-rack koppeling| SMB is robuust|
|--|--|--|--|--|
| Installatiekopie | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Het model op bedrijfs niveau."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Het model op ondernemings niveau."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Het model op SMB-niveau."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="Het SMB-robuust niveau model."::: |
| Model | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| Bewakings poorten | Maxi maal 15 RJ45 of 8 OPT | Maxi maal 8 RJ45 of 6 OPT | 4 RJ45 | Maxi maal 5 |
| Maximale band breedte [1](#anchortext) | 3 GB/sec. | 1 GB/sec. | 200 MB/sec. | 100 MB/sec. |
| Maxi maal beveiligde apparaten | 30.000 | 15.000 | 1000 | 800 |

Zie [specificaties](#appliance-specifications) van het apparaat voor details van de leverancier.

Over vooraf geconfigureerde Sens oren: micro soft heeft een partnerschap met pijl om vooraf geconfigureerde Sens oren te bieden. Als u een vooraf geconfigureerde sensor wilt kopen, moet u contact opnemen met de pijl op het volgende adres: <hardware.sales@arrow.com>

Over uw eigen apparaat halen: Bekijk de ondersteunde modellen die hier worden beschreven. Nadat u uw apparaat hebt aangeschaft, gaat u naar de ISO-installatie **van Defender voor IOT**-  >  **netwerk Sens oren**  >   om de software te downloaden.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Netwerk sensors ISO.":::

<a id="anchortext">1</a> bandbreedte capaciteit kan variëren, afhankelijk van de distributie van protocollen.

### <a name="virtual-sensors"></a>Virtuele Sens oren

Deze sectie bevat een overzicht van de virtuele Sens oren die beschikbaar zijn.

| Implementatie type | Bedrijf | Enterprise | SMB |
|--|--|--|--|
| Maximale band breedte | 2,5 GB/sec. | 800 MB/sec. | 160 MB/sec. |
| Maxi maal beveiligde apparaten | 30.000 | 10.000 | 2500 |

## <a name="on-premises-management-console-appliance"></a>On-premises beheer console-apparaat

De beheer console is beschikbaar als een virtuele implementatie.

| Implementatie type | Enterprise |
|--|--|
| Type apparaat | HPE DL20, VM |
| Aantal beheerde Sens oren | Maxi maal 300 |

Nadat u een on-premises beheer console hebt aangeschaft, gaat u naar de ISO-installatie van **Defender voor IOT**  >  **on-premises Management Console**  >   om de ISO te downloaden.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="On-premises beheer console.":::

## <a name="appliance-specifications"></a>Specificaties van apparaat

In deze sectie worden de hardwarespecificaties voor de volgende apparaten beschreven:

- Bedrijfs implementatie: HPE ProLiant DL360

- Enter prise-implementatie: HPE ProLiant DL20

- SMB-implementatie: HPE ProLiant DL20

- Specificaties van virtuele apparaten

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Bedrijfs implementatie: HPE ProLiant DL360

| Onderdeel | Technische specificaties |
|--|--|
| Chassis | 1U-rack server |
| Dimensies | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(in) |
| Gewicht | Maxi maal 16,27 kg (35,86 lb) |
| Processor | Intel Xeon Silver 4215 R 3,2 GHz, 11 min. cache, 8c/16T, 130 W |
| Chipset | Intel C621 |
| Geheugen | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 6 x 1,2-TB SAS 12G Enter prise 10K (2,5) in Hot-Plug harde schijf-RAID 5 |
| Netwerk controller | On-Board: 2 x 1 GB Broadcom BCM5720<br>On-board LOM: iDRAC-poort kaart 1-GB Broadcom BCM5720<br><br>Extern: 1 x Intel Ethernet I350 QP 1-GB server adapter, laag profiel |
| Beheer | HPE iLO Advanced |
| Apparaattoegang | Twee achteraan USB 3,0<br>Eén front USB-2,0<br>Eén interne USB 3,0 |
| Stroom | 2 x HPE 500 W Flex-sleuf Platinum hot-pluggable laag Halo-Power Supply Kit |
| Rack ondersteuning | HPE 1U Gen10 SFF-eenvoudige installatie Rail Kit |

### <a name="appliance-bom"></a>Apparaat-stuk lijst

| PN | Beschrijving | Aantal |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO-server | 1 |
| P19766-B21 | Europa-meertalige lokalisatie | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO Kit voor DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R Kit voor DL360 Gen10 | 1 |
| P00922-B21 | HPE 16 GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479-B21 | HPE 1,2-TB SAS 10K SFF/DS-HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350-adapter | 1 |
| P02377-B21 | HPE Smart Hybrid condensator w \_ 145 mm-kabel | 1 |
| 804331-B21 | HPE Smart Array P408i-een SR Gen10-controller | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350-adapter | 1 |
| 871244-B21 | HPE DL360 Gen10 High Performance ventilator Kit | 1 |
| 865408-B21 | HPE 500-W FS-invoeg toepassing voor hot-pluggable LH-Power Supply-Kit | 2 |
| 512485-B21 | HPE iLO adv 1-server licentie 1 jaar ondersteuning | 1 |
| 874543-B21 | HPE 1U Gen10 SFF-eenvoudige installatie Rail Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Enter prise-implementatie: HPE ProLiant DL20

| Onderdeel | Technische specificaties |
|--|--|
| Chassis | 1U-rack server |
| Afmetingen (hoogte x breedte x diepte) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 inch |
| Gewicht | 7,9 kg/17.41 lb |
| Processor | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Chipset | Intel C242 |
| Geheugen | 2 x 16 GB, Dual Rank X8-DDR4-2666 |
| Storage | 3 x 1 TB SATA 6G middenlijn 7,2 K SFF (2,5) – RAID 5 met Smart Matrix P408i-een SR-controller |
| Netwerk controller | On-Board: 2 x 1 GB <br>On-Board: iLO-poort kaart 1 GB <br>Extern: 1 x HPE Ethernet 1-GB 4-Port 366FLR-adapter |
| Beheer | HPE iLO Advanced |
| Apparaattoegang | Voor zijde: 1 x USB 3,0, 1 x USB iLO-service poort <br>Achterzijde: 2 x USB 3,0 <br>Intern: 1 x USB 3,0 |
| Stroom | Dubbele hot-pluggable voedings eenheden 500 W |
| Rack ondersteuning | De korte wrijvings Rail Kit voor HPE 1U |

### <a name="appliance-bom"></a>Apparaat-stuk lijst

| PN | Beschrijving: high end | Aantal |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO-server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO-server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO Kit | 1 |
| 879507-B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710-B21 | HPE 1-TB SATA 7,2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM-uitbreidings kaart kit | 1 |
| 665240-B21 | HPE Ethernet 1-GB 4-poort 366FLR-adapter | 1 |
| 782961-B21 | HPE 12-W Smart Storage-batterij | 1 |
| 869081-B21 | HPE Smart Array P408i-een SR-G10 LH-controller | 1 |
| 865408-B21 | HPE 500-W FS-invoeg toepassing voor hot-pluggable LH-Power Supply-Kit | 2 |
| 512485-B21 | HPE iLO adv 1-server licentie 1 jaar ondersteuning | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS-activering FIO Kit | 1 |
| 775612-B21 | De korte wrijvings Rail Kit voor HPE 1U | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB-implementatie: HPE ProLiant DL20

| Onderdeel | Technische specificaties |
|--|--|
| Chassis | 1U-rack server |
| Afmetingen (hoogte x breedte x diepte) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 inch |
| Gewicht | 7,88 kg/17.37 lb |
| Processor | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Chipset | Intel C242 |
| Geheugen | 1 x 8-GB Dual Rank X8-DDR4-2666 |
| Storage | 2 x 1 TB SATA 6G middenlijn 7,2 K SFF (2,5) – RAID 1 met Smart Matrix P208i-a |
| Netwerk controller | On-Board: 2 x 1 GB <br>On-Board: iLO-poort kaart 1 GB <br>Extern: 1 x HPE Ethernet 1-GB 4-Port 366FLR-adapter |
| Beheer | HPE iLO Advanced |
| Apparaattoegang | Voor zijde: 1 x USB 3,0, 1 x USB iLO-service poort <br>Achterzijde: 2 x USB 3,0 <br>Intern: 1 x USB 3,0 |
| Stroom | Hot-pluggable voeding 290 W |
| Rack ondersteuning | De korte wrijvings Rail Kit voor HPE 1U |

### <a name="appliance-bom"></a>Apparaat-stuk lijst

| PN | Beschrijving | Aantal |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO-server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO-server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO Kit | 1 |
| 879505-B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882-B21 | HPE 1-TB SATA 7,2 K LFF RW-HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM-uitbreidings kaart kit | 1 |
| 665240-B21 | HPE Ethernet 1-GB 4-poort 366FLR-adapter | 1 |
| 869079-B21 | HPE Smart Array E208i-een SR-G10 LH-controller | 1 |
| P21649-B21 | HPE DL20 Gen10-uitplater 290 W FIO PSU Kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC-kabelkit | 1 |
| 512485-B21 | HPE iLO adv 1-server licentie 1 jaar ondersteuning | 1 |
| 775612-B21 | De korte wrijvings Rail Kit voor HPE 1U | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>SMB-robuust: HPE Edgeline EL300

| Onderdeel | Technische specificaties |
|--|--|
| Bouw | Aluminium, fanless & stof-proef ontwerpen |
| Afmetingen (hoogte x breedte x diepte) | 200.5 mm (7,9 ") hoog, 232mm (9,14") breed op 100mm (3,9 ") diep |
| Gewicht | 4,91 KG (10,83 lbs) |
| CPU | Intel Core i7-8650U (1,9 GHz/4-core/15W) |
| Chipset | Hub Intel® Q170 platform controller |
| Geheugen | 8 GB DDR4 2133MHz Wide Tempe ratuur SODIMM |
| Storage | 128 GB 3ME3-mSATA SSD (Wide Tempe ratuur) |
| Netwerk controller | 6x Gigabit Ethernet-poorten door Intel® I219 |
| Apparaattoegang  | 4 USBs: 2 voor zijde; 2 achterzijde; 1 intern |
| Stroom adapter | 250V/10 BIS |
| Muur | Montagekit, DIN-rail |
| Bedrijfs temperatuur | 0C naar + 70C  |
| Vochtigheid | 10% ~ 90%, niet-condenserend |
| Trill | 0,3 Grms 10Hz tot 300Hz, 15 minuten per as-DIN-rail   |
| Puls | 10G 10 MS, halve sinus, drie voor elke as. (Zowel positieve & negatief Pulse) – DIN-rail |

### <a name="appliance-bom"></a>Apparaat-stuk lijst
| Product | Beschrijving |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 geconvergeerd Edge-systeem |
| P25828-B21 B19 | HPE EL300 v2-systeem met geconvergeerde rand |
| P25833-B21 | Intel Core i7-8650U (1,9 GHz/4-core/15W) FIO Basic processor Kit for HPE Edgeline EL300 |
| P09176-B21 | HPE Edgeline 8 GB (1x8GB) Dual Rank X8 DDR4-2666 SODIMM WT CAS-19-19-19 geregistreerde geheugen FIO Kit |
| P09188-B21 | HPE Edgeline 256GB SATA 6G Lees intensieve M. 2 2242 3 jaar Wty volledige tijdelijke SSD |
| P04054-B21 | HPE Edgeline EL300 SFF-naar-M. 2-activerings pakket |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO overdracht Board |
| P08641-B21 | HPE Edgeline EL300 80W 12VDC-voeding |
| AF564A | HPE C13-SI-32 IL 250V 10Amp 1.83 m stroom kabel |
| P25835-B21 | HPE EL300 v2 FIO-Carrier Board |
| R1P49AAE | HPE EL300 iSM adv 3 jaar 24x7 Sup_Upd E-LTU |
| P08018-B21 optioneel | HPE Edgeline EL300-pakket met laag profiel haakje  |
| P08019-B21 optioneel | HPE Edgeline EL300 DIN Rail Mount-Kit |
| P08020-B21 optioneel | HPE Edgeline EL300 Wall Mount-Kit |
| P03456-B21 optioneel | HPE Edgeline 1GbE 4-Port TSN FIO dochter kaart |

## <a name="virtual-appliance-specifications"></a>Specificaties van virtuele apparaten

### <a name="sensors"></a>Oren

| Type | Bedrijf | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Geheugen | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>On-premises beheer console-apparaat

| Type | Enterprise |
|--|--|
| Beschrijving | Virtuele apparaten voor Enter prise-implementatie typen |
| vCPU | 8 |
| Geheugen | 32 GB |
| Storage | 1,8 TB |

Ondersteunde Hyper visors: VMware ESXi versie 5,0 en hoger, Hyper-V

## <a name="additional-certified-appliances"></a>Aanvullende gecertificeerde apparaten

In deze sectie vindt u meer informatie over de apparaten die door micro soft zijn gecertificeerd, maar die niet worden aangeboden als vooraf geconfigureerde apparaten.

| Implementatie type | Enterprise |
|--|--|
| Installatiekopie | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Enter prise-implementatie type."::: |
| Model | Dell PowerEdge R340 XL |
| Bewakings poorten | Maxi maal negen RJ45 of zes OPT |
| Maximale band breedte [1](#anchortext2)| 1 GB/sec. |
| Maxi maal aantal beveiligde apparaten | 10.000 |

<a id="anchortext2">Een</a> De capaciteit van de band breedte kan variëren, afhankelijk van de distributie van het protocol.

Nadat u het apparaat hebt aangeschaft, gaat u naar de ISO-installatie **van Defender voor IOT**-  >  **netwerk Sens oren**  >   om de software te downloaden.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Netwerk sensors ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Enter prise-implementatie: Dell PowerEdge R340 XL

| Onderdeel | Technische specificaties |
|--|--|
| Chassis | 1U-rack server
| Dimensies | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(in) |
| Gewicht | Maxi maal 29,98 lb/13,6 kg |
| Processor | Intel Xeon E-2144G 3,6 GHz, 8 min. cache, 4C/8T, Turbo (71 W) |
| Chipset | Intel C246 |
| Geheugen | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 3 x 2 TB 7,2 K RPM SATA 6-Gbps 512n 3,5-in Hot-Plug harde schijf-RAID 5 |
| Netwerk controller | On-Board: 2 x 1 GB Broadcom BCM5720<br>On-board LOM: iDRAC-poort kaart 1-GB Broadcom BCM5720 <br><br>Extern: 1 x Intel Ethernet I350 QP 1-GB server adapter, laag profiel |
| Beheer | iDRAC negen onderneming |
| Apparaattoegang | Twee achteraan USB 3,0 <br> Eén front USB-3,0 |
| Stroom | Dubbele hot-pluggable voedings eenheden 350 W |
| Rack ondersteuning | ReadyRails II-schuif rails voor een gereedschap-less-aansluiting in 4 post-racks met vier Kante of niet-gethreadde ronde gaten of een koppeling naar een koppeling in 4 post-verwerkings rekken, met ondersteuning voor optionele arm met een beperkt beheer van de kabel. |

## <a name="dell-r340-bom"></a>Dell R340-stuk lijst

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340-stuk lijst.":::

## <a name="next-steps"></a>Volgende stappen

[Over de installatie van Azure Defender voor IoT](how-to-install-software.md)

[Over de installatie van het Azure Defender for IoT-netwerk](how-to-set-up-your-network.md)

