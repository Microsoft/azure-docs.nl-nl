---
title: Virtuele WAN-partners en-locaties van Azure | Microsoft Docs
description: Dit artikel bevat een lijst met virtuele WAN-partners en hub-locaties van Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: cherylmc
ms.custom: references_regions
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 1e8a8aa632d4c91cdfc6753d988a439d2de8938e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040533"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuele WAN-partners en virtuele-hub-locaties

In dit artikel vindt u informatie over regio's en partners die worden ondersteund voor virtuele WANs voor connectiviteit in virtuele hub.

Azure Virtual WAN is een netwerkservice die via Azure voor een geoptimaliseerde en geautomatiseerde verbinding tussen filialen zorgt. Met Virtual WAN kunt u apparaten in filialen verbinden en configureren, zodat deze met Azure kunnen communiceren. De verbinding en configuratie kan hand matig worden uitgevoerd, of door middel van provider apparaten via een virtuele WAN-partner. Door gebruik te maken van partner apparaten kunt u gebruiks gemak, vereenvoudiging van connectiviteit en configuratie beheer gebruiken.

De connectiviteit van het on-premises apparaat wordt op een geautomatiseerde manier ingesteld voor de virtuele hub. Een virtuele hub is een virtueel netwerk dat door micro soft wordt beheerd. De hub bevat verschillende service-eindpunten die verbindingen vanaf uw on-premises netwerk (vpnsite) mogelijk maken. U kunt slechts één hub per regio hebben.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Versleutelde IPsec-connectiviteits automatisering van partners

Apparaten die verbinding maken met Azure Virtual WAN hebben ingebouwde automatisering om verbinding te maken. Dit wordt meestal ingesteld in de gebruikers interface van het apparaat (of gelijkwaardig), waarmee de connectiviteit en het configuratie beheer tussen het VPN-vertakkings apparaat worden ingesteld op een Azure Virtual hub VPN-eind punt (VPN-gateway).

De volgende automatisering op hoog niveau is ingesteld in de console van het apparaat en het beheer centrum:

* Juiste machtigingen voor het apparaat om toegang te krijgen tot de Azure Virtual WAN-resource groep
* Een vertakkings apparaat uploaden naar een virtueel WAN van Azure
* Automatisch downloaden van Azure-verbindings gegevens
* Configuratie van een on-premises vertakkings apparaat 

Sommige connectiviteits partners kunnen de automatisering uitbreiden om de virtuele Azure-hub VNet en VPN Gateway op te nemen. Als u meer wilt weten over Automation, raadpleegt u de [richt lijnen voor automatisering voor virtuele WAN-partners](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Filiaal-verbindings partners

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

De volgende partners zijn zou op ons plan op basis van een voor waarden blad dat is ondertekend tussen de bedrijven die de reik wijdte van de werkzaamheden voor het automatiseren van de IPsec-verbinding tussen het partner apparaat en Azure Virtual WAN VPN-gateways: 128 technologieën, Arista, F5 Networks, Oracle SD-WAN (talari) en SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partners met geïntegreerde Virtual hub-aanbiedingen

Sommige partners hebben niet alleen IPsec-verbinding met een geautomatiseerd filiaal, maar ook **nva's (Network Virtual Appliances)** die rechtstreeks kunnen worden geïntegreerd in de virtuele WAN-hub van Azure.  Hierdoor kunnen klanten de mogelijkheid hebben om hun vertakkings verbindingen te beëindigen in een compatibel apparaat van derden in de virtuele hub.  

Partners die NVA bieden in de virtuele WAN-hub, moeten:

* De automatisering van de IPsec-verbinding vanaf hun vertakkings apparaat heeft geïmplementeerd en de NVA-aanbieding aan Azure Virtual WAN hub hebben toegevoegd.
* Een bestaand virtuele netwerk apparaat beschikbaar stellen in azure Marketplace.

Als u een partner bent en vragen hebt over de beheerde NVA in de virtuele hub-aanbieding, neemt u contact met ons op door een e-mail te verzenden naar vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Geïntegreerde virtuele hub-NVA-partners

Deze partners hebben **beheerde toepassings** aanbiedingen die nu beschikbaar zijn voor implementatie in de virtuele WAN-hub.

|Partners|Hand leiding voor configuratie/instructies/implementatie|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Barracuda CloudGen WAN Deployment Guide (Engelstalig)](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud service router (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Tijdens de open bare preview van Cisco Cloud Services (CSR) WAN in VWAN hub vereist Cisco dat de eind klant zich registreert als een Cisco EFT (vroege veld proef versie) door een e-mail te verzenden naar vwan_public_preview@external.cisco.com en de implementatie handleiding voor vManage te aanvragen. |

De volgende partners zijn zou om NVA in de virtuele hub te bieden in de nabije toekomst: Aviatrix-, Citrix-, VeloCloud-en versa-netwerken.

## <a name="locations"></a><a name="locations"></a>Locaties

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie de [Veelgestelde vragen over virtuele WAN](virtual-wan-faq.md)voor meer informatie over Virtual WAN.

* Zie [Automation-richt lijnen voor virtuele WAN-partners](virtual-wan-configure-automation-providers.md)voor meer informatie over het automatiseren van de connectiviteit met Azure Virtual WAN.
