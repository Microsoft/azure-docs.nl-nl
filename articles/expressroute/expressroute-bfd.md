---
title: 'Azure-ExpressRoute: BFD configureren'
description: In dit artikel vindt u instructies voor het configureren van BFD (bidirectionele doorstuur detectie) via particuliere peering van een ExpressRoute-circuit.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511260"
---
# <a name="configure-bfd-over-expressroute"></a>BFD configureren via ExpressRoute

ExpressRoute biedt ondersteuning voor bidirectionele forwarding-detectie (BFD) via persoonlijke en micro soft-peering. Wanneer u BFD via ExpressRoute inschakelt, kunt u de detectie van verbindings fouten tussen de micro soft Enter prise Edge (MSEE)-apparaten en de routers die uw ExpressRoute-circuit configureert configureren (CE/PE) versnellen. U kunt ExpressRoute configureren via uw rand routering apparaten of uw partner Edge-routerings apparaten (als u met de Managed Layer 3 Connection-service bent gegaan). In dit document wordt uitgelegd hoe u BFD nodig hebt en hoe u BFD inschakelt via ExpressRoute.

## <a name="need-for-bfd"></a>Benodigde BFD

In het volgende diagram ziet u het voor deel van het inschakelen van BFD over ExpressRoute-circuit: [![1]][1]

U kunt het ExpressRoute-circuit inschakelen via laag-2-verbindingen of beheerde laag-3-verbindingen. Als er in beide gevallen meer dan één laag-2-apparaat aanwezig zijn in het ExpressRoute, is de verantwoordelijkheid voor het detecteren van eventuele koppelings fouten in het pad ook de bovenliggende BGP-sessie.

Op de MSEE-apparaten worden BGP Keep-Alive en hold-time doorgaans geconfigureerd als 60 en 180 seconden. Om die reden kan het tot drie minuten duren om een koppelings fout te detecteren en verkeer naar een andere verbinding te scha kelen.

U kunt de BGP-timers beheren door een lagere BGP-Keep-Alive en wacht tijd op uw edge-apparaat te configureren. Als de BGP-timers niet hetzelfde zijn tussen de twee peering-apparaten, wordt de BGP-sessie tot stand gebracht met de laagste tijd waarde. BGP Keep-Alive kan worden ingesteld op Maxi maal drie seconden en de wacht tijd is 10 seconden lang. Het instellen van een zeer agressieve BGP-timer wordt echter niet aanbevolen, omdat het protocol proces intensief is.

In dit scenario kan BFD u helpen. BFD biedt de detectie van een lage overhead van een koppeling in een subseconde tijd interval. 


## <a name="enabling-bfd"></a>BFD inschakelen

BFD wordt standaard geconfigureerd onder alle zojuist gemaakte ExpressRoute-particuliere peering-interfaces op de Msee's. Om BFD in te scha kelen, hoeft u BFD alleen op uw primaire en secundaire apparaten te configureren. Het configureren van BFD is een proces dat uit twee stappen bestaat. U configureert de BFD op de interface en koppelt deze vervolgens aan de BGP-sessie.

Hieronder ziet u een voor beeld van een CE/PE-configuratie (met Cisco IOS XE). 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>BFD inschakelen onder een al bestaande persoonlijke peering; u moet de peering opnieuw instellen. Zie [ExpressRoute-peerings opnieuw instellen][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD timer-onderhandeling

Tussen BFD-peers wordt de verzend frequentie bepaald door de langzamere van de twee peers. Msee's BFD-verzen ding/ontvangst intervallen worden ingesteld op 300 milliseconden. In bepaalde scenario's kan het interval worden ingesteld op een hogere waarde van 750 milliseconden. Als u een hogere waarde configureert, kunt u ervoor zorgen dat deze intervallen langer zijn, maar het is niet mogelijk om ze korter te maken.

>[!NOTE]
>Als u geografisch redundante ExpressRoute-circuits hebt geconfigureerd of VPN-verbinding tussen sites als back-up gebruikt. Door BFD in te scha kelen, kunt u de failover sneller volgen door een ExpressRoute-verbindings fout. 
>

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende koppelingen voor meer informatie of hulp:

- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Routering voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD versnelt de tijd" voor het ongedaan maken van de koppelings fout

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md