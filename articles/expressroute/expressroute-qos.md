---
title: 'Azure ExpressRoute: QoS-vereisten'
description: Deze pagina bevat gedetailleerde vereisten voor het configureren en beheren van QoS. Skype voor bedrijven/voice-services worden besproken.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: duau
ms.openlocfilehash: 7f14f43fa341df40ecd35340b7311e1acd18004c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204704"
---
# <a name="expressroute-qos-requirements"></a>QoS-vereisten voor ExpressRoute
Skype voor Bedrijven heeft verschillende workloads die allemaal een andere QoS-behandeling vereisen. Als u via ExpressRoute voice-services wilt gaan gebruiken, moet u voldoen aan de vereisten die hieronder worden beschreven.

![Diagram waarin de spraak services worden weer gegeven die via ExpressRoute worden verzonden.](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS-vereisten zijn alleen van toepassing op de Microsoft-peering. De DSCP-waarden in uw netwerkverkeer dat is ontvangen op Openbare Azure-peering en Persoonlijke Azure-peering wordt ingesteld op 0. 
> 
> 

De volgende tabel bevat een lijst met DSCP-markeringen die worden gebruikt door micro soft teams en Skype voor bedrijven. Raadpleeg [Managing QoS for Skype for Business](/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) (QoS voor Skype voor Bedrijven beheren) voor meer informatie.

| **Verkeersklasse** | **Behandeling (DSCP-markering)** | **Werk belastingen van micro soft teams en Skype voor bedrijven** |
| --- | --- | --- |
| **Spraak** |EF (46) |Skype/micro soft teams/Lync Voice |
| **Interactief** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Apps delen | 
| **Prijs** |AF11 (10) |Bestandsoverdracht |
| |CS0 (0) |Overige |

* U moet de workloads classificeren en de juiste DSCP-waarden markeren. Volg [deze](/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) richtlijnen over het instellen van DSCP-markeringen in uw netwerk.
* U moet meerdere QoS-wachtrijen in uw netwerk configureren en ondersteunen. Voice moet een zelfstandige klasse zijn en de EF-behandeling ontvangen die is opgegeven in [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* U kunt het wachtrijmechanisme, het congestiedetectiebeleid en de bandbreedtetoewijzing per verkeersklasse bepalen. De DSCP-markering voor workloads voor Skype voor Bedrijven moet echter behouden blijven. Als u DSCP-markeringen gebruikt die niet worden weergegeven, bijvoorbeeld AF31 (26), moet u deze DSCP-waarde wijzigen in 0 voordat u het pakket naar Microsoft verzendt. Microsoft verzendt alleen pakketten die zijn gemarkeerd met de DSCP-waarde die wordt weergegeven in de bovenstaande tabel. 

## <a name="next-steps"></a>Volgende stappen
* Raadpleeg de vereisten voor [Routering](expressroute-routing.md) en [NAT](expressroute-nat.md).
* Klik op de volgende koppelingen als u uw ExpressRoute-verbinding wilt configureren.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-classic.md)
  * [Routering configureren](expressroute-howto-routing-classic.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md)