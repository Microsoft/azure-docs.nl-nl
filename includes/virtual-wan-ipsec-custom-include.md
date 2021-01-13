---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147586"
---
Houd bij het werken met aangepaste IPsec-beleids regels rekening met de volgende vereisten:

* **IKE** -voor IKE kunt u een wille keurige para meter uit IKE-versleuteling selecteren, plus alle para meters van IKE-integriteit, plus alle para meters uit de DH-groep.
* **IPSec** : voor IPSec kunt u een wille keurige para meter uit IPSec-versleuteling selecteren, plus alle para meters van IPSec-integriteit, plus PFS. Als een van de para meters voor IPsec-versleuteling of IPsec-integriteit GCM is, moeten de para meters voor beide instellingen GCM zijn.

>[!NOTE]
> Met aangepaste IPsec-beleids regels is er geen responder en initiator (in tegens telling tot standaard IPsec-beleid). Beide zijden (on-premises en Azure VPN-gateway) gebruiken dezelfde instellingen voor IKE fase 1 en IKE fase 2. Zowel IKEv1-als IKEv2-protocollen worden ondersteund. Er is alleen ondersteuning voor Azure als een responder.
>

**Beschik bare instellingen en para meters**

| Instelling | Parameters |
|--- |--- |
| IKE-versleuteling | GCMAES256, GCMAES128, AES256, AES128 |
| IKE-integriteit | SHA384, SHA256 |
| DH-groep | ECP384, ECP256, DHGroup24, DHGroup14 |
| IPsec-versleuteling | GCMAES256, GCMAES128, AES256, AES128, geen |
| IPsec-integriteit | GCMAES256, GCMAES128, SHA256 |
| PFS-groep | ECP384, ECP256, PFS24, PFS14, geen |
| SA-levensduur |geheeltallige min. 300/standaard 27000 seconden |
