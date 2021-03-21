---
title: Azure Load Balancer en Beschikbaarheidszones
titleSuffix: Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met Azure Standard Load Balancer en Beschikbaarheidszones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699110"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer en Beschikbaarheidszones

Azure Load Balancer ondersteunt scenario's met beschikbaarheids zones. U kunt Standard Load Balancer gebruiken om de beschik baarheid in het hele scenario te verg Roten door resources uit te lijnen met en distributie over zones.  Lees dit document voor meer informatie over deze concepten en ontwerp richtlijnen voor het basis scenario

Een Load Balancer kan **zone redundante, zonegebonden** of **niet-zonegebonden** zijn. Als u de aan de zone gerelateerde eigenschappen (hierboven genoemd) voor uw load balancer wilt configureren, selecteert u het juiste type frontend dat nodig is.

## <a name="zone-redundant"></a>Zone-redundant

In een regio met Beschikbaarheidszones kan een Standard Load Balancer zone-redundant zijn. Dit verkeer wordt geleverd door één IP-adres.

Een enkel IP-adres van de front-end heeft geen zone storingen. Het front-end-IP-adres kan worden gebruikt voor het bereiken van alle leden van de back-end-backend-groep die niet van invloed zijn op de zone. Een of meer beschikbaarheids zones kunnen mislukken en het gegevenspad blijft actief zolang er één zone in de regio in orde is.

Het IP-adres van de frontend wordt tegelijkertijd bediend door meerdere onafhankelijke infrastructuur implementaties in meerdere beschikbaarheids zones. Nieuwe pogingen of het opnieuw inbrengen van wijzigingen in andere zones die niet worden beïnvloed door de fout in de zone.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Afbeelding: redundante zone load balancer*

## <a name="zonal"></a>Zonegebonden

U kunt ervoor kiezen om een front-end te garanderen voor één zone, die ook wel een *zonegebonden* wordt genoemd.  Dit scenario betekent dat elke binnenkomende of uitgaande stroom wordt bediend door één zone in een regio.  Uw front-end-shares worden meegezonden met de status van de zone.  Het gegevenspad wordt niet beïnvloed door storingen in andere zones dan waar het is gegarandeerd. U kunt zonegebonden-frontends gebruiken om een IP-adres per beschikbaarheids zone beschikbaar te maken.  

Daarnaast wordt het gebruik van zonegebonden-frontends rechtstreeks voor eind punten met gelijke taak verdeling binnen elke zone ondersteund. U kunt deze configuratie gebruiken voor het weer geven van eind punten met gelijke taak verdeling voor elke zone afzonderlijk te bewaken. Voor open bare eind punten kunt u ze integreren met een product voor DNS-taak verdeling, zoals [Traffic Manager](../traffic-manager/traffic-manager-overview.md) en één DNS-naam gebruiken.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Afbeelding: zonegebonden load balancer*

Voor een open bare load balancer-frontend voegt u een para meter **zones** toe aan het open bare IP-adres. Naar dit open bare IP-adres wordt verwezen door de frontend-IP-configuratie die wordt gebruikt door de betreffende regel.

Voor een interne load balancer frontend voegt u een para meter **zones** toe aan de interne Load Balancer frontend-IP-configuratie. Met een zonegebonden-front-end wordt een IP-adres in een subnet gegarandeerd naar een specifieke zone.

## <a name="design-considerations"></a><a name="design"></a> Ontwerp overwegingen

Nu u de zone-gerelateerde eigenschappen van Standard Load Balancer begrijpt, kunnen de volgende overwegingen bij het ontwerpen helpen bij het ontwerpen van hoge Beschik baarheid.

### <a name="tolerance-to-zone-failure"></a>Tolerantie voor zone fout

- Een **zone redundante** Load Balancer kan een zonegebonden-bron in elke zone met één IP-adres hebben.  Het IP-adres kan een of meer zone storingen hebben, zolang ten minste één zone in orde blijft binnen de regio.
- Een **zonegebonden** -front-end is het verminderen van de service voor één zone en het delen van verspreiding met de betreffende zone. Als de zone uw implementatie bevindt, wordt deze fout niet overstaan in uw implementatie.

Het is raadzaam om zone redundante Load Balancer te gebruiken voor uw productie werkbelastingen.

### <a name="control-vs-data-plane-implications"></a>Controle over de implicaties van het gegevens vlak

Zone-redundantie impliceert geen Hitless gegevens vlak of besturings vlak. Zone-redundante stromen kunnen elke zone gebruiken en uw stromen gebruiken alle zones in een regio die in orde zijn. Als er een zone fout optreedt, worden de verkeers stromen met gezonde zones niet beïnvloed.

Verkeers stromen die gebruikmaken van een zone op het moment van de zone storing, kunnen worden beïnvloed, maar toepassingen kunnen worden hersteld. Verkeer blijft in de in orde zijnde zones binnen de regio na het opnieuw verzenden als Azure is geconvergeerd rond de zone storing.

Bekijk de [ontwerp patronen voor Azure-Clouds](/azure/architecture/patterns/) om de flexibiliteit van uw toepassing te verbeteren tot fouten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Beschikbaarheidszones](../availability-zones/az-overview.md)
- Meer informatie over [Standard Load Balancer](./load-balancer-overview.md)
- Meer informatie over het [taak verdeling van virtuele machines binnen een zone met behulp van een zonegebonden-Standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Meer informatie over het verdelen [van virtuele machines over zones met behulp van een zone redundante Standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Meer informatie over [ontwerp patronen voor Azure-Clouds](/azure/architecture/patterns/) voor het verbeteren van de tolerantie van uw toepassing tot fout scenario's.
