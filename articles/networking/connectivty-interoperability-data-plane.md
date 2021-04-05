---
title: 'Interoperabiliteit in Azure: gegevens vlak analyse'
description: Dit artikel bevat de gegevenslaag analyse van de test instellingen die u kunt gebruiken voor het analyseren van de interoperabiliteit tussen ExpressRoute, een site-naar-site-VPN en virtuele netwerk peering in Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234168"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabiliteit in Azure: gegevens vlak analyse

In dit artikel wordt de gegevenslaag analyse van de [test installatie][Setup]beschreven. U kunt ook de configuratie van de [test installatie][Configuration] en de analyse van het [controle vlak][Control-Analysis] van de test installatie controleren.

Gegevens vlak analyse onderzoekt het pad dat wordt gebruikt door pakketten die van het ene lokale netwerk (LAN of virtueel netwerk) naar een andere worden verzonden binnen een topologie. Het gegevenspad tussen twee lokale netwerken is niet noodzakelijkerwijs symmetrisch. Daarom analyseren we in dit artikel een doorstuur traject van een lokaal netwerk naar een ander netwerk dat gescheiden is van het omgekeerde pad.

## <a name="data-path-from-the-hub-vnet"></a>Gegevenspad van het hub-VNet

### <a name="path-to-the-spoke-vnet"></a>Pad naar de spoke-VNet

Met peering van virtuele netwerken (VNet) wordt de functionaliteit van de netwerk brug geëmuleerd tussen de twee VNets die gelijkwaardig zijn. Traceroute uitvoer van een hub-VNet naar een virtuele machine in het spoke VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

In de volgende afbeelding ziet u de grafische verbindings weergave van de hub VNet en de spoke VNet vanuit het perspectief van Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Pad naar het filiaal-VNet

Traceroute uitvoer van een hub-VNet naar een virtuele machine in het filiaal VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

In deze traceroute is de eerste hop de VPN-gateway in azure VPN Gateway van de hub VNet. De tweede hop is de VPN-gateway van het filiaal-VNet. Het IP-adres van de VPN-gateway van het filiaal-VNet wordt niet geadverteerd in de hub VNet. De derde hop is de virtuele machine op het filiaal-VNet.

In de volgende afbeelding ziet u de grafische verbindings weergave van de hub VNet en de vertakkings-VNet uit het perspectief van Network Watcher:

![2][2]

Voor dezelfde verbinding wordt in de volgende afbeelding de raster weergave in Network Watcher weer gegeven:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Pad naar de on-premises locatie 1

Traceroute uitvoer van een hub-VNet naar een virtuele machine in on-premises locatie 1 wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

In deze traceroute is de eerste hop het tunnel eindpunt van de Azure ExpressRoute-gateway naar een micro soft Enter prise edge-router (MSEE). De tweede en derde hop zijn de klant Edge (CE)-router en de on-premises locatie 1 LAN Ip's. Deze IP-adressen worden niet geadverteerd in het hub-VNet. De vierde hop is de virtuele machine op de on-premises locatie 1.


### <a name="path-to-on-premises-location-2"></a>Pad naar de on-premises locatie 2

Traceroute uitvoer van een hub-VNet naar een virtuele machine in on-premises locatie 2 wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

In deze traceroute is de eerste hop het tunnel eindpunt van de ExpressRoute-gateway naar een MSEE. De tweede en derde hop zijn de CE-router en de on-premises locatie 2 LAN Ip's. Deze IP-adressen worden niet geadverteerd in het hub-VNet. De vierde hop is de virtuele machine op de on-premises locatie 2.

### <a name="path-to-the-remote-vnet"></a>Pad naar het externe VNet

Traceroute uitvoer van een hub-VNet naar een virtuele machine in het externe VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

In deze traceroute is de eerste hop het tunnel eindpunt van de ExpressRoute-gateway naar een MSEE. De tweede hop is het IP-adres van de gateway van het externe VNet. Het tweede hop IP-bereik wordt niet geadverteerd in de hub VNet. De derde hop is de virtuele machine op het externe VNet.

## <a name="data-path-from-the-spoke-vnet"></a>Gegevenspad van de spoke-VNet

De spoke VNet deelt de netwerk weergave van het hub-VNet. Via VNet-peering gebruikt de spoke VNet de externe gateway connectiviteit van de hub VNet alsof deze rechtstreeks is verbonden met de spoke-VNet.

### <a name="path-to-the-hub-vnet"></a>Pad naar het hub-VNet

