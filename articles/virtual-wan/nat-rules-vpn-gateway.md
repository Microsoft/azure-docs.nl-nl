---
title: VPN NAT-regels configureren voor uw gateway
titleSuffix: Azure Virtual WAN
description: Meer informatie over het configureren van NAT-regels voor uw VWAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745866"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>NAT-regels configureren voor uw virtuele WAN-VPN-gateway-preview

> [!IMPORTANT]
> NAT-regels zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt uw virtuele WAN-VPN-gateway configureren met statische een-op-een NAT-regels. Een NAT-regel biedt een mechanisme voor het instellen van een-op-een-omzetting van IP-adressen. NAT kan worden gebruikt om twee IP-netwerken met incompatibele of overlappende IP-adressen te verbinden. Een typisch scenario is vertakkingen met overlappende IP-adressen die toegang willen hebben tot Azure VNet-resources.

Deze configuratie maakt gebruik van een stroom tabel voor het routeren van verkeer van een extern (host) IP-adres naar een intern IP-adres dat is gekoppeld aan een eind punt in een virtueel netwerk (virtuele machine, computer, container, enz.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram waarin architectuur wordt weer gegeven.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Regels configureren en weer geven

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

### <a name="configuration-considerations"></a>Overwegingen bij de configuratie

* De grootte van het subnet voor zowel interne als externe toewijzing moet hetzelfde zijn voor statische een-op-een NAT.
* Zorg ervoor dat u de VPN-site in de Azure Portal bewerkt om **ExternalMapping** -voor voegsels toe te voegen in het veld persoonlijke adres ruimte. Op dit moment moeten sites waarvoor BGP is ingeschakeld, ervoor zorgen dat de on-premises BGP-aankondiging (BGP-instellingen van het apparaat) een vermelding voor de externe toewijzings voorvoegsels bevat.

## <a name="next-steps"></a>Volgende stappen

Zie [een virtuele WAN-site-naar-site-verbinding configureren](virtual-wan-site-to-site-portal.md)voor meer informatie over site-naar-site-configuraties.
