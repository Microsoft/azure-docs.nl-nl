---
title: Back-ends met meerdere tenants
titleSuffix: Azure Application Gateway
description: Op deze pagina wordt de ondersteuning voor back-ends met meerdere tenants in Application Gateway beschreven.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397685"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway ondersteuning voor back-ends met meerdere tenants, zoals app service

In architectuur ontwerpen met meerdere tenants in webservers worden meerdere websites uitgevoerd op hetzelfde webserver exemplaar. Hostnamen worden gebruikt om onderscheid te maken tussen de verschillende toepassingen die worden gehost. Application Gateway wijzigt standaard niet de binnenkomende HTTP-host-header van de client en stuurt de header ongewijzigd terug naar de back-end. Dit werkt goed voor back-end-groeps leden, zoals Nic's, schaal sets voor virtuele machines, open bare IP-adressen, interne IP-adressen en FQDN, omdat deze niet afhankelijk zijn van een specifieke host-header of SNI-extensie om te kunnen omzetten naar het juiste eind punt. Er zijn echter veel services zoals Azure-app Services Web apps en Azure API Management die meerdere tenants op hetzelfde karakter hebben en afhankelijk zijn van een specifieke host-header of SNI-extensie om te kunnen omzetten naar het juiste eind punt. De DNS-naam van de toepassing, die op zijn beurt de DNS-naam die is gekoppeld aan de toepassings gateway, verschilt doorgaans van de domein naam van de back-end-service. Daarom is de host-header in de oorspronkelijke aanvraag die wordt ontvangen door de toepassings gateway niet hetzelfde als de hostnaam van de back-end-service. Als gevolg hiervan, tenzij de host-header in de aanvraag van de toepassings gateway naar de back-end is gewijzigd in de hostnaam van de back-end-service, kunnen de back-endservers van meerdere tenants de aanvraag niet omzetten naar het juiste eind punt. 

Application Gateway biedt een functie waarmee gebruikers de HTTP host-header in de aanvraag kunnen overschrijven op basis van de hostnaam van de back-end. Dankzij deze functie is er ondersteuning voor back-ends met meerdere tenants, zoals web-apps van Azure App Service en API-beheer. Deze functie is beschikbaar voor zowel de v1- en v2-standaard- als de WAF-SKU. 

