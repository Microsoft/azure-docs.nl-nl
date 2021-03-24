---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 685160e1075a0d4192157200ee4f5d981ca5f472
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953448"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Wordt het aangepaste beleid voor IPsec/IKE op alle Azure VPN Gateway-SKU's ondersteund?

Aangepast IPsec-/IKE-beleid wordt ondersteund in alle Azure-SKU's, behalve Basic.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Hoeveel beleidsregels kan ik opgeven voor een verbinding?

U kunt maar ***één*** beleidscombinatie opgeven voor een bepaalde verbinding.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Kan ik een gedeeltelijk beleid opgeven voor een verbinding? (Bijvoorbeeld alleen IKE-algoritmen, maar geen IPsec-algoritmen)

Nee, u moet alle algoritmen en parameters opgeven voor zowel IKE (Main Mode) en IPsec (Quick Mode). Gedeeltelijke beleidsspecificatie is niet toegestaan.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Wat zijn de algoritmen en belangrijkste sterke punten die in het aangepaste beleid worden ondersteund?

De volgende tabel bevat de ondersteunde cryptografische algoritmen en sleutelsterkten die door klanten kunnen worden geconfigureerd. U moet voor elk veld een optie selecteren.

| **IPsec/IKEv2**  | **Opties**                                                                   |
| ---              | ---                                                                           |
| IKEv2-versleuteling | AES256, AES192, AES128, DES3, DES                                             |
| IKEv2-integriteit  | SHA384, SHA256, SHA1, MD5                                                     |
| DH-groep         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, geen  |
| IPsec-versleuteling | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, geen      |
| IPsec-integriteit  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| PFS-groep        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, geen                              |
| QM SA-levensduur   | Seconden (geheel getal; **min. 300**/standaard 27000 seconden)<br>KB (geheel getal; **min. 1024**/standaard 102400000 KB)           |
| Verkeersselector | UsePolicyBasedTrafficSelectors ($True/$False; standaard $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> *  DHGroup2048 en PFS2048 zijn hetzelfde als Diffie-Hellman-groep **14** in IKE en IPsec PFS. Zie [Diffie-Hellman-groepen](#DH) voor de volledige toewijzingen.
> * Voor GCMAES-algoritmen moet u de hetzelfde GCMAES-algoritme en dezelfde lengte van de sleutel voor de IPsec-codering en -integriteit opgeven.
> * SA-levensduur voor IKEv2 Main Mode staat vastgesteld op 28.800 seconden op de Azure VPN-gateways.
> * De QM SA-levensduur is een optionele parameter. Als niets is opgegeven, worden de standaardwaarden 27.000 seconden (7,5 uur) en 102400000 kilobytes (102 GB) gebruikt.
> * UsePolicyBasedTrafficSelector is een optieparameter voor de verbinding. Zie het volgende FAQ-item voor ' UsePolicyBasedTrafficSelectors '.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Moeten het beleid van de Azure VPN-gateway en de configuraties van mijn on-premises VPN-apparaat volledig overeenkomen?

De configuratie van uw on-premises VPN-apparaat moet overeenkomen met of de volgende algoritmen en parameters bevatten die u in het Azure IPsec/IKE-beleid opgeeft:

* IKE-versleutelingsalgoritme
* IKE-integriteitsalgoritme
* DH-groep
* IPsec-versleutelingsalgoritme
* IPsec-integriteitsalgoritme
* PFS-groep
* Verkeersselector (*)

De SA-levensduren zijn alleen lokale specificaties en hoeven niet overeen te komen.

Als u **UsePolicyBasedTrafficSelectors** inschakelt, moet u ervoor zorgen dat voor uw VPN-apparaat dezelfde verkeersselectoren zijn gedefinieerd voor alle combinaties van de voorvoegsels van uw lokale netwerk (lokale netwerkgateway) naar/vanuit de voorvoegsels van het virtuele Azure-netwerk, in plaats van any-to-any. Als uw lokale netwerkvoorvoegsels bijvoorbeeld 10.1.0.0/16 en 10.2.0.0/16 zijn, en de voorvoegsels van uw virtuele netwerk 192.168.0.0/16 en 172.16.0.0/16, moet u de volgende verkeersselectoren opgeven:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Zie [Connect multiple on-premises policy-based VPN devices](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Verbinding maken met meerdere on-premises, op beleid gebaseerde VPN-apparaten) voor meer informatie.

### <a name="which-diffie-hellman-groups-are-supported"></a><a name ="DH"></a>Welke Diffie-Hellman-groepen worden ondersteund?

De volgende tabel bevat de ondersteunde Diffie-Hellman-groepen voor IKE (DHGroup) en IPsec (PFSGroup):

| **Diffie-Hellman-groep**  | **DHGroup**              | **PFSGroup** | **Sleutellengte** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768-bits MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bits MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bits MODP  |
| 19                        | ECP256                   | ECP256       | 256-bits ECP    |
| 20                        | ECP384                   | ECP384       | 384-bits ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bits MODP  |
|                           |                          |              |                |

Zie [RFC3526](https://tools.ietf.org/html/rfc3526) en [RFC5114](https://tools.ietf.org/html/rfc5114) voor meer informatie.

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Vervangt het aangepaste beleid de standaard IPsec/IKE-beleidssets voor Azure VPN-gateways?

Ja, zodra een aangepast beleid is opgegeven voor een verbinding, gebruikt de Azure VPN-gateway alleen het beleid op de verbinding, zowel als IKE-initiator als IKE-beantwoorder.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Als ik een aangepast beleid voor IPsec/IKE verwijder, wordt de verbinding dan onbeveiligd?

Nee, de verbinding wordt nog steeds beveiligd met IPsec/IKE. Wanneer u het aangepaste beleid van een verbinding verwijdert, wordt de Azure VPN-gateway teruggezet naar de [standaardlijst met IPsec/IKE-voorstellen](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) en wordt de IKE-handshake opnieuw gestart met uw on-premises VPN-apparaat.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Kan het toevoegen of bijwerken van een beleid voor IPsec/IKE mijn VPN-verbinding verstoren?

Ja, dit kan tot een onderbreking van een paar seconden leiden, omdat de Azure VPN-gateway de bestaande verbinding verbreekt en de IKE-handshake opnieuw start om de IPsec-tunnel opnieuw in te stellen met de nieuwe cryptografische algoritmen en parameters. Zorg ervoor dat uw on-premises VPN-apparaat met dezelfde algoritmen en sleutelsterkten wordt geconfigureerd om de onderbreking te minimaliseren.

### <a name="can-i-use-different-policies-on-different-connections"></a>Kan ik verschillende beleidsregels voor verschillende verbindingen gebruiken?

Ja. Aangepast beleid wordt per verbinding toegepast. U kunt verschillende IPsec/IKE-beleidsregels toepassen op verschillende verbindingen. U kunt ook aangepaste beleidsregels toepassen op een subset van verbindingen. Voor de resterende verbindingen worden de standaard IPsec/IKE-beleidssets voor Azure toegepast.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Kan ik het aangepaste beleid ook voor een VNet-naar-VNet-verbinding gebruiken?

Ja, u kunt een aangepast beleid toepassen op zowel cross-premises IPsec-verbindingen als VNet-naar-VNet-verbindingen.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Moet ik hetzelfde beleid opgeven voor beide VNet-naar-VNet-verbindingsresources?

Ja. Een VNet-naar-VNet-tunnel bestaat uit twee verbindingsresources in Azure, één voor elke richting. Zorg dat beide verbindingsresources hetzelfde beleid hebben, anders kan de VNet-naar-VNet-verbinding niet tot stand worden gebracht.

### <a name="what-is-the-default-dpd-timeout-value-can-i-specify-a-different-dpd-timeout"></a>Wat is de standaard time-outwaarde voor DPD? Kan ik een andere time-out voor DPD opgeven?

De standaard time-out voor DPD is 45 seconden. U kunt een andere time-outwaarde voor DPD opgeven voor elke IPsec of VNet-naar-VNet-verbinding tussen 9 tot 3600 seconden.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Werkt een aangepast IPsec/IKE-beleid op een ExpressRoute-verbinding?

Nee. IPsec/IKE-beleid werkt alleen op S2S-VPN- en VNet-naar-VNet-verbindingen via de Azure VPN-gateways.

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>Hoe maak ik verbindingen met IKEv1- of IKEv2-protocoltypen?

IKEv1-verbindingen kunnen worden gemaakt op alle RouteBased VPN-type SKU's, met uitzondering van de Basic, Standard en andere [verouderde SKU's](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#gwsku). U kunt een verbindingsprotocol opgeven van IKEv1 of IKEv2 wanneer u een verbinding maakt. Als u geen verbindingsprotocoltype opgeeft, wordt IKEv2 gebruikt als standaardinstelling waar dat mogelijk is. Raadpleeg de documentatie voor [PowerShell-cmdlets](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) voor meer informatie. Voor SKY-typen en IKEv1-/IKEv2-ondersteuning, raadpleegt u [Gateways verbinden met op beleid gebaseerde VPN-apparaten](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>Is er doorvoer tussen IKEv1- en IKEv2-verbindingen toegestaan?

Ja. Doorvoer tussen IKEv1- en IKEv2-verbindingen wordt ondersteund.

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>Kan ik een IKEv1-site-naar-site-verbindingen hebben in het VPN-type Basic SKU of RouteBased?

Nee. De Basic SKU ondersteunt dit niet.

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>Kan ik het verbindingsprotocoltype wijzigen nadat de verbinding is gemaakt (IKEv1 naar IKEv2 en vice versa)?

Nee. Nadat de verbinding is gemaakt, kunnen IKEv1-/IKEv2-protocollen niet worden gewijzigd. U moet een verbinding verwijderen en een nieuwe maken met het gewenste protocoltype.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Waar vind ik meer configuratie-informatie voor IPsec?

Zie [IPSec/IKE-beleid configureren voor S2S-of vnet-naar-vnet-verbindingen](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md).