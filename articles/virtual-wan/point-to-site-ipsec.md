---
title: Virtueel WAN Point-to-site IPsec-beleid
titleSuffix: Azure Virtual WAN
description: Meer informatie over Azure Virtual WAN Point-to-site IPsec-connectiviteits beleid.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745911"
---
# <a name="point-to-site-ipsec-policies"></a>Punt-naar-site-IPsec-beleid

In dit artikel worden de ondersteunde IPsec-beleids combinaties voor punt-naar-site-VPN-verbindingen in azure Virtual WAN weer gegeven.

## <a name="default-ipsec-policies"></a>Standaard IPsec-beleid

De volgende tabel bevat de standaard IPsec-para meters voor punt-naar-site-VPN-verbindingen. Deze para meters worden automatisch gekozen door de virtuele WAN punt-naar-site VPN-gateway wanneer een punt-naar-site-profiel wordt gemaakt.

| Instelling | Parameters |
|--- |--- |
| Fase 1 IKE-versleuteling | AES256 |
| Fase 1 IKE-integriteit |  SHA256 |
| DH-groep | DHGroup24 |
| Fase 2 IPsec-versleuteling | GCMAES256|
| IPsec-integriteit fase 2 | GCMAES25 |
| PFS-groep |PFS24|

## <a name="custom-ipsec-policies"></a>Aangepaste IPsec-beleids regels

Houd bij het werken met aangepaste IPsec-beleids regels rekening met de volgende vereisten:

* **IKE** -voor fase 1 IKE kunt u een wille keurige para meter uit IKE-versleuteling selecteren, plus alle para meters van IKE-integriteit, plus alle para meters uit de DH-groep.
* **IPSec** -voor Phase 2 IPSec kunt u een para meter selecteren uit IPSec-versleuteling, plus alle para meters van IPSec-integriteit, plus PFS. Als een van de para meters voor IPsec-versleuteling of IPsec-integriteit GCM is, moeten beide IPsec-versleuteling en-integriteit hetzelfde algoritme gebruiken. Als bijvoorbeeld GCMAES128 is gekozen voor IPsec-versleuteling, moet GCMAES128 ook worden gekozen voor IPsec-integriteit.  

De volgende tabel bevat de beschik bare IPsec-para meters voor punt-naar-site-VPN-verbindingen.

| Instelling | Parameters |
|--- |--- |
| Fase 1 IKE-versleuteling | AES128, AES256, GCMAES128, GCMAES256 |
| Fase 1 IKE-integriteit |  SHA256, SHA384 |
| DH-groep | DHGroup14, DHGroup24, ECP256, ECP384 |
| Fase 2 IPsec-versleuteling | GCMAES128, GCMAES256, SHA256|
| IPsec-integriteit fase 2 | GCMAES128, GCMAES256 |
| PFS-groep |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Volgende stappen

Zie [een punt-naar-site-verbinding maken](virtual-wan-point-to-site-portal.md)

Zie de [Veelgestelde vragen over virtuele WAN](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
