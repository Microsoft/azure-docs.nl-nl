---
title: Netwerk architectuur v1
description: Overzicht van de architectuur van de netwerk topologie van App Service omgevingen. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701810"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Overzicht van de netwerk architectuur van App Service omgevingen
App Service omgevingen worden altijd gemaakt binnen een subnet van een [virtueel netwerk][virtualnetwork] : apps die worden uitgevoerd in een app service Environment kunnen communiceren met privé-eind punten die zich in dezelfde virtuele netwerk topologie bevinden.  Omdat klanten delen van hun virtuele netwerk infrastructuur kunnen vergren delen, is het belang rijk om inzicht te krijgen in de typen netwerk communicatie stromen die zich voordoen met een App Service Environment.

## <a name="general-network-flow"></a>Algemene netwerk stroom
Wanneer een App Service Environment (ASE) een openbaar virtueel IP-adres (VIP) voor apps gebruikt, arriveert al het binnenkomende verkeer op die open bare VIP.  Dit omvat HTTP-en HTTPS-verkeer voor apps, evenals andere verkeer voor FTP, de functionaliteit voor fout opsporing op afstand en Azure-beheer bewerkingen.  Zie het artikel over het [beheren van inkomend verkeer][controllinginboundtraffic] naar een app service Environment voor een volledige lijst met de specifieke poorten (zowel vereist als optioneel) die beschikbaar zijn op de open bare VIP. 

App Service omgevingen bieden ook ondersteuning voor het uitvoeren van apps die alleen zijn gebonden aan een intern adres van een virtueel netwerk, ook wel een ILB-adres (interne load balancer) genoemd.  Op een ILB ingeschakeld ASE, HTTP-en HTTPS-verkeer voor apps en externe fout opsporings aanroepen, arriveren op het ILB-adres.  Voor de meeste veelvoorkomende ILB-ASE-configuraties zal het FTP/FTPS-verkeer ook op het ILB-adres arriveren.  Azure-beheer bewerkingen stromen echter nog wel naar poorten 454/455 op het open bare VIP van een ILB ingeschakelde ASE.

In het onderstaande diagram ziet u een overzicht van de verschillende binnenkomende en uitgaande netwerk stromen voor een App Service Environment waarbij de apps zijn gebonden aan een openbaar virtueel IP-adres:

![Algemene netwerk stromen][GeneralNetworkFlows]

Een App Service Environment kan communiceren met verschillende eind punten van particuliere klanten.  Zo kunnen apps die in de App Service Environment worden uitgevoerd, verbinding maken met een of meer data base-servers die worden uitgevoerd op virtuele IaaS-machines in dezelfde virtuele netwerk topologie.

> [!IMPORTANT]
> Als u het netwerk diagram bekijkt, worden de ' andere reken resources ' geïmplementeerd in een ander subnet dan het App Service Environment. Als u resources in hetzelfde subnet met de ASE implementeert, wordt de connectiviteit van ASE naar die bronnen geblokkeerd (met uitzonde ring van specifieke intra-ASE-route ring). Implementeer in plaats daarvan naar een ander subnet (in hetzelfde VNET). Het App Service Environment kan dan verbinding maken. Er is geen aanvullende configuratie nodig.
> 
> 

App Service omgevingen communiceren ook met SQL DB en Azure Storage bronnen die nodig zijn om een App Service Environment te beheren en te gebruiken.  Sommige van de SQL-en opslag resources waarmee een App Service Environment communiceert, bevinden zich in dezelfde regio als de App Service Environment, terwijl anderen zich in externe Azure-regio's bevinden.  Als gevolg hiervan is de uitgaande verbinding met Internet altijd vereist om een App Service Environment goed te laten functioneren. 

Omdat een App Service Environment is geïmplementeerd in een subnet, kunnen netwerk beveiligings groepen worden gebruikt voor het beheren van inkomend verkeer naar het subnet.  Zie het volgende [artikel][controllinginboundtraffic]voor meer informatie over het beheren van inkomend verkeer naar een app service environment.

Raadpleeg het volgende artikel over het werken met [Express route][ExpressRoute]voor meer informatie over het toestaan van uitgaande internet connectiviteit via een app service environment.  Dezelfde benadering die in het artikel wordt beschreven, is van toepassing wanneer u werkt met site-naar-site-connectiviteit en geforceerde tunneling gebruikt.

## <a name="outbound-network-addresses"></a>Uitgaande netwerk adressen
Wanneer een App Service Environment uitgaande aanroepen maakt, wordt er altijd een IP-adres gekoppeld aan de uitgaande oproepen.  Het specifieke IP-adres dat wordt gebruikt, is afhankelijk van het feit of het eind punt dat wordt aangeroepen zich in de topologie van het virtuele netwerk bevindt, of buiten de topologie van het virtuele netwerk.

