---
title: Hoe werkt Azure Traffic Manager? Microsoft Docs
description: Dit artikel helpt u te begrijpen hoe Traffic Manager verkeer routeert voor hoge prestaties en beschik baarheid van uw webtoepassingen
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: a1e1bd107e8b3b9209f99d1abfc4d7e391c3c4a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184336"
---
# <a name="how-traffic-manager-works"></a>Hoe Traffic Manager werkt

Met Azure Traffic Manager kunt u de distributie van verkeer over de eind punten van uw toepassing beheren. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost.

Traffic Manager biedt twee belang rijke voor delen:

- Distributie van verkeer op basis van een van de verschillende [routerings methoden voor verkeer](traffic-manager-routing-methods.md)
- [Voortdurende bewaking van de status van het eind punt](traffic-manager-monitoring.md) en automatische failover wanneer eind punten mislukken

Wanneer een client probeert verbinding maken met een service, moet deze eerst de DNS-naam van de service omzetten naar een IP-adres. De client maakt vervolgens verbinding met dat IP-adres om toegang te krijgen tot de service.

**Het belangrijkste punt om te begrijpen is dat Traffic Manager op het DNS-niveau werkt.**  Traffic Manager DNS gebruikt om clients te leiden naar specifieke service-eind punten op basis van de regels van de methode voor het routeren van verkeer. Clients maken **rechtstreeks** verbinding met het geselecteerde eind punt. Traffic Manager is geen proxy of gateway. Het verkeer dat door de client en de service wordt door Traffic Manager wordt niet weer gegeven.

## <a name="traffic-manager-example"></a>Traffic Manager-voor beeld

Contoso Corp heeft een nieuwe partner portal ontwikkeld. De URL voor deze portal is `https://partners.contoso.com/login.aspx` . De toepassing wordt gehost in drie regio's van Azure. Om de beschik baarheid te verbeteren en de wereld wijde prestaties te maximaliseren, gebruiken ze Traffic Manager om client verkeer te distribueren naar het dichtstbijzijnde beschik bare eind punt.

Om deze configuratie te vervolledigen, moet u de volgende stappen uitvoeren:

1. Implementeer drie instanties van de service. De DNS-namen van deze implementaties zijn ' contoso-us.cloudapp.net ', ' contoso-eu.cloudapp.net ' en ' contoso-asia.cloudapp.net '.
1. Maak een Traffic Manager-profiel met de naam ' contoso.trafficmanager.net ' en configureer dit voor het gebruik van de methode voor het routeren van verkeer van prestaties in de drie eind punten.
1. Configureer de Vanity-domein naam, ' partners.contoso.com ', om naar ' contoso.trafficmanager.net ' te verwijzen, met behulp van een DNS CNAME-record.

![DNS-configuratie Traffic Manager][1]

> [!NOTE]
> Wanneer u een Vanity-domein met Azure Traffic Manager gebruikt, moet u een CNAME gebruiken om uw Vanity-domein naam naar de domein naam van uw Traffic Manager te wijzen. Met DNS-standaarden kunt u geen CNAME maken op de ' Apex ' (of root) van een domein. Daarom kunt u geen CNAME maken voor ' contoso.com ' (ook wel een ' Blot ' domein genoemd). U kunt alleen een CNAME maken voor een domein onder ' contoso.com ', zoals ' www.contoso.com '. Als u deze beperking wilt omzeilen, kunt u het beste uw DNS-domein hosten op [Azure DNS](../dns/dns-overview.md) en [alias records](../dns/tutorial-alias-tm.md) gebruiken om te verwijzen naar uw Traffic Manager-profiel. U kunt ook een eenvoudige HTTP-omleiding gebruiken om aanvragen voor ' contoso.com ' om te leiden naar een alternatieve naam, zoals ' www.contoso.com '.

### <a name="how-clients-connect-using-traffic-manager"></a>Hoe clients verbinding maken via Traffic Manager

Wanneer een client de pagina opvraagt, `https://partners.contoso.com/login.aspx` voert de client de volgende stappen uit om de DNS-naam op te lossen en een verbinding tot stand te brengen:

![De verbinding tot stand brengen met behulp van Traffic Manager][2]

