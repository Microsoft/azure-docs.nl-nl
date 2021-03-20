---
title: Wat is een Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Overzicht van functies, resources, architectuur en implementatie van Virtual Network NAT. Meer informatie over de werking van Virtual Network NAT en hoe u NAT-gatewayresources gebruikt in de cloud.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 75a2bb187b2ed7a234e99d8cd293cb30148bcb1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91667194"
---
# <a name="what-is-virtual-network-nat"></a>Wat is Azure Virtual Network NAT?

Virtual Network NAT (Network Address Translation) vereenvoudigt uitsluitend uitgaande internetconnectiviteit voor virtuele netwerken. Indien geconfigureerd op een subnet, maken alle uitgaande verbindingen gebruik van uw opgegeven statische openbare IP-adressen.  Uitgaande connectiviteit is mogelijk zonder load balancer of openbare IP-adressen die rechtstreeks zijn gekoppeld aan virtuele machines. NAT is volledig beheerd en in hoge mate tolerant.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="Virtual Network NAT">
</p>



*Afbeelding: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statische IP-adressen voor uitsluitend uitgaand verkeer

Voor elk subnet met NAT kan uitgaande connectiviteit worden gedefinieerd.  Meerdere subnetten binnen hetzelfde virtuele netwerk kunnen verschillende NAT's hebben. Een subnet wordt geconfigureerd door de NAT-gatewayresource op te geven die moet worden gebruikt. Alle uitgaande UDP- en TCP-stromen van elk exemplaar van een virtuele machine gebruiken NAT. 

NAT is compatibel met resources van openbare IP-adressen van de standaard-SKU of resources van openbare IP-voorvoegsels of een combinatie van beide.  U kunt een openbaar IP-voorvoegsel rechtstreeks gebruiken of de openbare IP-adressen van het voorvoegsel verdelen over meerdere NAT-gatewayresources. NAT zal al het verkeer opschonen naar het bereik van de IP-adressen van het voorvoegsel.  IP-filtering van uw implementaties is nu eenvoudig.

Al het uitgaande verkeer voor het subnet wordt automatisch door NAT verwerkt zonder klantconfiguratie.  Door de gebruiker gedefinieerde routes zijn niet nodig. NAT heeft voorrang op andere uitgaande scenario's en vervangt de standaardinternetbestemming van een subnet.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT op aanvraag met meerdere IP-adressen voor schalen

NAT gebruikt 'Port Network Address Translation' (PNAT of PAT) en wordt aanbevolen voor de meeste workloads. Dynamische of uiteenlopende workloads kunnen eenvoudig worden afgestemd op een uitgaande stroomtoewijzing op aanvraag. Uitgebreide planning vooraf, toewijzing vooraf en uiteindelijk overmatige inrichting van uitgaande resources wordt voorkomen. De SNAT-poortresources worden gedeeld en zijn beschikbaar in alle subnetten die een specifieke NAT-gatewayresource gebruiken en worden weergegeven wanneer dat nodig is.

Een openbaar IP-adres dat is gekoppeld aan NAT biedt respectievelijk Maxi maal 64.000 gelijktijdige stromen voor UDP en TCP. U kunt beginnen met één IP-adres en tot 16 IP-adressen schalen met behulp van open bare IP-adressen of open bare IP-voor voegsels of beide.  Een NAT-gateway bron maakt gebruik van alle IP-adressen die zijn gekoppeld aan de resource voor uitgaande verbindingen van alle subnetten die zijn geconfigureerd met dezelfde NAT-gateway resource.

Met NAT kunnen stromen worden gemaakt van het virtuele netwerk naar internet. Het retourverkeer vanaf internet is alleen toegestaan als reactie op een actieve stroom.

In tegenstelling tot uitgaande SNAT van load balancer heeft NAT geen beperkingen met betrekking tot welk privé-IP-adres van een VM-instantie uitgaande verbindingen kan maken.  Primaire en secundaire IP-configuraties kunnen uitgaande Internet verbinding maken met NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Co-existentie van inkomend en uitgaand

NAT is compatibel met de volgende standaard SKU-resources:

- Load balancer
- Openbaar IP-adres
- Voorvoegsel voor het openbare IP-adres

Bij gebruik in combinatie met NAT bieden deze resources inkomende internetconnectiviteit voor uw subnet(ten). NAT voorziet in alle uitgaande internetconnectiviteit vanaf uw subnet(ten).

