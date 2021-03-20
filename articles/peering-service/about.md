---
title: Overzicht van Azure peering-service
description: Overzicht van Azure peering service
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026726"
---
# <a name="azure-peering-service-overview"></a>Overzicht van Azure peering-service

Azure Peering Service is een netwerkservice die de klantconnectiviteit verbetert met Microsoft-cloudservices, zoals Microsoft 365, Dynamics 365, SaaS-services (Software as a Service), Azure of een andere Microsoft-service die toegankelijk is via openbaar internet. Micro soft heeft wereld wijd een partnerschap gemaakt met Internet serviceproviders (Isp's), IXPs (Internet Exchange partners) en SDCI-providers (software-defined Cloud Interconnect) om betrouw bare en hoogwaardige open bare connectiviteit te bieden met optimale route ring van de klant naar het micro soft-netwerk.

Met peering service kunnen klanten een goed verbonden partner serviceprovider selecteren in een bepaalde regio. Open bare connectiviteit is geoptimaliseerd voor hoge betrouw baarheid en minimale latentie van Cloud Services naar de locatie van de eind gebruiker.

![Gedistribueerde connectiviteit met micro soft Cloud](./media/peering-service-about/peering-service-what.png)

Klanten kunnen ook kiezen voor telemetrie van de peering-service, zoals meet waarden voor gebruikers latentie voor het micro soft-netwerk, BGP-route bewaking en waarschuwingen tegen lekkage en overname door de peering service-verbinding te registreren in de Azure Portal. 

Klanten hoeven zich niet te registreren bij micro soft om gebruik te maken van een peering-service. De enige vereiste is om contact op te nemen met een [peering-service partner](location-partners.md) om de service te verkrijgen. Om u aan te melden voor telemetrie van de peering-service, moeten klanten zich in de Azure Portal registreren.

Zie voor instructies over het registreren van een peering-service [registreren van peering-service met behulp van de Azure Portal](azure-portal.md). 

> [!NOTE]
> Dit artikel is bedoeld voor netwerk architecten die verantwoordelijk zijn voor de connectiviteit van de onderneming met de Cloud en het internet.


## <a name="what-is-peering-service"></a>Wat is Peering Service?

Peering-service is:

- Een IP-service die gebruikmaakt van het open bare Internet. 
- Een samenwerkings platform met service providers en een service met toegevoegde waarde die is bedoeld om een optimale en betrouw bare route ring naar de klant te bieden via de partners van de service provider via het open bare netwerk.

Peering-service is geen privé-connectiviteits product, zoals Azure ExpressRoute of een VPN-product.

> [!NOTE]
> Zie [ExpressRoute-documentatie](../expressroute/index.yml)voor meer informatie over ExpressRoute.
>

## <a name="background"></a>Achtergrond

Microsoft 365, Dynamics 365 en andere micro soft SaaS-services worden gehost in meerdere micro soft-data centers en kunnen vanaf elke geografische locatie worden geopend. Het wereld wijde netwerk van micro soft heeft PoP-locaties (Point-of-Presence) van micro soft Edge over de hele wereld waar het verbinding kan maken met een eind gebruiker via hun service providers. 

Service providers van micro soft en partners zorgen ervoor dat het verkeer voor de voor voegsels die zijn geregistreerd bij een peering service-verbinding, binnenkomt en sluit de dichtstbijzijnde PoP-locaties van micro soft Edge op het micro soft Global Network. Micro soft zorgt ervoor dat het netwerk verkeer egressing van de voor voegsels die zijn geregistreerd met peering-service verbindingen de dichtstbijzijnde PoP-locaties van micro soft Edge op het wereld wijde micro soft-netwerk.

![Micro soft-netwerk en open bare connectiviteit](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Zie het [wereld wijde netwerk van micro soft](../networking/microsoft-global-network.md)voor meer informatie over het globale micro soft-netwerk.
>

## <a name="why-use-peering-service"></a>Waarom peering service gebruiken?

Ondernemingen die op zoek zijn naar Internet, hebben eerst toegang tot de Cloud of overwegen de architectuur van een SD-WAN of een hoog gebruik van micro soft SaaS-services nodig om robuuste en snelle Internet connectiviteit te hebben. Klanten kunnen die overgang doen met behulp van de peering-service. Micro soft en service providers hebben een partnerschap voor betrouw bare en prestatie gerichte open bare connectiviteit met de micro soft-Cloud. Enkele van de belangrijkste klant functies worden hier weer gegeven:

- De beste open bare route ring via internet om Microsoft Azure Cloud Services voor optimale prestaties en betrouw baarheid.
- De mogelijkheid om de voorkeurs serviceprovider te selecteren om verbinding te maken met de micro soft-Cloud.
- Verkeers inzichten zoals latentie rapportage en voorvoegsel controle.
- De optimale netwerk-hops (als hops) van de micro soft-Cloud.
- Route analyses en statistieken: gebeurtenissen voor ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) route afwijkingen (lekkage of detectie van overname) en suboptimale route ring.

### <a name="robust-reliable-peering"></a>Robuuste, betrouw bare peering

Peering-service gebruikt twee soorten redundantie:

- **Lokale redundantie**

   Micro soft-en service providers Interconnect over meerdere PoP-locaties van micro soft Edge voor het leveren van peering-service. Op elke locatie moet de verbinding tussen twee routers worden ondersteund.

   Elke peering-locatie wordt ingericht met redundante en diverse koppelingen voor peering.

- **Geografische redundantie**

   Micro soft heeft verbinding gemaakt met service providers op meerdere metro locaties, zodat als een van de Edge-knoop punten verminderde prestaties heeft, de verkeers routes van en naar micro soft via alternatieve sites. Micro soft routeert verkeer in het wereld wijde netwerk met behulp van SDN routering beleid voor optimale prestaties.

    Dit type redundantie maakt gebruik van het kortste routerings traject door altijd de dichtstbijzijnde micro soft Edge-PoP naar de eind gebruiker te kiezen en ervoor te zorgen dat de klant één netwerk-hop (als hops) van micro soft is.

   ![Geografische redundantie](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Optimale route ring

De volgende routerings techniek verdient de voor keur:

-  **Koel-aardappel routering**

   De door software gedefinieerde routerings techniek voor koud aardappel beheer biedt controle over het netwerk verkeer dat afkomstig is uit de micro soft-Cloud. Het zorgt ervoor dat verkeer altijd over de hoge capaciteit, de lage latentie en uiterst betrouw bare wereld wijde micro soft-netwerk blijft, totdat dit zo dicht mogelijk bij de bestemming ligt.
   
   Route ring die geen gebruik maakt van de koude-aardappel techniek, wordt aangeduid als Hot-aardappel routering. Met een hot-aardappel routering gaat het verkeer dat afkomstig is uit de micro soft-Cloud, via internet.

   ![Koel-aardappel routering](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Bewakings platform

   Service bewaking wordt aangeboden voor het analyseren van het verkeer en de route ring van klanten en biedt de volgende mogelijkheden: 

-  **Detectie van Internet BGP-route afwijkingen**
          
   Deze service wordt gebruikt voor het detecteren en waarschuwen van eventuele afwijkings gebeurtenissen van routes, zoals route overname naar de klant voorvoegsels.

-  **Latentie van klant**

   Met deze service worden de routerings prestaties tussen de locatie van de klant en micro soft gecontroleerd. 
   
   De routerings prestaties worden gemeten door de retour tijd van de client te valideren om de micro soft Edge-PoP te bereiken. Klanten kunnen de latentie rapporten voor verschillende geografische locaties weer geven.

   Door bewaking worden de gebeurtenissen vastgelegd in het geval van een service vermindering.

   ![Bewakings platform voor peering-service](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Verkeers beveiliging

Route ring vindt alleen plaats via een voorkeurs pad dat is gedefinieerd wanneer de klant is geregistreerd bij de peering-service.

Micro soft garandeert dat het verkeer via voorkeurs paden moet worden gerouteerd, zelfs als er schadelijke activiteiten worden gedetecteerd.

Er worden BGP-route afwijkingen gerapporteerd in de Azure Portal, indien van toepassing.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over peering service-verbindingen [peering Service Connections](connection.md).
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.
- Zie [peering service-partners en-locaties](location-partners.md)om een service provider partner te vinden.
- Zie [onboarding peering service model](onboarding-model.md)voor een onboarding van een peering service-verbinding.
- Zie [een peering service-verbinding registreren met behulp van de Azure Portal](azure-portal.md)om een verbinding te registreren met behulp van de Azure Portal.
- Zie [Verbindingstelemetrie meten](measure-connection-telemetry.md) om telemetrie te meten.