Traceroute uitvoer van de spoke VNet naar een virtuele machine in de hub-VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Pad naar het filiaal-VNet

Traceroute uitvoer van het spoke VNet naar een virtuele machine in het filiaal VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

In deze traceroute is de eerste hop de VPN-gateway van de hub VNet. De tweede hop is de VPN-gateway van het filiaal-VNet. Het IP-adres van de VPN-gateway van het filiaal-VNet wordt niet geadverteerd binnen de hub/spoke VNet. De derde hop is de virtuele machine op het filiaal-VNet.

### <a name="path-to-on-premises-location-1"></a>Pad naar de on-premises locatie 1

Traceroute uitvoer van het spoke VNet naar een virtuele machine in on-premises locatie 1 wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel-eind punt van de hub VNet naar een MSEE. De tweede en derde hop zijn de CE-router en de on-premises locatie 1 LAN Ip's. Deze IP-adressen worden niet geadverteerd in de hub/spoke VNet. De vierde hop is de virtuele machine op de on-premises locatie 1.

### <a name="path-to-on-premises-location-2"></a>Pad naar de on-premises locatie 2

Traceroute uitvoer van het spoke VNet naar een virtuele machine in on-premises locatie 2 wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel-eind punt van de hub VNet naar een MSEE. De tweede en derde hop zijn de CE-router en de on-premises locatie 2 LAN Ip's. Deze IP-adressen worden niet geadverteerd in de hub/spoke-VNets. De vierde hop is de virtuele machine op de on-premises locatie 2.

### <a name="path-to-the-remote-vnet"></a>Pad naar het externe VNet

Traceroute uitvoer van het spoke VNet naar een virtuele machine in het externe VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

In deze traceroute is de eerste hop het ExpressRoute gateway tunnel-eind punt van de hub VNet naar een MSEE. De tweede hop is het IP-adres van de gateway van het externe VNet. Het tweede hop IP-bereik wordt niet geadverteerd in de hub/spoke VNet. De derde hop is de virtuele machine op het externe VNet.

## <a name="data-path-from-the-branch-vnet"></a>Gegevenspad van het vertakkings-VNet

### <a name="path-to-the-hub-vnet"></a>Pad naar het hub-VNet

Traceroute uitvoer van het filiaal-VNet naar een virtuele machine in de hub VNet wordt hier weer gegeven:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

In deze traceroute is de eerste hop de VPN-gateway van het filiaal-VNet. De tweede hop is de VPN-gateway van het hub-VNet. Het IP-adres van de VPN-gateway van de hub VNet wordt niet geadverteerd in het externe VNet. De derde hop is de virtuele machine op het hub-VNet.

### <a name="path-to-the-spoke-vnet"></a>Pad naar de spoke-VNet

Traceroute uitvoer van het filiaal-VNet naar een virtuele machine in het spoke-VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

In deze traceroute is de eerste hop de VPN-gateway van het filiaal-VNet. De tweede hop is de VPN-gateway van het hub-VNet. Het IP-adres van de VPN-gateway van de hub VNet wordt niet geadverteerd in het externe VNet. De derde hop is de virtuele machine op de spoke-VNet.

### <a name="path-to-on-premises-location-1"></a>Pad naar de on-premises locatie 1

Traceroute uitvoer van het filiaal-VNet naar een virtuele machine in on-premises locatie 1 wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

In deze traceroute is de eerste hop de VPN-gateway van het filiaal-VNet. De tweede hop is de VPN-gateway van het hub-VNet. Het IP-adres van de VPN-gateway van de hub VNet wordt niet geadverteerd in het externe VNet. De derde hop is het eind punt van de VPN-tunnel op de primaire CE-router. De vierde hop is een intern IP-adres van on-premises locatie 1. Dit LAN IP-adres wordt niet geadverteerd buiten de CE-router. De vijfde hop is de doel-VM op de on-premises locatie 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Pad naar de on-premises locatie 2 en het externe VNet

Zoals we in de Control-vlak analyse hebben besproken, heeft het filiaal VNet geen zicht baarheid op on-premises locatie 2 of op het externe VNet per netwerk configuratie. De volgende resultaten van de ping worden bevestigd: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\rb>ping 10.17.30.4

Pinging 10.17.30.4 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.17.30.4:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

## <a name="data-path-from-on-premises-location-1"></a>Gegevenspad van on-premises locatie 1

### <a name="path-to-the-hub-vnet"></a>Pad naar het hub-VNet

Traceroute uitvoer van on-premises locatie 1 naar een virtuele machine in het hub-VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

