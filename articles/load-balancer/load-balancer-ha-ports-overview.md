---
title: Overzicht van poorten met hoge Beschik baarheid in azure
titleSuffix: Azure Load Balancer
description: Meer informatie over de taak verdeling van poorten met hoge Beschik baarheid op een interne load balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94694956"
---
# <a name="high-availability-ports-overview"></a>Overzicht van poorten met hoge Beschik baarheid

Met Azure Standard Load Balancer kunt u **alle** protocol stromen op **alle** poorten tegelijk verdelen wanneer u een interne Load Balancer via ha-poorten gebruikt.

Poorten met hoge Beschik baarheid (HA) is een soort taakverdelings regel die een eenvoudige manier biedt om **alle** stromen te verdelen die binnenkomen op **alle** poorten van een interne Standard Load Balancer. Er wordt per stroom een beslissing voor taakverdeling gemaakt. Deze actie is gebaseerd op de volgende vijf-tuple-verbinding: bron-IP-adres, bron poort, doel-IP-adres, doel poort en protocol

De taakverdelingsregels voor de HA-poorten helpen u bij kritieke scenario's, zoals hoge beschikbaarheid en schaal aanpassen voor NVA's (Network Virtual Appliance) in virtuele netwerken. De functie kan ook helpen wanneer over een groot aantal poorten taakverdeling moet worden uitgevoerd. 

De taakverdelings regels voor de HA-poorten worden geconfigureerd wanneer u de front-end-en back-end-poorten instelt op **0** en het protocol voor **alle**. Met de interne load balancer resource worden vervolgens alle TCP-en UDP-stromen gebalanceerd, ongeacht het poort nummer

## <a name="why-use-ha-ports"></a>Waarom HA-poorten gebruiken?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Virtuele netwerk apparaten

U kunt Nva's gebruiken om uw Azure-workload te beveiligen tegen meerdere soorten beveiligings Risico's. Wanneer u Nva's in deze scenario's gebruikt, moeten ze betrouwbaar en Maxi maal beschikbaar zijn en moeten ze voor vraag worden uitgeschaald.

U kunt deze doel stellingen eenvoudig bereiken door NVA-instanties toe te voegen aan de back-end-groep van uw interne load balancer en een Load Balancer-regel van HA-poorten te configureren.

Voor NVA HA-scenario's bieden HA-poorten de volgende voor delen:
- Geef een snelle failover naar gezonde instanties, met per-exemplaar status tests
- Zorg voor hogere prestaties met scale-out naar *n*-actieve instanties
- Bieden *n*-actief en actief/passieve scenario's
- Voorkom de nood zaak van complexe oplossingen, zoals Apache ZooKeeper knoop punten voor bewakings apparaten

In het volgende diagram ziet u een implementatie van een virtuele hub en spoke-netwerk. De spokes vereisen het verkeer naar het virtuele netwerk van de hub en via de NVA, voordat de vertrouwde ruimte wordt verlaten. De Nva's bevinden zich achter een interne Standard Load Balancer met de configuratie van HA-poorten. Al het verkeer kan dienovereenkomstig worden verwerkt en doorgestuurd. Wanneer de configuratie wordt weer gegeven in het volgende diagram, biedt de taak verdelings regel voor HA-poorten daarnaast een stroom symmetrie voor binnenkomend en uitgaand verkeer.

