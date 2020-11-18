---
title: Load balancer voor meerdere regio's (preview-versie)
titleSuffix: Azure Load Balancer
description: Overzicht van load balancer tier voor meerdere regio's voor Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 8b2f8e82cac1d26689659286a5a0f81f06fb68ca
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695058"
---
# <a name="cross-region-load-balancer-preview"></a>Load balancer voor meerdere regio's (preview-versie)

Azure Load Balancer distribueert binnenkomend verkeer dat binnenkomt bij de load balancer front-end-of back-end-groep.

Azure Standard Load Balancer ondersteunt taak verdeling tussen regio's, waarbij geo-redundante HA-scenario's worden ingeschakeld, zoals:

* Binnenkomend verkeer dat afkomstig is uit meerdere regio's.
* [Directe globale failover](#regional-redundancy) naar de volgende optimale regionale implementatie.
* Laad distributie over regio's naar de dichtstbijzijnde Azure-regio met een [hoge latentie](#ultra-low-latency).
* Mogelijkheid om [omhoog/omlaag te schalen](#ability-to-scale-updown-behind-a-single-endpoint) achter een enkel eind punt.
* [Statisch IP-adres](#static-ip)
* [Client-IP-behoud](#client-ip-preservation)
* [Bouwen op bestaande Load Balancer](#build-cross-region-solution-on-existing-azure-load-balancer) oplossing zonder leer curve

> [!IMPORTANT]
> De load balancer voor meerdere regio's is momenteel beschikbaar als preview-versie en is niet algemeen verkrijgbaar.  Neem contact op met het volgende om toegang te krijgen tot de preview-versie van load balancer voor meerdere regio's [crossregionlb@microsoft.com](mailto:crossregionlb@microsoft.com) . </br> </br>
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De taak verdeling tussen regio's biedt dezelfde voor delen van hoge prestaties en lage latentie als regionale standaard load balancer. 

De front-end-IP-configuratie van uw load balancer voor meerdere regio's is statisch en wordt geadverteerd in de [meeste Azure-regio's](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagram van de load balancer voor meerdere regio's." border="true":::

> [!NOTE]
> De back-end-poort van de taakverdelings regel voor de cross-regio load balancer moet overeenkomen met de frontend-poort van de taakverdelings regel of binnenkomende NAT-regel op regionale standaard load balancer. 

### <a name="regional-redundancy"></a>Regionale redundantie

Configureer regionale redundantie door een algemeen frontend openbaar IP-adres toe te voegen aan uw bestaande load balancers. 

Als de ene regio uitvalt, wordt het verkeer doorgestuurd naar het dichtstbijzijnde regionale load balancer.  

De status test van de Kruis regio load balancer informatie over beschik baarheid elke 20 seconden verzamelen. Als een regionale load balancer de beschik baarheid van 0 daalt, wordt de fout door de Kruis regio load balancer gedetecteerd. De regionale load balancer wordt vervolgens uit de rotatie gehaald. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagram van de weer gave wereld wijde regio verkeer." border="true":::

### <a name="ultra-low-latency"></a>Extreem lage latentie

Het geo-proximity-algoritme voor taak verdeling is gebaseerd op de geografische locatie van uw gebruikers en uw regionale implementaties. 

Verkeer dat vanaf een client is gestart, zal in de dichtstbijzijnde deelnemende regio raken en de micro soft Global Network-backbone door lopen om te kunnen aankomen bij de dichtstbijzijnde regionale implementatie. 

U hebt bijvoorbeeld een kruis regio load balancer met standaard load balancers in azure-regio's:

* VS - west
* Europa - noord

Als een stroom vanaf Seattle wordt gestart, voert het verkeer vs-West in. Deze regio is de meest overeenkomende deelname regio van Seattle. Het verkeer wordt doorgestuurd naar de dichtstbijzijnde regio load balancer, die VS West is.

Azure cross-Region load balancer maakt gebruik van geo-proximity-algoritme voor taak verdeling voor de beslissing van de route ring. 

De geconfigureerde taakverdelings modus van de regionale load balancers wordt gebruikt om de definitieve routerings beslissing te nemen wanneer meerdere regionale load balancers worden gebruikt voor geografische nabijheid.

Zie [De distributiemodus configureren voor Azure Load Balancer](./load-balancer-distribution-mode.md) voor meer informatie.


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Mogelijkheid om omhoog/omlaag te schalen achter een enkel eind punt

Wanneer u het globale eind punt van een kruis regio load balancer voor klanten beschikbaar maakt, kunt u regionale implementaties achter het globale eind punt toevoegen of verwijderen zonder dat dit gevolgen heeft voor de klant. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statisch IP-adres
De load balancer voor meerdere regio's wordt geleverd met een statisch openbaar IP-adres, wat ervoor zorgt dat de IP-adressen hetzelfde blijven. Meer informatie over statische IP vindt u [hier](../virtual-network/public-ip-addresses.md#allocation-method)

### <a name="client-ip-preservation"></a>Client-IP-behoud
De load balancer voor meerdere regio's is een netwerk load balancer met Layer-4-door gang. Met deze Pass-Through wordt het oorspronkelijke IP-adres van het pakket bewaard.  Het oorspronkelijke IP-adres is beschikbaar voor de code die op de virtuele machine wordt uitgevoerd. Hierdoor kunt u logica Toep assen die specifiek is voor een IP-adres.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Een oplossing voor meerdere regio's bouwen op bestaande Azure Load Balancer
De back-end-groep van de load balancer met meerdere regio's bevat een of meer regionale load balancers. 

Voeg uw bestaande load balancer-implementaties toe aan een kruis regio load balancer voor een Maxi maal beschik bare implementatie voor meerdere regio's.

In de **regio thuis** wordt de Load Balancer van de Kruis regio geïmplementeerd. Deze regio heeft geen invloed op hoe het verkeer wordt doorgestuurd. Als een thuis regio uitvalt, heeft dit geen invloed op de stroom van het verkeer.

### <a name="home-regions"></a>Thuis regio's
* US - oost 2
* VS - west
* Europa -west
* Azië - zuidoost
* Central US
* Europa - noord
* Azië - oost

> [!NOTE]
> U kunt uw load balancer voor meerdere regio's alleen implementeren in een van de bovenstaande 8 regio's.

In een **deelnemende regio** is het globale open bare IP-adres van de Load Balancer beschikbaar. 

Verkeer dat door de gebruiker is gestart, gaat naar de dichtstbijzijnde deelnemende regio via het micro soft-basis netwerk. 

De load balancer over meerdere regio's stuurt het verkeer naar de juiste regionale load balancer.

### <a name="participating-regions"></a>Deelnemende regio's
* VS - oost 
* Europa -west 
* US - centraal 
* US - oost 2 
* VS - west 
* Europa - noord 
* VS - zuid-centraal 
* US - west 2 
* Verenigd Koninkrijk Zuid 
* Azië - zuidoost 
* VS - noord-centraal 
* Japan East 
* Azië - oost 
* VS - west-centraal 
* Australia Southeast 
* Australië - oost 
* India - centraal 

## <a name="limitations"></a>Beperkingen

* Front-end frontend-IP-configuraties zijn alleen openbaar. Een interne frontend wordt momenteel niet ondersteund.

* Privé-of interne load balancer kan niet worden toegevoegd aan de back-end-groep van cross-Regional load balancer 

* IPv6-front-end-IP-configuraties voor meerdere regio's worden niet ondersteund. 

* Een status test kan momenteel niet worden geconfigureerd. Met een standaard status test wordt automatisch elke 20 seconden beschikbaarheids gegevens over de regionale load balancer verzameld. 

## <a name="pricing-and-sla"></a>Prijzen en SLA
Load balancer over meerdere regio's, deelt u de [Sla](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) van Standard Load Balancer.

 
## <a name="next-steps"></a>Volgende stappen

- Zie [Een openbare standaard load balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een load balancer.
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- [Veelgestelde vragen](load-balancer-faqs.md) over Load Balancer