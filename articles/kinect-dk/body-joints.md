---
title: Hoofd tracerings gewrichten van Azure Kinect
description: Inzicht in het hoofd frame, de gewrichten, de gezamenlijke coördinaten en de gezamenlijke hiërarchie in azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, poort, hoofd tekst, volgen, gewricht, hiërarchie, Bone, verbinding
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277325"
---
# <a name="azure-kinect-body-tracking-joints"></a>Hoofd tracerings gewrichten van Azure Kinect

Met de Azure Kinect-hoofd tracering kunnen meerdere menselijke instanties tegelijk worden bijgehouden. Elke hoofd tekst bevat een ID voor tijdelijke correlatie tussen frames en het kinematische skelet. Het aantal gedetecteerde instanties in elk frame kan worden verkregen met behulp van `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Gewrichten

Gemeen schappelijke positie en stand zijn schattingen ten opzichte van het algemene diepte sensor referentie kader. De positie wordt opgegeven in millimeters. De afdruk stand wordt uitgedrukt in een genormaliseerde quaternion.

## <a name="joint-coordinates"></a>Gezamenlijke coördinaten

De positie en stand van elke gezamenlijke vorm van een eigen gezamenlijk coördinaten systeem. Alle gezamenlijke coördinaten systemen zijn absolute coördinaten systemen ten opzichte van het 3D-coördinaten systeem van de diepte camera.

> [!NOTE]
> Gezamenlijke coördinaten bevinden zich in de richting van de as. De richting van de as wordt veel gebruikt bij commerciële avatars, game engines en rendering software. Door gebruik te maken van de stand van de as worden gespiegelde bewegingen vereenvoudigd, bijvoorbeeld beide armen met 20 graden.

![Gezamenlijke coördinaten](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Gezamenlijke hiërarchie

Een skelet omvat 32 gewrichten met de gemeen schappelijke hiërarchie die vanuit het midden van de hoofd tekst naar de uiteinden loopt. Elke verbinding (Bone) koppelt het bovenliggende item met een onderliggend gewricht. De afbeelding illustreert de gezamenlijke locaties en de verbinding ten opzichte van de menselijke hoofd tekst.

![Gezamenlijke hiërarchie](./media/concepts/joint-hierarchy.png)

De volgende tabel geeft een opsomming van de standaard gewrichts verbindingen.

|Index |Gezamenlijke naam     | Bovenliggend gewricht   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |NEK           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | NEK           |
| 27   |NEUS           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Volgende stappen

[Indexkaart voor het volgen van een lichaam](body-index-map.md)