NAT en compatibele functies van standaard-SKU zijn bekend met de richting waarin de stroom is gestart. De scenario's voor inkomend en uitgaand verkeer kunnen naast elkaar bestaan. Deze scenario's ontvangen de juiste omzettingen van het netwerkadres, omdat deze functies bekend zijn met de richting van de stroom. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Stroomrichting van Virtual Network NAT">
</p>

*Afbeelding: Stroomrichting van Virtual Network NAT*

## <a name="fully-managed-highly-resilient"></a>Volledig beheerd, in hoge mate tolerant

NAT wordt volledig uitgeschaald vanaf het begin. Er is geen bewerking voor verhogen of uitschalen vereist.  Azure beheert de werking van NAT voor u.  NAT heeft altijd meerdere foutdomeinen en kan meerdere storingen ondervinden zonder serviceonderbreking.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP Reset voor niet-herkende stromen

De privékant van NAT verzendt TCP Reset-pakketten voor pogingen om te communiceren op een TCP-verbinding die niet bestaat. Een voorbeeld hiervan zijn verbindingen die een time-out voor inactiviteit hebben bereikt. Het volgende ontvangen pakket retourneert een TCP Reset naar het privé-IP-adres om het sluiten van de verbinding te signaleren en af te dwingen.

De openbare kant van NAT genereert geen TCP Reset-pakketten of ander verkeer.  Alleen verkeer dat door het virtuele netwerk van de klant wordt geproduceerd, wordt verzonden.

## <a name="configurable-tcp-idle-timeout"></a>Configureerbare time-out voor TCP-inactiviteit

Er wordt een standaardtime-out voor TCP-inactiviteit van 4 minuten gebruikt, die tot 120 minuten worden verhoogd. Elke activiteit in een stroom kan ook de niet-actieve timer opnieuw instellen, inclusief TCP-keepalives.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionale of zone-isolatie met beschikbaarheidszones

NAT is standaard regionaal. Wanneer u scenario's voor [beschikbaarheidszones](../availability-zones/az-overview.md) maakt, kan NAT worden geïsoleerd in een specifieke zone (zonegebonden implementatie).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="Virtual Network NAT met beschikbaarheidszones">
</p>

*Afbeelding: Virtual Network NAT met beschikbaarheidszones*

## <a name="multi-dimensional-metrics-for-observability"></a>Multi-dimensionale metrische gegevens voor waarneembaarheid

U kunt de werking van uw NAT bewaken door middel van multidimensionale metrische gegevens die beschikbaar zijn in Azure Monitor. Deze metrische gegevens kunnen worden gebruikt om het gebruik te observeren en om problemen op te lossen.  NAT-gatewayresources geven de volgende metrische gegevens weer:
- Bytes
- Pakketten
- Verwijderde pakketten
- Totaal aantal SNAT-verbindingen
- Statusovergangen per interval van SNAT-verbinding.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Bij algemene beschikbaarheid is het NAT-gegevenspad ten minste 99,9% beschikbaar.

## <a name="pricing"></a>Prijzen

Zie [Virtual Network prijzen](https://azure.microsoft.com/pricing/details/virtual-network)voor prijs informatie.

## <a name="availability"></a>Beschikbaarheid

Virtual Network NAT en de NAT-gateway resource zijn beschikbaar in alle regio's van alle Azure-Cloud [regio's](https://azure.microsoft.com/global-infrastructure/regions/).

## <a name="suggestions"></a>Suggesties

We willen graag weten hoe we de service kunnen verbeteren. Doe een voorstel en stem op wat we hierna moeten bouwen op [UserVoice voor NAT](https://aka.ms/natuservoice).

## <a name="limitations"></a>Beperkingen

* NAT is compatibel met een openbaar IP-adres van een standaard-SKU, openbaar IP-voorvoegsel en load balancer-resources. Basisresources, zoals load balancer van het type Basic, en alle producten die hiervan zijn afgeleid, zijn niet compatibel met NAT.  Basisresources moeten worden geplaatst op een subnet dat niet is geconfigureerd met NAT.
* De IPv4-adresfamilie wordt ondersteund.  NAT communiceert niet met een IPv6-adresfamilie.  NAT kan niet worden geïmplementeerd op een subnet met een IPv6-voorvoegsel.
* NAT kan niet meerdere virtuele netwerken omvatten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md).
* [Vertel ons in UserVoice wat we verder kunnen ontwikkelen voor Virtual Network NAT](https://aka.ms/natuservoice).
