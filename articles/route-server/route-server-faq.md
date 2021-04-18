---
title: Veelgestelde vragen over Azure Route Server
description: Vind antwoorden op veelgestelde vragen over Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 0bbe16fb63a4546b4b4745df16074f6a4b0cb26b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599533"
---
# <a name="azure-route-server-preview-faq"></a>Veelgestelde vragen over Azure Route Server (preview)

> [!IMPORTANT]
> Azure Route Server (preview) is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="what-is-azure-route-server"></a>Wat is Azure Route Server?

Azure Route Server is een volledig beheerde service waarmee u eenvoudig routering kunt beheren tussen uw virtuele netwerkapparaat (NVA) en uw virtuele netwerk.

### <a name="is-azure-route-server-just-a-vm"></a>Is Azure Route Server slechts een VM?

Nee. Azure Route Server is een service die is ontworpen met hoge beschikbaarheid. Als deze is geïmplementeerd in een Azure-regio die ondersteuning biedt [Beschikbaarheidszones,](../availability-zones/az-overview.md)heeft deze redundantie op zoneniveau.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Hoeveel routeservers kan ik maken in een virtueel netwerk?

U kunt slechts één routeserver in een VNet maken. Deze moet worden geïmplementeerd in een aangewezen subnet met de *naam RouteServerSubnet.*

### <a name="does-azure-route-server-support-vnet-peering"></a>Ondersteunt Azure Route Server VNet-peering?

Ja. Als u een VNet dat als host voor de Azure Route-server host, aan een ander VNet peert en u Externe gateway op het laatste VNet gebruiken inschakelen, leert Azure Route Server de adresruimten van dat VNet en verzendt deze naar alle peered N NVA's. Ook worden de routes van de NNA's geprogrammaeerd naar de routeringstabel van de VM's in het peered VNet. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Welke routeringsprotocollen worden ondersteund door Azure Route Server?

Azure Route Server ondersteunt Border Gateway Protocol (BGP). Uw NVA moet externe BGP met meerdere hops ondersteunen, omdat u Azure Route Server moet implementeren in een toegewezen subnet in uw virtuele netwerk. De [ASN die](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) u kiest, moet verschillen van de ASN die Azure Route Server gebruikt wanneer u de BGP op uw NVA configureert.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Routeert Azure Route Server gegevensverkeer tussen mijn NVA en mijn VM's?

Nee. Azure Route Server wisselt alleen BGP-routes uit met uw NVA. Het gegevensverkeer gaat rechtstreeks van de NVA naar de doel-VM en rechtstreeks van de VM naar de NVA.

### <a name="does-azure-route-server-store-customer-data"></a>Worden klantgegevens opgeslagen in Azure Route Server?
Nee. Azure Route Server wisselt alleen BGP-routes uit met uw NVA en geeft deze vervolgens door aan uw virtuele netwerk.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Als Azure Route Server dezelfde route van meer dan één NVA ontvangt, hoe worden deze dan verwerkt?

Als de route dezelfde AS-padlengte heeft, programmeert Azure Route Server meerdere kopieën van de route, elk met een andere volgende hop, naar de VM's in het virtuele netwerk. Wanneer de VM's verkeer naar de bestemming van deze route verzenden, worden de VM-hosts Equal-Cost ECMP-routering (Multi-Path). Als één NVA echter de route verzendt met een kortere AS-padlengte dan andere NVA's, programmeert Azure Route Server alleen de route waarin de volgende hop is ingesteld op deze NVA naar de virtuele machines in het virtuele netwerk.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Behoudt Azure Route Server de BGP-community's van de route die deze ontvangt?

Ja, Azure Route Server geeft de route als het goed is door aan de BGP-community's.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Welke autonome systeemnummers (ASN's) kan ik gebruiken?

U kunt uw eigen openbare ASN's of persoonlijke ASN's gebruiken in het virtuele netwerkapparaat. U kunt de bereiken die door Azure of IANA zijn gereserveerd niet gebruiken.
De volgende ASN's zijn gereserveerd voor Azure of IANA:

* ASN's die zijn gereserveerd voor Azure:
    * Openbare ASN's: 8074, 8075, 12076
    * Privé-ASNs: 65515, 65517, 65518, 65519, 65520
* ASN's [die zijn gereserveerd door IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kan ik 32-bits (4 byte) ASN's gebruiken?

Nee, Azure Route Server ondersteunt alleen 16-bits ASN's (2 bytes).

## <a name="route-server-limits"></a><a name = "limitations"></a>Limieten voor routeservers

Azure Route Server heeft de volgende limieten (per implementatie).

| Resource | Limiet |
|----------|-------|
| Aantal ondersteunde BGP-peers | 8 |
| Aantal routes dat elke BGP-peer kan adverteren naar Azure Route Server | 200 |
| Aantal routes dat Azure Route Server kan adverteren naar ExpressRoute of VPN-gateway | 200 |

Als uw NVA meer routes adverteert dan de limiet, wordt de BGP-sessie gestopt. Als dit gebeurt bij de gateway en Azure Route Server, verliest u de connectiviteit van uw on-premises netwerk met Azure. Zie Diagnose an [Azure virtual machine routing problem (Diagnose uitvoeren voor een routeringsprobleem met virtuele Azure-machines) voor meer informatie.](../virtual-network/diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van Azure Route Server.](quickstart-configure-route-server-powershell.md)
