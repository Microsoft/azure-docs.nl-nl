---
title: 'Azure ExpressRoute: over versleuteling'
description: Meer informatie over ExpressRoute-versleuteling.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 693d2304324bdfcac298b3e20ddd0d882a16533c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899859"
---
# <a name="expressroute-encryption"></a>ExpressRoute-versleuteling
 
ExpressRoute ondersteunt een aantal versleutelings technologieën om de vertrouwelijkheid en integriteit te waarborgen van de gegevens die tussen uw netwerk en het netwerk van micro soft worden gepasseerd.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Point-to-Point Encryption by MACsec FAQ
MACsec is een [IEEE-standaard](https://1.ieee802.org/security/802-1ae/). De gegevens worden versleuteld op het MAC-niveau (Media Access Control) of netwerklaag 2. U kunt MACsec gebruiken om de fysieke koppelingen tussen uw netwerk apparaten en de netwerk apparaten van micro soft te versleutelen wanneer u via [ExpressRoute direct](expressroute-erdirect-about.md)verbinding maakt met micro soft. MACsec is standaard uitgeschakeld op ExpressRoute direct-poorten. U brengt uw eigen MACsec-sleutel voor versleuteling en slaat deze op in [Azure Key Vault](../key-vault/general/overview.md). U bepaalt wanneer u de sleutel wilt draaien. Zie andere veelgestelde vragen hieronder.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Kan ik MACsec inschakelen op mijn ExpressRoute-circuit dat is ingericht door een ExpressRoute-provider?
Nee. MACsec versleutelt al het verkeer op een fysieke koppeling met een sleutel die eigendom is van één entiteit (d.w.z. klant). Daarom is het alleen beschikbaar op ExpressRoute direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Kan ik een aantal van de ExpressRoute-circuits op mijn ExpressRoute direct-poorten versleutelen en andere circuits op dezelfde poorten niet-versleuteld houden? 
Nee. Zodra MACsec is ingeschakeld, is alle netwerk besturings verkeer, bijvoorbeeld het BGP-gegevens verkeer, en klant gegevens verkeer versleuteld. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Wanneer ik MACsec of update MACsec-sleutel inschakel of uitschakelt, verliest mijn on-premises netwerk connectiviteit met micro soft via ExpressRoute?
Ja. Voor de configuratie van MACsec ondersteunen we alleen de modus vooraf gedeelde sleutels. Dit betekent dat u de sleutel moet bijwerken op zowel uw apparaten als op micro soft (via onze API). Deze wijziging is niet atomisch, waardoor u geen verbinding meer hebt wanneer er een niet-overeenkomende sleutel is tussen de twee zijden. We raden u ten zeerste aan om een onderhouds venster te plannen voor de configuratie wijziging. Om de uitval tijd te minimaliseren, wordt u aangeraden de configuratie op één koppeling van ExpressRoute direct op een later tijdstip bij te werken nadat u uw netwerk verkeer naar de andere koppeling hebt overgeschakeld.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Loopt verkeer door stroom als de MACsec-sleutel niet overeenkomt tussen mijn apparaten en micro soft?
Nee. Als MACsec is geconfigureerd en er een niet-overeenkomende sleutel optreedt, verliest u de verbinding met micro soft. Met andere woorden, we vallen niet terug naar een niet-versleutelde verbinding, waardoor uw gegevens zichtbaar worden. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Schakelt MACsec in op ExpressRoute directe prestaties van het netwerk verminderen?
MACsec-versleuteling en-ontsleuteling vindt plaats in hardware op de routers die we gebruiken. Er is geen invloed op de prestaties van onze kant. Neem echter contact op met de leverancier van het netwerk voor de apparaten die u gebruikt en controleer of MACsec prestatie implicatie heeft.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Welke coderings suites worden ondersteund voor versleuteling?
Alleen de [uitgebreide pakket nummering](https://1.ieee802.org/security/802-1aebw/) versie van AES-128 en AES-256 wordt ondersteund. U moet ook de [Secure Channel Identifier (SCI)](https://wikipedia.org/wiki/IEEE_802.1AE) uitschakelen in de MACsec-configuratie op het apparaat. 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Veelgestelde vragen over end-to-end-versleuteling per IPsec
IPsec is een [IETF-standaard](https://tools.ietf.org/html/rfc6071). De gegevens worden versleuteld op het niveau van de Internet Protocol (IP) of netwerklaag 3. U kunt IPsec gebruiken voor het versleutelen van een end-to-end-verbinding tussen uw on-premises netwerk en uw virtuele netwerk (VNET) in Azure. Zie andere veelgestelde vragen hieronder.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Kan ik IPsec inschakelen naast MACsec op mijn ExpressRoute direct-poorten?
Ja. MACsec beveiligt de fysieke verbindingen tussen u en micro soft. IPsec beveiligt de end-to-end-verbinding tussen u en uw virtuele netwerken in Azure. U kunt deze onafhankelijk van elkaar inschakelen. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Kan ik Azure VPN-gateway gebruiken om de IPsec-tunnel in te stellen via persoonlijke Azure-peering?
Ja. Als u Azure Virtual WAN aanneemt, kunt u [deze stappen](../virtual-wan/vpn-over-expressroute.md) volgen om de end-to-end-verbinding te versleutelen. Als u een reguliere Azure-VNET hebt, kunt u [deze stappen](../vpn-gateway/site-to-site-vpn-private-peering.md) volgen om een IPSec-tunnel in te richten tussen Azure VPN gateway en uw on-PREMISES VPN-gateway.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Wat is de door Voer die ik ontvang nadat IPsec op mijn ExpressRoute-verbinding is ingeschakeld?
Als Azure VPN-gateway wordt gebruikt, controleert u [hier de prestatie aantallen](../vpn-gateway/vpn-gateway-about-vpngateways.md). Als er een VPN-gateway van derden wordt gebruikt, controleert u bij de leverancier voor de prestatie aantallen.

## <a name="next-steps"></a>Volgende stappen
Zie [MACsec configureren](expressroute-howto-macsec.md) voor meer informatie over de MACsec-configuratie.

Zie [Configure IPSec](site-to-site-vpn-over-microsoft-peering.md) (Engelstalig) voor meer informatie over de IPSec-configuratie.
