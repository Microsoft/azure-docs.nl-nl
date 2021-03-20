---
title: Azure VMware-oplossing per CloudSimple-netwerk controlelijst
description: Controle lijst voor het toewijzen van netwerk-CIDR op Azure VMware-oplossing door CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77025007"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Netwerk vereisten voor de Azure VMware-oplossing op CloudSimple

De Azure VMware-oplossing van CloudSimple biedt een VMware-privécloud die toegankelijk is voor gebruikers en toepassingen van on-premises omgevingen, door het bedrijf beheerde apparaten en Azure-resources. De connectiviteit wordt geleverd via netwerk services, zoals Vpn's en Azure ExpressRoute-verbindingen. Voor sommige van deze netwerk services moet u netwerk adresbereiken opgeven voor het inschakelen van de services. 

In de tabellen in dit artikel wordt de set adresbereiken en de bijbehorende services beschreven die gebruikmaken van de opgegeven adressen. Sommige adressen zijn verplicht en sommige zijn afhankelijk van de services die u wilt implementeren. Deze adres ruimten mogen niet overlappen met een van uw on-premises subnetten, Azure Virtual Network subnetten of geplande CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Netwerk adresbereiken vereist voor het maken van een privécloud

Tijdens het maken van een CloudSimple-service en een privécloud moet u als volgt voldoen aan de gespecificeerde CIDR-bereiken (Classless Inter-Domain Routing) van het netwerk.

| Naam/gebruikt voor     | Beschrijving                                                                                                                            | Adresbereik            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway-CIDR      | Vereist voor Edge-Services (VPN-gateways).  Deze CIDR is vereist tijdens het maken van de CloudSimple-service en moet afkomstig zijn uit de RFC 1918-ruimte. | /28                      |
| vSphere/vSAN CIDR | Vereist voor VMware-beheer netwerken. Deze CIDR moet worden opgegeven tijdens het maken van een privécloud.                                    | /24 of/23 of/22 of/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Het netwerk adres bereik dat is vereist voor een Azure-netwerk verbinding met een on-premises netwerk

Als u verbinding maakt vanaf een [on-premises netwerk met het particuliere cloud netwerk via ExpressRoute](on-premises-connection.md) , wordt er een Global Reach verbinding tot stand gebracht.  De verbinding maakt gebruik van Border Gateway Protocol (BGP) voor het uitwisselen van routes tussen uw on-premises netwerk, uw particuliere cloud netwerk en uw Azure-netwerken.

| Naam/gebruikt voor             | Beschrijving                                                                                                                                                                             | Adresbereik |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute-peering CIDR | Vereist wanneer u ExpressRoute-Global Reach gebruikt voor on-premises connectiviteit. Deze CIDR moet worden opgegeven wanneer een Global Reach verbindings aanvraag wordt gedaan via een ondersteunings ticket. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Het netwerk adres bereik dat is vereist voor het gebruik van een site-naar-site-VPN-verbinding met een on-premises netwerk

Voor het verbinden van een [on-premises netwerk met het particuliere cloud netwerk met behulp van site-naar-site-VPN](vpn-gateway.md) zijn de volgende IP-adressen, het on-premises netwerk en de id's vereist. 

| Adres/adres bereik | Beschrijving                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer-IP               | Openbaar IP-adres van on-premises VPN-gateway. Vereist voor het tot stand brengen van een site-naar-site-VPN-verbinding tussen een on-premises Data Center en de CloudSimple-service regio. Dit IP-adres is vereist tijdens het maken van de site-naar-site-VPN-gateway.                                         |
| Peer-id       | De peer-id van de on-premises VPN-gateway. Dit is meestal hetzelfde als het **IP-adres** van de peer.  Als er een unieke id is opgegeven op uw on-premises VPN-gateway, moet de id worden opgegeven.  De peer-ID is vereist tijdens het maken van de site-naar-site-VPN-gateway.   |
| On-premises netwerken   | On-premises voor voegsels waarvoor toegang is vereist tot CloudSimple netwerken in de regio.  Neem alle voor voegsels van een on-premises netwerk op dat toegang heeft tot het CloudSimple-netwerk, met inbegrip van het client netwerk van waaruit gebruikers toegang hebben tot het netwerk.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Het netwerk adres bereik dat is vereist voor het gebruik van punt-naar-site-VPN-verbindingen

Een punt-naar-site-VPN-verbinding maakt toegang tot het CloudSimple-netwerk vanaf een client computer mogelijk.  [Als u punt-naar-site-VPN wilt instellen](vpn-gateway.md), moet u het volgende netwerk adres bereik opgeven.

| Adres/adres bereik | Beschrijving                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Client subnet         | DHCP-adressen worden door het client-subnet gegeven wanneer u verbinding maakt met behulp van een punt-naar-site-VPN. Dit subnet is vereist tijdens het maken van een punt-naar-site-VPN-gateway in een CloudSimple-Portal.  Het netwerk is onderverdeeld in twee subnetten. een voor de UDP-verbinding en de andere voor TCP-verbindingen. |

## <a name="next-steps"></a>Volgende stappen

* [On-premises firewall instellen voor toegang tot uw privécloud](on-premises-firewall-configuration.md)
* [Quick Start: een CloudSimple-service maken](quickstart-create-cloudsimple-service.md)
* [Snelstartgids-een privécloud configureren](quickstart-create-private-cloud.md)
* Meer informatie over [Azure-netwerk verbindingen](cloudsimple-azure-network-connection.md)
* Meer informatie over [VPN-gateways](cloudsimple-vpn-gateways.md)
