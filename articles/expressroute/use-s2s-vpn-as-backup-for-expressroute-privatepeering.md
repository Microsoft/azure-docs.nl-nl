---
title: S2S VPN gebruiken als back-up voor Azure ExpressRoute privé-peering | Microsoft Docs
description: Deze pagina bevat architectuur aanbevelingen voor het maken van back-ups van Azure ExpressRoute private-peering met S2S VPN.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207917"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>S2S VPN gebruiken als back-up voor ExpressRoute-persoonlijke peering

In het artikel [over het ontwerpen van herstel na nood gevallen met persoonlijke ExpressRoute-peering][DR-PP]is de nood zaak van back-upconnectiviteits oplossing voor een persoonlijke peering-verbinding met ExpressRoute beschreven en wordt uitgelegd hoe u geo-redundante ExpressRoute-circuits kunt gebruiken voor het doel. In dit artikel kunt u overwegen hoe u site-naar-site (S2S) VPN kunt gebruiken en onderhouden als back-up voor ExpressRoute-persoonlijke peering. 

In tegens telling tot geo redundante ExpressRoute-circuits kunt u ExpressRoute-VPN-nood herstel alleen gebruiken in de modus actief-passief. Een grote uitdaging van het gebruik van back-upnetwerk verbindingen in de passieve modus is dat de passieve verbinding vaak naast de primaire verbinding zou mislukken. De gemeen schappelijke reden voor het mislukken van de passieve verbinding heeft geen actieve onderhouds werkzaamheden. In dit artikel wordt daarom aandacht besteed aan het controleren en actief onderhouden van S2S VPN-connectiviteit die een back-up maakt van een persoonlijke ExpressRoute-peering.

>[!NOTE] 
>Wanneer een bepaalde route via ExpressRoute en VPN wordt geadverteerd, wordt door Azure een route ring uitgevoerd via ExpressRoute.  
>

In dit artikel ziet u hoe u de connectiviteit kunt controleren op zowel het Azure-perspectief als het perspectief van de netwerk rand van de klant. De mogelijkheid om vanaf een van beide uiteinden te valideren, helpt u ongeacht of u de netwerk apparaten van de klanten die gelijkwaardig zijn met de micro soft-netwerk entiteiten beheert of niet. 

## <a name="example-topology"></a>Voorbeeld topologie

In onze installatie hebben we een on-premises netwerk verbonden met een Azure hub VNet via een ExpressRoute-circuit en een S2S-VPN-verbinding. De Azure hub VNet is op zijn beurt gekoppeld aan een spoke-VNet, zoals wordt weer gegeven in het onderstaande diagram:

![1][1]

In de installatie wordt het ExpressRoute-circuit op de on-premises beëindigd op een paar ' klanten Edge ' (CE)-routers. De on-premises LAN is verbonden met de CE-routers via een paar firewalls die in de modus Leader-volger actief zijn. De S2S-VPN wordt rechtstreeks beëindigd op de firewalls.

De volgende tabel geeft een lijst van de belangrijkste IP-voor voegsels van de topologie:

| **Entiteit** | **Voorvoegsel** |
| --- | --- |
| On-premises LAN | 10.1.11.0/25 |
| Azure hub VNet | 10.17.11.0/25 |
| Azure spoke VNet | 10.17.11.128/26 |
| On-premises test server | 10.1.11.10 |
| Spoke VNet-test-VM | 10.17.11.132 |
| ExpressRoute primaire verbinding P2P-subnet | 192.168.11.16/30 |
| ExpressRoute secundaire verbinding P2P-subnet | 192.168.11.20/30 |
| Primaire BGP-peer-IP van VPN-gateway | 10.17.11.76 |
| VPN gateway secundair BGP-peer-IP | 10.17.11.77 |
| On-premises firewall VPN BGP peer IP | 192.168.11.88 |
| Primaire CE-router i/f voor Firewall-IP | 192.168.11.0/31 |
| Firewall-i/f naar het IP-adres van de primaire CE-router | 192.168.11.1/31 |
| Secundaire CE-router i/f voor Firewall-IP | 192.168.11.2/31 |
| Firewall-i/f naar secundaire CE router-IP | 192.168.11.3/31 |


De volgende tabel bevat de Asn's van de topologie:

| **Autonoom systeem** | **ASN** |
| --- | --- |
| On-premises | 65020 |
| Micro soft Enter prise Edge | 12076 |
| Virtual Network GW (ExR) | 65515 |
| Virtual Network GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Hoge Beschik baarheid zonder asymmetrisch

### <a name="configuring-for-high-availability"></a>Configureren voor hoge Beschik baarheid

