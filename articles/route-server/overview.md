---
title: Wat is Azure route server (preview)?
description: Meer informatie over hoe Azure route server route ring tussen uw virtuele netwerk apparaat (NVA) en uw virtuele netwerk kan vereenvoudigen.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: d868c064b96f58ab3febc1fd3b7f20b74d507cb0
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437158"
---
# <a name="what-is-azure-route-server-preview"></a>Wat is Azure route server (preview)? 

Azure route Server vereenvoudigt dynamische route ring tussen uw virtuele netwerk apparaat (NVA) en uw virtuele netwerk. U kunt hiermee routerings gegevens rechtstreeks uitwisselen via Border Gateway Protocol (BGP) routerings Protocol tussen alle NVA die ondersteuning bieden voor het BGP-routerings protocol en het SDN (Azure software defined Network) in azure Virtual Network (VNET) zonder dat u hand matig route tabellen hoeft te configureren of te onderhouden. Azure route server is een volledig beheerde service die is geconfigureerd met hoge Beschik baarheid.

> [!IMPORTANT]
> Azure route server (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="how-does-it-work"></a>Hoe werkt het?

In het volgende diagram ziet u hoe Azure route server werkt met een SDWAN-NVA en een beveiligings NVA in een virtueel netwerk. Zodra u de BGP-peering hebt ingesteld, ontvangt Azure route server een on-premises route (10.250.0.0/16) van het SDWAN-apparaat en een standaard route (0.0.0.0/0) van de firewall. Deze routes worden vervolgens automatisch geconfigureerd op de Vm's in het virtuele netwerk. Als gevolg hiervan wordt al het verkeer dat bestemd is voor het on-premises netwerk, verzonden naar het SDWAN-apparaat. Hoewel al het Internet-afhankelijk verkeer wordt verzonden naar de firewall. In tegenovergestelde richting verzendt Azure route server het virtuele netwerk adres (10.1.0.0/16) naar beide Nva's. Het SDWAN-apparaat kan dit verder naar het on-premises netwerk door geven.

![Diagram met de Azure route server die is geconfigureerd in een virtueel netwerk.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Belangrijkste voordelen 

Azure route Server vereenvoudigt de configuratie, het beheer en de implementatie van uw NVA in uw virtuele netwerk.  

* U hoeft de routerings tabel niet meer hand matig bij te werken op uw NVA wanneer uw virtuele netwerk adressen worden bijgewerkt. 

* U hoeft door de [gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md) niet meer hand matig bij te werken wanneer uw NVA nieuwe routes aankondigt of een oud onderdeel intrekt. 

* U kunt meerdere exemplaren van uw NVA peeren met Azure route server. U kunt de BGP-kenmerken in uw NVA configureren en, afhankelijk van uw ontwerp (bijvoorbeeld actief-actief voor prestaties of actief-passief voor tolerantie), ervoor zorgen dat Azure route server weet welk NVA-exemplaar actief is of welk passief is. 

* De interface tussen NVA en Azure route server is gebaseerd op een gemeen schappelijk standaard protocol. Als uw NVA BGP ondersteunt, kunt u het peeren met behulp van Azure route server. Zie [routerings protocollen die worden ondersteund door route server](route-server-faq.md#protocol)voor meer informatie.

* U kunt Azure route server implementeren in een van uw nieuwe of bestaande virtuele netwerken. 

## <a name="faq"></a>Veelgestelde vragen

Voor veelgestelde vragen over Azure route Server raadpleegt u [Veelgestelde vragen over Azure route server](route-server-faq.md).

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het configureren van Azure route server](quickstart-configure-route-server-powershell.md)
- [Meer informatie over de werking van Azure route server met Azure ExpressRoute en Azure VPN](expressroute-vpn-support.md)