1. De client verzendt een DNS-query naar de geconfigureerde recursieve DNS-service om de naam ' partners.contoso.com ' op te lossen. Een recursieve DNS-service, ook wel ' lokale DNS-service ' genoemd, host geen DNS-domeinen rechtstreeks. In plaats daarvan wordt de-client uit-geladen om contact op te nemen met de verschillende gezaghebbende DNS-services via internet die nodig zijn om een DNS-naam op te lossen.
2. De recursieve DNS-service detecteert de naam servers voor het domein ' contoso.com ' om de DNS-naam op te lossen. Vervolgens neemt het contact op met deze naam servers om de DNS-record ' partners.contoso.com ' aan te vragen. De DNS-servers van contoso.com retour neren de CNAME-record die verwijst naar contoso.trafficmanager.net.
3. Vervolgens vindt de recursieve DNS-service de naam servers voor het domein ' trafficmanager.net ', die door de Azure Traffic Manager-service worden verschaft. Vervolgens wordt een aanvraag voor de DNS-record ' contoso.trafficmanager.net ' verzonden naar deze DNS-servers.
4. De Traffic Manager naam servers ontvangen de aanvraag. Ze kiezen een eind punt op basis van:

    - De geconfigureerde status van elk eind punt (uitgeschakelde eind punten worden niet geretourneerd)
    - De huidige status van elk eind punt, zoals bepaald door de Traffic Manager status controles. Zie [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)voor meer informatie.
    - De gekozen methode voor het routeren van verkeer. Zie [Traffic Manager routerings methoden](traffic-manager-routing-methods.md)voor meer informatie.

5. Het gekozen eind punt wordt geretourneerd als een andere DNS CNAME-record. In dit geval laten we ons zeggen dat contoso-eu.cloudapp.net wordt geretourneerd.
6. Vervolgens vindt de recursieve DNS-service de naam servers voor het domein ' cloudapp.net '. Er wordt contact opgenomen met deze naam servers om de DNS-record ' contoso-eu.cloudapp.net ' aan te vragen. Er wordt een DNS A-record geretourneerd met het IP-adres van het service-eind punt op basis van de EU.
7. De recursieve DNS-service consolideert de resultaten en retourneert één DNS-antwoord naar de client.
8. De client ontvangt de DNS-resultaten en maakt verbinding met het opgegeven IP-adres. De client maakt rechtstreeks verbinding met het eind punt van de toepassings service, niet via Traffic Manager. Omdat het een HTTPS-eind punt is, voert de client de benodigde SSL/TLS-Handshake uit en wordt vervolgens een HTTP GET-aanvraag voor de pagina '/login.aspx ' gemaakt.

De recursieve DNS-service slaat de DNS-antwoorden op die het ontvangt. De DNS-resolver op het client apparaat slaat ook het resultaat op in het cache geheugen. Met caching kunnen volgende DNS-query's sneller worden beantwoord met behulp van gegevens uit de cache in plaats van een query uit te voeren op andere naam servers. De duur van de cache wordt bepaald door de eigenschap time-to-Live (TTL) van elke DNS-record. Kortere waarden leiden ertoe dat het cache geheugen sneller verloopt en dat er dus Retouren naar de Traffic Manager naam servers worden geretourneerd. Meer waarden betekenen dat het langer kan duren om verkeer te sturen van een mislukt eind punt. Met Traffic Manager kunt u de TTL configureren die wordt gebruikt in Traffic Manager DNS-antwoorden op Maxi maal 0 seconden en Maxi maal 2.147.483.647 seconden (het maximum bereik dat compatibel is met [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), zodat u de waarde kunt kiezen die het beste de behoeften van uw toepassing verlaagt.

## <a name="faqs"></a>Veelgestelde vragen

* [Welk IP-adres Traffic Manager gebruiken?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Welk type verkeer kan worden gerouteerd met Traffic Manager?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Ondersteunt Traffic Manager ' Sticky ' sessies?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Waarom zie ik een HTTP-fout bij het gebruik van Traffic Manager?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Wat is de invloed van de prestaties van het gebruik van Traffic Manager?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Welke toepassings protocollen kan ik gebruiken met Traffic Manager?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [Kan ik Traffic Manager gebruiken met de domein naam ' Blot '?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Houdt Traffic Manager rekening met het subnet van de client wanneer DNS-query's worden verwerkt?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Wat is een DNS TTL en wat is van invloed op mijn gebruikers?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Hoe hoog of laag kan ik de TTL instellen voor Traffic Manager reacties?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Hoe kan ik inzicht krijgen in het volume van query's die worden verzonden naar mijn profiel?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Traffic Manager [endpoint-bewaking en automatische failover](traffic-manager-monitoring.md).

Meer informatie over methoden voor het [routeren](traffic-manager-routing-methods.md)van Traffic Manager verkeer.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png