Als het eind punt dat wordt aangeroepen, zich **buiten** de topologie van het virtuele netwerk bevindt, is het uitgaande adres (ook wel het uitgaande NAT-adres) dat wordt gebruikt, de open bare VIP van de app service environment.  Dit adres bevindt zich in de gebruikers interface van de portal voor de App Service Environment in de Blade eigenschappen.

![Uitgaand IP-adres][OutboundIPAddress]

Dit adres kan ook worden vastgesteld voor as die alleen een open bare VIP hebben door een app te maken in het App Service Environment en vervolgens een *nslookup* op het adres van de app uit te voeren. Het resulterende IP-adres is zowel het open bare VIP als het uitgaande NAT-adres van de App Service Environment.

Als het eind punt dat wordt aangeroepen zich **binnen** de topologie van het virtuele netwerk bevindt, wordt het uitgaande adres van de aanroepende app het interne IP-adres van de afzonderlijke Compute-resource die de app uitvoert.  Er is echter geen permanente toewijzing van interne IP-adressen van virtuele netwerken aan apps.  Apps kunnen door verschillende reken bronnen worden verplaatst en de groep beschik bare reken resources in een App Service Environment kan worden gewijzigd als gevolg van schaal bewerkingen.

Omdat een App Service Environment echter altijd binnen een subnet ligt, weet u zeker dat het interne IP-adres van een reken resource die een app uitvoert, altijd binnen het CIDR-bereik van het subnet ligt.  Als gevolg hiervan, wanneer er verfijnde Acl's of netwerk beveiligings groepen worden gebruikt om de toegang tot andere eind punten binnen het virtuele netwerk te beveiligen, moet het subnet-bereik met de App Service Environment toegang krijgen.

In het volgende diagram ziet u deze concepten meer details:

![Uitgaande netwerk adressen][OutboundNetworkAddresses]

In het bovenstaande diagram:

* Omdat de open bare VIP van de App Service Environment 192.23.1.2 is, is dat het uitgaande IP-adres dat wordt gebruikt bij het aanroepen van Internet-eind punten.
* Het CIDR-bereik van het bovenliggende subnet voor de App Service Environment is 10.0.1.0/26.  Andere eind punten binnen de infra structuur van het virtuele netwerk krijgen vanaf ergens binnen dit adres bereik aanroepen van apps.

## <a name="calls-between-app-service-environments"></a>Aanroepen tussen App Service omgevingen
Een complexere scenario kan zich voordoen als u meerdere App Service omgevingen in hetzelfde virtuele netwerk implementeert en uitgaande aanroepen van een App Service Environment naar een ander App Service Environment maakt.  Deze typen cross-App Service Environment-aanroepen worden ook behandeld als Internet-aanroepen.

Het volgende diagram toont een voor beeld van een gelaagde architectuur met apps op één App Service Environment (bijvoorbeeld ' front-deur ' web-apps) die apps aanroepen op een tweede App Service Environment (bijvoorbeeld interne back-end-API-apps die niet zijn bedoeld om te worden benaderd via internet). 

![Aanroepen tussen App Service omgevingen][CallsBetweenAppServiceEnvironments] 

In het bovenstaande voor beeld heeft de App Service Environment "ASE One" een uitgaand IP-adres van 192.23.1.2.  Als een app die op deze App Service Environment wordt uitgevoerd, een uitgaande oproep doet naar een app die wordt uitgevoerd op een tweede App Service Environment ("ASE twee") in hetzelfde virtuele netwerk, wordt de uitgaande oproep behandeld als een Internet aanroep.  Als gevolg hiervan wordt het netwerk verkeer dat binnenkomt op de tweede App Service Environment weer gegeven als afkomstig van 192.23.1.2 (dat wil zeggen, niet het adres bereik van het subnet van de eerste App Service Environment).

Hoewel aanroepen tussen verschillende App Service omgevingen worden behandeld als Internet-aanroepen, wanneer beide App Service omgevingen zich in dezelfde Azure-regio bevinden, blijft het netwerk verkeer op het regionale Azure-netwerk en niet fysiek over op het open bare Internet.  Als gevolg hiervan kunt u een netwerk beveiligings groep in het subnet van de tweede App Service Environment gebruiken om alleen inkomende aanroepen van de eerste App Service Environment (waarvan het uitgaande IP-adres 192.23.1.2 is) toe te staan, waardoor de communicatie tussen de App Service omgevingen gewaarborgd blijft.

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en informatie
Meer informatie over binnenkomende poorten die worden gebruikt door App Service omgevingen en het gebruik van netwerk beveiligings groepen om inkomend verkeer te beheren, is [hier][controllinginboundtraffic]beschikbaar.

Meer informatie over het gebruik van door de gebruiker gedefinieerde routes voor het verlenen van uitgaande internet toegang aan App Service omgevingen is beschikbaar in dit [artikel][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

