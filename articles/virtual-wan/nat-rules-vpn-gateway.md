---
title: VPN NAT-regels configureren voor uw gateway
titleSuffix: Azure Virtual WAN
description: Meer informatie over het configureren van NAT-regels voor uw VWAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431191"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>NAT-regels configureren voor uw virtuele WAN-VPN-gateway-preview

> [!IMPORTANT]
> NAT-regels zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt uw virtuele WAN-VPN-gateway configureren met statische een-op-een NAT-regels. Een NAT-regel biedt een mechanisme voor het instellen van een-op-een-omzetting van IP-adressen. NAT kan worden gebruikt om twee IP-netwerken met incompatibele of overlappende IP-adressen te verbinden. Een typisch scenario is vertakkingen met overlappende IP-adressen die toegang willen hebben tot Azure VNet-resources.

Deze configuratie maakt gebruik van een stroom tabel voor het routeren van verkeer van een extern (host) IP-adres naar een intern IP-adres dat is gekoppeld aan een eind punt in een virtueel netwerk (virtuele machine, computer, container, enz.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram waarin architectuur wordt weer gegeven.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>NAT-regels configureren

U kunt NAT-regels op elk gewenst moment configureren en weer geven op uw VPN-gateway-instellingen.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Scherm opname waarin regels worden bewerkt.":::

1. Navigeer naar uw virtuele hub.
1. Selecteer **VPN (site naar site)**.
1. Selecteer **NAT-regels (bewerken)**.
1. Op de pagina **NAT-regel bewerken** kunt u een NAT-regel **toevoegen/bewerken/verwijderen** met de volgende waarden:

   * **Naam:** Een unieke naam voor uw NAT-regel.
   * **Type:** Storing. Statische een-op-een NAT brengt een een-op-een-relatie tot stand tussen een intern adres en een extern adres.
   * **Modus:** IngressSnat of EgressSnat.  
      * De IngressSnat-modus (ook wel bekend als ingangs bron-NAT) is van toepassing op verkeer dat de site-naar-site VPN-gateway van de Azure-hub invult.
      * De EgressSnat-modus (ook wel bekend als uitgangs bron-NAT) is van toepassing op verkeer dat de site-naar-site VPN-gateway van de Azure-hub verlaat.
   * **InternalMapping:** Een adres voorvoegsel bereik met bron-IP-adressen in het binnen netwerk dat wordt toegewezen aan een set externe IP-adressen. Met andere woorden, uw vooraf-NAT-adres voorvoegsel bereik.
   * **ExternalMapping:** Een adres voorvoegsel bereik van bestemmings-IP-adressen op het externe netwerk waaraan de bron-Ip's worden toegewezen. Met andere woorden, uw post-NAT-adres voorvoegsel bereik.
   * **Verbinding koppelen:** Verbindings bron die vrijwel een VPN-site verbindt met de site-naar-site VPN-gateway van Azure hub.

### <a name="configuration-considerations"></a><a name="considerations"></a>Overwegingen bij de configuratie

* De grootte van het subnet voor zowel interne als externe toewijzing moet hetzelfde zijn voor statische een-op-een NAT.
* Zorg ervoor dat u de VPN-site in de Azure Portal bewerkt om **ExternalMapping** -voor voegsels toe te voegen in het veld persoonlijke adres ruimte. Op dit moment moeten sites waarvoor BGP is ingeschakeld, ervoor zorgen dat de on-premises BGP-aankondiging (BGP-instellingen van het apparaat) een vermelding voor de externe toewijzings voorvoegsels bevat.

## <a name="examples-and-verification"></a><a name="examples"></a>Voor beelden en verificatie

### <a name="ingress-mode-nat"></a>Ingangs modus NAT

De inschakel modus NAT-regels worden toegepast op pakketten die Azure invoeren via de VPN-gateway van het virtuele WAN-site. In dit scenario wilt u twee site-naar-site VPN-vertakkingen verbinden met Azure. VPN-site 1 maakt verbinding via Link1 en VPN-site 2 maakt verbinding via koppeling 2. Elke site heeft de adres ruimte 192.169.1.0/24.

In het volgende diagram ziet u het verwachte eind resultaat:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagram van de ingangs modus NAT.":::

1. Geef een NAT-regel op.

   Geef een NAT-regel op om ervoor te zorgen dat de site-naar-site VPN-gateway onderscheid kan maken tussen de twee vertakkingen met overlappende adres ruimten (zoals 192.168.1.0/24). In dit voor beeld zijn we gericht op Link1 voor VPN-site 1.

   De volgende NAT-regel kan worden ingesteld en gekoppeld aan koppeling 1 van een van de vertakkingen. Omdat dit een statische NAT-regel is, bevatten de adres ruimten van de InternalMapping en ExternalMapping hetzelfde aantal IP-adressen.

   * **Naam:** IngressRule01
   * **Type:** Storing
   * **Modus:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Verbinding koppelen:** Koppeling 1

1. Adverteer de juiste ExternalMapping.

   In deze stap zorgt u ervoor dat uw site-naar-site VPN-gateway de juiste ExternalMapping-adres ruimte adverteert aan de rest van uw Azure-resources. Er zijn verschillende instructies, afhankelijk van of BGP is ingeschakeld of niet.

   **Voor beeld 1: BGP is ingeschakeld**

   * Zorg ervoor dat de on-premises BGP-spreker op de VPN-site 1 is geconfigureerd voor het adverteren van de 10.1.1.0/24-adres ruimte.
   * Tijdens deze preview-fase moeten sites waarvoor BGP is ingeschakeld, ervoor zorgen dat de on-premises BGP-aankondiging (BGP-instellingen van het apparaat) een vermelding voor de externe toewijzings voorvoegsels bevat.

   **Voor beeld 2: BGP is niet ingeschakeld**

   * Navigeer naar de virtuele hub-resource die de site-naar-site-VPN-gateway bevat. Selecteer op de pagina virtuele hub onder **connectiviteit** de optie **VPN (site-naar-site)**.
   * Selecteer de VPN-site die is verbonden met de virtuele WAN-hub via koppeling 1. Selecteer **site** en invoer 10.1.1.0/24 als de privé-adres ruimte voor de VPN-site.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Scherm afbeelding die de pagina VPN-site bewerken weergeeft.":::

### <a name="packet-flow"></a>Pakket stroom

In dit voor beeld wil een on-premises apparaat een spoke-virtueel netwerk bereiken. De pakket stroom is als volgt, waarbij de NAT-vertalingen vet worden weer gegeven.

1. Verkeer van on-premises wordt gestart.
   * IP-adres van Bron: **192.168.1.1**
   * Doel-IP-adres: 30.0.0.1
1. Verkeer voert site-naar-site gateway in en wordt omgezet met behulp van de NAT-regel en vervolgens naar de spoke verzonden.
   * IP-adres van Bron: **10.1.1.1**
   * Doel-IP-adres: 30.0.0.1
1. Antwoord van spoke wordt gestart.
   * IP-adres van Bron: 30.0.0.1
   * IP-adres van doel: **10.1.1.1**
1. Verkeer voert de site-naar-site-VPN-gateway in en de vertaling wordt omgekeerd en naar on-premises verzonden.
   * IP-adres van Bron: 30.0.0.1
   * IP-adres van doel: **192.168.1.1**

### <a name="verification-checks"></a>Verificatie controles

In deze sectie wordt gecontroleerd of de configuratie juist is ingesteld.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>DefaultRouteTable, regels en routes valideren

Vertakkingen in het virtuele WAN koppelen aan de **DefaultRouteTable**, waarbij alle vertakkings verbindingen de routes ontdekken die in de DefaultRouteTable zijn gevuld. De NAT-regel wordt met het voor voegsel externe toewijzing weer geven in de actieve routes van de DefaultRouteTable.

Voorbeeld:

* **Voor voegsel:** 10.1.1.0/24  
* **Type volgende hop:** VPN_S2S_Gateway
* **Volgende hop:** VPN_S2S_Gateway resource

#### <a name="validate-address-prefixes"></a>Adres voorvoegsels valideren

Dit voor beeld is van toepassing op resources in virtuele netwerken die zijn gekoppeld aan de DefaultRouteTable.

De meest **efficiënte routes** op de netwerk interface kaarten (NIC) van elke virtuele machine in een spoke-virtueel netwerk dat is verbonden met de virtuele WAN-hub, moeten ook de adres voorvoegsels van de NAT-regels **ExternalMapping** bevatten.

#### <a name="validate-bgp-advertisements"></a>BGP-advertisements valideren

Als u BGP hebt geconfigureerd op de VPN-site verbinding, controleert u de on-premises BGP-spreker om er zeker van te zijn dat deze een vermelding voor de voor voegsels voor externe toewijzingen adverteert.

## <a name="next-steps"></a>Volgende stappen

Zie [een virtuele WAN-site-naar-site-verbinding configureren](virtual-wan-site-to-site-portal.md)voor meer informatie over site-naar-site-configuraties.
