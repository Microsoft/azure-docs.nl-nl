---
title: Overwegingen voor de netwerk topologie voor Azure Active Directory-toepassingsproxy
description: Bestrijkt overwegingen bij de netwerk topologie bij het gebruik van Azure Active Directory-toepassingsproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bbab5463f0d022cb9bf155c7d33e2d81c8bdd448
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199682"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>De verkeers stroom optimaliseren met Azure Active Directory-toepassingsproxy

In dit artikel wordt uitgelegd hoe u de verkeers stroom-en netwerk topologie overwegingen optimaliseert bij het gebruik van Azure Active Directory-toepassings proxy (Azure AD) voor het extern publiceren en openen van uw toepassingen.

## <a name="traffic-flow"></a>Verkeers stroom

Wanneer een toepassing wordt gepubliceerd via Azure AD-toepassingsproxy, loopt het verkeer van de gebruikers naar de toepassingen via drie verbindingen:

1. De gebruiker maakt verbinding met het open bare eind punt van de Azure AD-toepassingsproxy-service in azure
1. De Application proxy-service maakt verbinding met de toepassings proxy connector
1. De Application proxy-connector maakt verbinding met de doel toepassing

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="Diagram waarin de verkeers stroom van de gebruiker naar de doel toepassing wordt weer gegeven." lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>Groeps beleidsobjecten optimaliseren voor gebruik van de dichtstbijzijnde Cloud service voor de toepassing proxy (preview-versie)

Wanneer u zich aanmeldt voor een Azure AD-Tenant, wordt de regio van uw Tenant bepaald door het land/de regio die u opgeeft. Wanneer u toepassings proxy inschakelt, worden de **standaard** Cloud service-exemplaren van de toepassings proxy voor uw Tenant gekozen in dezelfde regio als uw Azure AD-Tenant of de dichtstbijzijnde regio.

Als het land of de regio van uw Azure AD-Tenant bijvoorbeeld het Verenigd Konink rijk is, worden alle connectors van de toepassings proxy **standaard** toegewezen aan het gebruik van service-exemplaren in Europese data centers. Wanneer uw gebruikers toegang krijgen tot gepubliceerde toepassingen, loopt het verkeer via de Cloud service-exemplaren van de toepassings proxy op deze locatie.

Als u connectors hebt geïnstalleerd in regio's die verschillen van uw standaard regio, kan het nuttig zijn om te wijzigen in welke regio uw connector groep is geoptimaliseerd voor het verbeteren van de prestaties die toegang hebben tot deze toepassingen. Zodra een regio is opgegeven voor een connector groep, wordt deze verbonden met Cloud Services van de toepassings proxy in de aangewezen regio.

Wijs de connector groep toe aan de dichtstbijzijnde regio om de verkeers stroom te optimaliseren en de latentie te verminderen voor een connector groep. Een regio toewijzen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als een toepassingsbeheerder van de map die gebruikmaakt van Application Proxy. Als het domein van de tenant bijvoorbeeld contoso.com is, moet de beheerder admin@contoso.com of een andere beheerdersalias in dat domein zijn.
1. Selecteer uw gebruikersnaam in de rechterbovenhoek. Controleer of u bent aangemeld in een directory die gebruikmaakt van Application Proxy. Als u van directory moet veranderen, selecteert u **Schakelen tussen directory’s** en kiest u een directory die gebruikmaakt van Application Proxy.
1. Selecteer **Azure Active Directory** in het navigatiepaneel aan de linkerkant.
1. Selecteer onder **Beheren** de optie **Application Proxy**.
1. Selecteer **nieuwe connector groep**, geef een **naam** op voor de connector groep.
1. Klik vervolgens onder **Geavanceerde instellingen** en selecteer de vervolg keuzelijst onder optimaliseren voor een specifieke regio en selecteer de regio die het dichtst bij de connectors ligt.
1. Selecteer **Maken**.
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="Configureer een nieuwe connector groep." lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. Zodra de nieuwe connector groep is gemaakt, kunt u selecteren welke connectors u wilt toewijzen aan deze connector groep. 
   - U kunt alleen connectors verplaatsen naar uw connector groep als deze zich in een connector groep bevindt met de standaard regio. De beste manier is om altijd te beginnen met de connectors die in de standaard groep zijn geplaatst en deze vervolgens te verplaatsen naar de juiste connector groep.
   - U kunt de regio van een connector groep alleen wijzigen als er **geen** connectors aan zijn toegewezen of hieraan apps zijn toegewezen.
1. Wijs vervolgens de connector groep toe aan uw toepassingen. Wanneer er toegang wordt verkregen tot de apps, gaat het verkeer naar de Cloud service Application proxy in de regio waarin de connector groep is geoptimaliseerd.

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

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="Diagram van weer gave van een connector die is geïnstalleerd in een Azure-Data Center" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

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

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="Diagram waarin gebruikers, proxy, connector en app worden weer gegeven, zijn allemaal in de Verenigde Staten." lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>Use-case 2

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten, met gebruikers die wereld wijd verspreid zijn. Er bestaat geen ExpressRoute of VPN tussen het Azure-Data Center en het bedrijfs netwerk.