![host negeren](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Dit is niet van toepassing op Azure-app service Environment (ASE), omdat ASE een specifieke resource is, in tegens telling tot Azure-app-service die een multi tenant resource is.

## <a name="override-host-header-in-the-request"></a>Host-header in de aanvraag overschrijven

De mogelijkheid om een host op te geven, wordt gedefinieerd in de [http-instellingen](./configuration-overview.md#http-settings) en kan worden toegepast op elke back-end-groep tijdens het maken van de regel. De volgende twee manieren om de host-header en de SNI-extensie voor back-ends met meerdere tenants te overschrijven, worden ondersteund:

- De mogelijkheid om de hostnaam in te stellen op een vaste waarde die expliciet is ingevoerd in de HTTP-instellingen. Op deze manier zorgt u ervoor dat de host-header wordt overschreven door deze waarde voor al het verkeer naar de back-end-groep waarin de specifieke HTTP-instellingen worden toegepast. Als u end-to-end TLS gebruikt, wordt deze overschreven hostnaam gebruikt in de SNI-extensie. Met deze mogelijkheid kunnen scenario's waarbij een back-end-pool farm een host-header verwacht die afwijkt van de inkomende host-header van de klant.

- De mogelijkheid om de hostnaam af te leiden van het IP-adres of de FQDN-naam van de back-end-groeps leden. HTTP-instellingen bieden ook een optie voor het dynamisch kiezen van de hostnaam uit de FQDN van een back-end-groep als deze is geconfigureerd met de optie voor het afleiden van een hostnaam van een afzonderlijke lid van een back-end-pool. Als u end-to-end TLS gebruikt, wordt deze hostnaam afgeleid van de FQDN-naam en gebruikt in de SNI-extensie. Met deze mogelijkheid kunnen scenario's waarin een back-end-pool twee of meer multi tenant PaaS Services, zoals Azure web apps en de hostheader van de aanvraag voor elk lid, de hostnaam bevatten die is afgeleid van de FQDN-naam. Voor de implementatie van dit scenario gebruiken we een switch in de HTTP-instellingen [Kies hostnaam van back-mailadres](./configuration-http-settings.md#pick-host-name-from-back-end-address) , waarmee de hostheader in de oorspronkelijke aanvraag dynamisch wordt overschreven naar de host die in de back-endadresgroep wordt vermeld.  Als uw back-end-groep bijvoorbeeld de naam ' contoso11.azurewebsites.net ' en ' contoso22.azurewebsites.net ' bevat, wordt de header van de oorspronkelijke aanvraag die contoso.com is, overschreven naar contoso11.azurewebsites.net of contoso22.azurewebsites.net wanneer de aanvraag wordt verzonden naar de juiste back-endserver. 

  ![web-app-scenario](./media/application-gateway-web-app-overview/scenario.png)

Met deze functie kunnen klanten opties opgeven in de HTTP-instellingen en kunnen ze aangepaste tests uitvoeren voor de gewenste configuratie. Deze instelling is vervolgens gekoppeld aan een listener en een back-end-pool met behulp van een regel.

## <a name="special-considerations"></a>Speciale overwegingen

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS-beëindiging en end-to-end TLS met multi tenant Services

TLS-beëindiging en end-to-end TLS-versleuteling wordt ondersteund met multi tenant-Services. TLS-certificaat voor het beëindigen van TLS op de Application Gateway blijft verplicht worden toegevoegd aan de listener van de Application Gateway. In het geval van end-to-end TLS, moeten vertrouwde Azure-Services, zoals Azure-app service-Web-apps, geen back-end in de toepassings gateway toestaan. Daarom is het niet nodig om verificatie certificaten toe te voegen. 

![end-to-end-TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

In de bovenstaande afbeelding is er geen vereiste om verificatie certificaten toe te voegen wanneer app service is geselecteerd als back-end.

### <a name="health-probe"></a>Statustest

Het overschrijven van de host-header in de **http-instellingen** is alleen van invloed op de aanvraag en de route ring. Dit heeft geen invloed op het gedrag van de status test. Als u wilt dat de end-to-end configuratie werkt, moeten zowel de test als de HTTP-instellingen worden bewerkt op basis van de juiste configuratie. Aangepaste tests bieden niet alleen de mogelijkheid om een host-header op te geven in de test configuratie, maar bieden ook ondersteuning voor de mogelijkheid om de host-header af te leiden van de momenteel geconfigureerde HTTP-instellingen. Deze configuratie kan worden opgegeven met behulp van de parameter `PickHostNameFromBackendHttpSettings` in de testconfiguratie.

### <a name="redirection-to-app-services-url-scenario"></a>Omleiding naar het URL-scenario van App Service

Er kunnen scenario's zijn waarbij de hostnaam in het antwoord van de app service de browser van de eind gebruiker kan omleiden naar de *. azurewebsites.net-hostnaam in plaats van het domein dat is gekoppeld aan de Application Gateway. Dit probleem kan zich voordoen wanneer:

- U hebt de omleiding geconfigureerd op uw App Service. Omleiding kan net zo eenvoudig zijn als het toevoegen van een afsluitende schuine streep aan de aanvraag.
- U hebt Azure AD-verificatie die de omleiding veroorzaakt.

Zie [problemen met omleiding naar het URL-probleem van de app service oplossen](./troubleshoot-app-service-redirection-app-service-url.md)om dergelijke gevallen op te lossen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een toepassings gateway met een multi tenant-app, zoals Azure-app service-Web-app als een lid van de back-end-groep, door te gaan [app service Web apps configureren met Application Gateway](./configure-web-app-portal.md)
