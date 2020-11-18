---
title: Hoge Beschik baarheid en taak verdeling-Azure AD-toepassingsproxy
description: Hoe verkeer distributie werkt met uw implementatie van de toepassings proxy. Bevat tips voor het optimaliseren van de prestaties van de connector en het gebruik van taak verdeling voor back-endservers.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403fa4cab94ad6149e388b10acccd9d5e7a2b7a8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658159"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Hoge Beschik baarheid en taak verdeling van de connectors en toepassingen van uw toepassings proxy

In dit artikel wordt uitgelegd hoe verkeer distributie werkt met de implementatie van uw toepassings proxy. We besteden aandacht aan:

- Hoe verkeer wordt verdeeld over gebruikers en connectors, samen met tips voor het optimaliseren van de prestaties van de connector

- Hoe verkeer loopt tussen connectors en back-end-app-servers, met aanbevelingen voor taak verdeling over meerdere back-endservers

## <a name="traffic-distribution-across-connectors"></a>Distributie van verkeer tussen connectors

Connectors maken hun verbindingen op basis van principes voor hoge Beschik baarheid. Er is geen garantie dat verkeer altijd gelijkmatig wordt verdeeld over connectors en er geen sessie affiniteit is. Het gebruik kan echter variëren en aanvragen worden wille keurig verzonden naar service-exemplaren van de toepassings proxy. Als gevolg hiervan wordt het verkeer doorgaans bijna gelijkmatig verdeeld over de connectors. In het diagram en de onderstaande stappen ziet u hoe verbindingen tot stand worden gebracht tussen gebruikers en connectors.

