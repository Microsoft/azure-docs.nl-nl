---
title: Veelgestelde vragen over Azure route server
description: Vind antwoorden op veelgestelde vragen over Azure route server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: c4c36013f100d2fc5265024432cc01a6622a4024
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932366"
---
# <a name="azure-route-server-preview-faq"></a>Veelgestelde vragen over Azure route server (preview)

> [!IMPORTANT]
> Azure route server (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="what-is-azure-route-server"></a>Wat is Azure route server?

Azure route server is een volledig beheerde service waarmee u eenvoudig route ring tussen uw virtuele netwerk apparaat (NVA) en uw virtuele netwerk kunt beheren.

### <a name="is-azure-route-server-just-a-vm"></a>Is Azure route server alleen een VM?

Nee. Azure route server is een service die is ontworpen met hoge Beschik baarheid. Als de app wordt geïmplementeerd in een Azure-regio die [Beschikbaarheidszones](../availability-zones/az-overview.md)ondersteunt, heeft deze een zone-niveau redundantie.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Welke routerings protocollen ondersteunt Azure route server?

Azure route server ondersteunt alleen Border Gateway Protocol (BGP). Uw NVA moet ondersteuning bieden voor externe BGP met meerdere hops omdat u Azure route server moet implementeren in een toegewezen subnet in het virtuele netwerk. De door u gekozen [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) moet verschillen van de Azure-route server die wordt gebruikt wanneer u de BGP op uw NVA configureert.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Routeert Azure route Server gegevens verkeer tussen mijn NVA en mijn Vm's?

Nee. Azure route Server Exchange de BGP-routes alleen met uw NVA. Het gegevens verkeer gaat rechtstreeks van de NVA naar de gekozen virtuele machine en rechtstreeks van de virtuele machine naar de NVA.

### <a name="does-azure-route-server-store-customer-data"></a>Slaat Azure route server klant gegevens op?
Nee. Met Azure route server worden BGP-routes alleen met uw NVA uitgewisseld en vervolgens door gegeven aan uw virtuele netwerk.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Als Azure route server dezelfde route van meer dan één NVA ontvangt, worden alle kopieën van de route (maar elk met een andere volgende hop) naar de Vm's in het virtuele netwerk geprogrammeerd?

Ja, alleen als de route gelijk is aan de lengte van het pad. Wanneer de Vm's verkeer naar het doel van deze route verzenden, wordt Equal-Cost Multi-Path (ECMP)-route ring door de VM-hosts uitgevoerd. Als een NVA echter de route verzendt met een kortere padlengte dan andere Nva's. De route server van Azure die de volgende hop heeft ingesteld voor deze NVA, wordt alleen naar de virtuele machines in het virtuele netwerk geleid.

### <a name="does-azure-route-server-support-vnet-peering"></a>Ondersteunt Azure route server VNet-peering?

Ja. Als u een VNet peert die de Azure route server host naar een ander VNet en u de externe gateway gebruiken op dat VNet inschakelt. Azure route server leert de adres ruimten van dat VNet en verzendt deze naar alle gepeerde Nva's.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Welke autonome systeem nummers (Asn's) kan ik gebruiken?

U kunt uw eigen open bare Asn's of particuliere Asn's gebruiken in uw virtuele netwerk apparaat. U kunt de bereiken die door Azure of IANA zijn gereserveerd niet gebruiken.
De volgende ASN's zijn gereserveerd voor Azure of IANA:

* ASN's die zijn gereserveerd voor Azure:
    * Openbare ASN's: 8074, 8075, 12076
    * Privé-ASNs: 65515, 65517, 65518, 65519, 65520
* ASN's [die zijn gereserveerd door IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kan ik 32-bits (4-bytes) Asn's gebruiken?

Nee, Azure route server ondersteunt alleen 16-bits (2 bytes) Asn's.

## <a name="route-server-limits"></a><a name = "limitations"></a>Route server limieten

Voor de Azure route Server gelden de volgende limieten (per implementatie).

| Resource | Limiet |
|----------|-------|
| Aantal ondersteunde BGP-peers | 8 |
| Aantal routes dat elke BGP-peer kan adverteren naar Azure route server | 200 |
| Aantal routes dat door Azure route server kan worden geadverteerd naar ExpressRoute of VPN-gateway | 200 |

Als uw NVA meer routes adverteert dan de limiet, wordt de BGP-sessie verwijderd. Als dit gebeurt met de gateway en Azure route server, verliest u de connectiviteit van uw on-premises netwerk naar Azure. Zie voor meer informatie [een probleem met de route ring van een Azure virtual machine diagnosticeren](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van Azure route server](quickstart-configure-route-server-powershell.md).
