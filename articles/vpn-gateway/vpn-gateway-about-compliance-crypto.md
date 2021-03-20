---
title: 'Azure VPN Gateway: cryptografische vereisten'
description: Meer informatie over het configureren van Azure VPN-gateways om te voldoen aan cryptografische vereisten voor zowel cross-premises S2S VPN-tunnels als Azure VNet-naar-VNet-verbindingen.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 47d14c5ee7f6c4816bf15351e9cb28a2aaa72b4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546842"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Over cryptografische vereisten en Azure VPN-gateways

In dit artikel wordt beschreven hoe u Azure VPN-gateways kunt configureren om te voldoen aan uw cryptografische vereisten voor zowel cross-premises S2S VPN-tunnels als VNet-naar-VNet-verbindingen in Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Over IKEv1 en IKEv2 voor Azure VPN-verbindingen

Traditioneel hebben we IKEv1-verbindingen voor alleen elementaire Sku's en toegestane IKEv2-verbindingen voor alle andere VPN-gateway-Sku's dan Basic Sku's toegestaan. De Basic-Sku's staan slechts 1 verbinding toe en samen met andere beperkingen, zoals prestaties, kunnen klanten die gebruikmaken van verouderde apparaten die alleen IKEv1-protocollen ondersteunen, een beperkte ervaring hebben. Om de gebruikers ervaring met IKEv1-protocollen te verbeteren, zijn er nu IKEv1-verbindingen voor alle VPN-gateway-Sku's, met uitzonde ring van de basis-SKU. Zie [VPN gateway sku's](./vpn-gateway-about-vpn-gateway-settings.md#gwsku)voor meer informatie.

![Azure VPN Gateway IKEv1-en IKEv2-verbindingen](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Wanneer IKEv1-en IKEv2-verbindingen worden toegepast op dezelfde VPN-gateway, wordt de door Voer tussen deze twee verbindingen automatisch ingeschakeld.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>IPsec-en IKE-beleids parameters voor Azure VPN-gateways

IPsec en IKE-protocol standaard bieden ondersteuning voor een breed scala van cryptografische algoritmen in verschillende combi Naties. Als u geen specifieke combi natie van cryptografische algoritmen en para meters aanvraagt, gebruiken Azure VPN-gateways een set standaard voorstellen. De standaard beleids sets zijn gekozen om de interoperabiliteit met een breed scala aan VPN-apparaten van derden in standaard configuraties te maximaliseren. Als gevolg hiervan kunnen de beleids regels en het aantal Voorst Ellen niet alle mogelijke combi Naties van beschik bare cryptografische algoritmen en sleutel sterkten beslaan.

### <a name="default-policy"></a>Standaard beleid

Het standaard beleid dat is ingesteld voor Azure VPN-gateway wordt weer gegeven in het artikel: [over VPN-apparaten en IPSec/IKE-para meters voor site-naar-site-VPN gateway verbindingen](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Cryptografische vereisten

Voor communicatie waarvoor specifieke cryptografische algoritmen of para meters vereist zijn, is het doorgaans mogelijk om de Azure VPN-gateways te configureren voor het gebruik van een aangepast IPsec/IKE-beleid met specifieke cryptografische algoritmen en sleutel sterkten, in plaats van de standaard-beleids sets van Azure.

Bijvoorbeeld: het IKEv2-beleid voor de hoofd modus voor Azure VPN-gateways maakt gebruik van alleen Diffie-Hellman groep 2 (1024 bits), maar u moet mogelijk sterkere groepen opgeven die moeten worden gebruikt in IKE, zoals groep 14 (2048-bits), groep 24 (2048-bits MODP Group) of ECP (elliptische curve groepen) 256 of 384 bits (respectievelijk groep 19 en groep 20). Vergelijk bare vereisten zijn ook van toepassing op IPsec-beleid voor snelle modus.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Aangepast IPsec/IKE-beleid met Azure VPN-gateways

Azure VPN-gateways bieden nu ondersteuning voor het aangepaste IPsec/IKE-beleid per verbinding. Voor een site-naar-site-of VNet-naar-VNet-verbinding kunt u een specifieke combi natie van cryptografische algoritmen kiezen voor IPsec en IKE met de gewenste sleutel sterkte, zoals wordt weer gegeven in het volgende voor beeld:

![IPSec-IKE-beleid](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

U kunt een IPsec/IKE-beleid maken en Toep assen op een nieuwe of bestaande verbinding.

### <a name="workflow"></a>Werkstroom

1. Maak de virtuele netwerken, VPN-gateways of lokale netwerk gateways voor uw connectiviteits topologie zoals beschreven in andere documenten
2. Een IPsec/IKE-beleid maken
3. U kunt het beleid Toep assen wanneer u een S2S-of VNet-naar-VNet-verbinding maakt
4. Als de verbinding al is gemaakt, kunt u het beleid Toep assen of bijwerken voor een bestaande verbinding

## <a name="ipsecike-policy-faq"></a>Veelgestelde vragen over IPsec/IKE-beleid

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [IPSec/IKE-beleid configureren](vpn-gateway-ipsecikepolicy-rm-powershell.md) voor stapsgewijze instructies over het configureren van aangepaste IPSec/IKE-beleid voor een verbinding.

Zie ook [meerdere op beleid gebaseerde VPN-apparaten verbinden](vpn-gateway-connect-multiple-policybased-rm-ps.md) voor meer informatie over de UsePolicyBasedTrafficSelectors-optie.
