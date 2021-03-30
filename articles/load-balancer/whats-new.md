---
title: Wat is er nieuw in Azure Load Balancer
description: Ontdek wat er nieuw is in Azure Load Balancer, zoals de laatste opmerkingen bij de release, bekende problemen, opgeloste problemen, verminderde functionaliteit en aankomende wijzigingen.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a30a42e8a8c4049b53274da512089dd29965e775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573150"
---
# <a name="whats-new-in-azure-load-balancer"></a>Wat is er nieuw in Azure Load Balancer?

Azure Load Balancer wordt regelmatig bijgewerkt. Blijf op de hoogte van de laatste aankondigingen. In dit artikel vindt u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit (indien van toepassing)

[Hier](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) vindt u de meest recente updates voor Azure Load Balancer en kunt u zich abonneren op de RSS-feed.

## <a name="recent-releases"></a>Recente releases

| Type |Naam |Beschrijving  |Datum toegevoegd  |
| ------ |---------|---------|---------|
| Functie | Ondersteuning voor verplaatsen tussen resourcegroepen | Ondersteuning voor standaard load balancer en standaard openbare IP voor [verplaatsen tussen resourcegroepen](https://azure.microsoft.com/updates/standard-resource-group-move/). | Oktober 2020 |
| Functie | Ondersteuning voor IP-gebaseerd beheer van back-endpools (preview) | Azure Load Balancer biedt ondersteuning voor het toevoegen en verwijderen van resources uit een back-endpool via een IPv4-of IPv6-adres. Dit stelt u in staat om containers, virtuele machines en virtuele-machineschaalset voor Load Balancer eenvoudig te beheren. Ook kunnen IP-adressen worden gereserveerd als onderdeel van een back-endpool voordat de gekoppelde resources worden aangemaakt. Klik [hier](backend-pool-management.md) voor meer informatie|Juli 2020 |
| Functie| Inzichten Azure Load Balancer-inzichten met Azure Monitor | Het is ontworpen als onderdeel van Azure Monitor voor netwerken, en biedt klanten topologische kaarten voor al hun Load Balancer-configuraties en statusdashboards voor Standard Load Balancers die zijn vooraf zijn geconfigureerd met metrische gegevens in het Azure-portal. [Aan de slag en meer informatie](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Juni 2020 |
| Validatie | Aanvulling van validatie voor HA-poorten | Er is een validatie toegevoegd om ervoor te zorgen dat HA-poortregels en niet-HA-poortregels alleen kunnen worden geconfigureerd wanneer Zwevend IP-adres is ingeschakeld. Voorheen zou deze configuratie worden geaccepteerd maar niet werken zoals de bedoeling is. Er zijn geen wijzigingen aangebracht in de functionaliteit. Klik [hier](load-balancer-ha-ports-overview.md#limitations) voor meer informatie| Juni 2020 |
| Functie| IPv6-ondersteuning voor Azure Load Balancer (algemeen beschikbaar) | U kunt IPv6-adressen gebruiken als front-end voor uw Azure Load Balancers. Meer informatie over het [maken van een dual stack-toepassing hier](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |April 2020|
| Functie| Opnieuw instellen van TCP bij Time-out voor inactiviteit (algemeen beschikbaar)| Gebruik Opnieuw instellen van TCP om he gedrag van de toepassing voorspelbaarder te maken. [Meer informatie](load-balancer-tcp-reset.md)| Februari 2020 |

## <a name="known-issues"></a>Bekende problemen

De productgroep werkt actief aan oplossingen voor de volgende bekende problemen:

|Probleem |Beschrijving  |Oplossing  |
| ---------- |---------|---------|
| Load Balancer-waarschuwingsgebeurtenis en statuslogboeken | Logboekregistratie werkt niet voor Load Balancer-waarschuwingsgebeurtenissen voor Basic en Standard Load Balancer en ook niet voor de statuslogboeken voor Basic Load Balancer  | [Gebruik Azure Monitor voor multidimensionale metrische gegevens voor uw Standard Load Balancer](load-balancer-standard-diagnostics.md). Azure Monitor biedt visualisatie voor een rijke set multidimensionale metrische gegevens die ook als logboeken kunnen worden geëxporteerd. U kunt gebruikmaken van het vooraf geconfigureerde dashboard met metrische gegevens via de subblade Insights van uw Load Balancer. Als u Basic Load Balancer gebruikt, voert u een [upgrade naar Standard](upgrade-basic-standard.md) uit voor bewaking van metrische gegevens op productieniveau.

  

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Load Balancer?](load-balancer-overview.md) en [veelgestelde vragen](load-balancer-faqs.md) voor meer informatie over Azure Load Balancer.