[ExpressRoute-en site-to-site-verbindingen configureren][Conf-CoExist] in dit artikel wordt beschreven hoe u het naast elkaar bestaande ExpressRoute-circuit en S2S VPN-verbindingen configureert. Net als bij het [ontwerpen voor hoge Beschik baarheid met ExpressRoute][HA], zorgt u ervoor dat de netwerk redundantie wordt beheerd door onze installatie van ExpressRoute hoge Beschik baarheid (voor komt dat single point of failure) tot de eind punten. Ook de primaire en secundaire verbindingen van de ExpressRoute-circuits zijn geconfigureerd om te worden uitgevoerd in de modus actief-actief door de on-premises voor voegsels op dezelfde manier te adverteren via beide verbindingen. 

De on-premises route advertentie van de primaire CE-router via de primaire verbinding van het ExpressRoute-circuit wordt hieronder weer gegeven (Junos-opdrachten):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

De on-premises route advertentie van de secundaire CE-router via de secundaire verbinding van het ExpressRoute-circuit wordt hieronder weer gegeven (Junos-opdrachten):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Ter verbetering van de maximale Beschik baarheid van de back-upverbinding, wordt de S2S VPN ook geconfigureerd in de modus actief-actief. De configuratie van de Azure VPN-gateway wordt hieronder weer gegeven. Opmerking Als onderdeel van de VPN-configuratie VPN de IP-adressen van de BGP-peer van de gateway--10.17.11.76 en 10.17.11.77--worden ook vermeld.

![2][2]

