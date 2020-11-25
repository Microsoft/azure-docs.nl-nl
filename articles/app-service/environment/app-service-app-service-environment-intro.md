---
title: Inleiding tot ASE v1
description: Meer informatie over de App Service Environment v1-functies. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008618"
---
# <a name="introduction-to-app-service-environment-v1"></a>Inleiding tot App Service Environment v1

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v1.  Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.

## <a name="overview"></a>Overzicht

Een App Service Environment is een optie voor een [Premium][PremiumTier] service plan van [Azure app service](../overview.md) die een volledig geïsoleerde en toegewezen omgeving biedt voor het veilig uitvoeren van Azure app service-apps op grote schaal, met inbegrip van Web Apps, Mobile apps en API apps.  

App Service omgevingen zijn ideaal voor werk belastingen van toepassingen waarvoor het volgende is vereist:

* Zeer hoge schaal
* Isolatie en beveiligde netwerk toegang

Klanten kunnen meerdere App Service omgevingen maken binnen één Azure-regio en ook in meerdere Azure-regio's.  Dit zorgt ervoor dat App Service omgevingen ideaal zijn voor Horizon taal schaalloze toepassings lagen ter ondersteuning van hoge RPS-workloads.

App Service omgevingen zijn alleen geïsoleerd voor het uitvoeren van de toepassingen van één klant en worden altijd geïmplementeerd in een virtueel netwerk.  Klanten hebben nauw keurige controle over zowel binnenkomend als uitgaand netwerk verkeer en toepassingen kunnen snelle, veilige verbindingen tot stand brengen via virtuele netwerken naar on-premises bedrijfs bronnen.

Zie voor een overzicht van de manier waarop App Service-omgevingen grootschalige en veilige toegang tot het netwerk bieden, de uitgebreide kennis van [AzureCon][AzureConDeepDive] in app service omgevingen!

Zie het artikel over het instellen van een [geografisch gedistribueerd app-Opper vlak][GeodistributedAppFootprint]voor een diep gaande op horizon taal schalen met meerdere app service omgevingen.

Zie het artikel over het implementeren van een [gelaagde beveiligings architectuur](app-service-app-service-environment-layered-security.md) met app service omgevingen voor meer informatie over hoe de beveiligings architectuur die wordt weer gegeven in de AzureCon diepe duik is geconfigureerd.

Voor apps die op App Service omgevingen worden uitgevoerd, kan de toegang worden gedecodeerd via upstream-apparaten, zoals Web Application firewalls (WAF).  Het artikel over het [configureren van een WAF voor app service omgevingen](app-service-app-service-environment-web-application-firewall.md) behandelt dit scenario.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Toegewezen reken resources

Alle reken resources in een App Service Environment zijn exclusief toegewezen aan één abonnement en een App Service Environment kan worden geconfigureerd met Maxi maal 50 (50) reken resources voor exclusief gebruik door één toepassing.

Een App Service Environment bestaat uit een front-end Compute-resource groep en een tot drie werk computer-reken resource groepen.

De front-end-pool bevat reken resources die verantwoordelijk zijn voor het beëindigen van TLS als een automatische taak verdeling van app-aanvragen binnen een App Service Environment.

Elke werknemers groep bevat reken resources die zijn toegewezen aan [app service plannen][AppServicePlan], die op zijn beurt een of meer Azure app service apps bevatten.  Omdat er Maxi maal drie verschillende werkgroepen kunnen zijn in een App Service Environment, hebt u de flexibiliteit om verschillende reken resources te kiezen voor elke werk groep.  

Zo kunt u bijvoorbeeld één werk groep maken met minder krachtige reken resources voor App Service plannen die zijn bedoeld voor ontwikkelings-en test toepassingen.  Een tweede (of zelfs derde) werk groep kan gebruikmaken van krachtigere reken resources die zijn bedoeld voor App Service plannen die productie-apps uitvoeren.

Zie [How to Configure an app service Environment][HowToConfigureanAppServiceEnvironment](Engelstalig) voor meer informatie over de hoeveelheid reken resources die beschikbaar zijn voor de front-end-en worker-groepen.  

Raadpleeg de pagina met [app service prijzen][AppServicePricing] voor meer informatie over de beschik bare berekenings resource grootten die worden ondersteund in een app service Environment en Bekijk de beschik bare opties voor app service omgevingen in de prijs categorie Premium.

## <a name="virtual-network-support"></a>Ondersteuning voor Virtual Network

Een App Service Environment kan worden **gemaakt in een** Azure Resource Manager virtueel netwerk **of** een klassiek implementatie model virtueel netwerk ([meer informatie over virtuele netwerken][MoreInfoOnVirtualNetworks]).  Omdat een App Service Environment altijd bestaat in een virtueel netwerk en nauw keuriger is binnen een subnet van een virtueel netwerk, kunt u gebruikmaken van de beveiligings functies van virtuele netwerken om zowel binnenkomende als uitgaande netwerk communicatie te beheren.  

Een App Service Environment kan Internet zijn met een openbaar IP-adres of intern gericht met alleen een Azure intern Load Balancer (ILB)-adres.

U kunt [netwerk beveiligings groepen][NetworkSecurityGroups] gebruiken om de inkomende netwerk communicatie te beperken tot het subnet waar een app service Environment zich bevindt.  Zo kunt u apps uitvoeren achter de upstream-apparaten en-services, zoals firewalls voor webtoepassingen en netwerk-SaaS-providers.

Apps hebben ook vaak toegang nodig tot bedrijfsresources zoals interne databases en webservices.  Een veelvoorkomende aanpak is om deze eind punten alleen beschikbaar te maken voor intern netwerk verkeer dat binnen een virtueel Azure-netwerk loopt.  Zodra een App Service Environment is gekoppeld aan hetzelfde virtuele netwerk als de interne services, hebben apps die in de omgeving worden uitgevoerd, toegang tot de toepassingen, waaronder eind punten die bereikbaar zijn via [site-naar-site-][SiteToSite] en [Azure ExpressRoute][ExpressRoute] -verbindingen.

Raadpleeg de volgende artikelen over de [netwerk architectuur][NetworkArchitectureOverview], [beheer van binnenkomend verkeer][ControllingInboundTraffic]en [veilig verbinding maken met back-endservers][SecurelyConnectingToBackends]voor meer informatie over de werking van app service omgevingen met virtuele netwerken en on-premises netwerken. 

## <a name="getting-started"></a>Aan de slag

Zie [een app service Environment maken][HowToCreateAnAppServiceEnvironment] om aan de slag te gaan met app service omgevingen

Zie het artikel overzicht van de [netwerk architectuur][NetworkArchitectureOverview] voor een overzicht van de app service Environment-netwerk architectuur.

Raadpleeg het volgende artikel over [Express route en app service omgevingen][NetworkConfigDetailsForExpressRoute]voor meer informatie over het gebruik van een app service Environment met ExpressRoute.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->