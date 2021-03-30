---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "67175856"
---
De veelgestelde vragen voor VNet-naar-VNet zijn van toepassing op VPN Gateway-verbindingen. Zie [Peering op virtueel netwerk](../articles/virtual-network/virtual-network-peering-overview.md) voor informatie over VNet-peering.

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Worden er door Azure kosten in rekening gebracht voor verkeer tussen VNets?

VNet-naar-VNet-verkeer binnen dezelfde regio is gratis in beide richtingen wanneer u een VPN Gateway-verbinding gebruikt. Voor uitgaand VNet-naar-VNet-verkeer tussen regio's gelden de tarieven voor uitgaande gegevensoverdracht tussen VNets op basis van de bronregio's. Zie de pagina [Prijzen van VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) voor meer informatie. Zie [Prijzen voor virtuele netwerken](https://azure.microsoft.com/pricing/details/virtual-network/) als u uw VNets verbindt op basis van VNet-peering in plaats van VPN Gateway.

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Verloopt VNet-naar-VNet-verkeer via internet?

Nee. Voor VNet-naar-VNet-verkeer wordt het Microsoft-netwerk gebruikt in plaats van internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Kan ik een VNet-naar-VNet-verbinding maken tussen verschillende AAD-tenants (Azure Active Directory)?

Ja, VNet-naar-VNet-verbindingen met behulp van Azure VPN-gateways werken tussen verschillende AAD-tenants.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Is het VNet-naar-VNet-verkeer beveiligd?

Ja, het is beveiligd met IPsec/IKE-versleuteling.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Heb ik een VPN-apparaat nodig om VNets met elkaar te verbinden?

Nee. Om meerdere virtuele netwerken van Azure met elkaar te verbinden, hebt u geen VPN-apparaat nodig, tenzij cross-premises connectiviteit is vereist.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Moeten mijn VNets zich in dezelfde regio bevinden?

Nee. De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-regio's (locaties) bevinden.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Als de VNets niet tot hetzelfde abonnement behoren, moeten de abonnementen dan aan dezelfde Active Directory-tenant zijn gekoppeld?

Nee.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Kan ik VNet-naar-VNet-verbindingen gebruiken tussen virtuele netwerken in verschillende Azure-exemplaren? 

Nee. VNet-naar-VNet ondersteunt het verbinden van virtuele netwerken binnen hetzelfde Azure-exemplaar. U kunt bijvoorbeeld geen verbinding maken tussen wereldwijde Azure-exemplaren en Chinese/Duitse/US government Azure-exemplaren. Voor deze scenario's kunt u een site-naar-site-VPN-verbinding gebruiken.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Kan ik VNet-naar-VNet- én multi-site-verbindingen gebruiken?

Ja. U kunt virtuele-netwerkverbindingen tegelijk gebruiken met multi-site-VPN’s.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Met hoeveel on-premises sites en virtuele netwerken kan één virtueel netwerk verbinding maken?

Zie de tabel [Gatewayvereisten](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Kan ik VNet-naar-VNet gebruiken om virtuele machines of cloudservices met elkaar te verbinden buiten een VNet?

Nee. VNet naar VNet ondersteunt het verbinden van virtuele netwerken. Er is geen ondersteuning voor het verbinden van virtuele machines of cloudservices die geen deel uitmaken van een virtueel netwerk.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Kan een cloudservice of een taakverdelingseindpunt VNets overbruggen?

Nee. Een cloudservice of een taakverdelingseindpunt kan geen virtuele netwerken overbruggen, zelfs niet als ze met elkaar zijn verbonden.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Kan ik een op beleid gebaseerd VPN-type gebruiken voor VNet-naar-VNet- of multi-site-verbindingen?

Nee. VNet-naar-VNet- en multi-site-verbindingen vereisen Azure VPN-gateways met op route gebaseerde VPN-typen (voorheen dynamische routering genoemd).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan ik een VNet met een op route gebaseerd VPN-type verbinden met een op beleid gebaseerd VPN-type?

Nee, voor beide virtuele netwerken MOET gebruik worden gemaakt van op route gebaseerde VPN's (voorheen dynamische routering genoemd).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Delen VPN-tunnels bandbreedte?

Ja. Alle VPN-tunnels van het virtuele netwerk delen de beschikbare bandbreedte op de Azure VPN-gateway en dezelfde SLA voor VPN-gatewaybedrijfstijd in Azure.

### <a name="are-redundant-tunnels-supported"></a>Worden redundante tunnels ondersteund?

Redundante tunnels tussen twee virtuele netwerken worden ondersteund, mits één virtuele-netwerkgateway is geconfigureerd als actief-actief.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Mogen er overlappende adresruimten zijn voor VNet-naar-VNet-configuraties?

Nee. Er mag geen sprake zijn van overlappende IP-adresbereiken.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Mogen er overlappende adresruimten zijn tussen de verbonden virtuele netwerken en on-premises lokale sites?

Nee. Er mag geen sprake zijn van overlappende IP-adresbereiken.