In deze traceroute maken de eerste twee hops deel uit van het on-premises netwerk. De derde hop is de primaire MSEE-interface die de CE-router bevindt. De vierde hop is de ExpressRoute-gateway van de hub VNet. Het IP-bereik van de ExpressRoute-gateway van de hub VNet wordt niet geadverteerd naar het on-premises netwerk. De vijfde hop is de doel-VM.

Network Watcher biedt alleen een Azure-georiënteerde weer gave. Voor een on-premises perspectief gebruiken we Azure Netwerkprestatiemeter. Netwerkprestatiemeter biedt agents die u kunt installeren op servers in netwerken buiten Azure voor analyse van gegevenspaden.

In de volgende afbeelding ziet u de topologie weergave van de on-premises locatie 1 VM-verbinding met de VM op het hub-VNet via ExpressRoute:

![4][4]

Zoals eerder besproken, maakt de test installatie gebruik van een site-naar-site-VPN als back-upconnectiviteit voor ExpressRoute tussen de on-premises locatie 1 en de hub-VNet. Om het pad naar de back-upgegevens te testen, gaan we een ExpressRoute-koppelings fout maken tussen de on-premises locatie 1 primaire CE-router en de bijbehorende MSEE. Als u een ExpressRoute-koppelings fout wilt veroorzaken, sluit u de CE-interface die de MSEE getoont:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

In de volgende afbeelding ziet u de topologie weergave van de on-premises locatie 1 VM-verbinding met de VM op het hub-VNet via site-naar-site VPN-verbinding wanneer ExpressRoute-connectiviteit is uitgeschakeld:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Pad naar de spoke-VNet

Traceroute uitvoer van on-premises locatie 1 naar een virtuele machine in het spoke VNet wordt hier weer gegeven:

We gaan de primaire ExpressRoute-verbinding terugbrengen om de gegevenspath-analyse uit te voeren naar de spoke-VNet:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Breng de primaire ExpressRoute 1-connectiviteit voor de rest van de analyse van het gegevenspad.

### <a name="path-to-the-branch-vnet"></a>Pad naar het filiaal-VNet

Traceroute uitvoer van on-premises locatie 1 naar een virtuele machine in het filiaal VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Pad naar de on-premises locatie 2

Bij het bespreken van de [controle vlak analyse][Control-Analysis]is de on-premises locatie 1 niet zichtbaar voor on-premises locatie 2 per netwerk configuratie. De volgende resultaten van de ping worden bevestigd: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Pad naar het externe VNet

Traceroute uitvoer van on-premises locatie 1 naar een virtuele machine in het externe VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Gegevenspad van on-premises locatie 2

### <a name="path-to-the-hub-vnet"></a>Pad naar het hub-VNet

Traceroute uitvoer van on-premises locatie 2 naar een virtuele machine in het hub-VNet wordt hier weer gegeven:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Pad naar de spoke-VNet

Traceroute uitvoer van on-premises locatie 2 naar een virtuele machine in het spoke VNet wordt hier weer gegeven:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Pad naar het filiaal-VNet, on-premises locatie 1 en het externe VNet

Bij het bespreken van de [controle vlak analyse][Control-Analysis]heeft de on-premises locatie 1 geen zicht baarheid van het filiaal VNet, naar on-premises locatie 1 of naar het externe VNet per netwerk configuratie. 

## <a name="data-path-from-the-remote-vnet"></a>Gegevenspad van het externe VNet

### <a name="path-to-the-hub-vnet"></a>Pad naar het hub-VNet

Traceroute uitvoer van het externe VNet naar een virtuele machine in de hub VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Pad naar de spoke-VNet

Traceroute uitvoer van het externe VNet naar een virtuele machine in het spoke VNet wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Pad naar het filiaal-VNet en on-premises locatie 2

Tijdens het bespreken van de [controle vlak analyse][Control-Analysis]heeft het externe vnet geen zicht baarheid voor het filiaal-vnet of de on-premises locatie 2 per netwerk configuratie. 

### <a name="path-to-on-premises-location-1"></a>Pad naar de on-premises locatie 1

Traceroute uitvoer van het externe VNet naar een virtuele machine in on-premises locatie 1 wordt hier weer gegeven:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute-en site-naar-site-VPN-verbinding

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-naar-site-VPN via ExpressRoute

