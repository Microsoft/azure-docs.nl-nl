---
title: 'Azure ExpressRoute: voor beelden van router configuratie-NAT'
description: Op deze pagina vindt u voor beelden van router configuraties voor Cisco-en Juniper-routers.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: ae0a39d65bf0f1bc5221cd5e46493c489f7630f8
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012662"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Voor beelden van router configuraties voor het instellen en beheren van NAT

In dit artikel vindt u voor beelden van NAT-configuraties voor Cisco ASA-en Juniper SRX-reeks routers bij het werken met ExpressRoute. Deze router configuraties zijn alleen bedoeld als voor beelden en mogen niet worden gebruikt als is. U moet samen werken met uw leverancier om te voldoen aan de juiste configuraties voor uw netwerk.

> [!IMPORTANT]
> Voor beelden op deze pagina zijn bedoeld als louter voor hulp. U moet samen werken met het verkoop-en technische team van uw leverancier en uw netwerk team om te voorzien in de juiste configuraties om te voldoen aan uw behoeften. Micro soft biedt geen ondersteuning voor problemen met configuraties die op deze pagina worden weer gegeven. Neem contact op met de leverancier van uw apparaat voor ondersteunings problemen.
> 
> 

* Voor beelden van router configuraties zijn van toepassing op de open bare en micro soft-peering van Azure. U configureert geen NAT voor persoonlijke Azure-peering. Bekijk [ExpressRoute-peerings](expressroute-circuit-peerings.md) en [ExpressRoute NAT-vereisten](expressroute-nat.md) voor meer informatie.

* U moet afzonderlijke NAT IP-adres groepen gebruiken voor de connectiviteit met internet en ExpressRoute. Het gebruik van dezelfde NAT IP-adres groep via internet en ExpressRoute leidt tot asymmetrische route ring en verlies van connectiviteit.


## <a name="cisco-asa-firewalls"></a>Cisco ASA-firewalls
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAT-configuratie voor verkeer van het klanten netwerk naar micro soft

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>PAT-configuratie voor verkeer van micro soft naar het klanten netwerk

**Interfaces en richting:**

Bron interface (waarbij het verkeer de ASA binnengaat): in de doel interface (waarbij het verkeer de ASA verlaat): buiten

**Configuratie**

NAT-groep:

```console
object network outbound-PAT
    host <NAT-IP>
```

Doel server:

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

Object groep voor IP-adressen van klant:

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

NAT-opdrachten:

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Routers van de Juniper SRX-serie
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. redundante Ethernet-interfaces maken voor het cluster

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2. Maak twee beveiligings zones
* Vertrouwens zone voor intern netwerk en niet-vertrouwde zone voor externe netwerk gerichte Edge-routers
* De juiste interfaces aan de zones toewijzen
* Services op de interfaces toestaan

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3. beveiligings beleidsregels maken tussen zones

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4. NAT-beleid configureren
* Maak twee NAT-Pools. Er wordt een NAT-verkeer naar micro soft en andere van micro soft naar de klant gebruikt.
* Regels maken voor NAT het respectieve verkeer

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Configureer BGP voor het adverteren van selectieve voor voegsels in elke richting
Raadpleeg voor beelden op de pagina [routerings configuratie voorbeelden](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. beleids regels maken

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>Volgende stappen
Zie [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.

