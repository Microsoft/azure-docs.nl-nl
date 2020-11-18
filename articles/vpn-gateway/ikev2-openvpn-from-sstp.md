---
title: Overgang naar OpenVPN of IKEv2 vanuit SSTP | Azure VPN Gateway
description: In dit artikel vindt u informatie over de manieren waarop u de limiet van 128 gelijktijdige verbindingen van SSTP kunt oplossen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 59aa6f5560917651d8f60c667145b0953bf72ef5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660862"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Overgang naar het OpenVPN-protocol of IKEv2 van SSTP

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Dit artikel is van toepassing op het Resource Manager-implementatie model en spreekt over manieren om de limiet van 128 gelijktijdige verbindingen van SSTP te overwinnen door over te stappen op het OpenVPN-protocol of IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Welk protocol gebruikt P2S?

Punt-naar-site-VPN kan gebruikmaken van een van de volgende protocollen:

* **Openvpn &reg; Protocol**, een op SSL/TLS gebaseerd VPN-protocol. Een SSL-VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls open TCP-poort 443 uitgaand, die SSL gebruikt. OpenVPN kan worden gebruikt om verbinding te maken vanaf Android, iOS (versie 11,0 en hoger), Windows, Linux en Mac-apparaten (OSX-versies 10,13 en hoger).

* **SSTP (Secure Socket Tunneling Protocol)**, een eigen op SSL gebaseerd VPN-protocol. Een SSL-VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls open TCP-poort 443 uitgaand, die SSL gebruikt. SSTP wordt alleen ondersteund op Windows-apparaten. Azure ondersteunt alle versies van Windows die SSTP (Windows 7 en hoger) hebben. **SSTP biedt alleen ondersteuning voor Maxi maal 128 gelijktijdige verbindingen, ongeacht de gateway-SKU**.

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).


>[!NOTE]
>IKEv2 en OpenVPN voor P2S zijn alleen beschikbaar voor het Resource Manager-implementatie model. Ze zijn niet beschikbaar voor het klassieke implementatie model. Basis gateway-SKU biedt geen ondersteuning voor IKEv2-of OpenVPN-protocollen. Als u de basis-SKU gebruikt, moet u een productie-SKU Virtual Network gateway verwijderen en opnieuw maken.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migreren van SSTP naar IKEv2 of OpenVPN

Het kan voor komen dat u meer dan 128 gelijktijdige P2S-verbinding met een VPN-gateway wilt ondersteunen, maar gebruikmaakt van SSTP. In dat geval moet u overstappen naar IKEv2-of OpenVPN-protocol.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Optie 1: IKEv2 toevoegen naast SSTP op de gateway

Dit is de eenvoudigste optie. SSTP en IKEv2 kunnen naast elkaar bestaan op dezelfde gateway en een groter aantal gelijktijdige verbindingen geven. U kunt IKEv2 gewoon inschakelen op de bestaande gateway en de client opnieuw downloaden.

Het toevoegen van IKEv2 aan een bestaande SSTP VPN-gateway heeft geen invloed op bestaande clients en u kunt ze configureren voor het gebruik van IKEv2 in kleine batches of alleen de nieuwe clients configureren voor het gebruik van IKEv2. Als een Windows-client is geconfigureerd voor zowel SSTP als IKEv2, probeert het eerst verbinding te maken met IKEV2. als dat niet het geval is, gaat deze terug naar SSTP.

**IKEv2 maakt gebruik van niet-standaard UDP-poorten. u moet er dus voor zorgen dat deze poorten niet worden geblokkeerd op de firewall van de gebruiker. De poorten in gebruik zijn UDP 500 en 4500.**

Als u IKEv2 aan een bestaande gateway wilt toevoegen, gaat u naar het tabblad punt-naar-site-configuratie onder de Virtual Network-gateway in de portal en selecteert u **ikev2 en SSTP (SSL)** in de vervolg keuzelijst.

![Scherm opname van de pagina ' punt-naar-site-configuratie ' met de vervolg keuzelijst ' tunnel type ' open en ' IKEv2 en SSTP (SSL) ' geselecteerd.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Optie 2: SSTP verwijderen en OpenVPN inschakelen op de gateway

Aangezien SSTP en OpenVPN beide TLS-protocollen zijn, kunnen ze niet naast elkaar bestaan op dezelfde gateway. Als u besluit om over te stappen van SSTP naar OpenVPN, moet u SSTP uitschakelen en OpenVPN inschakelen op de gateway. Met deze bewerking wordt de verbinding van de bestaande clients met de VPN-gateway verbroken totdat het nieuwe profiel is geconfigureerd op de client.

U kunt indien gewenst OpenVPN naast IKEv2 inschakelen. OpenVPN is op TLS gebaseerd en maakt gebruik van de standaard TCP 443-poort. Als u wilt overschakelen naar OpenVPN, gaat u naar het tabblad punt-naar-site-configuratie onder de gateway Virtual Network in portal en selecteert u **openvpn (SSL)** of **IKEv2 en openvpn (SSL)** in de vervolg keuzelijst.

![punt-naar-site](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Zodra de gateway is geconfigureerd, kunnen bestaande clients geen verbinding maken totdat u [de openvpn-clients implementeert en configureert](./vpn-gateway-howto-openvpn-clients.md).

Als u Windows 10 gebruikt, kunt u ook de [Azure VPN-client voor Windows](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client) gebruiken


## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="what-are-the-client-configuration-requirements"></a>Wat zijn de configuratievereisten voor de client?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Welke gateway-Sku's ondersteunen P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Zie [over VPN gateway-instellingen](vpn-gateway-about-vpn-gateway-settings.md#gwsku)voor aanbevelingen voor de gateway-SKU.

>[!NOTE]
>De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Welke IKE/IPsec-beleids regels zijn geconfigureerd op VPN-gateways voor P2S?


**IKEv2**

|**Sterkte** | **Integriteit** | **PRF** | **DH-groep** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Sterkte** | **Integriteit** | **PFS-groep** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Welke TLS-beleids regels zijn geconfigureerd op VPN-gateways voor P2S?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hoe kan ik een P2S-verbinding configureren?

Een P2S-configuratie vereist enkele specifieke stappen. De volgende artikelen bevatten de stappen voor het door lopen van P2S-configuratie en koppelingen naar het configureren van de VPN-client apparaten:

* [Een P2S-verbinding configureren-RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren-Azure systeem eigen certificaat authenticatie](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN configureren](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren-RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren-Azure systeem eigen certificaat authenticatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**' OpenVPN ' is een handels merk van OpenVPN Inc.**