U kunt een site-naar-site-VPN configureren door ExpressRoute micro soft-peering te gebruiken voor het privé uitwisselen van gegevens tussen uw on-premises netwerk en uw Azure VNets. Met deze configuratie kunt u gegevens uitwisselen met vertrouwelijkheid, authenticiteit en integriteit. De gegevens uitwisseling is ook anti-replay. Zie [site-naar-site VPN via ExpressRoute micro soft-peering][S2S-Over-ExR]voor meer informatie over het configureren van een site-naar-site IPSec VPN in de tunnel modus met behulp van ExpressRoute micro soft-peering. 

De primaire beperking van het configureren van een site-naar-site-VPN dat gebruikmaakt van micro soft-peering is door voer. De door Voer via de IPsec-tunnel wordt beperkt door de capaciteit van de VPN-gateway. De door Voer van de VPN-gateway is lager dan ExpressRoute door voer. In dit scenario zorgt het gebruik van de IPsec-tunnel voor zeer veilig verkeer en het gebruik van privé-peering voor al het andere verkeer voor het optimaliseren van het ExpressRoute bandbreedte gebruik.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-naar-site-VPN als een beveiligd failover-pad voor ExpressRoute

ExpressRoute fungeert als een redundant circuit paar om hoge Beschik baarheid te garanderen. U kunt geo-redundante ExpressRoute-connectiviteit configureren in verschillende Azure-regio's. Net als tijdens de test installatie, in een Azure-regio, kunt u ook een site-naar-site-VPN gebruiken om een pad voor de ExpressRoute-verbinding te maken. Wanneer dezelfde voor voegsels worden geadverteerd via zowel ExpressRoute als een site-naar-site-VPN, krijgen de ExpressRoute van Azure prioriteit. Om asymmetrische route ring tussen ExpressRoute en de site-naar-site-VPN te voor komen, moet de on-premises netwerk configuratie ook reciprocate met ExpressRoute-connectiviteit voordat de site-naar-site-VPN-verbinding wordt gebruikt.

Zie [ExpressRoute en site-naar-site][ExR-S2S-CoEx]-samen werking voor meer informatie over het configureren van naast elkaar bestaande verbindingen voor ExpressRoute en een site-naar-site-VPN.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Back-end-connectiviteit met spoke VNets-en vertakkings locaties uitbreiden

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke VNet-connectiviteit met behulp van VNet-peering

De hub-en spoke VNet-architectuur wordt veel gebruikt. De hub is een VNet in azure dat fungeert als een centraal punt van connectiviteit tussen uw spoke-VNets en uw on-premises netwerk. De spokes zijn VNets die peer met de hub en die u kunt gebruiken om werk belastingen te isoleren. Verkeer loopt tussen het on-premises Data Center en de hub via een ExpressRoute of een VPN-verbinding. Zie [een hub-spoke-netwerk topologie in azure implementeren][Hub-n-Spoke]voor meer informatie over de architectuur.

In VNet-peering binnen een regio kan spoke VNets hub VNet-gateways (zowel VPN-als ExpressRoute-gateways) gebruiken om te communiceren met externe netwerken.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Vertakkings-VNet-connectiviteit met behulp van site-naar-site-VPN

Mogelijk wilt u VNets, die zich in verschillende regio's bevinden, en on-premises netwerken met elkaar via een hub-VNet. De systeem eigen Azure-oplossing voor deze configuratie is een site-naar-site-VPN-verbinding met behulp van een VPN. U kunt ook een virtueel netwerk apparaat (NVA) gebruiken voor route ring in de hub.

Zie [Wat is VPN gateway?][VPN] en [Implementeer een Maxi maal beschik bare NVA][Deploy-NVA]voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen over ExpressRoute][ExR-FAQ] voor het volgende:
-   Meer informatie over het aantal ExpressRoute-circuits waarmee u verbinding kunt maken met een ExpressRoute-gateway.
-   Meer informatie over het aantal ExpressRoute-gateways waarmee u verbinding kunt maken met een ExpressRoute-circuit.
-   Meer informatie over andere schaal limieten van ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Network Watcher weer gave van connectiviteit vanuit een hub-VNet naar een spoke-VNet"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Network Watcher weer gave van connectiviteit vanuit een hub-VNet naar een vertakkings-VNet"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Network Watcher raster weergave van de connectiviteit van een hub-VNet naar een vertakkings-VNet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Netwerkprestatiemeter weer gave van connectiviteit vanaf de locatie 1 VM naar het hub-VNet via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Netwerkprestatiemeter weer gave van connectiviteit vanaf de locatie 1 VM naar de hub-VNet via een site-naar-site-VPN"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md