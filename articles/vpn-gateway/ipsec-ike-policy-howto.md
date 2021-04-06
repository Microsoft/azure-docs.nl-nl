---
title: 'IPsec/IKE-beleid voor S2S VPN-& VNet-naar-VNet-verbindingen: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Configureer IPsec/IKE-beleid voor S2S-of VNet-naar-VNet-verbindingen met Azure VPN-gateways met behulp van Azure Resource Manager en Azure Portal.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878541"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>IPsec/IKE-beleid configureren voor S2S VPN-of VNet-naar-VNet-verbindingen: Azure Portal

In dit artikel worden de stappen beschreven voor het configureren van IPsec/IKE-beleid voor VPN Gateway site-naar-site VPN-of VNet-naar-VNet-verbindingen met behulp van de Azure Portal. In de volgende secties vindt u informatie over het maken en configureren van een IPsec/IKE-beleid en het beleid Toep assen op een nieuwe of bestaande verbinding.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Over IPsec-en IKE-beleids parameters

IPsec en IKE-protocol standaard bieden ondersteuning voor een breed scala van cryptografische algoritmen in verschillende combi Naties. Raadpleeg [over cryptografische vereisten en Azure VPN-gateways](vpn-gateway-about-compliance-crypto.md) om te zien hoe dit kan helpen om te zorgen voor cross-premises en vnet-naar-vnet-connectiviteit om te voldoen aan uw nalevings-en beveiligings vereisten.

Dit artikel bevat instructies voor het maken en configureren van een IPsec/IKE-beleid en het Toep assen op een nieuwe of bestaande VPN Gateway verbinding.

### <a name="considerations"></a>Overwegingen

* IPsec/IKE-beleid werkt alleen op de volgende gateway-Sku's:
  * ***VpnGw1 ~ 5 en VpnGw1AZ ~ 5AZ***
  * ***Standaard** _ en _ *_High Performance_**
* U kunt maar ***één*** beleidscombinatie opgeven voor een bepaalde verbinding.
* U moet alle algoritmen en para meters opgeven voor zowel IKE (hoofd modus) als IPsec (snelle modus). Gedeeltelijke beleidsspecificatie is niet toegestaan.
* Neem contact op met de specificaties van de leverancier van uw VPN-apparaat om ervoor te zorgen dat het beleid wordt ondersteund op uw on-premises VPN-apparaten. S2S-of VNet-naar-VNet-verbindingen kunnen niet bepalen of het beleid niet compatibel is.

## <a name="workflow"></a><a name ="workflow"></a>Werkstroom

In deze sectie wordt de werk stroom beschreven om IPsec/IKE-beleid te maken en bij te werken op een S2S VPN-of VNet-naar-VNet-verbinding:

1. Maak een virtueel netwerk en een VPN-gateway.
2. Maak een lokale netwerk gateway voor cross-premises verbinding of een ander virtueel netwerk en een andere gateway voor VNet-naar-VNet-verbindingen.
3. Maak een verbinding (IPsec of VNet2VNet).
4. Het IPsec/IKE-beleid op de verbindings resources configureren/bijwerken/verwijderen.

De instructies in dit artikel helpen u bij het instellen en configureren van IPsec/IKE-beleid, zoals weer gegeven in het diagram:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE-beleids diagram" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Ondersteunde cryptografische algoritmen & sleutel sterktes

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmen en sleutels

De volgende tabel bevat de ondersteunde cryptografische algoritmen en sleutel sterktes die door de klanten kunnen worden geconfigureerd:

| **IPsec/IKE**    | **Opties**    |
| ---              | ---            |
| IKE-versleuteling   | AES256, AES192, AES128, DES3, DES                  |
| IKE-integriteit    | SHA384, SHA256, SHA1, MD5                          |
| DH-groep         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, geen |
| IPsec-versleuteling | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, geen    |
| IPsec-integriteit  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-groep        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, geen   |
| QM SA-levensduur   | (**Optioneel**: de standaard waarden worden gebruikt als er geen waarde is opgegeven)<br>Seconden (geheel getal; **min. 300**/standaard 27000 seconden)<br>KB (geheel getal; **min. 1024**/standaard 102400000 KB)    |
| Verkeersselector | UsePolicyBasedTrafficSelectors * * ($True/$False; **Optioneel**, standaard $false indien niet opgegeven)    |
| DPD-out      | Seconden (geheel getal: min. 9/Max. 3600; standaard 45 seconden) |
|  |  |

#### <a name="important-requirements"></a>Belang rijke vereisten

* De configuratie van uw on-premises VPN-apparaat moet overeenkomen met of de volgende algoritmen en parameters bevatten die u in het Azure IPsec/IKE-beleid opgeeft:
  * IKE-versleutelings algoritme (hoofd modus/fase 1)
  * IKE-integriteits algoritme (hoofd modus/fase 1)
  * DH-groep (hoofd modus/fase 1)
  * IPsec-versleutelings algoritme (snelle modus/fase 2)
  * IPsec-integriteits algoritme (snelle modus/fase 2)
  * PFS-groep (snelle modus/fase 2) > * verkeers selectie (als UsePolicyBasedTrafficSelectors wordt gebruikt)
  * De SA-levensduren zijn alleen lokale specificaties en hoeven niet overeen te komen.