**Aanbeveling:** Volg patroon 1, zoals beschreven in de vorige sectie.

Het algemene patroon is dat u de hop 3 optimaliseert, waar u de connector in de buurt van de app plaatst. Hop 3 is doorgaans duur, als deze zich in dezelfde regio bevindt. Hop 1 kan echter duurder zijn, afhankelijk van waar de gebruiker zich bevindt, omdat gebruikers over de hele wereld toegang moeten hebben tot het toepassings proxy-exemplaar in de Verenigde Staten. Het is een goed idee dat elke proxy oplossing vergelijk bare kenmerken heeft met betrekking tot de gebruikers die wereld wijd worden verspreid.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="Gebruikers zijn wereld wijd verspreid, maar alle andere gegevens bevinden zich in de VS" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>Use-case 3

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten. ExpressRoute met micro soft-peering bestaat tussen Azure en het bedrijfs netwerk.

**Aanbeveling:** Volg de patronen 1 en 2, zoals beschreven in de vorige sectie.

Plaats eerst de connector zo dicht mogelijk bij de app. Vervolgens gebruikt het systeem automatisch ExpressRoute voor hop 2.

Als de ExpressRoute-koppeling gebruikmaakt van micro soft-peering, loopt het verkeer tussen de proxy en de connector over deze koppeling. Hop 2 heeft een geoptimaliseerde latentie.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="Diagram van de ExpressRoute tussen de proxy en connector" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>Use-case 4

**Scenario:** De app bevindt zich in het netwerk van een organisatie in de Verenigde Staten. ExpressRoute met privé-peering bestaat tussen Azure en het bedrijfs netwerk.

**Aanbeveling:** Volg patroon 3, zoals beschreven in de vorige sectie.

Plaats de connector in het Azure-Data Center dat is verbonden met het bedrijfs netwerk via ExpressRoute-privé-peering.

De connector kan in het Azure-Data Center worden geplaatst. Omdat de connector nog steeds een regel voor een zicht heeft op de toepassing en het Data Center via het particuliere netwerk, blijft hop 3 geoptimaliseerd. Daarnaast is hop 2 verder geoptimaliseerd.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Connector in azure Data Center, ExpressRoute tussen connector en app" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>Use-case 5

**Scenario:** De app bevindt zich in het netwerk van een organisatie in Europa, de standaard Tenant regio is Verenigde Staten, met de meeste gebruikers in de Europa.

**Aanbeveling:** Plaats de connector in de buurt van de app. Werk de connector groep bij zodat deze is geoptimaliseerd voor het gebruik van service-exemplaren van de euro-toepassings proxy. Voor de stappen raadpleegt [u connector groepen optimaliseren voor het gebruik van de dichtstbijzijnde Cloud service van de toepassings proxy](application-proxy-network-topology#Optimize connector-groups-to-use-closest-Application-Proxy-cloud-service).

Omdat Europe-gebruikers toegang krijgen tot een exemplaar van een toepassings proxy dat zich in dezelfde regio bevindt, is hop 1 niet duur. Hop 3 is geoptimaliseerd. Overweeg het gebruik van ExpressRoute om hop 2 te optimaliseren.

### <a name="use-case-6"></a>Use-case 6

**Scenario:** De app bevindt zich in het netwerk van een organisatie in Europa, de standaard Tenant regio is Verenigde Staten, met de meeste gebruikers in de VS.

**Aanbeveling:** Plaats de connector in de buurt van de app. Werk de connector groep bij zodat deze is geoptimaliseerd voor het gebruik van service-exemplaren van de euro-toepassings proxy. Voor de stappen raadpleegt [u connector groepen optimaliseren voor het gebruik van de dichtstbijzijnde Cloud service van de toepassings proxy](/application-proxy-network-topology#Optimize connector-groups-to-use-closest-Application-Proxy-cloud-service). Hop 1 kan duurder zijn omdat alle Amerikaanse gebruikers toegang moeten hebben tot het toepassings proxy-exemplaar in Europa.

U kunt ook overwegen om in deze situatie een andere variant te gebruiken. Als de meeste gebruikers in de organisatie zich in de Verenigde Staten bevinden, is de kans groot dat uw netwerk ook wordt uitgebreid naar de Verenigde Staten. Plaats de connector in de VS, blijf de standaard Amerikaanse regio voor uw connector groepen gebruiken en gebruik de exclusieve regel voor het interne bedrijfs netwerk voor de toepassing in Europa. Op deze manier worden de hops 2 en 3 geoptimaliseerd.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="In het diagram worden gebruikers, proxy en connectors in de Verenigde Staten, apps in Europa, weer gegeven." lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>Volgende stappen

- [Toepassings proxy inschakelen](application-proxy-add-on-premises-application.md)
- [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
- [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)
- [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)