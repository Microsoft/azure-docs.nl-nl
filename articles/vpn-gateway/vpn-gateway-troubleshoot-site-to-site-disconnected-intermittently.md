---
title: Problemen met een VPN-verbinding tussen sites met Azure oplossen
description: Informatie over het oplossen van het probleem waarbij de site-naar-site-VPN-verbinding regel matig is verbroken.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 63c9bd93e75533e54c42c2041cb9f55a1f0036b6
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872142"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Probleemoplossing: Azure site-naar-site-VPN wordt herhaaldelijk verbroken

U kunt het probleem ondervinden dat een nieuwe of bestaande Microsoft Azure site-naar-site-VPN-verbinding niet stabiel is of regel matig de verbinding verbreekt. In dit artikel worden de stappen beschreven waarmee u de oorzaak van het probleem kunt vaststellen en oplossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

### <a name="prerequisite-step"></a>Vereiste stap

Controleer het type van de gateway van het virtuele Azure-netwerk:

1. Ga naar [Azure Portal](https://portal.azure.com).
2. Controleer de **overzichts** pagina van de virtuele netwerk gateway voor de type-informatie.
    
    ![Het overzicht van de gateway](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Stap 1 Controleer of het on-premises VPN-apparaat is gevalideerd

1. Controleer of u een [gevalideerd VPN-apparaat en de versie van het besturings systeem](vpn-gateway-about-vpn-devices.md#devicetable)gebruikt. Als het VPN-apparaat niet is gevalideerd, moet u wellicht contact opnemen met de fabrikant van het apparaat om te zien of er compatibiliteits problemen zijn.
2. Controleer of het VPN-apparaat correct is geconfigureerd. Zie voor meer informatie [apparaat configureren voor beelden](vpn-gateway-about-vpn-devices.md#editing)van het bewerken van apparaatconfiguratie.

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Stap 2 Controleer de instellingen van de beveiligings koppeling (voor Azure Virtual Network-gateways op basis van beleid)

1. Zorg ervoor dat het virtuele netwerk, subnetten en, bereiken in de definitie van de **lokale netwerk gateway** in Microsoft Azure hetzelfde zijn als de configuratie op het on-PREMISES VPN-apparaat.
2. Controleer of de instellingen van de beveiligings koppeling overeenkomen.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Stap 3 controleren op User-Defined routes of netwerk beveiligings groepen op Gateway subnet

Een door de gebruiker gedefinieerde route op het gateway-subnet kan een beperkt verkeer beperken en ander verkeer toestaan. Hierdoor lijkt het alsof de VPN-verbinding onbetrouwbaar is voor een bepaald verkeer en goed voor anderen. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Stap 4 Schakel de instelling ' Eén VPN-tunnel per subnet paar ' in (voor op beleid gebaseerde virtuele netwerk gateways)

Zorg ervoor dat het on-premises VPN-apparaat is ingesteld op **één VPN-tunnel per subnet paar** voor virtuele netwerk gateways op basis van beleid.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Stap 5 controleren op beveiligings koppelings beperkingen (voor virtuele netwerk gateways op basis van beleid)

De op beleid gebaseerde virtuele netwerk gateway heeft limiet van 200 subnet-beveiligings koppelings paren. Als het aantal subnetten van het virtuele Azure-netwerk vermenigvuldigd met het aantal lokale subnetten groter is dan 200, ziet u sporadische subnetten die de verbinding verbreken.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Stap 6 het externe interface adres van on-premises VPN-apparaat controleren

- Als het Internet gericht IP-adres van het VPN-apparaat is opgenomen in de definitie van de **lokale netwerk gateway** in azure, kunnen er soms problemen optreden met de verbinding.
- De externe interface van het apparaat moet rechtstreeks op internet zijn. Er mag geen NAT (Network Address Translation) of firewall tussen internet en het apparaat zijn.
-  Als u Firewall clusters wilt configureren voor een virtueel IP-adres, moet u het cluster verbreekt en het VPN-apparaat rechtstreeks beschikbaar maken op een open bare interface waarmee de gateway een interface kan maken.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Stap 7 Controleer of het on-premises VPN-apparaat Perfect Forward Secrecy is ingeschakeld

De **perfecte functie voor Forward Secrecy** kan problemen met de verbinding veroorzaken. Als het VPN-apparaat **Perfect Forward Secrecy** is ingeschakeld, schakelt u de functie uit. [Werk vervolgens het IPsec-beleid van de virtuele netwerk gateway](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)bij.

## <a name="next-steps"></a>Volgende stappen

- [Een site-naar-site-verbinding met een virtueel netwerk configureren](./tutorial-site-to-site-portal.md)
- [IPsec/IKE-beleid configureren voor site-naar-site-VPN-verbindingen](vpn-gateway-ipsecikepolicy-rm-powershell.md)