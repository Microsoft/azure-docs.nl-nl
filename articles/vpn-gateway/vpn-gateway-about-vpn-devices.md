---
title: 'Azure VPN Gateway: over VPN-apparaten voor verbindingen'
description: Dit artikel gaat over VPN-apparaten en IPSec-parameters voor cross-premises site-naar-site-VPN-gateway-verbindingen. Het artikel bevat koppelingen naar configuratie-instructies en voorbeelden.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 4c6bd62e96d85305036626a8672c39ff1b9f6b26
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201090"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>VPN-apparaten en IPSec-/IKE-parameters voor site-naar-site-VPN-gateway-verbindingen

U hebt een VPN-apparaat nodig om een cross-premises site-naar-site-VPN-verbinding te configureren. Site-naar-site-verbindingen kunnen worden gebruikt om een hybride oplossing te maken of wanneer u beveiligde verbindingen wilt maken tussen uw on-premises netwerken en virtuele netwerken. Dit artikel bevat een lijst met gevalideerde VPN-apparaten en een lijst met IPSec-/IKE-parameters voor VPN-gateways.

> [!IMPORTANT]
> Raadpleeg [Bekende compatibiliteitsproblemen](#known) als u problemen ondervindt met de connectiviteit tussen uw lokale VPN-apparaten en VPN-gateways.
>

### <a name="items-to-note-when-viewing-the-tables"></a>Waar u op moet letten wanneer u de tabellen bekijkt:

* Er is een terminologiewijziging voor Azure VPN-gateways. Alleen de namen zijn gewijzigd. Er is geen wijziging in de functionaliteit.
  * Statische routering = PolicyBased
  * Dynamische routering = RouteBased
* De specificaties voor een HighPerformance-VPN-gateway en een RouteBased VPN-gateway zijn hetzelfde, tenzij anders wordt vermeld. Zo zijn de gevalideerde VPN-apparaten die compatibel zijn met RouteBased VPN-gateways, ook compatibel met de HighPerformance VPN-gateway.

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>Gevalideerde VPN-apparaten en apparaatconfiguratiehandleidingen

We hebben samen met apparaatleveranciers een reeks standaard VPN-apparaten gevalideerd. Alle apparaten in de apparaatfamilies in de volgende lijst kunnen met VPN-gateways worden gebruikt. Zie [Over VPN-gatewayinstellingen](vpn-gateway-about-vpn-gateway-settings.md#vpntype) voor informatie over welk VPN-type u moet gebruiken (PolicyBased of RouteBased) voor de VPN-gatewayoplossing die u wilt configureren.

Raadpleeg de koppelingen die overeenkomen met de juiste familie voor meer informatie over het configureren van uw VPN-apparaat. De koppelingen naar configuratie-instructies worden naar beste vermogen geleverd. Voor ondersteuning van VPN-apparaten neemt u contact op met de fabrikant van uw apparaat.

|**Leverancier**          |**Apparaatfamilie**     |**Minimale versie van het besturingssysteem** |**PolicyBased configuratie-instructies** |**RouteBased configuratie-instructies** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Niet compatibel  |[Configuratie handleiding](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |VPN-routers uit AR-serie |AR-Series 5.4.7 +               | [Configuratie handleiding](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[Configuratie handleiding](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | CloudEOS-router | vEOS 4.24.0 FX | (niet getest) | [Configuratie handleiding](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen-firewall |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Configuratie handleiding](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[Configuratie handleiding](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |Security Gateway |R 80.10 |[Configuratie handleiding](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Configuratie handleiding](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Ondersteund |[Configuratie handleiding *](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |Ondersteund |Ondersteund |
| Cisco | CSR | RouteBased: IOS-XE 16,10 | (niet getest) | [Configuratie script](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |Ondersteund |Ondersteund |
| Cisco |Meraki (MX) | MX v-15.12 |Niet compatibel | [Configuratie handleiding](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (Viptela OS) | 18.4.0 (actieve/passieve modus)<br><br>19,2 (actieve/actieve modus) | Niet compatibel |  [Hand matige configuratie (actief/passief)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Cloud opstap-configuratie (actief/actief)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 en hoger |[Configuratie handleiding](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Niet compatibel |
| F5 |BIG-IP-serie |12.0 |[Configuratie handleiding](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Configuratie handleiding](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (niet getest) |[Configuratie handleiding](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone-netwerken | Firewalls van de volgende generatie (NGFW) | 5.5 R7  | (niet getest) | [Configuratie handleiding](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |SEIL-serie |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Configuratie handleiding](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Niet compatibel |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Ondersteund |[Configuratie script](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J-serie |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Ondersteund |[Configuratie script](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Ondersteund |[Configuratie script](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Ondersteund |[Configuratie script](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Ondersteund |[Configuratie script](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Routering en Remote Access-Service |Windows Server 2012 |Niet compatibel |Ondersteund |
| Open Systems AG |Mission Control Security Gateway |N.v.t. |[Configuratie handleiding](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |Niet compatibel |
| Palo Alto Networks |Alle apparaten waarop PAN-OS wordt uitgevoerd |PAN-OS<br>PolicyBased: 6.1.5 of hoger<br>RouteBased: 7.1.4 |Ondersteund |[Configuratie handleiding](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (ontwikkel aars) | VyOS | VyOS 1.2.2 | (niet getest) | [Configuratie handleiding ](https://docs.vyos.io/en/latest/configexamples/azure-vpn-bgp.html)|
| ShareTech | Next Generation UTM (NU-serie) | 9.0.1.3 | Niet compatibel | [Configuratie handleiding](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ-serie, NSA-serie<br>SuperMassive-serie<br>E-Class NSA-serie |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Niet compatibel |[Configuratie handleiding](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG Firewall van de volgende generatie | XG v17 | (niet getest) | [Configuratie handleiding](https://community.sophos.com/kb/127546)<br><br>[Configuratie handleiding-meerdere SAs](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | 1.1.5/VpnPlusServer-1.2.0 | (niet getest) | [Configuratie handleiding](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v 1,10 | (niet getest) | [BGP via IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI via IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E-636L3 | 5.2.0. T3-build-13  | (niet getest) | Configuratie handleiding |
| WatchGuard |Alles |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Configuratie handleiding](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Configuratie handleiding](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| ZyXEL |ZyWALL USG-serie<br>ZyWALL ATP-serie<br>ZyWALL VPN-serie | ZLD v 4.32 + | (niet getest) | [VTI via IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP via IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) Met Cisco ASA versie 8.4 en hoger wordt IKEv2-ondersteuning toegevoegd en kan verbinding worden gemaakt met Azure VPN-gateways met behulp van een aangepast IPsec/IKE-beleid met de optie UsePolicyBasedTrafficSelectors. Raadpleeg dit [artikel met instructies](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (\*\*) Routers uit de ISR 7200-serie bieden alleen ondersteuning voor PolicyBased VPN's.

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>Configuratie scripts voor VPN-apparaten downloaden van Azure

Voor bepaalde apparaten kunt u configuratie scripts rechtstreeks vanuit Azure downloaden. Zie [configuratie scripts voor VPN-apparaten downloaden](vpn-gateway-download-vpndevicescript.md)voor meer informatie en instructies voor het downloaden.

### <a name="devices-with-available-configuration-scripts"></a>Apparaten met beschik bare configuratie scripts

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>Niet-gevalideerde VPN-apparaten

Als uw apparaat niet in de tabel met gevalideerde VPN-apparaten wordt vermeld, werkt het misschien toch met een site-naar-site-verbinding. Neem contact op met de fabrikant van uw apparaat voor aanvullende ondersteuning en configuratie-instructies.

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>Voorbeelden van het bewerken van apparaatconfiguraties

Nadat u het bij het VPN-apparaat meegeleverde configuratievoorbeeld hebt gedownload, moet u enkele waarden veranderen zodat ze overeenkomen met de instellingen voor uw omgeving.

### <a name="to-edit-a-sample"></a>U bewerkt een voorbeeld als volgt:

1. Open het voorbeeld met Kladblok.
2. Zoek alle <*tekst*>-tekenreeksen en vervang ze door de waarden die betrekking hebben op uw omgeving. Zorg dat u < en > opneemt. Als u een naam opgeeft, moet deze uniek zijn. Als een opdracht niet werkt, raadpleeg dan de documentatie van de fabrikant van uw apparaat.

| **Voorbeeldtekst** | **Wijzig in** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |De naam die u voor dit object hebt gekozen. Voorbeeld: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |De naam die u voor dit object hebt gekozen. Voorbeeld: myAzureNetwork |
| &lt;RP_AccessList&gt; |De naam die u voor dit object hebt gekozen. Voorbeeld: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |De naam die u voor dit object hebt gekozen. Voorbeeld: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |De naam die u voor dit object hebt gekozen. Voorbeeld: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Geef het bereik op. Voorbeeld: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Geef het subnetmasker op. Voorbeeld: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Geef het on-premises bereik op. Voorbeeld: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Geef het on-premises subnetmasker op. Voorbeeld: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Deze informatie is specifiek voor uw virtuele netwerk en u vindt deze in de beheerportal als **IP-adres van gateway**. |
| &lt;SP_PresharedKey&gt; |Deze informatie is specifiek voor uw virtuele netwerk en u vindt deze in de beheerportal onder Sleutel beheren. |

## <a name="default-ipsecike-parameters"></a><a name="ipsec"></a>Standaard IPsec/IKE-para meters

De onderstaande tabellen bevatten de combi Naties van algoritmen en para meters die Azure VPN-gateways gebruiken in de standaard configuratie (**standaard beleid**). Voor op routes gebaseerde VPN-gateways die zijn gemaakt met het Azure Resource Management-implementatiemodel, kunt u voor elke afzonderlijke verbinding een aangepast beleid opgeven. Raadpleeg [Configure IPsec/IKE policy](vpn-gateway-ipsecikepolicy-rm-powershell.md) (IPsec/IKE-beleid configureren) voor gedetailleerde instructies.

Daarnaast moet u TCP **MSS** op **1350** zetten. Als uw VPN-apparaten het vastzetten van MSS niet ondersteunen, kunt u in plaats daarvan ook de **MTU** op de tunnelinterface **1400** instellen op bytes.

In de volgende tabellen:

* SA = Security Association
* IKE Phase 1 wordt ook 'Main Mode' genoemd
* IKE Phase 2 wordt ook 'Quick Mode' genoemd

### <a name="ike-phase-1-main-mode-parameters"></a>Parameters voor IKE Phase 1 (Main Mode)

| **Eigenschap**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| IKE-versie           |IKEv1              |IKEv1 en IKEv2    |
| Diffie-Hellman-groep  |Groep 2 (1024 bits) |Groep 2 (1024 bits) |
| Verificatiemethode |Vooraf gedeelde sleutel     |Vooraf gedeelde sleutel     |
| Versleutelings- en hash-algoritmen |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA-levensduur           |28.800 seconden     |28.800 seconden     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parameters voor IKE Phase 2 (Quick Mode)

| **Eigenschap**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| IKE-versie                   |IKEv1          |IKEv1 en IKEv2                              |
| Versleutelings- en hash-algoritmen |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[RouteBased QM SA-aanbiedingen](#RouteBasedOffers) |
| SA-levensduur (tijd)            |3.600 seconden  |27.000 seconden                               |
| SA-levensduur (bytes)           |102.400.000 kB |102.400.000 kB                               |
| Perfect Forward Secrecy (PFS) |No             |[RouteBased QM SA-aanbiedingen](#RouteBasedOffers) |
| Dead Peer Detection (DPD)     |Niet ondersteund  |Ondersteund                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>Aanbiedingen RouteBased VPN IPsec Security Association (IKE Quick Mode SA)

De volgende tabel bevat aanbiedingen van IPSec-SA (IKE Quick Mode). De aanbiedingen staan in volgorde van voorkeur waarin de aanbieding is gepresenteerd of geaccepteerd.

#### <a name="azure-gateway-as-initiator"></a>Azure-gateway als initiator

|-  |**Versleuteling**|**Verificatie**|**PFS-groep**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Geen         |
| 2 |AES256        |SHA1              |Geen         |
| 3 |3DES          |SHA1              |Geen         |
| 4 |AES256        |SHA256            |Geen         |
| 5 |AES128        |SHA1              |Geen         |
| 6 |3DES          |SHA256            |Geen         |

#### <a name="azure-gateway-as-responder"></a>Azure-Gateway als antwoorder

|-  |**Versleuteling**|**Verificatie**|**PFS-groep**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Geen         |
| 2 |AES256        |SHA1              |Geen         |
| 3 |3DES          |SHA1              |Geen         |
| 4 |AES256        |SHA256            |Geen         |
| 5 |AES128        |SHA1              |Geen         |
| 6 |3DES          |SHA256            |Geen         |
| 7 |DES           |SHA1              |Geen         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Geen         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* U kunt IPsec ESP NULL-versleuteling opgeven met RouteBased VPN-gateways en HighPerformance VPN-gateways. Op null gebaseerde versleuteling biedt geen beveiliging voor gegevens tijdens de overdracht. Dit mag alleen worden gebruikt wanneer maximale doorvoer en minimale latentie zijn vereist. Clients kunnen ervoor kiezen dit te gebruiken voor communicatie tussen VNET's of wanneer elders in de oplossing versleuteling wordt toegepast.
* Gebruik voor cross-premises connectiviteit via internet de standaardinstellingen voor Azure VPN-gateways met versleuteling en hash-algoritmen die in de tabel hierboven worden vermeld, om beveiliging van uw kritieke communicatie te waarborgen.

## <a name="known-device-compatibility-issues"></a><a name="known"></a>Bekende compatibiliteits problemen met apparaten

> [!IMPORTANT]
> Dit zijn de bekende compatibiliteitsproblemen tussen VPN-apparaten van derden en Azure VPN-gateways. Het team van Azure werkt samen met de leveranciers aan een oplossing voor de hier vermelde problemen. Zodra de problemen zijn opgelost, wordt deze pagina bijgewerkt met de meest actuele informatie. Bekijk deze pagina daarom regelmatig.
>
>

### <a name="feb-16-2017"></a>16 februari 2017

**Palo Alto Networks-apparaten met een oudere versie dan 7.1.4** voor op route gebaseerde Azure VPN: als u VPN-apparaten van Palo Alto Networks gebruikt met een PAN-OS-versie die ouder is dan 7.1.4 en u connectiviteitsproblemen hebt met op route gebaseerde Azure VPN-gateways, voert u de volgende stappen uit:

1. Controleer de firmwareversie van uw Palo Alto Networks-apparaat. Als de PAN-OS-versie ouder is dan 7.1.4, voert u een upgrade uit naar 7.1.4.
2. Op het Palo Alto Networks-apparaat wijzigt u de levensduur van de beveiligingskoppeling fase 2 (of de beveiligingskoppeling in snelle modus) in 28.800 seconden (8 uur) wanneer er verbinding met de Azure VPN-gateway wordt gemaakt.
3. Als het connectiviteitsprobleem zich nog steeds voordoet, opent u een ondersteuningsaanvraag via de Azure-portal.