* Als GCMAES wordt gebruikt als voor IPsec-versleutelings algoritme, moet u hetzelfde GCMAES-algoritme en dezelfde sleutel lengte voor IPsec-integriteit selecteren. u kunt bijvoorbeeld GCMAES128 gebruiken voor beide.

* In de bovenstaande [tabel met algoritmen en sleutels](#table1) :
  * IKE komt overeen met de hoofd modus of fase 1
  * IPsec komt overeen met snelle modus of fase 2
  * DH-groep Hiermee geeft u de Diffie-Hellmen groep op die in de hoofd modus of fase 1 wordt gebruikt
  * PFS-groep opgegeven de Diffie-Hellmen groep die wordt gebruikt in de snelle modus of fase 2

* De levens duur van de SA-IKE-hoofd modus wordt 28.800 seconden op de Azure VPN-gateways vastgesteld.

* Als u **UsePolicyBasedTrafficSelectors** instelt op $True voor een verbinding, wordt de Azure VPN-gateway geconfigureerd om verbinding te maken met op beleid gebaseerde VPN-Firewall on-premises. Als u PolicyBasedTrafficSelectors inschakelt, moet u ervoor zorgen dat op uw VPN-apparaat de overeenkomende verkeers selectie is gedefinieerd met alle combi Naties van de voor voegsels van uw on-premises netwerk (lokale netwerk gateway) naar/van de voor voegsels van het virtuele Azure-netwerk (in plaats van any). Als uw lokale netwerkvoorvoegsels bijvoorbeeld 10.1.0.0/16 en 10.2.0.0/16 zijn, en de voorvoegsels van uw virtuele netwerk 192.168.0.0/16 en 172.16.0.0/16, moet u de volgende verkeersselectoren opgeven:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Zie voor meer informatie over op beleid gebaseerde verkeers selecties [verbinding maken met meerdere on-premises op beleid gebaseerde VPN-apparaten](vpn-gateway-connect-multiple-policybased-rm-ps.md).

* DPD-out: de standaard waarde is 45 seconden op Azure VPN-gateways. Als u de time-out instelt op kortere Peri Oden, wordt IKE op agressief versleuteld, waardoor de verbinding in sommige exemplaren lijkt te zijn losgekoppeld. Dit is mogelijk niet wenselijk als uw on-premises locaties zich verder bevinden in de Azure-regio waar de VPN-gateway zich bevindt, of als de voor waarde voor de fysieke verbinding pakket verlies kan oplopen. De algemene aanbeveling is de time-out van **30 tot 45** seconden in te stellen.

### <a name="diffie-hellman-groups"></a>Diffie-Hellman groepen

De volgende tabel bevat de overeenkomende Diffie-Hellman groepen die worden ondersteund door het aangepaste beleid:

| **Diffie-Hellman-groep**  | **DHGroup**              | **PFSGroup** | **Sleutellengte** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-bits MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bits MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bits MODP  |
| 19                        | ECP256                   | ECP256       | 256-bits ECP    |
| 20                        | ECP384                   | ECP384       | 384-bits ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bits MODP  |

Raadpleeg [RFC3526](https://tools.ietf.org/html/rfc3526) en [RFC5114](https://tools.ietf.org/html/rfc5114) voor meer informatie.

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>S2S VPN met IPsec/IKE-beleid

In deze sectie worden de stappen beschreven voor het maken van een site-naar-site-VPN-verbinding met een IPsec/IKE-beleid. Met de volgende stappen maakt u de verbinding, zoals weer gegeven in het volgende diagram:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Beleid voor site-naar-site" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Stap 1: het virtuele netwerk, de VPN-gateway en de lokale netwerk gateway maken

Maak de volgende resources, zoals wordt weer gegeven in de onderstaande scherm afbeeldingen. Zie [een site-naar-site-VPN-verbinding maken](./tutorial-site-to-site-portal.md)voor instructies.

* **Virtueel netwerk:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN-gateway:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Gateway":::

* **Lokale netwerk gateway:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Site":::

* **Verbinding:** VNet1 naar Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Verbinding":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Stap 2: IPsec/IKE-beleid configureren voor de S2S-VPN-verbinding

In deze sectie configureert u een IPsec/IKE-beleid met de volgende algoritmen en para meters:

* IKE: AES256, SHA384, DHGroup24, DPD-time-out 45 seconden
* IPsec: AES256, SHA256, PFS geen, SA-levens duur van 30000 seconden en 102400000KB

1. Ga in het Azure Portal naar de verbindings bron **VNet1toSite6**. Selecteer **configuratie** pagina en selecteer **aangepast** IPSec/IKE-beleid om alle configuratie opties weer te geven. In de onderstaande scherm afbeelding ziet u de configuratie volgens de lijst:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Site 6":::

1. Als u GCMAES voor IPsec gebruikt, moet u hetzelfde GCMAES-algoritme en dezelfde sleutel lengte gebruiken voor IPsec-versleuteling en-integriteit. In de onderstaande scherm afbeelding worden bijvoorbeeld GCMAES128 voor IPsec-versleuteling en IPsec-integriteit opgegeven:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="GCMAES voor IPsec":::

1. U kunt optioneel **inschakelen** selecteren voor de optie **op beleid gebaseerde verkeers selectie gebruiken** om Azure VPN gateway in te scha kelen om verbinding te maken met op beleid gebaseerde VPN-apparaten op locatie, zoals hierboven wordt beschreven.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Op beleid gebaseerde verkeers selectie":::

1. Als alle opties zijn geselecteerd, selecteert u **Opslaan** om de wijzigingen in de verbindings bron door te voeren. Het beleid wordt in ongeveer een minuut afgedwongen.

> [!IMPORTANT]
>
> * Zodra een IPsec/IKE-beleid is opgegeven voor een verbinding, verzendt of accepteert de Azure VPN-gateway alleen het IPsec/IKE-voor stel met de opgegeven cryptografische algoritmen en sleutel sterkten op die specifieke verbinding. Zorg ervoor dat uw on-premises VPN-apparaat voor de verbinding de nauw keurige combi natie van het beleid gebruikt of accepteert, anders zal de S2S VPN-tunnel niet tot stand worden gebracht.
>
> * **Op beleid gebaseerde verkeers selectie** en **DPD time-** outopties kunnen worden opgegeven met het **standaard** beleid, zonder het aangepaste IPSec/IKE-beleid, zoals weer gegeven in de bovenstaande scherm afbeelding.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet-naar-VNet met IPsec/IKE-beleid

De stappen voor het maken van een VNet-naar-VNet-verbinding met een IPsec/IKE-beleid zijn vergelijkbaar met die van een S2S-VPN-verbinding.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Het VNet-naar-VNet-beleids diagram" border="false":::

1. Volg de stappen in het artikel [een vnet-naar-vnet-verbinding maken](vpn-gateway-vnet-vnet-rm-ps.md) om uw vnet-naar-vnet-verbinding te maken.

2. Nadat u de stappen hebt voltooid, ziet u twee VNet-naar-VNet-verbindingen, zoals wordt weer gegeven in de onderstaande scherm afbeelding van de VNet2GW-resource:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="VNet-naar-VNet-verbindingen":::

3. Ga naar de verbindings bron en ga naar de **configuratie** pagina op de portal. Selecteer **aangepast** in het **IPSec/IKE-beleid** om de aangepaste beleids opties weer te geven. Selecteer de cryptografische algoritmen met de bijbehorende sleutel lengten.

   De scherm opname bevat een ander IPsec/IKE-beleid met de volgende algoritmen en para meters:
   * IKE: AES128, SHA1, DHGroup14, DPD time-out 45 seconden
   * IPsec: GCMAES128, GCMAES128, PFS14, levens duur van 14400 seconden & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Verbindings beleid":::

4. Selecteer **Opslaan** om de beleids wijzigingen op de verbindings bron toe te passen.

5. Pas hetzelfde beleid toe op de andere verbindings bron VNet2toVNet1. Als dat niet het geval is, zal de IPsec/IKE VPN-tunnel geen verbinding maken vanwege niet-overeenkomende beleids regels.

   > [!IMPORTANT]
   > Zodra een IPsec/IKE-beleid is opgegeven voor een verbinding, verzendt of accepteert de Azure VPN-gateway alleen het IPsec/IKE-voor stel met de opgegeven cryptografische algoritmen en sleutel sterkten op die specifieke verbinding. Zorg ervoor dat het IPsec-beleid voor beide verbindingen hetzelfde is, anders zal de VNet-naar-VNet-verbinding niet tot stand worden gebracht.

6. Nadat u deze stappen hebt voltooid, wordt de verbinding in een paar minuten tot stand gebracht en hebt u de volgende netwerk topologie:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE-beleids diagram" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Aangepaste IPsec/IKE-beleid uit een verbinding verwijderen

1. Als u een aangepast beleid van een verbinding wilt verwijderen, gaat u naar de verbindings bron en gaat u naar de pagina **configuratie** om het huidige beleid weer te geven.

2. Selecteer **standaard** in de optie **IPSec/IKE-beleid** . Hiermee verwijdert u alle eerder opgegeven aangepaste beleids regels voor de verbinding en herstelt u de standaard instellingen voor IPsec/IKE voor deze verbinding:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Beleid verwijderen":::

3. Selecteer **Opslaan** om het aangepaste beleid te verwijderen en de standaard instellingen voor IPSec/IKE voor de verbinding te herstellen.

## <a name="next-steps"></a>Volgende stappen

Zie [verbinding maken met meerdere on-premises op beleid gebaseerde VPN-apparaten](vpn-gateway-connect-multiple-policybased-rm-ps.md) voor meer informatie over op beleid gebaseerde verkeers selecties.