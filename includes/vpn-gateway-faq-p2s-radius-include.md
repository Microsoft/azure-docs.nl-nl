---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b802cab7d52fd886770c326e97d0378a0da64762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75751572"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>Wordt RADIUS-verificatie ondersteund op alle Azure VPN Gateway SKU’s?

RADIUS-verificatie wordt ondersteund voor de SKU's VpnGw1, VpnGw2 en VpnGw3. Als u verouderde SKU's gebruikt, wordt RADIUS-verificatie ondersteund op Standard- en High Performance-SKU's. Het wordt niet ondersteund op de Basic Gateway-SKU. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>Wordt RADIUS-verificatie ondersteund voor het klassieke implementatiemodel?
 
Nee. RADIUS-verificatie wordt niet ondersteund voor het klassieke implementatiemodel.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Worden RADIUS-servers van derden ondersteund?

Ja, RADIUS-servers van derden worden ondersteund.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Wat zijn de connectiviteitsvereisten om ervoor te zorgen dat de Azure-gateway een on-premises RADIUS-server kan bereiken?

Er is een site-to-site VPN-verbinding met de on-premises site nodig waarop de juiste routes zijn geconfigureerd.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Kan het verkeer naar een on-premises RADIUS-server (van de Azure VPN-gateway) worden gerouteerd via een ExpressRoute-verbinding?

Nee. Dit verkeer kan alleen worden gerouteerd via een site-to-site-verbinding.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Is er een wijziging in het aantal SSTP-verbindingen dat met RADIUS-verificatie wordt ondersteund? Wat is het maximumaantal ondersteunde SSTP- en IKEv2-verbindingen?

Het maximumaantal SSTP-verbindingen dat op een gateway met RADIUS-verificatie wordt ondersteund, is niet gewijzigd. Dit blijft 128 voor SSTP, maar is afhankelijk van de gateway-SKU voor IKEv2.  Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) voor meer informatie over het aantal ondersteunde verbindingen.
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>Wat is het verschil tussen certificaatverificatie met behulp van een RADIUS-server en de systeemeigen verificatie van Azure (door een vertrouwd certificaat naar Azure te uploaden)?

Bij RADIUS-certificaatverificatie wordt de verificatieaanvraag doorgestuurd naar een RADIUS-server, waar de werkelijke certificaatvalidatie wordt uitgevoerd. Deze optie is nuttig als u via RADIUS wilt integreren met een certificaatverificatie-infrastructuur die u al hebt.
  
Wanneer u Azure gebruikt voor certificaatverificatie, voert de Azure VPN-gateway de validatie van het certificaat uit. U moet de openbare sleutel van uw certificaat uploaden naar de gateway. U kunt ook een lijst opgeven met ingetrokken certificaten die niet mogen worden gebruikt om verbinding te maken.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>Werkt RADIUS-verificatie met zowel IKEv2 als SSTP VPN?

Ja, RADIUS-verificatie wordt ondersteund voor zowel IKEv2 als SSTP VPN. 

### <a name="does-radius-authentication-work-with-the-openvpn-client"></a>Werkt RADIUS-verificatie met de OpenVPN client?

RADIUS-verificatie wordt ondersteund voor het OpenVPN-protocol via PowerShell.
