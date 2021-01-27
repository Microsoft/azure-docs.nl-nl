---
title: 'Extern werken met P2S: Azure VPN Gateway'
description: Op deze pagina wordt beschreven hoe u VPN Gateway kunt gebruiken om extern te werken vanwege de COVID-19 Pandemic.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880081"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Extern werk met Azure VPN Gateway punt-naar-site

>[!NOTE]
>In dit artikel wordt beschreven hoe u Azure VPN Gateway, azure, micro soft Network en het Azure-partner ecosysteem kunt gebruiken om op afstand te werken en om netwerk problemen te verhelpen die u tegen komt vanwege COVID-19-crisis.
>

In dit artikel worden de opties beschreven die beschikbaar zijn voor organisaties om externe toegang voor hun gebruikers in te stellen of om hun bestaande oplossingen aan te vullen met extra capaciteit tijdens de COVID-19-epidemie.

De Azure Point-to-site-oplossing is in de cloud gebaseerd en kan snel worden ingericht voor de verhoogde vraag van gebruikers om vanaf thuis te werken. Het kan eenvoudig en snel omhoog en omlaag worden geschaald wanneer de verhoogde capaciteit niet meer nodig is.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Over punt-naar-site-VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor telewerkers die verbinding willen maken met Azure VNets of on-premises data centers vanaf een externe locatie, zoals vanaf thuis of een conferentie. In dit artikel wordt beschreven hoe u gebruikers in staat stelt om op afstand te werken op basis van verschillende scenario's.

In de volgende tabel ziet u de client besturingssystemen en de beschik bare verificatie opties. Het is handig om de verificatie methode te selecteren op basis van het client besturingssysteem dat al in gebruik is. Selecteer bijvoorbeeld OpenVPN met verificatie op basis van certificaten als u een combi natie van client besturingssystemen hebt die verbinding moeten maken. Houd er ook rekening mee dat punt-naar-site-VPN alleen wordt ondersteund op op route gebaseerde VPN-gateways.

![Scherm opname van client besturingssystemen en beschik bare verificatie opties.](./media/working-remotely-support/ostable.png "Besturingssysteem")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenario 1: gebruikers moeten alleen toegang hebben tot resources in azure

In dit scenario hebben de externe gebruikers alleen toegang tot resources in Azure.

![Diagram waarin een punt-naar-site-scenario wordt weer gegeven voor gebruikers die alleen toegang tot resources in azure nodig hebben.](./media/working-remotely-support/scenario1.png "Scenario 1")

Op hoog niveau zijn de volgende stappen nodig om gebruikers in staat te stellen om veilig verbinding te maken met Azure-bronnen:

1. Een virtuele netwerk gateway maken (als deze niet bestaat).
2. Configureer punt-naar-site-VPN op de gateway.

   * Volg [deze koppeling](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)voor verificatie via certificaat.
   * Volg voor OpenVPN [deze koppeling](vpn-gateway-howto-openvpn.md).
   * Volg [deze koppeling](openvpn-azure-ad-tenant.md)voor Azure AD-verificatie.
   * Voor het oplossen van problemen met Point-to-site-verbindingen volgt u [deze koppeling](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. De configuratie van de VPN-client downloaden en distribueren.
4. Distribueer de certificaten (als certificaat verificatie is geselecteerd) aan de clients.
5. Verbinding maken met Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenario 2: gebruikers moeten toegang hebben tot resources in Azure en/of on-premises resources

In dit scenario moeten de externe gebruikers toegang hebben tot resources die zich in azure bevinden en in het on-premises Data Center (s).

![Diagram waarin een punt-naar-site-scenario wordt weer gegeven voor gebruikers die toegang nodig hebben tot resources in Azure.](./media/working-remotely-support/scenario2.png "Scenario 2")

Op hoog niveau zijn de volgende stappen nodig om gebruikers in staat te stellen om veilig verbinding te maken met Azure-bronnen:

1. Een virtuele netwerk gateway maken (als deze niet bestaat).
2. Configureer punt-naar-site-VPN op de gateway (Zie [scenario 1](#scenario1)).
3. Configureer een site-naar-site-tunnel op de gateway van het virtuele Azure-netwerk waarvoor BGP is ingeschakeld.
4. Configureer het on-premises apparaat om verbinding te maken met de gateway van het virtuele Azure-netwerk.
5. Het punt-naar-site-profiel downloaden van de Azure Portal en distribueren naar clients

Zie [deze koppeling](./tutorial-site-to-site-portal.md)voor meer informatie over het instellen van een site-naar-site-VPN-tunnel.

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Veelgestelde vragen over systeem eigen Azure-certificaat verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Veelgestelde vragen over RADIUS-verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren-Azure AD-verificatie](openvpn-azure-ad-tenant.md)

* [Een P2S-verbinding configureren-RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren-Azure systeem eigen certificaat authenticatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**' OpenVPN ' is een handels merk van OpenVPN Inc.**