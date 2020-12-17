---
title: Over Azure Point-to-site VPN-verbindingen | VPN Gateway
description: Dit artikel helpt u bij het begrijpen van Point-to-site-verbindingen en helpt u bij het bepalen van welk P2S VPN-gateway-verificatie type u wilt gebruiken.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 795b6f13913590041b463115c0be65a6201fedab
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654061"
---
# <a name="about-point-to-site-vpn"></a>Over punt-naar-site-VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor telewerkers die verbinding willen maken met een Azure VNet vanaf een externe locatie, zoals vanaf thuis of een conferentie. P2S-VPN is ook een uitstekende oplossing in plaats van een S2S-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Dit artikel is van toepassing op het Resource Manager-implementatiemodel.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Welk protocol gebruikt P2S?

Punt-naar-site-VPN kan gebruikmaken van een van de volgende protocollen:

* **OpenVPN®-protocol**, een op SSL/TLS gebaseerd VPN-protocol. Een TLS-VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls open TCP-poort 443 uitgaand, die TLS gebruikt. OpenVPN kan worden gebruikt om verbinding te maken vanaf Android, iOS (versie 11,0 en hoger), Windows, Linux en Mac-apparaten (OSX-versies 10,13 en hoger).

* SSTP (Secure Socket Tunneling Protocol), een eigen op TLS gebaseerd VPN-protocol. Een TLS-VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls open TCP-poort 443 uitgaand, die TLS gebruikt. SSTP wordt alleen ondersteund op Windows-apparaten. Azure ondersteunt alle versies van Windows die SSTP (Windows 7 en hoger) hebben.

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).


>[!NOTE]
>IKEv2 en OpenVPN voor P2S zijn alleen beschikbaar voor het Resource Manager-implementatie model. Ze zijn niet beschikbaar voor het klassieke implementatie model.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Hoe worden P2S VPN-clients geverifieerd?

Voordat Azure een P2S-VPN-verbinding accepteert, moet de gebruiker eerst worden geverifieerd. Er zijn twee mechanismen die Azure biedt om een verbinding met een gebruiker te verifiëren.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Verifiëren met behulp van systeem eigen Azure-certificaat verificatie

Wanneer u de systeem eigen Azure-certificaat verificatie gebruikt, wordt een client certificaat dat aanwezig is op het apparaat gebruikt voor verificatie van de gebruiker die verbinding maakt. Client certificaten worden gegenereerd op basis van een vertrouwd basis certificaat en vervolgens geïnstalleerd op elke client computer. U kunt een basis certificaat gebruiken dat is gegenereerd met een bedrijfs oplossing of u kunt een zelfondertekend certificaat genereren.

De validatie van het client certificaat wordt uitgevoerd door de VPN-gateway en gebeurt tijdens het instellen van de P2S VPN-verbinding. Het basis certificaat is vereist voor de validatie en moet worden geüpload naar Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Verifiëren met behulp van systeem eigen Azure Active Directory authenticatie

Met Azure AD-verificatie kunnen gebruikers verbinding maken met Azure met behulp van hun Azure Active Directory referenties. Systeem eigen Azure AD-verificatie wordt alleen ondersteund voor het OpenVPN-protocol en Windows 10 en vereist het gebruik van de [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554).

Met systeem eigen Azure AD-verificatie kunt u gebruikmaken van de voorwaardelijke toegang van Azure AD en de functies van Multi-Factor Authentication (MFA) voor VPN.

Op hoog niveau moet u de volgende stappen uitvoeren om Azure AD-verificatie te configureren:

1. [Een Azure Active Directory-tenant configureren](openvpn-azure-ad-tenant.md)

2. [Azure AD-verificatie inschakelen op de gateway](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Azure VPN-client downloaden en configureren](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Verifiëren met behulp van de domein server van Active Directory (AD)

Met AD-domein verificatie kunnen gebruikers verbinding maken met Azure met behulp van de domein referenties van hun organisatie. Hiervoor is een RADIUS-server vereist die met de AD-server kan worden geïntegreerd. Organisaties kunnen ook gebruikmaken van de bestaande RADIUS-implementatie.
  
De RADIUS-server kan on-premises of in uw Azure VNet worden geïmplementeerd. Tijdens de verificatie fungeert de Azure-VPN Gateway als een Pass-Through en stuurt verificatie berichten terug tussen de RADIUS-server en het apparaat waarmee u verbinding maakt. Daarom is de gateway bereikbaar voor de RADIUS-server belang rijk. Als de RADIUS-server on-premises aanwezig is, is een VPN S2S-verbinding van Azure naar de on-premises site vereist voor bereik baarheid.  
  
De RADIUS-server kan ook worden geïntegreerd met AD Certificate Services. Hiermee kunt u de RADIUS-server en de implementatie van het ondernemings certificaat voor P2S-certificaat verificatie gebruiken als alternatief voor de Azure-certificaat verificatie. Het voor deel is dat u geen basis certificaten en ingetrokken certificaten naar Azure hoeft te uploaden.

Een RADIUS-server kan ook worden geïntegreerd met andere externe identiteits systemen. Hiermee opent u veel verificatie opties voor P2S VPN, waaronder multi-factor-opties.

![Diagram waarin een punt-naar-site-VPN met een on-premises site wordt weer gegeven.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>Wat zijn de configuratievereisten voor de client?

>[!NOTE]
>Voor Windows-clients moet u over beheerders rechten beschikken voor het client apparaat om de VPN-verbinding van het client apparaat naar Azure te initiëren.
>

Gebruikers gebruiken de systeem eigen VPN-clients op Windows-en Mac-apparaten voor P2S. Azure biedt een zip-bestand voor de configuratie van een VPN-client dat instellingen bevat die nodig zijn voor deze systeem eigen clients om verbinding te maken met Azure.

* Voor Windows-apparaten bestaat de configuratie van de VPN-client uit een installatie pakket dat door gebruikers op hun apparaten wordt geïnstalleerd.
* Voor Mac-apparaten bestaat het uit het mobileconfig-bestand dat gebruikers op hun apparaten installeren.

Het zip-bestand bevat ook de waarden van een aantal belang rijke instellingen voor de Azure-zijde die u kunt gebruiken om uw eigen profiel voor deze apparaten te maken. Enkele van de waarden zijn het adres van de VPN-gateway, geconfigureerde tunnel typen, routes en het basis certificaat voor de gateway validatie.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Welke gateway-Sku's ondersteunen P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Zie [over VPN gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md#gwsku)voor aanbevelingen voor de gateway-SKU.

>[!NOTE]
>De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Welke IKE/IPsec-beleids regels zijn geconfigureerd op VPN-gateways voor P2S?


**IKEv2**

| **Sterkte** | **Integriteit** | **PRF** | **DH-groep** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Sterkte** | **Integriteit** | **PFS-groep** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Welke TLS-beleids regels zijn geconfigureerd op VPN-gateways voor P2S?
**TLS**

|**Beleidsregels** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hoe kan ik een P2S-verbinding configureren?

Een P2S-configuratie vereist enkele specifieke stappen. De volgende artikelen bevatten de stappen voor het door lopen van P2S-configuratie en koppelingen naar het configureren van de VPN-client apparaten:

* [Een P2S-verbinding configureren-RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren-Azure systeem eigen certificaat authenticatie](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN configureren](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>De configuratie van een P2S-verbinding verwijderen

Zie de [Veelgestelde vragen](#removeconfig)hieronder voor instructies.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Veelgestelde vragen over systeem eigen Azure-certificaat verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Veelgestelde vragen over RADIUS-verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren-RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren-Azure systeem eigen certificaat authenticatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