De on-premises route wordt door de firewalls geadverteerd naar de primaire en secundaire BGP-peers van de VPN-gateway. De route-advertisements worden hieronder weer gegeven (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Het configureren van de S2S VPN in de modus actief-actief biedt niet alleen een hoge Beschik baarheid voor uw back-upnetwerk voor herstel na nood gevallen, maar biedt ook een hogere door voer naar de back-upconnectiviteit. Met andere woorden, het configureren van S2S VPN in de modus actief-actief wordt aanbevolen omdat het geforceerd meerdere onderliggende tunnels maakt.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configureren voor symmetrische verkeers stroom

Wanneer een bepaalde on-premises route wordt geadverteerd via zowel ExpressRoute als S2S VPN, geeft Azure de voor keur aan het pad ExpressRoute. Om Azure de voor keur te geven aan het S2S VPN-pad via de naast elkaar bestaande ExpressRoute, moet u specifieke routes (meer voor voegsel met een groter subnetmasker) adverteren via de VPN-verbinding. Het doel is om de VPN-verbindingen alleen als back-up te gebruiken. Daarom is het standaard gedrag voor het selectie patroon van Azure in overeenstemming met ons doel. 

Het is onze verantwoordelijkheid om ervoor te zorgen dat het verkeer dat is bestemd voor Azure van on-premises ook ExpressRoute Path over S2S VPN. De standaard lokale voor keur van de CE-routers en firewalls in onze on-premises installatie is 100. Daarom kunnen we door het configureren van de lokale voor keur van de routes die zijn ontvangen via de persoonlijke ExpressRoutes van meer dan 100 (bijvoorbeeld 150), het verkeer dat bestemd is voor Azure, het ExpressRoute-circuit in de stationaire status laten staan.

De BGP-configuratie van de primaire CE-router waarmee de primaire verbinding van het ExpressRoute-circuit wordt verbroken, wordt hieronder weer gegeven. Let op: de waarde van de lokale voor keur van de routes die worden geadverteerd via de iBGP-sessie, is geconfigureerd om 150 te zijn. Op dezelfde manier moeten we ervoor zorgen dat de lokale voor keur van de secundaire CE-router die de secundaire verbinding van het ExpressRoute-circuit beëindigt, ook is geconfigureerd als 150.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

In de routerings tabel van de on-premises firewalls wordt (hieronder weer gegeven) bevestigd dat voor het on-premises verkeer dat is bestemd voor Azure, het voorkeurs pad ExpressRoute in de modus stabiel is.

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

In de bovenstaande route tabel voor de hub-en spoke VNet-routes--10.17.11.0/25 en 10.17.11.128/26--we zien dat ExpressRoute-Circuit de voor keur heeft boven VPN-verbindingen. De 192.168.11.0 en 192.168.11.2 zijn IP-adressen voor de Firewall interface naar de CE-routers.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validatie van route Exchange over S2S VPN

Eerder in dit artikel hebben we de on-premises route advertentie van de firewalls geverifieerd voor de primaire en secundaire BGP-peers van de VPN-gateway. U kunt ook Azure-routes bevestigen die door de firewalls worden ontvangen van de primaire en secundaire BGP-peers van de VPN-gateway.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

Laten we ook verifiëren voor on-premises netwerk route-prefixen die worden ontvangen door de Azure VPN-gateway. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

Zoals hierboven wordt weer gegeven, heeft de VPN-gateway routes ontvangen die zowel door de primaire als de secundaire BGP-peers van de VPN-gateway. Het bevat ook zicht baarheid van de routes die zijn ontvangen via de primaire en secundaire ExpressRoute-verbindingen (de poorten met de AS-pad voor 12076). Voor het bevestigen van de routes die via VPN-verbindingen worden ontvangen, moeten we de on-premises BGP-peer-IP van de verbindingen weten. In onze installatie wordt het 192.168.11.88 en zien we de ontvangen routes.

Vervolgens controleren we de routes die zijn geadverteerd door de Azure VPN-gateway naar de on-premises BGP-peer (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Fout bij het weer geven van route uitwisselingen duiden op een verbindings fout. Zie [probleem oplossing: een Azure site-naar-site-VPN-verbinding kan geen verbinding maken en stopt met werken][VPN Troubleshoot] voor hulp bij het oplossen van problemen met de VPN-verbinding.

## <a name="testing-failover"></a>Failover testen

Nu we geslaagde route-uitwisselingen via de VPN-verbinding (besturings vlak) hebben bevestigd, zijn we ingesteld om verkeer (Data Traffic) over te scha kelen van de ExpressRoute-verbinding met de VPN-verbinding. 

>[!NOTE] 
>In productie omgevingen moet Failover testen worden uitgevoerd tijdens het werk venster gepland netwerk onderhoud. Dit kan de service onderbreken.
>

Voordat u de verkeers schakeling gaat volgen, wordt het huidige pad naar de virtuele machine in de installatie van de on-premises test server naar de test-VM in de spoke VNet geleid.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

De primaire en secundaire ExpressRoute Point-to-point-verbinding subnetten van onze installatie zijn respectievelijk, 192.168.11.16/30 en 192.168.11.20/30. In de bovenstaande Traceer route ziet u in stap 3 dat we 192.168.11.18 hebben. Dit is het Interface-IP-adres van de primaire MSEE. Met de aanwezigheid van de MSEE-interface wordt bevestigd dat het huidige pad zich boven de ExpressRoute bevindt.

Zoals gerapporteerd in de [ExpressRoute-circuit-peers opnieuw instellen][RST], gebruiken we de volgende Power shell-opdrachten om de primaire en secundaire peering van het ExpressRoute-circuit uit te scha kelen.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

De failover-switch tijd is afhankelijk van de BGP-convergentie tijd. In onze installatie duurt de failover-switch enkele seconden (minder dan 10). Na de switch wordt in het traceroute het volgende pad weer gegeven:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

Het traceroute-resultaat bevestigt dat de back-upverbinding via S2S VPN actief is en dat de service continuïteit kan worden geboden als zowel de primaire als de secundaire ExpressRoute-verbindingen mislukken. Voor het volt ooien van de test voor de testfailover, laten we de ExpressRoute-verbindingen weer inschakelen en de verkeers stroom normaliseren, met behulp van de volgende reeks opdrachten.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Als u wilt controleren of het verkeer is teruggeschakeld naar ExpressRoute, herhaalt u de traceroute en controleert u of deze de ExpressRoute-persoonlijke peering doorloopt.

## <a name="next-steps"></a>Volgende stappen

ExpressRoute is ontworpen voor hoge Beschik baarheid zonder Single Point of Failure in het micro soft-netwerk. Nog steeds een ExpressRoute-circuit wordt beperkt tot één geografische regio en naar een service provider. S2S VPN kan een goede, passieve back-upoplossing voor herstel na nood geval zijn voor een ExpressRoute-circuit. Voor een betrouw bare oplossing voor een passieve back-up, is regel matig onderhoud van de passieve configuratie en periodieke validatie van de verbinding belang rijk. Het is essentieel dat de VPN-configuratie niet verouderd is en periodiek (elke kwar taal zeggen) de validatie-en testfailover testen tappen die in dit artikel worden beschreven, worden herhaald tijdens het onderhouds venster.

Als u bewaking en waarschuwingen wilt inschakelen op basis van metrische gegevens voor de VPN-gateway, raadpleegt [u waarschuwingen instellen voor VPN gateway metrische gegevens][VPN-alerts].

Als u BGP-convergentie wilt versnellen na een ExpressRoute-fout, [configureert u BFD via ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologie in overweging"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configuratie van VPN-GW"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md