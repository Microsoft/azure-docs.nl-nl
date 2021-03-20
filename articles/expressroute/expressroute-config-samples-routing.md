---
title: 'Azure ExpressRoute: Voorbeelden van routerconfiguraties'
description: Gebruik deze interface en routerings configuratie voorbeelden voor Cisco IOS-XE-en Juniper MX-serie routers als voor beelden om te werken met Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89397403"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Voorbeelden van routerconfiguraties voor het instellen en beheren van routering
Op deze pagina vindt u voor beelden van interface-en routerings configuratie voor Cisco IOS-XE-en Juniper MX-serie routers wanneer u met Azure ExpressRoute werkt.

> [!IMPORTANT]
> Voor beelden op deze pagina zijn uitsluitend voor hulp. U moet samen werken met het verkoop-en technische team van uw leverancier en uw netwerk team om de juiste configuraties te vinden die aan uw behoeften voldoen. Micro soft biedt geen ondersteuning voor problemen met configuraties die op deze pagina worden weer gegeven. Neem contact op met de leverancier van uw apparaat voor ondersteunings problemen.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MTU-en TCP MSS-instellingen op router interfaces
De MTU (maximum trans Mission Unit) voor de ExpressRoute-interface is 1500. Dit is de typische standaard-MTU voor een Ethernet-interface op een router. Tenzij uw router standaard een andere MTU heeft, hoeft er geen waarde op de router interface te worden opgegeven.

In tegens telling tot een Azure VPN-gateway hoeft de TCP Maximum Segment Size (MSS) voor een ExpressRoute-circuit niet te worden opgegeven.

De voor beelden van de router configuratie in dit artikel zijn van toepassing op alle peerings. Bekijk [ExpressRoute-peerings](expressroute-circuit-peerings.md) en [ExpressRoute-routerings vereisten](expressroute-routing.md) voor meer informatie over route ring.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-op XE gebaseerde routers
De voor beelden in deze sectie zijn van toepassing op elke router met de IOS-XE-besturingssysteem familie.

### <a name="configure-interfaces-and-subinterfaces"></a>Interfaces en Subinterfaces configureren
U hebt een subinterface nodig per peering in elke router waarmee u verbinding maakt met micro soft. Een subinterface kan worden geïdentificeerd met een VLAN-ID of een gestapeld paar VLAN-Id's en een IP-adres.

**Interface definitie van Dot1Q**

Dit voor beeld bevat de subinterface-definitie voor een subinterface met één VLAN-ID. De VLAN-ID is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**Interface definitie van QinQ**

Dit voor beeld bevat de subinterface-definitie voor een subinterface met twee VLAN-Id's. De buitenste VLAN-ID (s-tag), indien gebruikt, blijft hetzelfde voor alle peerings. De binnenste VLAN-ID (c-tag) is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>EBGP-sessies instellen
U moet voor elke peering een BGP-sessie met micro soft instellen. Stel een BGP-sessie in met behulp van het volgende voor beeld. Als het IPv4-adres dat u hebt gebruikt voor de subinterface, a. b. c. d is, is het IP-adres van de BGP-Neighbor (micro soft) a. b. c. d + 1. Het laatste octet van het IPv4-adres van de BGP-neighbor zal altijd een even getal zijn.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Voor voegsels instellen die moeten worden geadverteerd via de BGP-sessie
Configureer uw router om te adverteren Selecteer voor voegsels naar micro soft met behulp van het volgende voor beeld.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Route kaarten
Gebruik route kaarten en voorvoegsel lijsten om voor voegsels te filteren die worden door gegeven in uw netwerk. Raadpleeg het volgende voor beeld en controleer of u de juiste lijst met voor voegsels hebt ingesteld.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
 exit-address-family
!
route-map <MS_Prefixes_Inbound> permit 10
 match ip address prefix-list <MS_Prefixes>
!
```

### <a name="configure-bfd"></a>BFD configureren

U configureert BFD op twee plaatsen: één op interface niveau en een andere op BGP-niveau. Het voor beeld hier is voor de QinQ-interface. 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Juniper MX-serie routers
De voor beelden in deze sectie zijn van toepassing op elke Juniper MX-serie router.

### <a name="configure-interfaces-and-subinterfaces"></a>Interfaces en Subinterfaces configureren

**Interface definitie van Dot1Q**

Dit voor beeld bevat de subinterface-definitie voor een subinterface met één VLAN-ID. De VLAN-ID is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

```console
    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }
```


**Interface definitie van QinQ**

Dit voor beeld bevat de subinterface-definitie voor een subinterface met twee VLAN-Id's. De buitenste VLAN-ID (s-tag), indien gebruikt, blijft hetzelfde voor alle peerings. De binnenste VLAN-ID (c-tag) is uniek per peering. Het laatste octet van uw IPv4-adres zal altijd een oneven getal zijn.

```console
    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           
```

### <a name="set-up-ebgp-sessions"></a>EBGP-sessies instellen
U moet voor elke peering een BGP-sessie met micro soft instellen. Stel een BGP-sessie in met behulp van het volgende voor beeld. Als het IPv4-adres dat u hebt gebruikt voor de subinterface, a. b. c. d is, is het IP-adres van de BGP-Neighbor (micro soft) a. b. c. d + 1. Het laatste octet van het IPv4-adres van de BGP-neighbor zal altijd een even getal zijn.

```console
    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Voor voegsels instellen die moeten worden geadverteerd via de BGP-sessie
Configureer uw router om te adverteren Selecteer voor voegsels naar micro soft met behulp van het volgende voor beeld.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Routerings beleid
U kunt route toewijzingen en voorvoegsel lijsten gebruiken om voor voegsels te filteren die worden door gegeven in uw netwerk. Raadpleeg het volgende voor beeld en controleer of u de juiste lijst met voor voegsels hebt ingesteld.

```console
    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                    prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>BFD configureren
Configureer BFD alleen onder de sectie Protocol BGP.

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>MACSec configureren
Voor de configuratie van MACSec moet de verbindings koppelings sleutel (CAK) en de connectiviteits sleutel naam (CKN) worden vergeleken met de geconfigureerde waarden via Power shell-opdrachten.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie.



