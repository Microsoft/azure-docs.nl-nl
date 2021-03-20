---
title: Selectie van Azure-pad op meerdere ISP-koppelingen
titleSuffix: Azure Virtual WAN
description: Meer informatie over het selecteren van Azure-paden en virtuele WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267751"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Selectie van Azure-pad op meerdere ISP-koppelingen

Azure Virtual WAN biedt een gebruiker de mogelijkheid om koppelings gegevens op te kunnen stellen op een VPN-site, waarbij scenario's worden ingeschakeld waarin de oplossing VPN/SD-WAN-apparaat vertakkings beleid kan Program meren om verkeer over diverse koppelingen naar Azure te sturen. Dit heet **Azure**-Padselectie.

## <a name="architecture"></a>Architectuur

We gaan het voor beeld van een virtuele WAN-site en een site-naar-site-verbinding gebruiken om te begrijpen hoe de selectie van Azure-paden werkt.

Een VPN-site vertegenwoordigt het on-premises SD-WAN/VPN-apparaat met informatie zoals een openbaar IP-adres, model en naam, enzovoort. De huidige on-premises VPN-site kan meerdere ISP-koppelingen hebben die ook kunnen worden opgenomen in de gegevens van de virtuele WAN-VPN-site. Zo kunt u de koppelings gegevens in azure bekijken.

Een site-naar-site-IPsec-verbinding in een virtueel WAN-netwerk wordt beëindigd op de VPN-gateway instanties binnen een virtuele hub. Een site-naar-site-verbinding vertegenwoordigt de connectiviteit tussen de VPN-site en de Azure VPN-gateway. Het bestaat uit een of meer koppelings verbindingen. Elke koppelings verbinding bestaat uit twee tunnels waarbij elke tunnel wordt beëindigd op een uniek exemplaar van de Azure Virtual WAN VPN-gateway. Er kunnen Maxi maal vier koppelings verbindingen worden ingesteld in de site-naar-site-verbinding, waardoor het mogelijk is om Maxi maal acht tunnels te hebben binnen een site-naar-site-verbinding. Azure ondersteunt Maxi maal 2000 tunnels die worden beëindigd binnen één virtuele WAN-VPN-gateway.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagram met meerdere koppelingen":::

In deze afbeelding ziet u meerdere koppelingen op een site die verbinding maakt met een virtueel WAN van Azure. In dit diagram:

* Er zijn twee ISP-koppelingen op de on-premises Branch (VPN/SD-WAN-apparaat). Elke ISP-koppeling komt overeen met een koppelings verbinding.

* Er wordt van uitgegaan dat de on-premises klant Manager VPN/SD-WAN-apparaat IKEv1 of IKEv2 IPsec ondersteunt.

* Elke virtuele WAN-verbinding van Azure site-naar-site bestaat uit koppelings verbindingen binnen zichzelf. Een verbinding ondersteunt Maxi maal vier koppelings verbindingen. Azure brengt een kosten voor de verbindings eenheid in rekening voor de virtuele WAN-verbinding. Er worden geen kosten in rekening gebracht voor de koppelings verbindingen.

* Elke koppelings verbinding bestaat op zijn beurt uit twee IPsec-tunnels die op twee verschillende exemplaren van de virtuele WAN-VPN-gateway kunnen worden beëindigd. De gateways worden ingesteld als actieve-actieve gateways voor tolerantie. Elke koppelings verbinding is vereist voor een uniek IP-adres en een IP van BGP-peering. In het diagram kan tunnel 0 eindigen op instantie 0 en tunnel 1 kan eindigen op instantie 1.

* Branch-apparaten die het selecteren van het pad bieden, kunnen het juiste beleid inschakelen in de oplossing voor vertakkings beheer om verkeer te sturen naar meerdere koppelingen naar Azure. De koppeling van de Internet provider 1 kan bijvoorbeeld worden gebruikt voor verkeer met een hogere prioriteit en de koppeling van de Internet provider 2 kan worden gebruikt als back-up.

* Het is belang rijk te weten dat virtuele HUB VPN gebruikmaakt van ECMP (gelijke kosten Multi-Path route ring) voor alle eindigende tunnels.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen over Azure](virtual-wan-faq.md).