<a node="diagram"></a>
![Diagram van hub en spoke virtueel netwerk, met Nva's geïmplementeerd in HA-modus](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Als u Nva's gebruikt, bevestigt u met hun providers hoe u het beste HA-poorten kunt gebruiken en hoe u kunt zien welke scenario's worden ondersteund.

### <a name="load-balancing-large-numbers-of-ports"></a>Grote aantallen poorten voor taak verdeling

U kunt ook HA-poorten gebruiken voor toepassingen waarvoor taak verdeling van een groot aantal poorten nodig is. U kunt deze scenario's vereenvoudigen met behulp van een interne [Standard Load Balancer](./load-balancer-overview.md) met ha-poorten. Eén taak verdelings regel vervangt meerdere afzonderlijke regels voor taak verdeling, één voor elke poort.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De functie HA-poorten is beschikbaar in alle wereld wijde Azure-regio's.

## <a name="supported-configurations"></a>Ondersteunde configuraties

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Een enkelvoudige, niet-zwevende IP (non-direct server return) HA-poorten configuratie op een interne Standard Load Balancer

Deze configuratie is een configuratie van basis HA-poorten. U kunt een taakverdelings regel voor HA-poorten configureren op één front-end-IP-adres door het volgende te doen:
1. Schakel bij het configureren van Standard Load Balancer het selectie vakje **ha-poorten** in de Load Balancer regel configuratie in.
2. Selecteer **uitgeschakeld** voor **zwevend IP-adres**.

Bij deze configuratie is geen enkele andere configuratie van regel voor taak verdeling op de huidige load balancer resource toegestaan. Ook is er geen andere interne load balancer resource configuratie toegestaan voor de opgegeven reeks back-end-exemplaren.

U kunt echter een open bare Standard Load Balancer voor de back-end-exemplaren configureren naast deze regel voor de HA-poort.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Een enkele, zwevende IP (direct server return) HA-poorten configuratie op een interne Standard Load Balancer

U kunt uw load balancer ook configureren voor het gebruik van een taakverdelings regel met **ha-poort** met één front-end door de **zwevende IP** in te stellen op **ingeschakeld**. 

Met deze configuratie kunt u meer zwevende IP-taakverdelings regels en/of een open bare load balancer toevoegen. U kunt echter geen niet-zwevende IP-configuratie met HA-poorten voor taak verdeling gebruiken boven deze configuratie.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Configuraties met meerdere HA-poorten op een interne Standard Load Balancer

Als uw scenario vereist dat u meer dan één HA-poort front-end voor dezelfde back-end-groep configureert, kunt u het volgende doen: 
- Configureer meer dan één privé-IP-adres voor de voor kant van een interne Standard Load Balancer bron.
- Configureer meerdere taakverdelings regels, waarbij voor elke regel één uniek IP-adres voor front-end is geselecteerd.
- Selecteer de optie **ha-poorten** en stel **zwevend IP-adres** in op **ingeschakeld** voor alle regels voor taak verdeling.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Een intern load balancer met HA-poorten en een open bare load balancer op hetzelfde back-end-exemplaar

U kunt *één* open bare Standard Load Balancer bron configureren voor de back-endservers, samen met één interne Standard load BALANCER met ha-poorten.

## <a name="limitations"></a>Beperkingen

- De taakverdelings regels voor HA-poorten zijn alleen beschikbaar voor interne Standard Load Balancer.
- De combi natie van een regel voor taak verdeling van HA-poorten en een niet-HA-poort regel voor taak verdeling die verwijst naar dezelfde backend (s) voor de back-end, wordt **niet** ondersteund op een configuratie met één frontend-IP, tenzij voor beide zwevende IP-adressen zijn ingeschakeld.
- Bestaande IP-fragmenten worden doorgestuurd door HA-poorten taakverdelings regels naar hetzelfde doel als eerste pakket.  IP-fragmentatie van een UDP-of TCP-pakket wordt niet ondersteund.
- Flow-symmetrie (voornamelijk voor NVA-scenario's) wordt ondersteund met een back-end-exemplaar en één NIC (en één IP-configuratie) alleen wanneer deze worden gebruikt zoals weer gegeven in het diagram hierboven en met behulp van de taakverdelings regels voor HA-poorten. Het is in geen enkel ander scenario opgenomen. Dit betekent dat twee of meer Load Balancer resources en de bijbehorende regels onafhankelijke beslissingen nemen en nooit gecoördineerd zijn. Bekijk de beschrijving en het diagram voor [virtuele netwerk apparaten](#nva). Wanneer u meerdere Nic's gebruikt of de NVA tussen een open bare en interne Load Balancer, is de stroom symmetrie niet beschikbaar.  U kunt dit mogelijk omzeilen door middel van de bron NAT'ing de ingangs stroom naar het IP-adres van het apparaat zodat antwoorden op dezelfde NVA kunnen worden ontvangen.  We raden u echter ten zeerste aan om één NIC te gebruiken en de referentie architectuur te gebruiken die wordt weer gegeven in het bovenstaande diagram.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Standard Load Balancer](load-balancer-overview.md)
