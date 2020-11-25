---
title: Azure front-deur-taak verdeling met de leverings suite van Azure. Microsoft Docs
description: Dit artikel helpt u bij het aanwijzen van de belasting verdeling met de Application Delivery Suite van Azure
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019460"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Taakverdeling realiseren met de Azure-suite voor toepassingslevering

## <a name="introduction"></a>Introductie
Microsoft Azure biedt diverse wereld wijde en regionale Services voor het beheren van de manier waarop uw netwerk verkeer wordt gedistribueerd en taak verdeling: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Samen met de vele regio's en zonegebonden-architectuur van Azure kunt u met behulp van deze services tegelijk robuuste, schaal bare en hoogwaardige toepassingen bouwen.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Application Delivery Suite":::
 
Deze services zijn onderverdeeld in twee categorieën:
1. **Wereld wijde taakverdelings Services** zoals Traffic Manager en de voor deur distribueren verkeer van uw eind gebruikers over uw regionale back-ends, in alle Clouds en zelfs uw hybride on-premises Services. Met globale taak verdeling wordt uw verkeer naar uw dichtstbijzijnde service back-end gerouteerd en worden de wijzigingen in de betrouw baarheid van de service gecommuniceerd om altijd Beschik baarheid en hoge prestaties voor uw gebruikers te behouden. 
1. **Regionale taakverdelings Services** zoals load balancers en toepassings gateways bieden de mogelijkheid om verkeer te distribueren naar virtuele machines (vm's) binnen een virtueel netwerk (VNETs) of service-eind punten binnen een regio.

Wanneer u deze wereld wijde en regionale Services combineert, profiteert uw toepassing van betrouw bare en beveiligde end-to-end verkeer dat van uw eind gebruikers wordt verzonden naar uw IaaS-, PaaS-of on-premises Services. In de volgende sectie worden deze services beschreven.

## <a name="global-load-balancing"></a>Globale taak verdeling
**Traffic Manager** voorziet in globale DNS-taak verdeling. Er wordt gekeken naar inkomende DNS-aanvragen en reageert met een gezonde back-end, na het routerings beleid dat de klant heeft geselecteerd. Opties voor routerings methoden zijn:
- * * Prestatie routering verzendt aanvragen naar de dichtstbijzijnde back-end met de minste latentie.
- Met **prioriteit routert** u alle verkeer naar een back-end, met andere back-ends als back-ups.
- Met **gewogen Round Robin-route ring** wordt verkeer gedistribueerd op basis van de weging die aan elke back-end is toegewezen.
- **Geografische route ring** zorgt ervoor dat aanvragen die afkomstig zijn van specifieke geografische regio's, worden verwerkt door back-endservers die zijn toegewezen voor deze regio's. (Alle verzoeken van Spanje moeten bijvoorbeeld worden omgeleid naar de Frankrijk-centraal Azure-regio)
- Met **subnet routering** kunt u IP-adresbereiken toewijzen aan back-ends, zodat inkomende aanvragen voor deze ip's naar de specifieke back-end worden verzonden. (Gebruikers die verbinding maken vanuit het IP-adres bereik van uw bedrijfs hoofd kantoor, moeten bijvoorbeeld andere webinhoud krijgen dan de algemene gebruikers)

De client maakt rechtstreeks verbinding met die back-end. Azure Traffic Manager detecteert wanneer een back-end beschadigd is en stuurt de clients vervolgens om naar een ander ongezonde exemplaar. Raadpleeg de documentatie van [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) voor meer informatie over de service.

De **Azure front-deur** biedt dynamische website versnelling (DSA), inclusief algemene http-taak verdeling.  Er wordt gekeken naar binnenkomende HTTP-aanvragen routes naar de dichtstbijzijnde service back-end/regio voor de opgegeven hostnaam, het URL-pad en de geconfigureerde regels.  
De voor deur beëindigt HTTP-aanvragen aan de rand van het netwerk van micro soft en controleert actief om de status van de toepassing of infra structuur te detecteren.  Met de voor deur wordt verkeer altijd gerouteerd naar de snelste en beschik bare back-end. Raadpleeg de voor deur [routerings architectuur](front-door-routing-architecture.md) Details en [methoden voor verkeers routering](front-door-routing-methods.md) voor meer informatie over de service.

## <a name="regional-load-balancing"></a>Regionale taak verdeling
Application Gateway biedt ADC (Application Delivery controller) als een service met verschillende mogelijkheden voor taak verdeling voor laag 7 voor uw toepassing. Hiermee kunnen klanten de productiviteit van webfarms optimaliseren door CPU-intensieve TLS-beëindiging naar de Application Gateway te offloaden. Andere extra Layer 7-routerings mogelijkheden omvatten ook round robin-distributie van inkomend verkeer, sessie affiniteit op basis van cookies, route ring op basis van URL-pad en de mogelijkheid om meerdere websites achter één toepassings gateway te hosten. Application Gateway kunnen worden geconfigureerd als een Internet gericht eind punt, een intern eind punt of een combi natie van beide. Application Gateway is volledig beheerd met Azure, waardoor u schaal baarheid en hoge Beschik baarheid kunt bieden. Het biedt een uitgebreide verzameling diagnostische gegevens en functies voor logboekregistratie voor betere beheersbaarheid.

Load balancers vormen een integraal onderdeel van de Azure SDN-stack. Dit biedt u de beschikking over hoogwaardige, lage latentie Services voor taak verdeling voor alle UDP-en TCP-protocollen. U kunt open bare of interne eind punten met taak verdeling configureren door regels te definiëren waarmee binnenkomende verbindingen worden toegewezen aan back-endservers. Met de status van het controleren van de beschik baarheid van TCP of HTTPS, kunt u hiermee uw service beschikbaarheid beheren.

## <a name="choosing-a-global-load-balancer"></a>Een globale load balancer kiezen
Bij het kiezen van een globale load balancer tussen Traffic Manager en Azure front deur voor wereld wijde route ring, moet u rekening houden met wat vergelijkbaar is en wat de twee services verschillen.   Beide services bieden
- **Multi-geo-redundantie:** Als een regio buiten dienst gaat, stuurt het verkeer naadloos naar de dichtstbijzijnde regio zonder tussen komst van de eigenaar van de toepassing.
- **Dichtstbijzijnde regio routering:** Verkeer wordt automatisch doorgestuurd naar de dichtstbijzijnde regio

</br>In de volgende tabel worden de verschillen tussen Traffic Manager en Azure front-deur beschreven:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Elk protocol:** Omdat Traffic Manager werkt op de DNS-laag, kunt u elk type netwerk verkeer routeren. HTTP, TCP, UDP, enzovoort. | **Http-versnelling:** Met de voor deur wordt verkeer aan de rand van het micro soft-netwerk geproxyeerd. HTTP/S-aanvragen bekijken verbeteringen in latentie en door Voer, waardoor de latentie voor TLS-onderhandeling wordt verminderd.|
|**On-premises route ring:** Met route ring op een DNS-laag loopt verkeer altijd van punt naar punt.  Route ring van uw filiaal naar uw on-premises Data Center kan een direct pad hebben. zelfs op uw eigen netwerk met behulp van Traffic Manager. | **Onafhankelijke schaal baarheid:** Omdat de voor deur werkt met de HTTP-aanvraag, kunnen aanvragen voor verschillende URL-paden worden doorgestuurd naar verschillende back-end/regionale service groepen (micro Services) op basis van regels en de status van elke toepassings micro service.|
|**Facturerings indeling:** Facturering op basis van DNS kan worden geschaald met uw gebruikers en voor services met meer gebruikers en voor het verlagen van de kosten bij een hoger gebruik. |**Inline beveiliging:** Met de voor deur kunt u regels, zoals frequentie limiet en IP-ACL, zodanig instellen dat u uw back-ends kunt beveiligen voordat het verkeer uw toepassing bereikt. 

</br>We raden klanten aan om voor hun HTTP-workload voor deur te gebruiken vanwege de prestaties, operability en beveiligings voordelen die HTTP met de voor deur werkt. Traffic Manager en de voor deur kunnen parallel worden gebruikt om al het verkeer voor uw toepassing te leveren. 

## <a name="building-with-azures-application-delivery-suite"></a>Voortbouwend met de leverings suite van Azure Application 
We raden u aan alle websites, Api's en services geografisch redundant te maken zodat het verkeer naar de gebruikers van de dichtstbijzijnde locatie kan worden overgebracht wanneer dat mogelijk is.  Door meerdere taakverdelings services te combi neren kunt u geografische en zonegebonden redundantie bouwen om de betrouw baarheid en prestaties te maximaliseren.

In het volgende diagram wordt een voorbeeld architectuur beschreven die gebruikmaakt van een combi natie van al deze services voor het bouwen van een wereld wijde webservice. De architect gebruikt Traffic Manager om verkeer te routeren naar globale back-ends voor bestands-en object levering. Tijdens het gebruik van de voor deur, de route ring van URL-paden die overeenkomen met het patroon/Store/* naar de service die ze hebben gemigreerd naar App Service. Router ten slotte alle andere aanvragen naar regionale toepassings gateways.

In elke regio van de IaaS-service heeft de ontwikkelaar van de toepassing besloten dat alle Url's die overeenkomen met het patroon/images/* worden geleverd vanuit een toegewezen groep Vm's. Deze groep Vm's wijkt af van de rest van de webfarm.

Daarnaast moet de standaard-VM-groep met de dynamische inhoud communiceren met een back-end-data base die wordt gehost op een cluster met hoge Beschik baarheid. De volledige implementatie wordt geconfigureerd via Azure Resource Manager.

In het volgende diagram ziet u de architectuur van dit scenario:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Gedetailleerde architectuur van Application Delivery Suite":::

> [!NOTE]
> Dit voor beeld is slechts een van de vele mogelijke configuraties van de taakverdelings services die Azure biedt. Traffic Manager, front deur, Application Gateway en Load Balancer kunnen worden gecombineerd en afgestemd op uw behoeften voor taak verdeling. Als bijvoorbeeld TLS/SSL-offload of Layer 7-verwerking niet nodig is, kan Load Balancer worden gebruikt in plaats van Application Gateway.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
