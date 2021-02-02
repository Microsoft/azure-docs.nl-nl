---
title: Aandachtspunten voor de netwerk topologie voor Azure AD-toepassingsproxy
description: Bestrijkt overwegingen bij de netwerk topologie bij het gebruik van Azure AD-toepassingsproxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67505e7112c41b21b2ae5e8acc834ff047a470d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254802"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Overwegingen voor de netwerk topologie bij het gebruik van Azure Active Directory-toepassingsproxy

In dit artikel worden de overwegingen voor netwerk topologie beschreven bij het gebruik van Azure Active Directory-toepassings proxy (Azure AD) voor het op afstand publiceren en openen van uw toepassingen.

## <a name="traffic-flow"></a>Verkeers stroom

Wanneer een toepassing wordt gepubliceerd via Azure AD-toepassingsproxy, loopt het verkeer van de gebruikers naar de toepassingen via drie verbindingen:

1. De gebruiker maakt verbinding met het open bare eind punt van de Azure AD-toepassingsproxy-service in azure
1. De Application proxy-service maakt verbinding met de toepassings proxy connector
1. De Application proxy-connector maakt verbinding met de doel toepassing

![Diagram van de verkeers stroom van de gebruiker naar de doel toepassing](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Tenant locatie en toepassings proxy service

Wanneer u zich aanmeldt voor een Azure AD-Tenant, wordt de regio van uw Tenant bepaald door het land/de regio die u opgeeft. Wanneer u toepassings proxy inschakelt, worden de service-exemplaren van de toepassings proxy voor uw Tenant geselecteerd of gemaakt in dezelfde regio als uw Azure AD-Tenant of de dichtstbijzijnde regio.

Als het land of de regio van uw Azure AD-Tenant bijvoorbeeld het Verenigd Konink rijk is, gebruiken alle connectors van de toepassings proxy service-exemplaren in Europese data centers. Wanneer uw gebruikers toegang krijgen tot gepubliceerde toepassingen, loopt het verkeer via de service-exemplaren van de toepassings proxy op deze locatie.

## <a name="considerations-for-reducing-latency"></a>Overwegingen voor het verminderen van de latentie

Alle proxy oplossingen introduceren latentie in uw netwerk verbinding. Ongeacht welke proxy-of VPN-oplossing u kiest als uw oplossing voor externe toegang, bevat deze altijd een set servers die de verbinding met binnen uw bedrijfs netwerk mogelijk maakt.

Organisaties omvatten doorgaans server eindpunten in het perimeter netwerk. Met Azure AD-toepassingsproxy loopt het verkeer echter via de proxy service in de Cloud terwijl de connectors zich in het bedrijfs netwerk bevinden. Er is geen perimeter netwerk vereist.

De volgende secties bevatten aanvullende suggesties om u te helpen de latentie nog verder te verlagen. 

### <a name="connector-placement"></a>Plaatsing van de connector

Toepassings proxy kiest de locatie van de instanties voor u, op basis van de locatie van uw Tenant. U krijgt echter de mogelijkheid om de connector te installeren, zodat u de latentie kenmerken van uw netwerk verkeer kunt definiëren.

Wanneer u de Application proxy-service instelt, moet u de volgende vragen stellen:

- Waar bevindt de app zich?
- Waar bevinden de meeste gebruikers toegang tot de app?
- Waar bevindt het toepassings proxy-exemplaar zich?
- Hebt u al een specifieke netwerk verbinding met Azure-data centers ingesteld, zoals Azure ExpressRoute of een soortgelijk VPN?

De connector moet communiceren met zowel Azure als uw toepassingen (stap 2 en 3 in het stroom diagram verkeer), zodat de plaatsing van de connector van invloed is op de latentie van deze twee verbindingen. Houd bij het evalueren van de plaatsing van de connector de volgende punten in acht:

- Als u Kerberos-beperkte delegering (KCD) wilt gebruiken voor eenmalige aanmelding, moet de connector een regel voor het zicht van een Data Center hebben. Daarnaast moet de connector server lid zijn van een domein.  
- Als u twijfelt, installeert u de connector dichter bij de toepassing.

### <a name="general-approach-to-minimize-latency"></a>Algemene benadering om latentie te minimaliseren

U kunt de latentie van het end-to-end verkeer minimaliseren door elke netwerk verbinding te optimaliseren. Elke verbinding kan worden geoptimaliseerd door:

- De afstand tussen de twee uiteinden van de hop te verminderen.
- Kiezen van het juiste netwerk om door te gaan. Het door lopen van een privé netwerk in plaats van het open bare Internet kan bijvoorbeeld sneller zijn vanwege specifieke koppelingen.

Als u een exclusieve VPN-of ExpressRoute-koppeling tussen Azure en uw bedrijfs netwerk hebt, kunt u die gebruiken.

## <a name="focus-your-optimization-strategy"></a>Richt u op uw optimalisatie strategie

Het is niet meer mogelijk om de verbinding tussen uw gebruikers en de Application proxy-service te beheren. Gebruikers kunnen toegang krijgen tot uw apps vanuit een thuis netwerk, een café of een ander land/nieuwe regio. In plaats daarvan kunt u de verbindingen van de Application proxy-service optimaliseren naar de Application proxy-connectors voor de apps. U kunt de volgende patronen opnemen in uw omgeving.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Patroon 1: de connector dicht bij de toepassing plaatsen

Plaats de connector dicht bij de doel toepassing in het netwerk van de klant. Deze configuratie minimaliseert stap 3 in het topografische diagram, omdat de connector en toepassing sluiten zijn.

Als uw connector een regel voor de detectie van de domein controller nodig heeft, is dit patroon handig. De meeste klanten gebruiken dit patroon, omdat het goed werkt voor de meeste scenario's. Dit patroon kan ook worden gecombineerd met patroon 2 om het verkeer tussen de service en de connector te optimaliseren.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Patroon 2: profiteren van ExpressRoute met micro soft-peering

Als ExpressRoute met micro soft-peering is ingesteld, kunt u de snellere ExpressRoute-verbinding gebruiken voor verkeer tussen de toepassings proxy en de connector. De connector bevindt zich nog steeds in uw netwerk en dicht bij de app.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Patroon 3: profiteren van ExpressRoute met privé-peering

Als u een speciaal VPN-of ExpressRoute hebt ingesteld met privé-peering tussen Azure en uw bedrijfs netwerk, hebt u nog een andere optie. In deze configuratie wordt het virtuele netwerk in azure doorgaans beschouwd als een uitbrei ding van het bedrijfs netwerk. Daarom kunt u de connector in het Azure-Data Center installeren en nog steeds voldoen aan de vereisten voor de lage latentie van de connector-to-app-verbinding.

De latentie wordt niet aangetast omdat verkeer via een specifieke verbinding loopt. U krijgt ook verbeterde latentie van de service-to-connector van de toepassings proxy omdat de connector is geïnstalleerd in een Azure-Data Center, dicht bij uw Azure AD-Tenant locatie.

![Diagram van weer gave van een connector die is geïnstalleerd in een Azure-Data Center](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andere benaderingen

Hoewel de focus van dit artikel over de plaatsing van een connector bestaat, kunt u ook de plaatsing van de toepassing wijzigen om betere latentie kenmerken te krijgen.

Organisaties verplaatsen hun netwerken steeds in gehoste omgevingen. Hierdoor kunnen ze hun apps plaatsen in een gehoste omgeving die ook deel uitmaakt van het bedrijfs netwerk en zich nog steeds binnen het domein bevindt. In dit geval kunnen de patronen die in de voor gaande secties worden besproken, worden toegepast op de nieuwe locatie van de toepassing. Als u deze optie overweegt, raadpleegt u [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

U kunt ook uw connectors ordenen met behulp van [connector groepen](application-proxy-connector-groups.md) om apps te richten die zich op verschillende locaties en netwerken bevinden.

## <a name="common-use-cases"></a>Algemene scenario’s

In deze sectie worden enkele veelvoorkomende scenario's door lopen. Stel dat de Azure AD-Tenant (en dus proxy service-eind punt) zich bevindt in de Verenigde Staten (VS). De overwegingen in deze use-cases zijn ook van toepassing op andere regio's over de hele wereld.

Voor deze scenario's noemen we elke verbinding een ' hop ' en nummer ze voor eenvoudiger discussies:

- **Hop 1**: gebruiker naar de Application proxy-service
- **Hop 2**: Service toepassings proxy aan de connector voor de toepassings proxy
- **Hop 3**: toepassings proxy connector voor de doel toepassing 

### <a name="use-case-1"></a>Use-case 1

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten, met gebruikers in dezelfde regio. Er bestaat geen ExpressRoute of VPN tussen het Azure-Data Center en het bedrijfs netwerk.

**Aanbeveling:** Volg patroon 1, zoals beschreven in de vorige sectie. Voor verbeterde latentie kunt u eventueel ExpressRoute gebruiken, indien nodig.

Dit is een eenvoudig patroon. U optimaliseert hop 3 door de connector in de buurt van de app te plaatsen. Dit is ook een natuurlijke keuze, omdat de connector doorgaans wordt geïnstalleerd met een gezichts lijn voor de app en het Data Center voor het uitvoeren van KCD-bewerkingen.

![Diagram waarin gebruikers, proxy, connector en app worden weer gegeven, zijn allemaal in de Verenigde Staten](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Use-case 2

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten, met gebruikers die wereld wijd verspreid zijn. Er bestaat geen ExpressRoute of VPN tussen het Azure-Data Center en het bedrijfs netwerk.

**Aanbeveling:** Volg patroon 1, zoals beschreven in de vorige sectie.

Het algemene patroon is dat u de hop 3 optimaliseert, waar u de connector in de buurt van de app plaatst. Hop 3 is doorgaans duur, als deze zich in dezelfde regio bevindt. Hop 1 kan echter duurder zijn, afhankelijk van waar de gebruiker zich bevindt, omdat gebruikers over de hele wereld toegang moeten hebben tot het toepassings proxy-exemplaar in de Verenigde Staten. Het is een goed idee dat elke proxy oplossing vergelijk bare kenmerken heeft met betrekking tot de gebruikers die wereld wijd worden verspreid.

![Gebruikers zijn wereld wijd verspreid, maar alle andere gegevens bevinden zich in de VS](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Use-case 3

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten. ExpressRoute met micro soft-peering bestaat tussen Azure en het bedrijfs netwerk.

**Aanbeveling:** Volg de patronen 1 en 2, zoals beschreven in de vorige sectie.

Plaats eerst de connector zo dicht mogelijk bij de app. Vervolgens gebruikt het systeem automatisch ExpressRoute voor hop 2.

Als de ExpressRoute-koppeling gebruikmaakt van micro soft-peering, loopt het verkeer tussen de proxy en de connector over deze koppeling. Hop 2 heeft een geoptimaliseerde latentie.

![Diagram van de ExpressRoute tussen de proxy en connector](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Use-case 4

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten. ExpressRoute met privé-peering bestaat tussen Azure en het bedrijfs netwerk.

**Aanbeveling:** Volg patroon 3, zoals beschreven in de vorige sectie.

Plaats de connector in het Azure-Data Center dat is verbonden met het bedrijfs netwerk via ExpressRoute-privé-peering.

De connector kan in het Azure-Data Center worden geplaatst. Omdat de connector nog steeds een regel voor een zicht heeft op de toepassing en het Data Center via het particuliere netwerk, blijft hop 3 geoptimaliseerd. Daarnaast is hop 2 verder geoptimaliseerd.

![Connector in azure Data Center, ExpressRoute tussen connector en app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Use-case 5

**Scenario:** De app bevindt zich in het netwerk van een organisatie in Europa, met het exemplaar van de toepassings proxy en de meeste gebruikers in de VS.

**Aanbeveling:** Plaats de connector in de buurt van de app. Omdat de Amerikaanse gebruikers toegang krijgen tot een toepassings proxy-exemplaar dat zich in dezelfde regio bevindt, is hop 1 niet te duur. Hop 3 is geoptimaliseerd. Overweeg het gebruik van ExpressRoute om hop 2 te optimaliseren.

![Diagram toont gebruikers en proxy in de V.S., connector en app in Europa](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

U kunt ook overwegen om in deze situatie een andere variant te gebruiken. Als de meeste gebruikers in de organisatie zich in de Verenigde Staten bevinden, is de kans groot dat uw netwerk ook wordt uitgebreid naar de Verenigde Staten. Plaats de connector in de VS en gebruik de exclusieve interne bedrijfs netwerk lijn voor de toepassing in Europa. Op deze manier worden de hops 2 en 3 geoptimaliseerd.

![Diagram toont gebruikers, proxy en connectors in de VS, app in Europa](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassings proxy inschakelen](application-proxy-add-on-premises-application.md)
- [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
- [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)
- [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)
