---
title: Voorbeeld configuratie voor het verbinden van Cisco ASA-apparaten met Azure VPN-gateways
description: Dit artikel bevat een voorbeeld configuratie voor het verbinden van Cisco ASA-apparaten met Azure VPN-gateways.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426167"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Voorbeeld configuratie: Cisco ASA-apparaat (IKEv2/geen BGP)
In dit artikel vindt u voor beelden van configuraties voor het koppelen van Cisco Adaptive Security Appliance-apparaten (ASA) aan Azure VPN-gateways. Het voor beeld is van toepassing op Cisco ASA-apparaten met IKEv2 zonder de Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>Apparaat in één oogopslag

* Leverancier van apparaat: **Cisco**
* Model apparaat: **ASA**           
* Doel versie: **8,4 en hoger**
* Getest model: **ASA 5505**
* Geteste versie: **9,2**             
* IKE-versie: **IKEv2**                  
* BGP: **Nee**      
* Azure VPN-gateway type: **op route gebaseerde VPN-gateway**

> [!NOTE]
> De voorbeeld configuratie verbindt een Cisco ASA-apparaat met een **op Azure route gebaseerde** VPN-gateway. De verbinding maakt gebruik van een aangepast IPsec/IKE-beleid met de optie **UsePolicyBasedTrafficSelectors** , zoals beschreven in [dit artikel](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Het voor beeld vereist dat ASA-apparaten het **IKEv2** -beleid gebruiken met op toegangs lijsten gebaseerde configuraties, niet op VTI. Raadpleeg de specificaties van de leverancier van uw VPN-apparaat om te controleren of het IKEv2-beleid wordt ondersteund op uw on-premises VPN-apparaten.


## <a name="vpn-device-requirements"></a>Vereisten voor VPN-apparaten
Azure VPN-gateways gebruiken de standaard IPsec/IKE-protocol suites om een S2S-VPN-tunnel (site-naar-site) tot stand te brengen. Zie [over VPN-apparaten](vpn-gateway-about-vpn-devices.md)voor de gedetailleerde IPSec/IKE-protocol parameters en standaard cryptografische algoritmen voor Azure VPN-gateways.

> [!NOTE]
> U kunt optioneel een exacte combi natie van cryptografische algoritmen en sleutel sterkten voor een specifieke verbinding opgeven, zoals beschreven in [informatie over cryptografische vereisten](vpn-gateway-about-compliance-crypto.md). Als u een exacte combi natie van algoritmen en sleutel sterkte opgeeft, moet u ervoor zorgen dat u de corresponderende specificaties op uw VPN-apparaten gebruikt.

## <a name="single-vpn-tunnel"></a>Eén VPN-tunnel
Deze configuratie bestaat uit één S2S VPN-tunnel tussen een Azure VPN-gateway en een on-premises VPN-apparaat. U kunt de BGP ook configureren via de VPN-tunnel.

![Single S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Zie [Single VPN tunnel Setup](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)(Engelstalig) voor stapsgewijze instructies voor het bouwen van de Azure-configuraties.

### <a name="virtual-network-and-vpn-gateway-information"></a>Informatie over virtuele netwerk-en VPN-gateway
In deze sectie vindt u de para meters voor het voor beeld.

| **Parameter**                | **Waarde**                    |
| ---                          | ---                          |
| Adres voorvoegsels van virtuele netwerken        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN-gateway-IP         | Azure_Gateway_Public_IP      |
| On-premises adres voorvoegsels | 10.51.0.0/16<br>10.52.0.0/16 |
| IP-adres van on-premises VPN-apparaat    | OnPrem_Device_Public_IP     |
| * BGP ASN van virtueel netwerk                | 65010                        |
| * Azure BGP-peer-IP           | 10.12.255.30                 |
| * On-premises BGP ASN         | 65050                        |
| * On-premises BGP-peer-IP     | 10.52.255.254                |
|                              |                              |

\* Optionele para meter voor alleen BGP.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE-beleid en-para meters
De volgende tabel bevat de IPsec/IKE-algoritmen en-para meters die in het voor beeld worden gebruikt. Raadpleeg de specificaties van uw VPN-apparaat om de algoritmen te controleren die worden ondersteund voor de modellen van uw VPN-apparaten en firmware.

| **IPsec/IKEv2**  | **Waarde**                            |
| ---              | ---                                  |
| IKEv2-versleuteling | AES256                               |
| IKEv2-integriteit  | SHA384                               |
| DH-groep         | DHGroup24                            |
| * IPsec-versleuteling | AES256                               |
| * IPsec-integriteit  | SHA1                                 |
| PFS-groep        | PFS24                                |
| QM SA-levensduur   | 7.200 seconden                         |
| Verkeersselector | UsePolicyBasedTrafficSelectors $True |
| Vooraf gedeelde sleutel   | PreSharedKey                         |
|                  |                                      |

\* Op sommige apparaten moet IPsec-integriteit een null-waarde zijn wanneer de IPsec-versleutelings algoritme AES-GCM is.

### <a name="asa-device-support"></a>Ondersteuning voor het ASA-apparaat

* Ondersteuning voor IKEv2 vereist ASA versie 8,4 en hoger.

* Ondersteuning voor DH-groep en PFS-groep buiten groep 5 vereist ASA versie 9. x.

* Ondersteuning voor IPsec-versleuteling met AES-GCM en IPsec-integriteit met SHA-256, SHA-384 of SHA-512 vereist ASA versie 9. x. Deze ondersteunings vereiste is van toepassing op nieuwere ASA-apparaten. Op het moment van publicatie, hebben ASA-modellen 5505, 5510, 5520, 5540, 5550 en 5580 geen ondersteuning voor deze algoritmen. Raadpleeg de specificaties van uw VPN-apparaat om de algoritmen te controleren die worden ondersteund voor de modellen van uw VPN-apparaten en firmware.


### <a name="sample-device-configuration"></a>Voor beeld van apparaatconfiguratie
Het script bevat een voor beeld dat is gebaseerd op de configuratie en para meters die in de vorige secties worden beschreven. De configuratie van de S2S VPN-tunnel bestaat uit de volgende onderdelen:

1. Interfaces en routes
2. Toegangs lijsten
3. IKE-beleid en-para meters (fase 1 of hoofd modus)
4. IPsec-beleid en-para meters (fase 2 of snelle modus)
5. Andere para meters, zoals TCP MSS-klem

> [!IMPORTANT]
> Voer de volgende stappen uit voordat u het voorbeeld script gebruikt. Vervang de waarden van de tijdelijke aanduidingen in het script door de apparaatinstellingen voor uw configuratie.

* De configuratie van de interface voor zowel binnen-als externe interfaces opgeven.
* Identificeer de routes voor uw binnen/privé-en buiten-en open bare netwerken.
* Zorg ervoor dat alle namen en beleids nummers uniek zijn op uw apparaat.
* Zorg ervoor dat de cryptografische algoritmen op uw apparaat worden ondersteund.
* Vervang de volgende **waarden voor de tijdelijke aanduiding** door de werkelijke waarden voor uw configuratie:
  - Buiten de interface naam: **buiten**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Namen van virtuele netwerk-en lokale netwerk gateways: **VNetName** en **LNGName**
  - Virtuele netwerk-en on-premises netwerk adres **voorvoegsels**
  - Juiste **netmaskers**

#### <a name="sample-script"></a>Voorbeeldscript

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Eenvoudige opdrachten voor fout opsporing

Gebruik de volgende ASA-opdrachten voor fout opsporing:

* De IPsec-of IKE-beveiligings koppeling (SA) weer geven:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Modus voor fout opsporing invoeren:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug`Met de opdrachten kan aanzienlijke uitvoer op de console worden gegenereerd.

* De huidige configuraties op het apparaat weer geven:
    ```
    show run
    ```
    Gebruik `show` subopdrachten om specifieke onderdelen van de apparaatconfiguratie weer te geven, bijvoorbeeld:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [actieve en actieve VPN-gateways configureren](vpn-gateway-activeactive-rm-powershell.md)voor meer informatie over het configureren van Active-Active-cross-premises en vnet-naar-vnet-verbindingen.