![Diagram van de verbindingen tussen gebruikers en connectors](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Een gebruiker op een client apparaat probeert toegang te krijgen tot een on-premises toepassing die is gepubliceerd via een toepassings proxy.
2. De aanvraag doorloopt een Azure Load Balancer om te bepalen welk toepassings proxy service-exemplaar de aanvraag moet uitvoeren. Per regio zijn er tien exemplaren beschikbaar om de aanvraag te accepteren. Deze methode helpt het verkeer gelijkmatig te verdelen over de service-exemplaren.
3. De aanvraag wordt verzonden naar [Service Bus](../../service-bus-messaging/index.yml).
4. Service Bus signalen naar een beschik bare connector. De connector kiest vervolgens de aanvraag van Service Bus.
   - In stap 2 worden aanvragen naar verschillende service-exemplaren van de toepassings proxy verzonden, waardoor verbindingen waarschijnlijk meer worden gemaakt met verschillende connectors. Als gevolg hiervan worden connectors bijna gelijkmatig in de groep gebruikt.
5. De connector stuurt de aanvraag door naar de back-endserver van de toepassing. Vervolgens stuurt de toepassing het antwoord terug naar de connector.
6. De connector voltooit het antwoord door een uitgaande verbinding te openen met het service-exemplaar van waaruit de aanvraag afkomstig is. Deze verbinding wordt dan onmiddellijk gesloten. Standaard is elke connector beperkt tot 200 gelijktijdige uitgaande verbindingen.
7. Het antwoord wordt vervolgens door gegeven aan de client vanuit het service-exemplaar.
8. Bij volgende aanvragen van dezelfde verbinding worden de bovenstaande stappen herhaald.

Een toepassing heeft vaak veel resources en opent meerdere verbindingen wanneer deze worden geladen. Elke verbinding wordt de bovenstaande stappen door lopen om te worden toegewezen aan een service-exemplaar. Selecteer een nieuwe beschik bare connector als de verbinding nog niet eerder is gekoppeld aan een connector.


## <a name="best-practices-for-high-availability-of-connectors"></a>Aanbevolen procedures voor hoge Beschik baarheid van connectors

- Vanwege de manier waarop verkeer wordt verdeeld over connectors voor hoge Beschik baarheid, is het essentieel om altijd ten minste twee connectors in een connector groep te hebben. Drie connectors worden aanbevolen om extra buffer te bieden tussen connectors. Volg de documentatie over capaciteits planning om het juiste aantal benodigde connectors te bepalen.

- Plaats connectors op verschillende uitgaande verbindingen om een Single Point of Failure te voor komen. Als connectors dezelfde uitgaande verbinding gebruiken, kan een netwerk probleem met de verbinding van invloed zijn op alle connectors.

- Voorkom het afdwingen van connectors om opnieuw op te starten wanneer er verbinding wordt gemaakt met productie toepassingen. Dit kan een negatieve invloed hebben op de distributie van verkeer tussen connectors. Als Connect oren opnieuw worden gestart, zijn meer connectors niet beschikbaar en worden de verbindingen met de resterende beschik bare connector afgedwongen. Het resultaat is in eerste instantie een oneven gebruik van de connectors.

- Vermijd alle vormen van inline inspectie op uitgaande TLS-communicatie tussen connectors en Azure. Dit type inline-inspectie leidt tot degradatie van de communicatie stroom.

- Zorg ervoor dat de automatische updates voor uw connectors actief blijven. Als de toepassings proxy Connector Updater service wordt uitgevoerd, worden uw connectors automatisch bijgewerkt en ontvangen ze de meest recente bijgewerkte versie. Als de Connector Updater-Service niet wordt weer geven op uw server, moet u de connector opnieuw installeren om eventuele updates op te halen.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Verkeers stroom tussen connectors en back-end-toepassings servers

Een ander belang rijk gebied waarbij hoge Beschik baarheid een factor is, is de verbinding tussen connectors en de back-endservers. Wanneer een toepassing wordt gepubliceerd via Azure AD-toepassingsproxy, loopt het verkeer van de gebruikers naar de toepassingen via drie hops:

1. De gebruiker maakt verbinding met het open bare eind punt van de Azure AD-toepassingsproxy-service in Azure. De verbinding wordt tot stand gebracht tussen het oorspronkelijke client-IP-adres (openbaar) van de client en het IP-adres van het eind punt van de toepassings proxy.
2. De Application proxy-connector haalt de HTTP-aanvraag van de client van de Application proxy-service op.
3. De Application proxy-connector maakt verbinding met de doel toepassing. De connector gebruikt een eigen IP-adres om de verbinding tot stand te brengen.

![Diagram van de gebruiker die verbinding maakt met een toepassing via een toepassings proxy](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-doorgestuurd-voor koptekst veld overwegingen
In sommige situaties (zoals auditing, taak verdeling enz.) moet u het oorspronkelijke IP-adres van de externe client delen met de on-premises omgeving. Om de vereiste op te lossen, voegt Azure AD-toepassingsproxy-connector het veld X-doorgestuurd-voor koptekst met het oorspronkelijke client-IP-adres (openbaar) toe aan de HTTP-aanvraag. De juiste netwerk apparaten (load balancer, firewall) of de webserver of de back-end-toepassing kunnen de gegevens vervolgens lezen en gebruiken.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Aanbevolen procedures voor taak verdeling tussen meerdere app-servers
Wanneer de connector groep die is toegewezen aan de toepassings proxy toepassing twee of meer connectors heeft en u de back-end-webtoepassing op meerdere servers (server farm) uitvoert, is een goede strategie voor taak verdeling vereist. Een goede strategie zorgt ervoor dat servers client aanvragen gelijkmatig ophalen en voor komen dat servers in de server farm worden gebruikt.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenario 1: back-end-toepassing vereist geen sessie persistentie
Het eenvoudigste scenario is dat de back-end-webtoepassing geen sessie persistentie vereist (sessie persistentie). Elke aanvraag van de gebruiker kan worden verwerkt door elk exemplaar van de back-end-toepassing in de server farm. U kunt een laag 4-load balancer gebruiken en deze configureren zonder affiniteit. Enkele opties zijn onder andere micro soft Network Load Balancing en Azure Load Balancer of een load balancer van een andere leverancier. Daarnaast kan Round-Robin DNS worden geconfigureerd.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenario 2: back-endtoepassing vereist een sessie persistentie
In dit scenario vereist de back-end-webtoepassing sessie persistentie (sessie persistentie) tijdens de geverifieerde sessie. Alle aanvragen van de gebruiker moeten worden verwerkt door het exemplaar van de back-end-toepassing dat wordt uitgevoerd op dezelfde server in de server farm.
Dit scenario kan ingewik kelder zijn omdat de client doorgaans meerdere verbindingen met de Application proxy-service tot stand brengt. Aanvragen via verschillende verbindingen kunnen aankomen op verschillende connectors en servers in de farm. Omdat elke connector een eigen IP-adres voor deze communicatie gebruikt, kan de load balancer de sessie persistentie niet garanderen op basis van het IP-adres van de connectors. De bron-IP-affiniteit kan niet worden gebruikt.
Hier volgen enkele opties voor scenario 2:

- Optie 1: de sessie persistentie baseren op een sessie cookie die is ingesteld door de load balancer. Deze optie wordt aanbevolen omdat de belasting gelijkmatig over de back-endservers kan worden verdeeld. Hiervoor is een laag 7-load balancer met deze mogelijkheid vereist, die het HTTP-verkeer kan verwerken en de TLS-verbinding kan beëindigen. U kunt Azure-toepassing gateway (sessie affiniteit) of een load balancer van een andere leverancier gebruiken.

- Optie 2: de sessie persistentie baseren op het veld X-doorgestuurd-voor koptekst. Deze optie vereist een laag 7-load balancer met deze mogelijkheid en dat het HTTP-verkeer kan verwerken en de TLS-verbinding kan beëindigen.  

- Optie 3: Configureer de back-end-toepassing om geen sessie persistentie te vereisen.

Raadpleeg de documentatie van uw software leverancier om inzicht te krijgen in de vereisten voor taak verdeling van de back-end-toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Toepassings proxy inschakelen](application-proxy-add-on-premises-application.md)
- [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
- [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)
- [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)
- [Meer informatie over hoe Azure AD-architectuur hoge Beschik baarheid ondersteunt](../fundamentals/active-directory-architecture.md)