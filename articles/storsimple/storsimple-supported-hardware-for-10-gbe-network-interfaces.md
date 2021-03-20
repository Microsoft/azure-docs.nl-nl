---
title: Hardware voor StorSimple 10 GbE-interfaces | Microsoft Docs
description: Hierin worden de ondersteunde kleine pluggable-transceivers (SFP), kabels en switches voor de 10 GbE-netwerk interfaces op uw StorSimple-apparaat beschreven.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954083"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Ondersteunde hardware voor de 10 GbE-netwerk interfaces op uw StorSimple-apparaat

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Dit artikel bevat informatie over aanvullende hardware die werkt met uw Microsoft Azure StorSimple-apparaat.

## <a name="list-of-devices-tested-by-microsoft"></a>Lijst met apparaten die door micro soft zijn getest
Micro soft heeft de volgende kleine SFP-transceivers, kabels en switches van de vorm factor getest, om ervoor te zorgen dat ze optimaal werken met apparaten. (De volgende tabellen worden bijgewerkt als nieuwe hardware wordt getest.)

### <a name="sfp-transceivers"></a>SFP + transceivers
| Merk | Model |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kabels
| S. Nee. | Merk | Model |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Switches
| S. Nee. | Merk | Model |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Lijst met apparaten die in het veld zijn getest
Deze sectie bevat de lijst met apparaten die zijn geïmplementeerd in het veld door klanten van StorSimple. Deze zijn niet getest door micro soft, maar werken waarschijnlijk met uw StorSimple-apparaat.

| Parameter | Waarde |
| --- | --- |
| Schakelaar maken |Juniper |
| Model overschakelen |ex4550-32F |
| Versie van besturings systeem wijzigen |JunOS 12.3 R 9,4 |
| Blade model |Poorten onboarding (PIC 0) |
| Ontvanger van transceiver |Juniper |
| Zender model |Onderdeel nummer 740-021308 <br></br> Onderdeel nummer 740-030658 |
| Versie van transceiver-firmware |Rev 01 versie 0,0 (gerapporteerd) |
| Kabel model |Duplex jumper LC/LC 50/125μ, OM3, LSZH |
| StorSimple-model |8600 |
| StorSimple-software versie |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lijst met apparaten die zijn getest door de OEM-provider (Mellanox)
Mellanox heeft de volgende kleine SFP-transceivers, kabels en switches van een vorm (geschikt) getest om ervoor te zorgen dat ze optimaal functioneren met de Mellanox-netwerk interfaces, zoals de 10 GbE-netwerk interfaces op uw StorSimple-apparaat.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabels en modules die worden ondersteund door Mellanox
De volgende tabel geeft een lijst van de kabels en modules die door Mellanox worden ondersteund. Deze zijn niet getest door micro soft, maar werken waarschijnlijk met uw StorSimple-apparaat.

| S. Nee. | Snelheid | Model | Beschrijving | Merk |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |passieve koper kabel SFP + 10 GB/s 1M |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2 MIN. |passieve koper kabel SFP + 10 GB/s 2 min. |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3 METER |passieve koper kabel SFP + 10 GB/s |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5 MIN. |passieve koper kabel SFP + 10 GB/s 5 min. |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP +-kabel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP +-kabel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP +-kabel |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP + naar SFP + 1M direct attach koper kabel |HP |
| 9. |10 GbE |455883-B21 HP BLc |10 GB SR SFP + opt |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP + opt |HP |
| 11. |10 GbE |487649-B21 HP BLc |SFP + 0,5 m 10GbE koper kabel |HP |
| 12. |10 GbE |487652-B21 HP BLc |SFP + 1M 10GbE koper kabel |HP |
| 13. |10 GbE |487655-B21 HP BLc |SFP + 3 meter 10GbE koper kabel |HP |
| 15. |10 GbE |487658-B21 HP BLc |SFP + 7 min. 10GbE koper kabel |HP |
| 15. |10 GbE |537963-B21 HP BLc |SFP + 5 min. 10GbE koper kabel |HP |
| 18. |10 GbE |AP784A HP |3-Series, met passieve kopers SFP +-kabel |HP |
| Nr. |10 GbE |AP785A HP |5 min. C-serie passieve kopers SFP +-kabel |HP |
| 18,0. |10 GbE |AP818A HP |1 a B-serie actief koper SFP + kabel |HP |
| 19. |10 GbE |AP819A HP |B-serie 3 meters actief koper SFP + kabel |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP + transceiver voor LC SR |HP |
| 20. |10 GbE |J9151A HP |X132 10G SFP + LR-transceiver |HP |
| 21. |10 GbE |J9283B HP |X242 10G SFP + SFP + 3 meter DAC-kabel |HP |
| 22. |10 GbE |J9285B HP |X242 10G SFP + SFP + 7 min. DAC-kabel |HP |
| uur. |10 GbE |JD095B HP |X240 10G SFP + SFP + 0.65 m DAC-kabel |HP |
| 1,25. |10 GbE |JD096B HP |X240 10G SFP + SFP + 1.2 m DAC-kabel |HP |
| artikel. |10 GbE |JD097B HP |X240 10G SFP + SFP + 3 meter DAD-kabel |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP naar SFP + adapter |Mellanox-technologieën |
| 28. |10 GbE |MC2309124-006 mt |Passieve koper kabel 1x SFP + naar QSFP 10Gb/s 24awg 7 min. |Mellanox-technologieën |
| artikel. |10 GbE |MC2309124-007 mt |Passieve koper kabel 1x SFP + naar QSFP 10Gb/s 24awg 7 min. |Mellanox-technologieën |
| 30. |10 GbE |MC2309130-003 mt |Passieve koper kabel 1x SFP + tot QSFP 10 GB/s 30AWG 3 meter |Mellanox-technologieën |
| uiterlijk. |10 GbE |MC2309130-00A mt |Passieve koper kabel 1x SFP + naar QSFP 10Gb/s 30AWG 0,5 m |Mellanox-technologieën |
| 32. |10 GbE |MC3309124-005 mt |Passieve koper kabel 1x SFP + 10Gb/s 24awg 5 min. |Mellanox-technologieën |
| 33. |10 GbE |MC3309124-007 mt |Passieve koper kabel 1x SFP + 10Gb/s 24awg 7 min. |Mellanox-technologieën |
| 34. |10 GbE |MC3309130-003 mt |Passieve koper kabel 1x SFP + 10 GB/s 30AWG 3 meter |Mellanox-technologieën |
| 35. |10 GbE |MC3309130-00A mt |Passieve koper kabel 1x SFP + 10Gb/s 30AWG 0,5 m |Mellanox-technologieën |

### <a name="switches-supported-by-mellanox"></a>Switches die door Mellanox worden ondersteund
De volgende tabel geeft een lijst van de switches die door Mellanox worden ondersteund. Deze zijn niet getest door micro soft, maar werken waarschijnlijk met uw StorSimple-apparaat.

| S. Nee. | Snelheid | Model | Beschrijving | Merk |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |HP ProCurve 6120XG 10GbE Ethernet-Blade Switch |HP |
| 2. |10GbE |538113-B21 |HP 10GbE Pass-Through module (PTM) |HP |
| 3. |10GbE |EN4093 |IBM PureFlex System Fabric EN4093 10 Gigabit-schaal bare switch module |IBM |
| 4. |1GbE |3020 |Cisco Catalyst 3020 1GbE switch-Blade |Cisco |
| 5. |1GbE |3020X |Cisco Catalyst 3020X 1GbE switch-Blade |Cisco |
| 6. |1GbE |438030-B21 |HP 1GbE switch-module-GbE2c Layer 2/3 Ethernet-Blade Switch |HP |
| 7. |1GbE |6120G |HP ProCurve 6120G/XG 1GbE switch Blade |HP |

## <a name="next-steps"></a>Volgende stappen
Meer [informatie over StorSimple en status](./storsimple-8000-monitor-hardware-status.md).